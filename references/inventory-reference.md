Items & Inventory
Creating Items
public Item_Definition Item;
public override void Start()
{
  Item = Item_Definition.Create(new ItemDescription()
  {
    Id = "my_item",
    Icon = "my_asset.png",
    Name = "Fancy Item",
    StackSize = -1, // Infinite
  });

  Item.RegisterUseHandler((player, itemInstance) =>
  {
    Log.Info("Use the fancy item!!!");
  });
}
Creating an Inventory
public Inventory MyInventory;
public void OnLoad()
{
  Inventory.Fetch($"{UserId}_bag", 20, (inventory) =>
  {
    MyInventory = inventory;
    MyInventory.OnInventoryEvent += (eventName, item) => { };
  });
}
Granting an Item
public void GrantItem()
{
  MyInventory.GrantItem(Item, 10 /* Amount */);
}
Draing the Inventory
// Option 1: Use the pre-built hotbar
public override void Update()
{
  Inventory.DrawHotbar($"{UserId}_storage", new Inventory.DrawOptions()
  {
    HotbarItemCount = 5,
    TargetItemSize = 100,
    AllowDragDrop = true,

    EnableUseFromHotbar = true,
    ScrollItemSelection = true,
    KeyboardItemSelection = true,
  });
}