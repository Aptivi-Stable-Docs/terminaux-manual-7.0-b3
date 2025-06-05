---
description: Breaking changes for API v7.0
icon: up
---

# API v7.0

Here is a list of breaking changes that happened during the API v7.0 period when differing versions of Terminaux introduced breaking changes.

## From 6.0.x to 7.0.x

Between the 6.0.x and 7.0.x version range, we've made the following breaking changes:

### Removed all obsolete functions

{% code title="Affected classes" lineNumbers="true" %}
```csharp
public static class GraphicsTools
public static class ListWriterColor
public static class AlignedFigletTextColor
public static class AlignedTextColor
public static class BarChartColor
public static class BorderColor
public static class BorderTextColor
public static class BoxColor
public static class BoxFrameColor
public static class BreakdownChartColor
public static class CanvasColor
public static class FigletColor
public static class FigletWhereColor
public static class PowerLineColor
public static class ProgressBarColor
public static class ProgressBarVerticalColor
public static class RainbowBackTextWriterColor
public static class RainbowTextWriterColor
public static class SliderColor
public static class SliderVerticalColor
public static class StickChartColor
public static class TableColor
public static class TruncatedLineText
public static class TruncatedText
public static class KeybindingsWriter
public static class LineHandleRangedWriter
public static class LineHandleWriter
```
{% endcode %}

The above classes, which were marked as deprecated back in Terminaux 6.0, have been removed in this API revision to clean things up as we're getting ready to stabilize the cyclic writers to make them more consistent and usable than before.

{% hint style="info" %}
You'll have to use cyclic writers if you want to continue using fancy writers. You can consult [this page](../usage/console-tools/console-writers/cyclic-writers/) for more info.
{% endhint %}

### Base shell info generic class implemented

{% code title="BaseShellInfo.cs" lineNumbers="true" %}
```csharp
public abstract class BaseShellInfo : IShellInfo
```
{% endcode %}

When you create your own shell info classes to give your shell some important information, such as commands and other info, you had to override the `BaseShell` property so that it returned a new instance of your shell. However, this can be a hassle in certain scenarios.

Starting from Nitrocid KS 0.1.2 and Terminaux 7.0, you'll no longer have to override the BaseShell property because we've implemented a generic version of the above class that takes a type of your shell. For example, if your shell is called `OxygenShell` and your shell info class is called `OxygenShellInfo`, you can just inherit the generic version of the class so that it becomes `BaseShellInfo<OxygenShell>`.

As a result, the following properties are removed from the IShellInfo interface:

{% code title="IShellInfo.cs" lineNumbers="true" %}
```csharp
BaseShell? ShellBase { get; }
PromptPresetBase CurrentPreset { get; }
```
{% endcode %}

{% hint style="info" %}
When inheriting the non-generic base shell class, your shell info class might hold wrong information about your shell, even if your commands are defined. Therefore, you must migrate to the generic version of the class if you want to retain your shell settings.
{% endhint %}

### Cyclic writers now use typed `CyclicWriter` classes

The cyclic writers have been redefined to make their classes use the typed `CyclicWriter` classes. These classes consist of:

* `SimpleCyclicWriter`
* `GraphicalCyclicWriter`

This was required because we needed applications to be able to determine whether the writer that they're using was a simple one or a graphical one. Simple cyclic writers are suitable for command line applications, while graphical ones are used in TUIs.

The following writers have been migrated to `SimpleCyclicWriter`:

* `Decoration`
* `Emoji`
* `FigletText`
* `Kaomoji`
* `KeyShortcut`
* `Keybindings`
* `LineHandle`
* `ListEntry`
* `Listing`
* `NerdFonts`
* `PowerLine`
* `ProgressBar`
* `ProgressBarNoText`
* `SimpleProgress`
* `Slider`
* `Spinner`
* `StemLeafChart`
* `TextException`
* `TextMarquee`

The following writers have been migrated to `GraphicalCyclicWriter`:

* `AlignedFigletText`
* `AlignedText`
* `AnimatedCanvas`
* `AnimatedText`
* `BarChart`
* `Border`
* `BoundedText`
* `Box`
* `BoxFrame`
* `BreakdownChart`
* `Calendars`
* `Canvas`
* `Eraser`
* `Line`
* `LineChart`
* `LinesChart`
* `PieChart`
* `Selection`
* `StickChart`
*  `SyntaxText`
* `Table`
* `TextPath`
* `WinsLosses`

The following shape renderers have been migrated to `GraphicalCyclicWriter`:

* `Arc`
* `Circle`
*  `Ellipsis`
* `Parallelogram`
* `Rectangle`
* `Square`
* `Trapezoid`
* `Triangle`

Part of the introduction of the two typed cyclic writer classes involved removing both `IStaticRenderable` and `IGeometricShape` interfaces to reduce the complexity involved.

{% hint style="info" %}
Consult the updated cyclic writer documentation for more information.
{% endhint %}

### Changed left and right margins to widths in some graphical writers

{% code title="Some graphical writers" lineNumbers="true" %}
```csharp
public int LeftMargin
{
    get => leftMargin;
    set => leftMargin = value;
}

public int RightMargin
{
    get => rightMargin;
    set => rightMargin = value;
}
```
{% endcode %}

We have replaced the two above properties with the `Width` property that some graphical writers that are derived from `GraphicalCyclicWriter` use to determine the element width. The following writers are affected:

* `FigletText`
* `ProgressBar`
* `ProgressBarNoText`
* `SimpleProgress`
* `TextMarquee`

{% hint style="info" %}
You'll have to calculate the total width that the affected writers need to render to the terminal.
{% endhint %}

### `Keybindings` and `StemLeafChart` writers now act as a simple writer

{% code title="Keybindings.cs + StemLeafChart.cs" lineNumbers="true" %}
```csharp
public int Left { get; set; }
public int Top { get; set; }
```
{% endcode %}

The two above properties have been removed because simple writers are not supposed to use anything related to positioning.

{% hint style="info" %}
You can still use the rendering tools functions to accommodate your needs, should you use positioning to write these.
{% endhint %}

### Moved `WriteRenderable()` and `RenderRenderable()` to `RendererTools`

We have moved all `WriteRenderable()` and `RenderRenderable()` function overloads from `ContainerTools` to `RendererTools` to better convey the goals as we have created the two base cyclic writer classes.

### Migrated `InfoBoxInputPasswordColor` to `InfoBoxInputColor`

{% code title="InfoBoxInputPasswordColor.cs" lineNumbers="true" %}
```csharp
public static class InfoBoxInputPasswordColor
```
{% endcode %}

We've migrated the two classes into one by adding functions labelled with `Password`, such as `WriteInfoBoxInputPassword()`, to the `InfoBoxInputColor` to reduce maintenance burden and to avoid code repetition.

{% hint style="info" %}
You'll have to reference the same functions but with the `InfoBoxInputColor` class.
{% endhint %}

### Removed `SplitVTSequencesMultiple()`

{% code title="VtSequenceTools.cs" lineNumbers="true" %}
```csharp
public static string[] SplitVTSequencesMultiple(string Text, VtSequenceType types = VtSequenceType.All)
```
{% endcode %}

As `SplitVTSequencesMultiple()` provides the same functionality as `SplitVTSequences()` with the target type set to `All`, we've seen the above function as redundant; therefore, we've removed it.

{% hint style="info" %}
Replace all calls to the removed function with `SplitVTSequences()`.
{% endhint %}

### Removed `SliderInside` from `Selection`

{% code title="Selection.cs" lineNumbers="true" %}
```csharp
public bool SliderInside { get; set; }
```
{% endcode %}

As development of Terminaux 7.0 continues, we've seen the above property as redundant due to the fact that it was buggy. We've removed this property to make the selection implementation more simple. This removal was part of the broader mouse improvements that were done in this version of Terminaux.

{% hint style="info" %}
You'll have to manually adjust the left position to add `1`, depending on the look of your application. If you're using this property, you'll have to increment the left position. Otherwise, you don't have to.
{% endhint %}

### Presentation system now uses the input modules

{% code title="InputInfo.cs" lineNumbers="true" %}
```csharp
public class InputInfo
```
{% endcode %}

Input modules have been implemented, and they are stable enough that the presentation input elements are actually duplicates of the broader input module classes. As a result, we've decided to eliminate the presentation-specific input elements and to rename the `InputInfo` class to `PresentationInputInfo` to avoid ambiguity. As a result, the following input methods have been removed together with the corresponding interface, `IInputMethod`, and the base class, `BaseInputMethod`:

* `MaskedTextInputMethod`
* `SelectionInputMethod`
* `SelectionMultipleInputMethod`
* `TextInputMethod`

This is done as a result of the addition of a new infobox that uses the input modules.

{% hint style="info" %}
You'll have to replace all definitions of the older `InputMethod` classes with the newer `InputModule` classes. The following input methods can be replaced with:

* `MaskedTextInputMethod` -> `MaskedTextBoxModule`
* `SelectionInputMethod` -> `ComboBoxModule`
* `SelectionMultipleInputMethod` -> `MultiComboBoxModule`
* `TextInputMethod` -> `TextBoxModule`
{% endhint %}

### Refactored CIE color models

{% code title="Cie*Full.cs" lineNumbers="true" %}
```csharp
public class CieLabFull : BaseColorModel, IEquatable<CieLabFull> {}
public class CieLchFull : BaseColorModel, IEquatable<CieLchFull> {}
public class CieLuvFull : BaseColorModel, IEquatable<CieLuvFull> {}
```
{% endcode %}

We've refactored the CIE color model classes so that both the limited version and the full version are migrated to one class without the `Full` suffix. This is to reduce confusion and to improve the overall codebase when it comes to color models.

Functions that were suffixed with `Full` were also migrated in the same manner as the affected classes.

{% hint style="info" %}
You'll have to use the functions and the classes of CIE-Lab, CIE-Lch, and CIE-Luv without the `Full` suffix.
{% endhint %}

### Refactored the input management code

{% code title="Input.cs" lineNumbers="true" %}
```csharp
public static bool KeyboardInputAvailable
public static bool MouseInputAvailable
public static bool InputAvailable
```
{% endcode %}

Improved mouse support on Linux means that we'll have to enable non-block I/O operations on the `stdin` stream, which means removing the "poll" operation that uses the `ioctl()` function. However, this approach allowed the mouse escape sequences to "leak" to the console, which is unfeasible and could cause graphical glitches.

As a result, we've decided to ditch the read-twice design and go for the read-once design in a single function, `ReadPointerOrKey()`, while maintaining the async version of the function, called `ReadPointerOrKeyNoBlock()`. This is further improved by introducing our own event stack, which takes the most recent event received from each type.

{% hint style="info" %}
You need to switch the loop code from the below model:

```csharp
SpinWait.SpinUntil(() => Input.InputAvailable);
if (Input.MouseInputAvailable)
{
    // Mouse input received.
    var mouse = Input.ReadPointer();
    if (mouse is null)
        continue;
        
    // Process mouse input here...
}
else if (ConsoleWrapper.KeyAvailable && !Input.PointerActive)
{
    var key = Input.ReadKey();
    
    // Process keyboard input here...
}
```

...to this:

```csharp
var (mouse, key) = Input.ReadPointerOrKey();
if (mouse is not null)
{
    // Process mouse input here...
}
else if (key is ConsoleKeyInfo cki && !Input.PointerActive)
{
    // Process keyboard input here...
}
```
{% endhint %}

### Removed icon quality, keeping the normal version

{% code title="IconsQuality.cs" lineNumbers="true" %}
```csharp
public enum IconsQuality
```
{% endcode %}

We've removed the icons quality enumeration because it's useful only in actual GUIs. As for the console, the icon quality doesn't matter, so we've removed the icons quality enumeration, which causes all the functions in the `IconsManager` class to no longer hold the option parameter, `quality`.

{% hint style="danger" %}
There is no alternative for this enum, and we're planning to roll out this change to Terminaux 6.1.x and older.
{% endhint %}

### Aligned text writer improved

{% code title="AlignedText.cs and AlignedFigletText.cs" lineNumbers="true" %}
```csharp
public int LeftMargin
public int RightMargin
public bool OneLine (AlignedFigletText)
```
{% endcode %}

The aligned text writer and the figlet counterpart have been improved to get rid of the old margin system that has been causing problems in certain situations. We shouldn't assume that such text is written in the center of the console, so we've decided to make a radical change in `AlignedText` to reflect that in the figlet counterpart.

{% hint style="info" %}
You can use the `Left` and the `Width` properties to replace the older `LeftMargin` and `RightMargin` properties.
{% endhint %}
