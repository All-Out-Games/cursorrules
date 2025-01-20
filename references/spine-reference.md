You may want to utilize some of the existing animations that are part of the default rig.

Idle Simple idling. Used by default by the engine.
Run Simple running. Used by default in the engine.
Idle (IK) Idling while the hand is following the mouse. IK can be controlled with Player.SetMouseIKEnabled
Run (IK) Running while the hand is following the mouse. IK can be controlled with Player.SetMouseIKEnabled
Dance Used in emotes. Trigger: dance
Fart Used in emotes. Trigger: fart
Thumbs Up Used in emotes. Trigger: thumbs_up
Wave Used in emotes. Trigger: wave
Attack Swinging a sword like weapon. Recommended to use this, bone following, and mouse ik together. Trigger attack
Dodge Roll Jump and roll. Trigger: dodge_roll
Death Fall over and die. Trigger: death
Shoot Shoot a gun like weapon. Recommended to use this, bone following, and mouse ik together. Trigger shoot
Mouse IK
To have a spine animator be aware of the player's mouse/aiming direction use Player.SetMouseIKEnabled.

Crewshia
You can control the color of any spine rig, including player's character with the .SetCrewchsia(int) method of the Spine Animator.

The colors are defined as:
Red(Index 0)
Cyan(Index 1)
Green(Index 2)
Yellow(Index 3)
Light Green(Index 4)
Pink(Index 5)
Orange(Index 6)
Black(Index 7)
Purple(Index 8)
Light Gray(Index 9)
Black 2 (Index 10)
Green 3 (Index 11)
Orange 2 (Index 12)
Purple 2 (Index 13)
Red 2 (Index 14)
Blue 2 (Index 15)
Brown 1 (Index 16)
Purple 3 (Index 17)
White 1 (Index 18)

# Some other commonly used spine methods
SpineInstance.Scale = new Vector2(2.4f, 2.4f);
SpineInstance.SetSkeleton(Assets.GetAsset<SpineSkeletonAsset>("path"));
SpineInstance.SetStateMachine(BookshelfLever.MakeBookshelfStateMachine(), Entity);

var murderLayer = SpineAnimator.SpineInstance.StateMachine.CreateLayer("murder_layer", 10);
var aoLayer = SpineAnimator.SpineInstance.StateMachine.TryGetLayerByName("main");
var aoIdleState = aoLayer.TryGetStateByName("Idle");
var aoRunState = aoLayer.TryGetStateByName("Run_Fast");
var idleState = murderLayer.CreateState("MURD_002/empty", 0, true);
murderLayer.SetInitialState(idleState);

var pointBool = SpineAnimator.SpineInstance.StateMachine.CreateVariable("point", StateMachineVariableKind.BOOLEAN);
var pointExaggerateTrigger = SpineAnimator.SpineInstance.StateMachine.CreateVariable("point_exaggerate", StateMachineVariableKind.TRIGGER);
var pointState = murderLayer.CreateState("MURD_002/point_mIK_AL", 0, true);
var pointExaggerateState = murderLayer.CreateState("MURD_002/point_ex_mIK_AL", 0, false);
murderLayer.CreateTransition(idleState, pointState, false).CreateBoolCondition(pointBool, true);
murderLayer.CreateTransition(pointState, pointExaggerateState, false).CreateTriggerCondition(pointExaggerateTrigger);
murderLayer.CreateTransition(pointExaggerateState, pointExaggerateState, false).CreateTriggerCondition(pointExaggerateTrigger);
murderLayer.CreateTransition(pointExaggerateState, pointState, true);
murderLayer.CreateTransition(pointState, idleState, false).CreateBoolCondition(pointBool, false);

public class TransformFromKillerEffect : MyEffect
{
    public override bool IsActiveEffect => true;
    public override bool FreezePlayer => true;

    public override void OnEffectStart(bool isDropIn)
    {
        SFX.Play(Assets.GetAsset<AudioAsset>("sfx/invisibility_on.wav"), new() {Positional = true, Position = Player.Entity.Position});
        Player.KillerSpineAnimator.SpineInstance.StateMachine.SetTrigger("transform_back_start");
        if (isDropIn)
        {
            DurationRemaining = Player.KillerSpineAnimator.SpineInstance.StateMachine.TryGetLayerByName("killer_layer").GetCurrentStateLength();
        }
    }

    public override void OnEffectEnd(bool interrupt)
    {
        Player.AddEffect<FinishTransformFromKillerEffect>();
        if (interrupt)
        {
            Player.RemoveEffect<FinishTransformFromKillerEffect>(true);
        }
    }
}