# SkriptProxy Lua API

## Functions

| Function | Description |
|----------|-------------|
| SendPacket | Send text packet to server |
| SendPacketRaw | Send GamePacket to server |
| SendPacketRawClient | Send GamePacket to client |
| SendVarlist | Send varlist to client |
| SendPacketToClient | Send text packet to client |
| SendChat | Send chat message |
| Warp | Warp to world |
| log | Log to console |
| Sleep | Delay in thread |
| RunThread | Run function in new thread |
| GetLocal | Get local player |
| GetPlayers | Get all players |
| GetInventory | Get inventory items |
| GetItemCount | Get item count by id |
| GetIteminfo | Get item info by id |
| GetObjects | Get dropped world objects |
| GetTile | Get tile at position |
| GetTiles | Get all world tiles |
| GetPlayerPos | Get local player position |
| GetPlayerName | Get local player name |
| GetWorldName | Get current world name |
| GetPing | Get ping in ms |
| MessageBox | Show dialog to client |
| AddCallback | Register event hook |
| RemoveCallback | Remove callback by name |
| RemoveCallbacks | Remove all callbacks |
| IsSolid | Check if tile is solid |

---

## SendPacket

```lua
SendPacket(int type, string packet)
```

Sends a text packet to the GT server. A trailing `\n` is appended automatically if missing.

| Type | Description |
|------|-------------|
| 2 | NET_MESSAGE_GENERIC_TEXT — use for action|input, action|respawn, etc. |
| 3 | NET_MESSAGE_GAME_MESSAGE — use for action|join_request only |

```lua
SendPacket(2, "action|respawn")
SendPacket(2, "action|input\ntext|hello world")
SendPacket(3, "action|join_request\nname|START\ninvitedWorld|0")
```

---

## SendPacketRaw

```lua
SendPacketRaw(GamePacket packet)
```

Sends a GamePacket to the GT server.

```lua
local packet = {}
packet.type = 10
packet.int_data = 48
SendPacketRaw(packet)
```

---

## SendPacketRawClient

```lua
SendPacketRawClient(GamePacket packet)
```

Sends a GamePacket to the game client (inject server→client).

```lua
local packet = {}
packet.type = 0
packet.flags = 48
SendPacketRawClient(packet)
```

---

## SendVarlist

```lua
SendVarlist(table varlist)
```

Sends a varlist packet to the game client. `netid` is required.

```lua
local var = {}
var[0] = "OnConsoleMessage"
var[1] = "Hello!"
var.netid = -1
SendVarlist(var)
```

---

## SendPacketToClient

```lua
SendPacketToClient(int type, string packet)
```

Sends a text packet directly to the game client.

```lua
SendPacketToClient(2, "action|log\nmsg|injected")
```

---

## SendChat

```lua
SendChat(string text)
```

Sends chat through the event system.

```lua
SendChat("hello world")
SendChat("/ban badguy")
```

---

## Warp

```lua
Warp(string world)
```

```lua
Warp("START")
```

---

## log

```lua
log(string message)
```

Logs to the proxy debug console.

```lua
log("Hello!")
log("Ping: " .. tostring(GetPing()))
```

---

## Sleep

```lua
Sleep(int ms)
```

Pauses the current thread. Only use inside `RunThread`.

```lua
RunThread(function()
    log("start")
    Sleep(2000)
    log("2 seconds later")
end)
```

---

## RunThread

```lua
RunThread(function)
```

Runs a function in a background thread.

```lua
RunThread(function()
    while true do
        SendPacket(2, "action|respawn")
        Sleep(5000)
    end
end)
```

---

## GetLocal

```lua
GetLocal() -> NetAvatar
```

```lua
local me = GetLocal()
log(me.name .. " in " .. me.world)
```

---

## GetPlayers

```lua
GetPlayers() -> table[NetAvatar]
```

```lua
for _, p in pairs(GetPlayers()) do
    log(p.name .. " netid=" .. p.netid)
end
```

---

## GetInventory

```lua
GetInventory() -> table[InventoryItem]
```

```lua
for _, item in pairs(GetInventory()) do
    log("id=" .. item.id .. " count=" .. item.count)
end
```

---

## GetItemCount

```lua
GetItemCount(int id) -> int
```

```lua
log("Dirt: " .. GetItemCount(2))
```

---

## GetIteminfo

```lua
GetIteminfo(int id) -> ItemInfo
```

```lua
log(GetIteminfo(2).name)
```

---

## GetObjects

```lua
GetObjects() -> table[WorldObject]
```

```lua
for _, obj in pairs(GetObjects()) do
    log("id=" .. obj.id .. " at " .. obj.pos_x .. "," .. obj.pos_y)
end
```

---

## GetTile

```lua
GetTile(int x, int y) -> Tile
```

```lua
local tile = GetTile(5, 3)
log("fg=" .. tile.fg)
```

---

## GetTiles

```lua
GetTiles() -> table[Tile]
```

```lua
for _, tile in pairs(GetTiles()) do
    if tile.fg == 2 then
        log("Dirt at " .. tile.pos_x .. "," .. tile.pos_y)
    end
end
```

---

## GetPlayerPos

```lua
GetPlayerPos() -> float x, float y
```

```lua
local x, y = GetPlayerPos()
log(x .. ", " .. y)
```

---

## GetPlayerName

```lua
GetPlayerName() -> string
```

---

## GetWorldName

```lua
GetWorldName() -> string
```

---

## GetPing

```lua
GetPing() -> int
```

```lua
log("Ping: " .. GetPing() .. "ms")
```

---

## MessageBox

```lua
MessageBox(string title, string content)
```

```lua
MessageBox("Info", "Script loaded!")
```

---

## AddCallback

```lua
AddCallback(string name, string event, function fn)
```

Return `true` to cancel the packet.

### Events

| Event | Args | Direction |
|-------|------|-----------|
| OnVarlist | (table varlist, string raw) | Server → Client |
| OnPacket | (int type, string packet) | Client → Server |
| OnRawPacket | (GamePacket packet) | Client → Server |
| OnIncomingRawPacket | (GamePacket packet) | Server → Client |

```lua
AddCallback("blockdlg", "OnVarlist", function(varlist, raw)
    if varlist[0] and varlist[0]:find("OnDialogRequest") then
        return true
    end
end)

AddCallback("logchat", "OnPacket", function(type, packet)
    if packet:find("action|input") then
        log("OUT: " .. packet)
    end
end)

AddCallback("nostate", "OnRawPacket", function(packet)
    if packet.type == 0 then return true end
end)

AddCallback("logstate", "OnIncomingRawPacket", function(packet)
    if packet.type == 0 then
        log("State netid=" .. packet.netid .. " flags=" .. packet.flags)
    end
end)
```

---

## RemoveCallback

```lua
RemoveCallback(string name)
```

---

## RemoveCallbacks

```lua
RemoveCallbacks()
```

---

## IsSolid

```lua
IsSolid(int x, int y) -> bool
```

---

# Structs

## NetAvatar

| Type | Field |
|------|-------|
| string | name |
| string | world |
| string | country |
| float | pos_x |
| float | pos_y |
| int | tile_x |
| int | tile_y |
| int | netid |
| int | userid |
| int | gems |
| bool | facing_left |
| int | flags |

## WorldObject

| Type | Field |
|------|-------|
| int | id |
| int | oid |
| float | pos_x |
| float | pos_y |
| int | count |
| int | flags |

## InventoryItem

| Type | Field |
|------|-------|
| int | id |
| int | count |

## Tile

| Type | Field |
|------|-------|
| int | fg |
| int | bg |
| int | pos_x |
| int | pos_y |
| int | flags |
| bool | water |
| bool | fire |
| bool | ready |

## GamePacket

| Type | Field |
|------|-------|
| int | type |
| int | netid |
| int | flags |
| int | int_data |
| int | decompressed_size |

## VariantList

| Type | Field |
|------|-------|
| int | netid |
| string | [0] |
| any | [1] |
| any | [2] |
| any | [3] |
| any | [4] |
| any | [5] |

## ItemInfo

| Type | Field |
|------|-------|
| string | name |
| int | item_type |
| int | growth |
| int | rarity |
| int | size |
