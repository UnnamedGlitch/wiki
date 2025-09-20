This dimension is utilized by other submodules to temporarily store data that cannot be efficiently maintained in custom storages. By default, the framework force-loads the chunk at coordinates `(0, 0)` to ensure it is always available and places a stone layer at `y = 0` to prevent summoned entities from falling into the void.

| Position                | Purpose                                                  |
|-------------------------|----------------------------------------------------------|
|    000    001    000    | Temporary barrel placement.                              |
|    001    001    000    | Armor stand summoning.                                   |