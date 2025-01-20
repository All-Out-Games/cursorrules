Effects
Effects are a powerful system for applying temporary effects to your player. Some example use cases:

public partial class BoardMeetingEffect : MyEffect, INetworkedComponent
{
  public override bool IsActiveEffect => true;
  public override bool BlockAbilityActivation => true;
  public override bool FreezePlayer => true;
  public override float DefaultDuration => 17f;

  public bool HasPlayedEndSound;

  public override void OnEffectStart(bool isDropIn)
  {

  }

  public override void OnEffectEnd(bool interrupt)
  {

  }

  public override void OnEffectUpdate()
  {
    if (AO.Util.OneTime(DurationRemaining <= 4.25f, ref HasPlayedEndSound))
    {

    }
  }
}
