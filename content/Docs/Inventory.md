# Inventory
The main module of the plugin, provides the **inventories**, **items** and other base features.
![](Assets/Pasted%20image%2020260419150632.png)
## Item Descriptors
Item descriptors are assets that, as the name suggests, describe an item: How it behaves, how it looks, what it can do, etc.

The two most defining elements of a descriptor are *Fragments* and *Parameters*.
![](Assets/Pasted%20image%2020260419234446.png)
## Item Fragments
An Item Descriptor can have one or more *Fragments* that define the capabilities of the item.
Item Fragments are blueprints and can be scripted both in CPP and in blueprints.

Fragments built into the plugin:
- **Instantiable**: (Provided with the plugin)
  Can this item be "physically" in the world? How should it look?
- **UIExtension**: (Provided with the plugin. Requires Epic's UIExtension plugin.)
  Should the Item show in UI, where and how?

Custom fragment examples you could implement:
- **Equippable**: The item can be equipped. Which hand? Is it a weapon? Does it have ammo?
- **Spawnable**: The item can spawn randomly in the world. How should it do it? How many times?

If you are working with GAS, fragments can do things like grant abilities when the item is added or on events, remove them when the item is removed, apply effects, etc.

> [!TIP] You can add a display names and icons to item fragments for better readability
> ![](Assets/Pasted%20image%2020260419205906.png)![](Assets/Pasted%20image%2020260419210055.png)

### Creating a Fragment Type
Fragments must inherit `UIEItemFragment`.

{{< details title="Blueprints" >}}
<!--![](../Assets/Pasted%20image%2020260419205906.png)-->
{{< /details >}}

{{< details title="CPP" closed="true" >}}
```cpp
#include <IEItemDescriptor.h>

UCLASS(Meta = (DisplayName = "My Fragment"))
class UItemFragment_MyFragment : public UIEItemFragment
{
	GENERATED_BODY()

public:
	// Property exposed to item descriptors:
	UPROPERTY(EditAnywhere, Category = WidgetExtension)
	FGameplayTag Tag;

	// Optional functions to provide logic to an item inside an inventory slot:
	void OnSlotAdded(UIEInventorySlot* Slot) const override;
	void OnSlotRemoved(UIEInventorySlot* Slot) const override;
	void OnEvent(FGameplayTag EventTag, const struct FInstancedStruct& EventData, const TArray<UIEInventorySlot*>& Slots) const override;
};
```
{{< /details >}}


## Item Parameters
> WIP

Parameters are runtime data bound to an item instance (`FIEItem`) and identified by a gameplay tag. This data is usually in the form of structs, or literals like integers, floats or bools.

## Items
When we work with items we usually refer to Struct instances of items (`FIEItem`).

Unlike other inventory systems in unreal, items are not uobjects, so they can be copied and worked with out of the box like any other struct.

## Inventory Component
An actor component that holds Items.
### Inventory Slot
An **UObject** that represents space inside an inventory, that usually is occupied by an item, but may be empty.
### Item
An **Struct** instance of an item descriptor. It can have parameters that define things like, durability, ammo, count, etc.

### Item Descriptor



## How-Tos
### Adding Items to an Inventory
Adding Items to an inventory will try to find space for them in existing slots and place them there.
If there is not enough space for a certain Item, that is considered "Excess" and returned.


> [!NOTE]- Blueprints
> 
> ![](Assets/Pasted%20image%2020260409193233.png)

> [!NOTE]- CPP
> ```cpp
> // Add one item
> Inventory->AddItem(Item, Excess);
> // Add multiple items
> Inventory->AddItems(Items, Excess);
> ```
