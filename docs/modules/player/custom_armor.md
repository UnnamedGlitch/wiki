## Simple 

Simple ass fucking armor to prevent fuckups

### Implementation Concept

### Custom Data Format

The following data is set at the path `UF.custom_armor` in the `minecraft:custom_data` data component.

- `(S) id`  
  : A unique resource identifier in the format `<namespace>:<name>`.
- `(E) type`
  : The type of armor. Can be one of 'simple' or 'ability'.
- `{.} data`  
  : A user-controlled field for storing armor-specific ability data.

## Ability

This armor type enables a passive ability via a user-defined enchantment.

??? warning "Warn"
    Custom armor pieces are based on the chainmail armor set. Other armor types do not support custom abilities. Use data components and custom item models to overwrite vanilla Minecraft behavior and textures. Click [here](https://minecraft.wiki/w/Data_component_format) for more information on data components.

??? abstract "Technical Concept"
    Equipping custom armor pieces is tracked using a `minecraft:inventory_changed` advancement trigger on a per-piece basis. When a custom armor piece is equipped, the specified enchantment is applied, activating the associated ability.

    Unequipping custom armor pieces is tracked using a single global `minecraft:inventory_changed` advancement trigger. When a custom armor piece is unequipped, the associated function locates the new slot the armor piece has moved to and removes the specified enchantment, deactivating the corresponding ability.

    ??? warning "Info"
        This can result in the ability being briefly deactivated and immediately reactivated if the player quickly moves the item from and back into an armor slot. This behavior is a limitation of the current detection system and cannot be fully avoided.

    Dropping a custom armor piece directly from an armor slot is tracked using a `minecraft:inventory_changed` advancement trigger, along with scoreboard entries using the `minecraft.dropped` criteria for each chainmail armor piece (`minecraft:chainmail_helmet`, `minecraft:chainmail_chestplate`, `minecraft:chainmail_leggings`, and `minecraft:chainmail_boots`). When a custom armor piece is dropped, the associated function locates the new item stack of the custom armor piece and removes the specified enchantment, thereby deactivating the corresponding ability.

    By dynamically adding and removing the custom enchantment from custom armor pieces, performance is improved, as there's no need to constantly check whether the enchantment is active in a specific inventory slot. This also reduces visual clutter on the item and allows it to be freely enchanted using the enchanting table. As a result, this method is minimally invasive to vanilla player behavior.

### Custom Data Format

The following data is set at the path `UF.custom_armor` in the `minecraft:custom_data` data component.

- `(S) id`  
  : A unique resource identifier in the format `<namespace>:<name>`.
- `(E) type`
  : The type of armor. Can be one of 'simple' or 'ability'.
- `{.} data`  
  : A user-controlled field for storing armor-specific ability data.
- `{.} ability`
    - `(B) $active`
      : An internal activation flag.
    - `(S) enchantment`
      : The resource location of a user-defined armor enchantment.

### Examples

??? example "Ability"

    This loot table provides the player with a `minecraft:chainmail_helmet` that has a passive ability enchantment. The used ability enchantment is found at the resource location `example:passive`.

    Click [here](https://minecraft.wiki/w/Enchantment_definition) for more information on enchantment definition.

    To maintain compatibility with future updates of this framework, the general item modifier is applied first. The specific data for the axe is then overwritten, ensuring seamless integration with potential future changes.

        --8<-- "docs/modules/player/examples/custom_armor/ability.json"
        