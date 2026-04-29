---
title: Inventory Extension
toc: true
---


A 3-in-1 inventory system for Unreal Engine with **inventories**, **looting** and **crafting**.

## FAB & Installation
This plugin is available for sale in FAB [HERE](https://www.unrealengine.com/marketplace/inventory-extension) and can be installed using Epic Games's Launcher.
For installing manually, you can do it like any other plugin ([See how](https://unreal-garden.com/tutorials/how-to-install-plugin/)).
## Modules
**Inventory Extension** is a plugin composed by three different modules:

{{< cards >}}
  {{< card link="Docs/Inventory" title="Inventory" icon="book-open" subtitle="The main module of the plugin, provides the **inventory** itself and **items**." >}}
  {{< card link="Docs/Loot" title="Loot" icon="book-open" subtitle="Provides **looting**, where items can be randomly obtained using item queues.">}}
  {{< card link="Docs/Craft" title="Craft" icon="book-open" subtitle="Provides **crafting**, where items can be converted into other items.">}}
{{< /cards >}}

**Craft** and **Loot** are **independent and optional**, meaning, if you dont need them you can just ignore them or even remove them.

> [!tip]
> Most variables, functions and classes have descriptions when you hover over them:
> 
> ![](Docs/Assets/Pasted%20image%2020260409222222.png)

## FAQ
### Can Items be created in runtime?
Yes. You have two main ways. You can create item descriptor assets in runtime, or you can use parameters to customize the properties of an item, or both.

For example, <span style="color:orange">I want a weapon to spawn with random damage in a range</span>:
I can <span style="color:green">assign a damage parameter</span> and give it a value of my choice when creating the spawned item.
### Does it support GAS (Gameplay Ability System)?
While this plugins doesn't directly integrate GAS for dependency reasons, it has been used before with it and all concepts of the plugin are friendly to it, so you can easily integrate it in your projects.

## Design pillars of the plugin
This plugin is designed to be:
- **Extensible** and **Scriptable**: Designed to be extended with **cpp and blueprints** in a modular way.
- **Easy to use**: Custom editors simplify editing and creating content in scale.
- **Replicated**: Work on multiplayer projects.
- **Performant**
	- **UObjects are avoided when possible**: Reduces memory footprint
	- **Batch operations are preferred**: Do one time 100 Somethings,  instead of do 100 times Something
