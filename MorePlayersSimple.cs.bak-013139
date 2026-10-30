using System.Reflection;
using BepInEx;
using BepInEx.Configuration;
using BepInEx.Logging;
using HarmonyLib;
using Photon.Pun;
using Photon.Realtime;
using UnityEngine;

namespace MorePlayersSimple
{
	[BepInPlugin("dyxc666.MorePlayersSimple", "More Players Simple", "1.2.5")]
	public class MorePlayersSimplePlugin : BaseUnityPlugin
	{
		internal static ManualLogSource Log;
		public static ConfigEntry<int> MaxPlayersPublic;
		public static ConfigEntry<int> MaxPlayersPrivate;

		internal static string SelfCreatedRoomName = string.Empty;

		private readonly Harmony harmony = new Harmony("dyxc666.MorePlayersSimple");

		private void Awake()
		{
			Log = Logger;

			MaxPlayersPublic = Config.Bind("General", "MaxPlayersPublic", 20,
				new ConfigDescription("公开房间最大玩家数（1-20）。修改后需重新开房生效。",
					new AcceptableValueRange<int>(1, 20)));

			MaxPlayersPrivate = Config.Bind("General", "MaxPlayersPrivate", 6,
				new ConfigDescription("私人房间最大玩家数（1-6）。修改后需重新开房生效。",
					new AcceptableValueRange<int>(1, 6)));

			harmony.PatchAll();

			Log.LogInfo($"More Players Simple v1.2.3 loaded! Public={MaxPlayersPublic.Value}, Private={MaxPlayersPrivate.Value}");
		}
	}

	// Photon 创建房间
	[HarmonyPatch(typeof(LoadBalancingClient), "OpCreateRoom")]
	public static class OpCreateRoomPatch
	{
		[HarmonyPrefix]
		public static bool Prefix(EnterRoomParams enterRoomParams)
		{
			if (enterRoomParams.RoomOptions == null)
				enterRoomParams.RoomOptions = new RoomOptions();

			bool isVisible = enterRoomParams.RoomOptions.IsVisible;
			byte target = (byte)(isVisible ? MorePlayersSimplePlugin.MaxPlayersPublic.Value : MorePlayersSimplePlugin.MaxPlayersPrivate.Value);

			enterRoomParams.RoomOptions.MaxPlayers = target;

			if (!string.IsNullOrEmpty(enterRoomParams.RoomName))
				MorePlayersSimplePlugin.SelfCreatedRoomName = enterRoomParams.RoomName;

			MorePlayersSimplePlugin.Log.LogInfo($"OpCreateRoom [{(isVisible ? "PUBLIC" : "PRIVATE")}]: MaxPlayers = {target}");
			return true;
		}
	}

	// JoinOrCreate 房间（含语音子房间，语音上限强制设为20）
	[HarmonyPatch(typeof(LoadBalancingClient), "OpJoinOrCreateRoom")]
	public static class OpJoinOrCreateRoomPatch
	{
		[HarmonyPrefix]
		public static bool Prefix(EnterRoomParams enterRoomParams)
		{
			if (enterRoomParams.RoomOptions == null)
				enterRoomParams.RoomOptions = new RoomOptions();

			bool isVoiceRoom = enterRoomParams.RoomName != null && enterRoomParams.RoomName.EndsWith("_voice_");
			bool isVisible = enterRoomParams.RoomOptions.IsVisible;
			byte target;

			if (isVoiceRoom)
			{
				// 语音子房间：强制设 20，不受私人房 6 人限制
				target = 20;
			}
			else
			{
				target = (byte)(isVisible ? MorePlayersSimplePlugin.MaxPlayersPublic.Value : MorePlayersSimplePlugin.MaxPlayersPrivate.Value);
			}

			enterRoomParams.RoomOptions.MaxPlayers = target;

			if (!string.IsNullOrEmpty(enterRoomParams.RoomName))
				MorePlayersSimplePlugin.SelfCreatedRoomName = enterRoomParams.RoomName;

			MorePlayersSimplePlugin.Log.LogInfo($"[JoinOrCreate] [{(isVisible ? "PUBLIC" : "PRIVATE")}]{(isVoiceRoom ? " [VOICE]" : "")}: MaxPlayers = {target}");
			return true;
		}
	}

	// Steam 大厅上限
	[HarmonyPatch(typeof(SteamLobbyManager), "HostLobby")]
	public static class HostLobbyPatch
	{
		[HarmonyPostfix]
		public static void Postfix(SteamLobbyManager __instance)
		{
			try
			{
				var lobbyField = AccessTools.Field(typeof(SteamLobbyManager), "currentLobby");
				if (lobbyField == null) return;

				var lobby = lobbyField.GetValue(__instance);
				if (lobby == null) return;

				var lobbyType = lobby.GetType();
				var setLimitMethod = lobbyType.GetMethod("SetLobbyMemberLimit");
				if (setLimitMethod != null)
				{
					setLimitMethod.Invoke(lobby, new object[] { MorePlayersSimplePlugin.MaxPlayersPublic.Value });
					MorePlayersSimplePlugin.Log.LogInfo($"Steam Lobby member limit set to {MorePlayersSimplePlugin.MaxPlayersPublic.Value}");
				}
				else
				{
					var maxMembersField = lobbyType.GetField("MaxMembers") ?? lobbyType.GetField("maxMembers");
					if (maxMembersField != null)
					{
						maxMembersField.SetValue(lobby, MorePlayersSimplePlugin.MaxPlayersPublic.Value);
						MorePlayersSimplePlugin.Log.LogInfo($"Steam Lobby MaxMembers set to {MorePlayersSimplePlugin.MaxPlayersPublic.Value}");
					}
				}
			}
			catch
			{
				MorePlayersSimplePlugin.Log.LogWarning("Failed to set Steam lobby member limit.");
			}
		}
	}

	// 房间列表 UI
	[HarmonyPatch(typeof(MenuPageServerList), "CreateServerElement")]
	public static class CreateServerElementPatch
	{
		[HarmonyPrefix]
		public static bool Prefix(object _room)
		{
			if (_room == null) return true;

			var roomNameField = _room.GetType().GetField("roomName", BindingFlags.Public | BindingFlags.Instance);
			if (roomNameField == null) return true;

			var roomName = roomNameField.GetValue(_room) as string;
			if (string.IsNullOrEmpty(roomName)) return true;

			bool isSelfRoom = (roomName == MorePlayersSimplePlugin.SelfCreatedRoomName);
			if (!isSelfRoom && PhotonNetwork.InRoom && PhotonNetwork.CurrentRoom != null)
				isSelfRoom = (roomName == PhotonNetwork.CurrentRoom.Name);

			if (isSelfRoom)
			{
				var maxPlayersField = _room.GetType().GetField("maxPlayers", BindingFlags.Public | BindingFlags.Instance);
				if (maxPlayersField != null)
					maxPlayersField.SetValue(_room, MorePlayersSimplePlugin.MaxPlayersPublic.Value);
			}

			return true;
		}
	}
}