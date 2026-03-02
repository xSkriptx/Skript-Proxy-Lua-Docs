# SkriptProxy Lua API

## Functions

| Function | Description |
|----------|-------------|
| [SendPacket](#sendpacket) | Send text packet to server |
| [SendPacketRaw](#sendpacketraw) | Send GamePacket to server |
| [SendPacketRawClient](#sendpacketrawclient) | Send GamePacket to client |
| [SendVarlist](#sendvarlist) | Send varlist to client |
| [SendPacketToClient](#sendpackettoclient) | Send text packet to client |
| [SendChat](#sendchat) | Send chat message |
| [Warp](#warp) | Warp to world |
| [log](#log) | Log to console |
| [Sleep](#sleep) | Delay in thread |
| [RunThread](#runthread) | Run function in new thread |
| [GetLocal](#getlocal) | Get local player |
| [GetPlayers](#getplayers) | Get all players |
| [GetInventory](#getinventory) | Get inventory items |
| [GetItemCount](#getitemcount) | Get item count by id |
| [GetIteminfo](#getiteminfo) | Get item info by id |
| [GetObjects](#getobjects) | Get dropped world objects |
| [GetTile](#gettile) | Get tile at position |
| [GetTiles](#gettiles) | Get all world tiles |
| [GetPlayerPos](#getplayerpos) | Get local player position |
| [GetPlayerName](#getplayername) | Get local player name |
| [GetWorldName](#getworldname) | Get current world name |
| [GetPing](#getping) | Get ping in ms |
| [MessageBox](#messagebox) | Show dialog to client |
| [AddCallback](#addcallback) | Register event hook |
| [RemoveCallback](#removecallback) | Remove callback by name |
| [RemoveCallbacks](#removecallbacks) | Remove all callbacks |
| [IsSolid](#issolid) | Check if tile is solid |

---

## SendPacket

\`\`\`lua
SendPacket(int type, string packet)
\`\`\`

Sends a text packet to the GT server. A trailing `\n` is appended automatically if missing.

| Type | Description |
|------|-------------|
| `2` | `NET_MESSAGE_GENERIC_TEXT` — use for `action|input`, `action|respawn`, etc. |
| `3` | `NET_MESSAGE_GAME_MESSAGE` — use for `action|join_request` only |

\`\`\`lua
SendPacket(2, "action|respawn")
SendPacket(2, "action|input\ntext|hello world")
SendPacket(3, "action|join_request\nname|START\ninvitedWorld|0")
\`\`\`

---

## SendPacketRaw

\`\`\`lua
SendPacketRaw(GamePacket packet)
\`\`\`

Sends a [GamePacket](#gamepacket) to the GT server.

\`\`\`lua
local packet = {}
packet.type = 10
packet.int_data = 48  -- Clothing ID (Jeans)
SendPacketRaw(packet)
\`\`\`

---

## SendPacketRawClient

\`\`\`lua
SendPacketRawClient(GamePacket packet)
\`\`\`

Sends a [GamePacket](#gamepacket) to the game client (inject server→client).

\`\`\`lua
local packet = {}
packet.type = 0
packet.flags = 48
SendPacketRawClient(packet)
\`\`\`

---

## SendVarlist

\`\`\`lua
SendVarlist(table varlist)
\`\`\`

Sends a varlist packet to the game client. `netid` is required.

\`\`\`lua
local var = {}
var[0] = "OnConsoleMessage"
var[1] = "Hello!"
var.netid = -1
SendVarlist(var)
\`\`\`

---

## SendPacketToClient

\`\`\`lua
SendPacketToClient(int type, string packet)
\`\`\`

Sends a text packet directly to the game client.

\`\`\`lua
SendPacketToClient(2, "action|log\nmsg|injected")
\`\`\`

---

## SendChat

\`\`\`lua
SendChat(string text)
\`\`\`

Sends chat through the event system (triggers OnPacket callbacks, honors command hooks).

\`\`\`lua
SendChat("hello world")
SendChat("/ban badguy")
\`\`\`

---

## Warp

\`\`\`lua
Warp(string world)
\`\`\`

\`\`\`lua
Warp("START")
\`\`\`

---

## log

\`\`\`lua
log(string message)
\`\`\`

Logs to the proxy debug console.

\`\`\`lua
log("Hello!")
log("Ping: " .. tostring(GetPing()))
\`\`\`

---

## Sleep

\`\`\`lua
Sleep(int ms)
\`\`\`

Pauses the current thread. Only use inside `RunThread`.

\`\`\`lua
RunThread(function()
    log("start")
    Sleep(2000)
    log("2 seconds later")
end)
\`\`\`

---

## RunThread

\`\`\`lua
RunThread(function)
\`\`\`

Runs a function in a background thread. Required for loops and Sleep.

\`\`\`lua
RunThread(function()
    while true do
        SendPacket(2, "action|respawn")
        Sleep(5000)
    end
end)
\`\`\`

---

## GetLocal

\`\`\`lua
GetLocal() -> NetAvatar
\`\`\`

\`\`\`lua
local me = GetLocal()
log(me.name .. " in " .. me.world)
\`\`\`

---

## GetPlayers

\`\`\`lua
GetPlayers() -> table[NetAvatar]
\`\`\`

\`\`\`lua
for _, p in pairs(GetPlayers()) do
    log(p.name .. " netid=" .. p.netid)
end
\`\`\`

---

## GetInventory

\`\`\`lua
GetInventory() -> table[InventoryItem]
\`\`\`

\`\`\`lua
for _, item in pairs(GetInventory()) do
    log("id=" .. item.id .. " count=" .. item.count)
end
\`\`\`

---

## GetItemCount

\`\`\`lua
GetItemCount(int id) -> int
\`\`\`

\`\`\`lua
log("Dirt: " .. GetItemCount(2))
\`\`\`

---

## GetIteminfo

\`\`\`lua
GetIteminfo(int id) -> ItemInfo
\`\`\`

\`\`\`lua
log(GetIteminfo(2).name)
\`\`\`

---

## GetObjects

\`\`\`lua
GetObjects() -> table[WorldObject]
\`\`\`

\`\`\`lua
for _, obj in pairs(GetObjects()) do
    log("id=" .. obj.id .. " at " .. obj.pos_x .. "," .. obj.pos_y)
end
\`\`\`

---

## GetTile

\`\`\`lua
GetTile(int x, int y) -> Tile
\`\`\`

\`\`\`lua
local tile = GetTile(5, 3)
log("fg=" .. tile.fg)
\`\`\`

---

## GetTiles

\`\`\`lua
GetTiles() -> table[Tile]
\`\`\`

\`\`\`lua
for _, tile in pairs(GetTiles()) do
    if tile.fg == 2 then
        log("Dirt at " .. tile.pos_x .. "," .. tile.pos_y)
    end
end
\`\`\`

---

## GetPlayerPos

\`\`\`lua
GetPlayerPos() -> float x, float y
\`\`\`

\`\`\`lua
local x, y = GetPlayerPos()
log(x .. ", " .. y)
\`\`\`

---

## GetPlayerName

\`\`\`lua
GetPlayerName() -> string
\`\`\`

---

## GetWorldName

\`\`\`lua
GetWorldName() -> string
\`\`\`

---

## GetPing

\`\`\`lua
GetPing() -> int
\`\`\`

\`\`\`lua
log("Ping: " .. GetPing() .. "ms")
\`\`\`

---

## MessageBox

\`\`\`lua
MessageBox(string title, string content)
\`\`\`

\`\`\`lua
MessageBox("Info", "Script loaded!")
\`\`\`

---

## AddCallback

\`\`\`lua
AddCallback(string name, string event, function fn)
\`\`\`

Registers a callback. Return `true` to cancel the packet.

### Events

| Event | Args | Direction |
|-------|------|-----------|
| `OnVarlist` | `(table varlist, string raw)` | Server → Client |
| `OnPacket` | `(int type, string packet)` | Client → Server |
| `OnRawPacket` | `(GamePacket packet)` | Client → Server |
| `OnIncomingRawPacket` | `(GamePacket packet)` | Server → Client |

\`\`\`lua
-- Block all dialogs
AddCallback("blockdlg", "OnVarlist", function(varlist, raw)
    if varlist[0] and varlist[0]:find("OnDialogRequest") then
        return true
    end
end)

-- Log outgoing chat
AddCallback("logchat", "OnPacket", function(type, packet)
    if packet:find("action|input") then
        log("OUT: " .. packet)
    end
end)

-- Block own state packet
AddCallback("nostate", "OnRawPacket", function(packet)
    if packet.type == 0 then return true end
end)

-- Log incoming state
AddCallback("logstate", "OnIncomingRawPacket", function(packet)
    if packet.type == 0 then
        log("State netid=" .. packet.netid .. " flags=" .. packet.flags)
    end
end)
\`\`\`

---

## RemoveCallback

\`\`\`lua
RemoveCallback(string name)
\`\`\`

---

## RemoveCallbacks

\`\`\`lua
RemoveCallbacks()
\`\`\`

---

## IsSolid

\`\`\`lua
IsSolid(int x, int y) -> bool
\`\`\`

---

# Structs

## NetAvatar

| Type | Field | Description |
|------|-------|-------------|
| string | `name` | Player name |
| string | `world` | Current world (local only) |
| string | `country` | Country/flag ID |
| float | `pos_x` | X pixel position |
| float | `pos_y` | Y pixel position |
| int | `tile_x` | X tile position |
| int | `tile_y` | Y tile position |
| int | `netid` | Net ID |
| int | `userid` | User ID |
| int | `gems` | Gem count |
| bool | `facing_left` | Facing left |
| int | `flags` | Player flags |

## WorldObject

| Type | Field | Description |
|------|-------|-------------|
| int | `id` | Item ID |
| int | `oid` | Object index |
| float | `pos_x` | X pixel position |
| float | `pos_y` | Y pixel position |
| int | `count` | Item count |
| int | `flags` | Object flags |

## InventoryItem

| Type | Field | Description |
|------|-------|-------------|
| int | `id` | Item ID |
| int | `count` | Item count |

## Tile

| Type | Field | Description |
|------|-------|-------------|
| int | `fg` | Foreground block ID |
| int | `bg` | Background block ID |
| int | `pos_x` | Tile X position |
| int | `pos_y` | Tile Y position |
| int | `flags` | Tile flags |
| bool | `water` | Has water |
| bool | `fire` | On fire |
| bool | `ready` | Ready to harvest |

## GamePacket

| Type | Field | Description |
|------|-------|-------------|
| int | `type` | Packet type |
| int | `netid` | Net ID |
| int | `flags` | Packet flags |
| int | `int_data` | Data size / generic int |
| int | `decompressed_size` | Decompressed data size |

## VariantList (OnVarlist table)

| Type | Field | Description |
|------|-------|-------------|
| int | `netid` | Net ID |
| string | `[0]` | Function name |
| any | `[1]` | Param 1 |
| any | `[2]` | Param 2 |
| any | `[3]` | Param 3 |
| any | `[4]` | Param 4 |
| any | `[5]` | Param 5 |

## ItemInfo

| Type | Field | Description |
|------|-------|-------------|
| string | `name` | Item name |
| int | `item_type` | Item type |
| int | `growth` | Growth time |
| int | `rarity` | Rarity |
| int | `size` | List size |
