---
weight: "1"
---
# Inventory
The main module of the plugin, provides the **inventories**, **items** and other base features.
![](Assets/20260419.png)
## Item Descriptors
Item descriptors are assets that, as the name suggests, describe an item: How it behaves, how it looks, what it can do, etc.

The two most defining elements of a descriptor are *Fragments* and *Parameters*.
![](Assets/20260419_6.png)
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

> [!TIP]- You can add a display names and icons to item fragments for better readability
> 1. ![](Assets/20260419_4.png)
> 2. ![](Assets/20260419_5.png)

### Creating Fragment Types
Fragments must inherit `UIEItemFragment`.

{{< details title="Blueprints" closed="true" >}}
1. Create a blueprint class inheriting **Item Fragment**
   <img src="../Assets/20260430_5.jpg" width="50%"/> <img src="../Assets/20260430.png" width="75%"/>
2. Create variables to your liking and expose them to see them when adding the fragment to items.
   ![](Assets/20260430_1.png)
   > [!Tip] You can use variable categories too.
3. Often fragments provide logic to items, we can override functions to do this.
   ![](Assets/20260430_2.png)
   
{{< /details >}}

{{< details title="C++" closed="true" >}}
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
<span style="color:yellow">Replicates</span><br>
Parameters are runtime data bound to an item instance (`FIEItem`) and identified by a gameplay tag. This data is usually in the form of structs, or literals like integers, floats or bools.
![](Assets/20260430_3.png)

### Creating Parameter Types
While some parameters can be simple literals like ints, floats or bools, others can be complex structs with many variables.

> [!Note]
> Support for **user defined structs** may come in the future.

{{< details title="C++" closed="true" >}}
```cpp
#include <IEItemParameter.h>

// This parameter example represents ammunition, with a type and count
USTRUCT(BlueprintType, DisplayName = "Ammo")
struct FItemParam_Ammo : public FIEItemParameter
{
    GENERATED_BODY()
	
	UPROPERTY(SaveGame, EditAnywhere, BlueprintReadWrite, Category = Parameter)
    FGameplayTag Type;
    
    UPROPERTY(SaveGame, EditAnywhere, BlueprintReadWrite, Category = Parameter)
    int32 Count = 20;
};
```
{{< /details >}}

> [!Tip]
> Items can be nested inside parameters, essentially being "contained" inside.
> {{< details title="C++" closed="true" >}}
> ```cpp
> USTRUCT(BlueprintType, DisplayName = "Weapon")
>struct FItemParam_Weapon : public FIEItemParameter
>{
>    GENERATED_BODY()
>
>    UPROPERTY(SaveGame, EditAnywhere, BlueprintReadWrite, Category = Parameter)
>    FIEItem Silencer;
>
>    UPROPERTY(SaveGame, EditAnywhere, BlueprintReadWrite, Category = Parameter)
>    FIEItem Scope;
>};
> ```
> {{< /details >}}

## Items
<span style="color:yellow">Replicates</span><br>
When we work with items we usually refer to Struct instances of items (`FIEItem`).

Unlike other inventory systems in unreal, items are not uobjects, so they can be copied and worked with out of the box like any other struct.

## Inventory Component
<span style="color:yellow">Replicates</span><br>
An actor component that can hold many items.
### Settings
The tooltips of the inventory have detailed tooltips that explain their purpose, but here are some:
![](Assets/20260430_4.png)
1. **Max Slots**: Maximum number of simultaneous slots allowed
2. **Unique Slot Tag Limits**: gameplay Tags that limit items allowed by their tags
3. **Max Total Weight**: Total weight allowed considering the weight of all items in the inventory

> [!Tip]
> Inventories themselves can be inherited from both in C++ and blueprints to create custom behavior.
### Replication
There are no restrictions on which actors can have this component, but if you are replicating, keep in mind the replication of the component is the same as that of its owner actor.
For example, an inventory on a Controller will not be available on all clients.
### Inventory Slot
An **UObject** that represents space inside an inventory, that usually is occupied by an item, but may be empty.
### Usage
#### Adding Items to an Inventory
Adding Items to an inventory will try to find space for them in existing slots and place them there.

If there is not enough space for a certain Item, that is considered "**Excess**" and returned.

{{< details title="Blueprints" closed="true" >}}
![](Assets/20260409.png)
{{< /details >}}

{{< details title="C++" closed="true" >}}
```cpp
// Add one item
Inventory->AddItem(Item, Excess);
// Add multiple items
Inventory->AddItems(Items, Excess);
```
{{< /details >}}

## Dropping Items

Items can be dropped into the world *if they contain an "**Instantiable**" fragment*.

This fragment defines which actor class to use (though a default one from settings can be used), but this actor class must implement **InstantiatedItem** interface.
<img src="../Assets/20260430_6.png" width="75%"/>
### Dropping from an Inventory
Items can be dropped from an inventory, optionally removing them from the inventory, and spawning an item actor as requested.

We use the inventory slot of the item for this.
{{< details title="Blueprints" closed="true" >}}
![](Assets/20260430_5.png)
{{< /details >}}

{{< details title="C++" >}}
```cpp
// Add one item
UIEInventorySlot* Slot = Inventory->FindFirstSlotByItem(ItemDescriptor);
// If it is valid
Slot->DropItem(...);
```
{{< /details >}}
### Dropping manually (Spawning)
Items can also be spawned without an inventory, only spawning an item actor as requested, and not affecting the source item (so you can do with it what you wish).

{{< details title="Blueprints" closed="true" >}}
![](Assets/20260430_7.png)
{{< /details >}}

{{< details title="C++" >}}
```cpp
Item.Spawn(...);
```
{{< /details >}}

