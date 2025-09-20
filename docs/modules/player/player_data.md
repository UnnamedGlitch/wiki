This module manages player-specific data, provides easy and efficient access to it, and automatically ensures synchronization across player rejoining, thereby avoiding recalculation each time the data is needed. To achieve this, the two advancements `join` and `rejoin`, invoked via `minecraft:tick` advancement triggers, regulate the conditions under which player data may be initialized or resynchronized.

### Join

The `join` advancement ensures that each newly joined player's data, if not previously initialized, is properly initialized.

As part of its core functionality, the module initializes several scores for each newly joined player. It assigns a unique `player identifier (PID)` and sets the player’s `reset index` to align with the global one.

Accessible via distributed PIDs, players also receive a custom storage, which contains the following data:

| Path                    | Data                                                     |
|-------------------------|----------------------------------------------------------|
| Name                    | The player's custom name.                                |
| UUID                    | The player's hyphenated hexadecimal UUID.                |

### Rejoin

The `rejoin` advancement ensures that each newly joined player's data, if previously initialized, is properly resynchronized.

>💡 **Note:** If a player rejoins and is out of sync with the reset cycles (as indicated by the corresponding _reset index_ scoreboard entry), the player is forcefully reinitialized and subsequently resynchronized.