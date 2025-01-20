
UI is custom. Here is and example of how to use it:
// Should be in late update so it takes into account player movement this frame.
public override void LateUpdate()
{
  using var _1 = UI.PUSH_CONTEXT(UI.Context.WORLD);
  // Use the player's Z Offset
  using var _2 = IM.PUSH_Z(GetZOffset() - 0.001f);
  using var _3 = UI.PUSH_PLAYER_MATERIAL(this);
  var rect = new Rect(Entity.Position, Entity.Position).Grow(0.125f);

  var ts = GetTextSettings(0.225f);
  UI.Text(rect, "CEO", ts);


  void DrawRecipeEntry(ref Rect listRect, int index, string recipeName, bool canCraft, Texture icon)
  {
    using var _1 = UI.PUSH_ID(index);

    var buttonRect = listRect.CutTop(48).Inset(5, 44, 5, 44);

    var bs = recipeBs;
    if (index == SelectedRecipePlusOne)
    {
      bs.Sprite = RecipeSelectedTexture;
    }

    var recipeInteract = UI.BeginButton(buttonRect, string.Empty, bs, UI.TextSettings.Empty);

    if (canCraft) recipeTs.Color = Vector4.White;
    else recipeTs.Color = new Vector4(.5f, .5f, .5f, 1.0f);
    UI.Text(recipeInteract.Rect.Offset(-12, 0), recipeName, recipeTs);

    var iconRect = recipeInteract.Rect.SubRect(0.5f, 0, 0.5f, 1).Offset(86, 0).Grow(5, 0, 5, 0).FitAspect(icon.Aspect, Rect.FitAspectKind.KeepHeight);
    UI.Image(iconRect, icon);

    UI.EndButton();
  }

  var statusRect = row.InsetLeft(200).Offset(55, 0);
  var grid = UI.GridLayout.Make(statusRect, 2, 1, UI.GridLayout.SizeSource.ElementCount);

  var innocentRect = grid.Next();
  var susRect = grid.Next();

  var innocentButtonResult = UI.Button(innocentRect, "INNOCENT", new UI.ButtonSettings(), statusTs);
  var susButtonResult = UI.Button(susRect, "SUS", new UI.ButtonSettings(), statusTs);
}

If you need text, these are the available text settings and good defaults:
var ts = new UI.TextSettings()
{
  Font = uint.Fonts.BarlowBold,
  Size = size,
  Color = color ?? Vector4.White,
  DropShadowColor = new Vector4(0f, 0f, 0.02f, 0.5f),
  DropShadowOffset = new Vector2(0f, -3f),
  HorizontalAlignment = halign,
  VerticalAlignment = UI.VerticalAlignment.Center,
  WordWrap = false,
  WordWrapOffset = 0,
  Outline = true,
  OutlineThickness = 3,
  Offset = new Vector2(0, offset),
};


# UI
using AO;

public partial class MyPlayer : Player
{
    public static UI.TextSettings ListTextSettings = new UI.TextSettings()
    {
        Size = 20,
        Color = new Vector4(0, 0, 0, 1),
        Font = UI.Fonts.Barlow,
        VerticalAlignment = UI.VerticalAlignment.Center,
        HorizontalAlignment = UI.HorizontalAlignment.Center,
    };

    public static UI.ButtonSettings ListButtonSettings = new UI.ButtonSettings()
    {
        Sprite = UI.WhiteSprite,
        BackgroundColorMultiplier = new Vector4(0.5f, 0.5f, 0.5f, 1),
        PressScaling = 0.25f,
    };

    public static int CurrentSelection;

    public static void DrawUIDemo()
    {
        var listRect = UI.SafeRect.SubRect(0, 0.5f, 0, 0.5f).Grow(250, 300, 250, 0).Offset(10, -35);
        UI.Image(listRect, null, new Vector4(0, 0, 0, 0.75f));

        Action demoFunction = null;
        var sv = UI.PushScrollView("list", listRect, new UI.ScrollViewSettings(){Vertical = true});
        {
            bool Entry(ref Rect listRect, int selection, string text)
            {
                var rect = listRect.CutTop(45);
                var bs = ListButtonSettings;
                if (CurrentSelection == selection)
                {
                    bs.BackgroundColorMultiplier = new Vector4(0.25f, 1, 0.25f, 1);
                }
                if (UI.Button(rect.Inset(5), text, bs, ListTextSettings).Clicked)
                {
                    CurrentSelection = selection;
                }
                return CurrentSelection == selection;
            }

            if (Entry(ref sv.contentRect, 0,  "None"))                    {}
            if (Entry(ref sv.contentRect, 1,  "Serial Numbers 1"))        { demoFunction = DemoSerialNumbers1; }
            if (Entry(ref sv.contentRect, 2,  "Serial Numbers 2"))        { demoFunction = DemoSerialNumbers2; }
            if (Entry(ref sv.contentRect, 3,  "Layers"))                  { demoFunction = DemoLayers;         }
            if (Entry(ref sv.contentRect, 4,  "Z"))                       { demoFunction = DemoZ;              }
            if (Entry(ref sv.contentRect, 5,  "IDs"))                     { demoFunction = DemoIDs;            }
            if (Entry(ref sv.contentRect, 6,  "Autoscaling"))             { demoFunction = DemoAutoscaling;    }
            if (Entry(ref sv.contentRect, 7,  "Rect Cuts"))               { demoFunction = DemoRectCuts;       }
            if (Entry(ref sv.contentRect, 8,  "Grids"))                   { demoFunction = DemoGrids;          }
        }
        UI.PopScrollView();

        if (demoFunction != null)
        {
            demoFunction();
        }
    }

    [UIPreview]
    public static void PreviewUI(Rect rect)
    {
        DrawUIDemo();
    }

    public override void Update()
    {
        if (IsLocal)
        {
            DrawUIDemo();
        }
    }

    public static void DemoSerialNumbers1()
    {
        var bgRect = UI.ScreenRect.CenterRect().Grow(150, 200, 150, 200);
        UI.Image(bgRect, null, new Vector4(1, 1, 1, 1));

        var leftRect = UI.ScreenRect.CenterRect().Grow(25).Offset(-100, 0);
        UI.Image(leftRect, null, new Vector4(1, 0, 0, 1));
        UI.Image(leftRect.Offset(25, -25), null, new Vector4(0, 1, 0, 1));

        var rightRect = UI.ScreenRect.CenterRect().Grow(25).Offset(100, 0);
        var serial = IM.GetNextSerial();
        UI.Image(rightRect, null, new Vector4(1, 0, 0, 1));
        IM.SetNextSerial(serial);
        UI.Image(rightRect.Offset(25, -25), null, new Vector4(0, 1, 0, 1));
    }

    public static void DemoSerialNumbers2()
    {
        var bgRect = UI.ScreenRect.CenterRect().Grow(100, 150, 100, 150);
        UI.Image(bgRect, null, new Vector4(1, 1, 1, 1));

        var bgSerial = IM.GetNextSerial();
        var ts = ListTextSettings;
        ts.WordWrap = true;
        var str = "This is a long message with an auto-sizing background.";
        var actualTextRect = UI.Text(bgRect, str.Substring(0, ((int)(Time.TimeSinceStartup * 10)) % (str.Length+1)), ts);
        IM.SetNextSerial(bgSerial);
        UI.Image(actualTextRect, null, new Vector4(0, 1, 0, 1));
    }

    public static void DemoLayers()
    {
        var bgRect = UI.ScreenRect.CenterRect().Grow(150, 200, 150, 200);
        UI.Image(bgRect, null, new Vector4(1, 1, 1, 1));

        void DrawWindow(Rect rect, ref ulong rng)
        {
            for (int i = 0; i < 3; i++)
            {
                UI.Image(rect, null, new Vector4(RNG.RangeFloat(ref rng, 0.5f, 1.0f), RNG.RangeFloat(ref rng, 0.5f, 1.0f), RNG.RangeFloat(ref rng, 0.5f, 1.0f), 1));
                rect = rect.Inset(30);
            }
        }


        ulong rng = RNG.Seed(1337);
        var layer1 = (((int)Time.TimeSinceStartup) % 2 == 0) ? 100 : 200;
        var layer2 = 150;
        {
            using var _ = UI.PUSH_LAYER(layer1);
            DrawWindow(UI.ScreenRect.CenterRect().Grow(100).Offset(-75, 25), ref rng);
        }
        {
            using var _ = UI.PUSH_LAYER(layer2);
            DrawWindow(UI.ScreenRect.CenterRect().Grow(100).Offset( 75, -25), ref rng);
        }

        // todo(josh): demo layering problems with interleaving
    }

    public static void DemoZ()
    {
        var bgRect = UI.ScreenRect.CenterRect().Grow(150, 200, 150, 200);
        UI.Image(bgRect, null, new Vector4(1, 1, 1, 1));

        {
            using var _ = UI.PUSH_LAYER(100); // just so that we are guaranteed to draw above the bgRect above.

            var rect1 = UI.ScreenRect.CenterRect().Grow(25).Offset(-15, 0);
            var rect2 = UI.ScreenRect.CenterRect().Grow(25).Offset(15, 0);
            UI.Image(rect1, null, new Vector4(1, 0, 0, 1));

            {
                var y = MathF.Sin(Time.TimeSinceStartup * 1.5f) * 50;
                using var _1 = IM.PUSH_Z(y);
                UI.Image(rect2.Offset(0, y), null, new Vector4(0, 1, 0, 1));
            }
        }
    }

    public static void DemoIDs()
    {
        var bgRect = UI.ScreenRect.CenterRect().Grow(150, 200, 150, 200);
        UI.Image(bgRect, null, new Vector4(1, 1, 1, 1));

        var rect = UI.ScreenRect.CenterRect().Grow(25, 75, 25, 75).Offset(0, 50);
        for (int i = 0; i < 3; i++)
        {
            using var _ = UI.PUSH_ID(i); // try commenting this out and see what happens!
            if (UI.Button(rect.Inset(5), "Button", ListButtonSettings, ListTextSettings).Clicked)
            {
                Log.Info("Clicked " + i);
            }
            rect = rect.Offset(0, -50);
        }
    }

    public static void DemoAutoscaling()
    {
        var bgRect = UI.ScreenRect.CenterRect().Grow(150, 200, 150, 200);
        UI.Image(bgRect, null, Vector4.White);

        // no scaling
        {
            using var _ = UI.PUSH_SCALE_FACTOR(1);
            UI.Image(bgRect.SubRect(0.5f, 1, 0.5f, 1).Grow(0, 200, 75, 200), null, new Vector4(1, 0, 0, 1));
        }

        // with scaling
        {
            UI.Image(bgRect.SubRect(0.5f, 0, 0.5f, 0).Grow(0, 200, 75, 200), null, new Vector4(0, 1, 0, 1));
        }
    }

    public static void DemoRectCuts()
    {
        var bgRect = UI.ScreenRect.CenterRect().Grow(200, 200, 200, 200);
        UI.Image(bgRect, null, Vector4.White);

        var rect = bgRect.TopRect().Offset(0, -15); // TopRect() returns a 0 height rect spanning the top of bgRect

        // hardcoded cut
        {
            UI.Text(rect.CutTop(25), "Text 1", ListTextSettings);
            UI.Text(rect.CutTop(25), "Text 1", ListTextSettings);
            UI.Text(rect.CutTop(25), "Text 1", ListTextSettings);
        }

        rect.CutTop(50);

        // cut based on text height, but its wrong!
        {
            void DrawText(ref Rect rect)
            {
                var actualTextRect = UI.Text(rect, "Text 2", ListTextSettings);
                rect.CutTop(actualTextRect.Height); // wrong! use CutTopUnscaled!
            }

            DrawText(ref rect);
            DrawText(ref rect);
            DrawText(ref rect);
        }

        rect.CutTop(50);

        // cut based on EXACT VALUE of text height, not scaled
        {
            void DrawText(ref Rect rect)
            {
                var actualTextRect = UI.Text(rect, "Text 3", ListTextSettings);
                rect.CutTopUnscaled(actualTextRect.Height); // good :)
            }

            DrawText(ref rect);
            DrawText(ref rect);
            DrawText(ref rect);
        }
    }

    public static void DemoGrids()
    {
        var bgRect = UI.ScreenRect.CenterRect().Grow(200, 200, 200, 200);
        UI.Image(bgRect, null, Vector4.White);

        var grid = UI.GridLayout.Make(bgRect, 4, 4, UI.GridLayout.SizeSource.ElementCount, padding: 4);
        ulong rng = RNG.Seed(1337);
        for (int i = 0; i < 16; i++)
        {
            var rect = grid.Next();
            using var _ = UI.PUSH_ID(i);
            var bs = new UI.ButtonSettings();
            bs.Sprite = UI.WhiteSprite;
            bs.ColorMultiplier = new Vector4(RNG.RangeFloat(ref rng, 0.25f, 1.0f), RNG.RangeFloat(ref rng, 0.25f, 1.0f), RNG.RangeFloat(ref rng, 0.25f, 1.0f), 1);
            if (UI.Button(rect, "", bs, default).Clicked)
            {
                Log.Info("Clicked " + i);
            }
        }
    }
}

# UI Rect manipulation functions. DO NOT USE ANY OTHER FUNCTIONS.

The cut functions modify the rect and return the rect that was "cut out".

public struct Rect
{
  public Rect SubRect(float xMin, float yMin, float xMax, float yMax)
  public Rect SubRect(float xMin, float yMin, float xMax, float yMax, float top, float right, float bottom, float left)
  public Rect Inset(float top, float right, float bottom, float left)
  public Rect Inset(float all)
  public Rect Grow(float top, float right, float bottom, float left)
  public Rect Grow(float all)
  public Rect Offset(float x, float y)
  public Rect CutTop(float pixels)
  public Rect CutRight(float pixels)
  public Rect CutBottom(float pixels)
  public Rect CutLeft(float pixels)
  public Rect InsetTop(float pixels)
  public Rect InsetRight(float pixels)
  public Rect InsetBottom(float pixels)
  public Rect InsetLeft(float pixels)
  public Rect GrowTop(float pixels)
  public Rect GrowRight(float pixels)
  public Rect GrowBottom(float pixels)
  public Rect GrowLeft(float pixels)
  public Rect SubRectUnscaled(float xMin, float yMin, float xMax, float yMax, float top, float right, float bottom, float left)
  public Rect InsetUnscaled(float top, float right, float bottom, float left)
  public Rect GrowUnscaled(float top, float right, float bottom, float left)
  public Rect OffsetUnscaled(float x, float y)
  public Rect CutTopUnscaled(float pixels)
  public Rect CutRightUnscaled(float pixels)
  public Rect CutBottomUnscaled(float pixels)
  public Rect CutLeftUnscaled(float pixels)
  public Rect InsetTopUnscaled(float pixels)
  public Rect InsetRightUnscaled(float pixels)
  public Rect InsetBottomUnscaled(float pixels)
  public Rect InsetLeftUnscaled(float pixels)
  public Rect GrowTopUnscaled(float pixels)
  public Rect GrowRightUnscaled(float pixels)
  public Rect GrowBottomUnscaled(float pixels)
  public Rect GrowLeftUnscaled(float pixels)
  public Rect Scale(float all)
  public Rect Scale(float x, float y)
  public Rect Slide(float x, float y)
  public Rect FitAspect(float aspect, FitAspectKind kind = FitAspectKind.Auto)
}

# IM class
public static class IM
{
    public struct QuadData
    {
        public QuadData() {}
        public QuadData(Vector2 min, Vector2 max, Vector4 color, Texture sprite)
        public QuadData(Vector2 min, Vector2 max, float rotationRadians, Vector4 color, Texture sprite)
        public QuadData(Vector2 min, Vector2 max, Vector2 uvMin, Vector2 uvMax, Vector4 color, Texture sprite)
        public QuadData(Vector2 p1, Vector2 p2, Vector2 p3, Vector2 p4, Vector2 uv1, Vector2 uv2, Vector4 c, Texture sprite)
        public QuadData(Vector2 p1, Vector2 p2, Vector2 p3, Vector2 p4, Vector2 uv1, Vector2 uv2, Vector2 uv3, Vector2 uv4, Vector4 c, Texture sprite)
    }
}