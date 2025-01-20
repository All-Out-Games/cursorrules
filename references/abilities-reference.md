
# Abilities

Make sure the person has a MyAbility class that extends Ability so we can reference the player
public abstract class MyAbility : Ability
{
  public new MyPlayer Player => (MyPlayer)base.Player;
}

Example Melee Kill Ability:
```cs
public partial class KillAbility : MyAbility
{
  public override TargettingMode TargettingMode => TargettingMode.Nearest;
  // The other available targetting modes are 
  public enum TargettingMode
  {
    Nearest,
    Self,
    Select,
    Line,
    CircleAOE,
    PointAndClick,
    DirectionOnNearest,
    FiniteLine
  }

  public override float MaxDistance => 1.5f;
  public override int MaxTargets => 1;
  public override Texture Icon => Assets.GetAsset<Texture>("Ability_Icons/kill_cleaver_icon.png");
  public override float Cooldown => 30f;

  public override bool CanTarget(Player p)
  {
    var op = (OfficePlayer)p;
    return op.CurrentRole != Role.JANITOR && op.CurrentRole != Role.OVERSEER;
  }

  public override bool OnTryActivate(List<Player> targetPlayers, Vector2 positionOrDirection, float magnitude)
  {
    Player.KillerSpineAnimator.SpineInstance.StateMachine.SetTrigger("attack");

    if (Network.IsServer)
    {
      Player.CallClient_ShowNotification("Kill (+20% EXP +$10)");
      Player.Experience.Set(Player.Experience + 20);
      Player.Cash.Set(Player.Cash + 10);

      CallClient_KillPlayer(targetPlayers[0], Player);
    }

    return true;
  }

  // Make sure you implement this
  public override bool CanUse()
  {
    return true;
  }

  [ClientRpc]
  public static void KillPlayer(Player player, Player killer)
  {
    player.AddEffect<KillEffect>();
  }
}
```

Draw the default ability UI in your Player's LateUpdate()
```cs
if (IsLocal)
{
  if (HasEffect<KillerEffect>())
  {
    DrawDefaultAbilityUI(new AbilityDrawOptions()
                         {
                           AbilityElementSize = 125,
                           Abilities = new Ability[] { GetAbility<KillAbility>() }
                         });
  }
}
```