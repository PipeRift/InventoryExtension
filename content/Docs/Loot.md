# Loot
> [!WARNING] If you have not already, it is recommended to read about [Inventory](Inventory.md) before Loot

Looting provides functionality to select items randomly from "item queues".

> [!TIP]
> Most variables, functions and classes have descriptions when you hover over them:
> 
> ![](Assets/Pasted%20image%2020260409222222.png)

### Item Queues
Item queues represent a list of items that work like a deck of cards, and it is used to pop items randomly.

### Shared Item Queues

**Shared Item Queues** are essentially the same as normal Item Queues, except they can be referenced by a gameplay tag globally.
#### Shared Item Queue Owners
**Shared Item Queues** are owned by a **Shared Item Queue Owners** that contains a list of them.
Owners are **registered under a base tag**, that all its shared tasks must use.

You can create your own owners, for example, for players. But some owners are already provided by the plugin:
- **Level** shared queues: Their state lasts as long as the current level does.
  They use the **base tag** `Loot.Shared.Level`.
  Queues inside must be sub-tags of `Level`, like `Loot.Shared.Level.Mobs`
- **Game** shared queues: Their state lasts as long as the entire game does.
  They use the **base tag** `Loot.Shared.Game`.
  Queues inside must be sub-tags of `Game`, like `Loot.Shared.Game.Mobs`

#### How are Owners found
You may have though already, if there are two players with shared queues as owners, how do we know which one to get queues from for popping items?

This is where **ItemQueueContext** comes in.
ItemQueueContext is used to be aware of the context in which we are running loot logic.

It gets passed a **context object** from which it will try to find owners **if they implement the SharedQueuesOwner interface**:
- Game owner
- Level owner
- Context
- Actor of Context (if it is a Component)
- Controller of Context (if it is an Actor or Component)
- PlayerState of Context (if it is an Actor or Component)
### Implementing shared queue owners
1. Go to Class Settings in the object you want to make an owner. ![](Assets/Pasted%20image%2020260419190846.png)
   
2. Add the SharedQueuesOwner interface ![](Assets/Pasted%20image%2020260419190819.png)
   
3. Add a SharedItemQueues variable to the class.
   "**SharedItemQueues**", not SharedItemQueuesHandle!!
   ![](Assets/Pasted%20image%2020260419191641.png)
   
4. Set a BaseTag in this variable or it wont be detected as an owner. ![](Assets/Pasted%20image%2020260419191820.png)
   
5. Implement **GetSharedQueues** and pass the SharedItemQueues variable you have created before using "Make a Shared Queues Handle" or "As Handle": ![](Assets/Pasted%20image%2020260419145453.png)