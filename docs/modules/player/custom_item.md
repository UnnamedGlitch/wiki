Enables items to have primary and secondary abilities, each of which invokes a custom function upon activation. Items can be configured to be consumed or preserved when an ability is used. A single, shared cooldown can be configured for both abilities, and items can be assigned to a cooldown group for coordinated cooldown behavior.

### Technical Concept

Uses the `minecraft:consumable` data component in combination with `minecraft:consume_item` advancement triggers to execute a custom function when the item is right-clicked (primary ability) or shift + right-clicked (secondary ability). A shared cooldown is applied to the associated custom item group upon use via the `minecraft:use_cooldown` data component.

### Custom Data Format

The following data is set in the `minecraft:custom_data` data component under the global namespace `UF` of the framework.

```yaml
custom_item
├──(🇸) id: Unique resource identifier. Must be of '<namespace>:<name>'.
├──(🇪) type: The type of the item. Can be one of 'simple' or 'ability'.
├──{.} data: Custom data field for item specific data storage.
└──{.} ability: Specification of the primary and / or secondary ability.
    ├──{.} primary: Specifies the RIGHT-CLICK ability of the item. Can be one of 'empty' or an NBT object.
    │   ├──(🇧) consume_item: Whether the item should be consumed on ability activation.
    │   └──(🇸) function: The function to execute on ability activation.
    └──{.} secondary: Specifies the SHIFT+RIGHT-CLICK ability of the item. Can be one of 'empty' or an NBT object.
        ├──(🇧) consume_item: Whether the item should be consumed on ability activation.
        └──(🇸) function: The function to execute on ability activation.
``` 

### Data Components Format

```yaml
use_cooldown
├──(🇮) seconds: The item's cooldown in seconds.
└──(🇸) cooldown_group: The item's cooldown group. Can be one of 'default' or '<namespace>:<name>'.
``` 

### Examples

<details style="border: 1px solid #ccc; padding: 10px; border-radius: 5px;">
<summary>Dual Ability</summary>

</br>

This loot table provides the player with a `minecraft:diamond_axe` that has a primary and a secondary ability:

1. **Right-click**: Executes the function at the resource location `example:primary`.
2. **Shift + Right-click**: Executes the function at the resource location `example:secondary`.

Click [here](https://minecraft.wiki/w/Function_(Java_Edition)) for more information on functions.

To maintain compatibility with future updates of this framework, the general item modifier is applied first. The specific data for the axe is then overwritten, ensuring seamless integration with potential future changes.

```json
{
  "pools": [
    {
      "rolls": 1,
      "entries": [
        {
          "type": "minecraft:item",
          "name": "minecraft:diamond_axe",
          "weight": 1,
          "functions": [
            {
              "function": "minecraft:reference",
              "name": "uf_api:player/custom_item/ability"
            },
            {
              "function": "minecraft:set_custom_data",
              "tag": {
                "UF": {
                  "custom_item": {
                    "id": "example:my_super_special_axe",
                    "ability": {
                      "primary": {
                        "consume_item": false,
                        "function": "example:primary"
                      },
                      "secondary": {
                        "consume_item": false,
                        "function": "example:secondary"
                      }
                    }
                  }
                }
              }
            }
          ]
        }
      ]
    }
  ]
}
```
</details>

<details style="border: 1px solid #ccc; padding: 10px; border-radius: 5px;">
<summary>Single Ability</summary>

</br>

This loot table provides the player with a `minecraft:diamond_axe` that has only a primary ability:

1. **Right-click**: Executes the function at the resource location `example:primary`.
2. **Shift + Right-click**: Also executes the function at the resource location `example:primary`.

Click [here](https://minecraft.wiki/w/Function_(Java_Edition)) for more information on functions.

To maintain compatibility with future updates of this framework, the general item modifier is applied first. The specific data for the axe is then overwritten, ensuring seamless integration with potential future changes.

```json
{
  "pools": [
    {
      "rolls": 1,
      "entries": [
        {
          "type": "minecraft:item",
          "name": "minecraft:diamond_axe",
          "weight": 1,
          "functions": [
            {
              "function": "minecraft:reference",
              "name": "uf_api:player/custom_item/ability"
            },
            {
              "function": "minecraft:set_custom_data",
              "tag": {
                "UF": {
                  "custom_item": {
                    "id": "example:my_super_special_axe",
                    "ability": {
                      "primary": {
                        "consume_item": false,
                        "function": "example:primary"
                      },
                      "secondary": "empty"
                    }
                  }
                }
              }
            }
          ]
        }
      ]
    }
  ]
}
```
</details>

<details style="border: 1px solid #ccc; padding: 10px; border-radius: 5px;">
<summary>Single Ability with Cooldown</summary>

</br>

This loot table provides the player with a `minecraft:diamond_axe` that has only a primary ability and a cooldown of `3s` in the cooldown group `example:axes`:

1. **Right-click**: Executes the function at the resource location `example:primary`.
2. **Shift + Right-click**: Also executes the function at the resource location `example:primary`.

Click [here](https://minecraft.wiki/w/Function_(Java_Edition)) for more information on functions.
Click [here](https://minecraft.wiki/w/Data_component_format) for more information on data components.

To maintain compatibility with future updates of this framework, the general item modifier is applied first. The specific data for the axe is then overwritten, ensuring seamless integration with potential future changes.

```json
{
  "pools": [
    {
      "rolls": 1,
      "entries": [
        {
          "type": "minecraft:item",
          "name": "minecraft:diamond_axe",
          "weight": 1,
          "functions": [
            {
              "function": "minecraft:reference",
              "name": "uf_api:player/custom_item/ability"
            },
            {
              "function": "minecraft:set_custom_data",
              "tag": {
                "UF": {
                  "custom_item": {
                    "id": "example:my_super_special_axe",
                    "ability": {
                      "primary": {
                        "consume_item": false,
                        "function": "example:primary"
                      },
                      "secondary": "empty"
                    }
                  }
                }
              }
            },
            {
              "function": "minecraft:set_components",
              "components": {
                "minecraft:use_cooldown": {
                  "seconds": 3,
                  "cooldown_group": "example:axes"
                }
              }
            }
          ]
        }
      ]
    }
  ]
}
```
</details>
