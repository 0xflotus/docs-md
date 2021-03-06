---
kind: ClassDeclaration
name: ChangeDetectorRef
module: core
---

# ChangeDetectorRef

## description

Base class that provides change detection functionality.
A change-detection tree collects all views that are to be checked for changes.
Use the methods to add and remove views from the tree, initiate change-detection,
and explicitly mark views as _dirty_, meaning that they have changed and need to be re-rendered.

```ts
class ChangeDetectorRef {
  static __NG_ELEMENT_ID__: () => ChangeDetectorRef = () =>
    SWITCH_CHANGE_DETECTOR_REF_FACTORY();

  abstract markForCheck(): void;
  abstract detach(): void;
  abstract detectChanges(): void;
  abstract checkNoChanges(): void;
  abstract reattach(): void;
}
```

[Link to repo](https://github.com/timdeschryver/angular/blob/master/packages/core/src/change_detection/change_detector_ref.ts#L56-L118)

## see

[Using change detection hooks](guide/lifecycle-hooks#using-change-detection-hooks)
[Defining custom change detection](guide/lifecycle-hooks#defining-custom-change-detection)

## usageNotes

The following examples demonstrate how to modify default change-detection behavior
to perform explicit detection when needed.

### Use `markForCheck()` with `CheckOnce` strategy

The following example sets the `OnPush` change-detection strategy for a component
(`CheckOnce`, rather than the default `CheckAlways`), then forces a second check
after an interval. See [live demo](http://plnkr.co/edit/GC512b?p=preview).

<code-example path="core/ts/change_detect/change-detection.ts"
region="mark-for-check"></code-example>

### Detach change detector to limit how often check occurs

The following example defines a component with a large list of read-only data
that is expected to change constantly, many times per second.
To improve performance, we want to check and update the list
less often than the changes actually occur. To do that, we detach
the component's change detector and perform an explicit local check every five seconds.

<code-example path="core/ts/change_detect/change-detection.ts" region="detach"></code-example>

### Reattaching a detached component

The following example creates a component displaying live data.
The component detaches its change detector from the main change detector tree
when the `live` property is set to false, and reattaches it when the property
becomes true.

<code-example path="core/ts/change_detect/change-detection.ts" region="reattach"></code-example>

## publicApi

## Properties

| Name              | Type                      | Description |
| ----------------- | ------------------------- | ----------- |
| **NG_ELEMENT_ID** | `() => ChangeDetectorRef` |             |

## Methods

### markForCheck

#### description (#markForCheck-description)

When a view uses the {@link ChangeDetectionStrategy#OnPush OnPush} (checkOnce)
change detection strategy, explicitly marks the view as changed so that
it can be checked again.

Components are normally marked as dirty (in need of rerendering) when inputs
have changed or events have fired in the view. Call this method to ensure that
a component is checked even if these triggers have not occured.

<!-- TODO: Add a link to a chapter on OnPush components -->

```ts
abstract markForCheck(): void;
```

[Link to repo](https://github.com/timdeschryver/angular/blob/master/packages/core/src/change_detection/change_detector_ref.ts#L69-L69)

### detach

#### description (#detach-description)

Detaches this view from the change-detection tree.
A detached view is not checked until it is reattached.
Use in combination with `detectChanges()` to implement local change detection checks.

Detached views are not checked during change detection runs until they are
re-attached, even if they are marked as dirty.

<!-- TODO: Add a link to a chapter on detach/reattach/local digest -->
<!-- TODO: Add a live demo once ref.detectChanges is merged into master -->

```ts
abstract detach(): void;
```

[Link to repo](https://github.com/timdeschryver/angular/blob/master/packages/core/src/change_detection/change_detector_ref.ts#L83-L83)

### detectChanges

#### description (#detectChanges-description)

Checks this view and its children. Use in combination with {@link ChangeDetectorRef#detach
detach}
to implement local change detection checks.

<!-- TODO: Add a link to a chapter on detach/reattach/local digest -->
<!-- TODO: Add a live demo once ref.detectChanges is merged into master -->

```ts
abstract detectChanges(): void;
```

[Link to repo](https://github.com/timdeschryver/angular/blob/master/packages/core/src/change_detection/change_detector_ref.ts#L94-L94)

### checkNoChanges

#### description (#checkNoChanges-description)

Checks the change detector and its children, and throws if any changes are detected.

Use in development mode to verify that running change detection doesn't introduce
other changes.

```ts
abstract checkNoChanges(): void;
```

[Link to repo](https://github.com/timdeschryver/angular/blob/master/packages/core/src/change_detection/change_detector_ref.ts#L102-L102)

### reattach

#### description (#reattach-description)

Re-attaches the previously detached view to the change detection tree.
Views are attached to the tree by default.

<!-- TODO: Add a link to a chapter on detach/reattach/local digest -->

```ts
abstract reattach(): void;
```

[Link to repo](https://github.com/timdeschryver/angular/blob/master/packages/core/src/change_detection/change_detector_ref.ts#L111-L111)
