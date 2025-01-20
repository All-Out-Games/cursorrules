# Example usage for Interactables:

using AO;

public class GymPass : Component
{
  public int Cost = 100;
  private Interactable interactable;

  public override void Awake()
  {
    interactable = Entity.AddComponent<Interactable>();
    interactable.CanUseCallback += (Player p) => 
    {
      var myPlayer = (MyPlayer)p;
      return !myPlayer.HasGymPass;
    };

    interactable.OnInteract = (Player p) => 
    {
      if (!Network.IsServer) return;
      var myPlayer = (MyPlayer)p;

      if (myPlayer.Cash < Cost)
      {
        myPlayer.CallClient_ShowNotification("You don't have enough money to buy a gym pass!");
        return;
      }

      myPlayer.Cash.Set(myPlayer.Cash - Cost);
      myPlayer.HasGymPass.Set(true);
      myPlayer.CallClient_PlaySFX("sfx/money.wav");
      myPlayer.CallClient_ShowNotification("You now have access to the gym...");
    };
  }

  public override void Update()
  {
    if (Network.IsServer) return;
    interactable.Text = $"Buy a gym pass for ${Cost}";
  }
}