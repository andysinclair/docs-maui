---
title: ".NET MAUI handlers"
description: "Learn about .NET MAUI handlers, which map cross-platform controls to performant native controls on each platform."
ms.date: 08/02/2022
---

# Overview

.NET Multi-platform App UI (.NET MAUI) provides a collection of controls that can be used to display data, initiate actions, indicate activity, display collections, pick data, and more. Each control has an interface representation, that abstracts the control. Cross-platform controls that implement these interfaces are known as *virtual views*. *Handlers* map these virtual views to native views on each platform, and are responsible for creating the underlying native view, and mapping the cross-platform view API to the native view API. For example, on iOS a .NET MAUI handler maps a .NET MAUI `Button` to an iOS `UIButton`. On Android, the `Button` is mapped to an `AppCompatButton`:

:::image type="content" source="media/overview/button-handler.png" alt-text="Button handler architecture." border="false":::

Handlers are accessed through their control-specific interface, such as `IButton` for a `Button`. This avoids the cross-platform control having to reference its handler, and the handler having to reference the cross-platform control.

Each handler class exposes the native view that implements the cross-platform control via its `PlatformView` property. This property can be accessed to set native view properties, invoke native view methods, and subscribe to native view events. In addition, the cross-platform control implemented by the handler is exposed via its `VirtualView` property.

Handler can be customized to augment the appearance and behavior of existing cross-platform controls beyond the customization that's possible through a control's API. Handlers are global, and customizing a handler for a control will result in all control of the same type being customized in your app. For more information, see [Customize .NET MAUI controls with handlers](customize.md).

Handlers can also be used to create custom controls whose implementations are backed by native views. For more information, see [Create custom controls with .NET MAUI handlers](create.md).

## Mappers

Each handler typically provides a *property mapper*, and sometimes a *command mapper*, that maps the cross-platform control's API to the native view's API.

A *property mapper* defines what actions to take when a property change occurs in the cross-platform control. It's a `Dictionary` that maps the cross-platform control's interface properties to their associated Actions. Each platform handler implementation then provides implementations of the Actions, which manipulate the native view API. The overall effect is that when a property is set on a cross-platform control, the underlying native view is updated as required.

A *command mapper* maps cross-platform control commands to native view APIs via mapper methods. Command mappers provide a way for cross-platform controls to send instructions to native views on each platform. They're similar to property mappers, but allow for additional data to be passed. Commands, in this context, doesn't mean `ICommand` implementations. Instead, a command is just an instruction, and optionally its data, that's sent to a native view. For example, in .NET MAUI the `ScrollViewHandler`, which provides the platform implementations for the `ScrollView`, uses a command mapper for the `ScrollView` to request its handler to instruct the underlying native views to scroll to a specific location, and passes the required scroll arguments. The `ScrollViewHandler` on each platform parses the scroll arguments and invokes native view functionality to perform the required scroll.

The advantage of using *mappers* is that native views can be decoupled from cross-platform controls, removing the need for native views to subscribe to cross-platform control events. It also allows for easy customization because mappers can be modified without subclassing.

## Handler lifecycle

All handler-based .NET MAUI controls support two handler lifecycle events:

- `HandlerChanging` is raised when a new handler is about to be created for a cross-platform control, and when an existing handler is about to be removed from a cross-platform control. The `HandlerChangingEventArgs` object that accompanies this event has `NewHandler` and `OldHandler` properties, of type `IElementHandler`. When the `NewHandler` property isn't `null`, the event indicates that a new handler is about to be created for a cross-platform control. When the `OldHandler` property isn't `null`, the event indicates that the existing native control is about be removed from the cross-platform control, and therefore that any native events should be unwired and other cleanup performed.
- `HandlerChanged` is raised after the handler for a cross-platform control has been created. This event indicates that the native control that implements the cross-platform control is available, and all the property values set on the cross-platform control have been applied to the native control.

> [!NOTE]
> The `HandlerChanging` event is raised on a cross-platform control before the `HandlerChanged` event.

In addition to these events, each cross-platform control also has an overridable `OnHandlerChanged` method that's invoked when the `HandlerChanged` event is raised, and a `OnHandlerChanging` method that's invoked when the `HandlerChanging` event is raised.

## Handler-based views

The following table lists the types that implement handler-based views in .NET MAUI:

| View | Interface | Handler | Mapper |
| -- | -- | -- | -- |
| `ActivityIndicator` | `IActivityIndicator` | `ActivityIndicatorHandler` | `Mapper` |
| `BlazorWebView` | `IBlazorWebView` | `BlazorWebViewHandler` | `BlazorWebViewMapper` |
| `Border` | `IBorderView` | `BorderHandler` | `Mapper` |
| `Button` | `IButton` | `ButtonHandler` | `Mapper` |
| `CarouselView` | | `CarouselViewHandler` | `Mapper` |
| `CheckBox` | `ICheckBox` | `CheckBoxHandler` | `Mapper` |
| `CollectionView` |  | `CollectionViewHandler` | `Mapper` |
| `ContentView` | `IContentView` | `ContentViewHandler` | `Mapper` |
| `DatePicker` | `IDatePicker` | `DatePickerHandler` | `Mapper` |
| `Editor` | `IEditor` | `EditorHandler` | `Mapper` |
| `Ellipse` | | `ShapeViewHandler` | `Mapper` |
| `Entry` | `IEntry` | `EntryHandler` | `Mapper` |
| `GraphicsView` | `IGraphicsView` | `GraphicsViewHandler` | `Mapper` |
| `Image` | `IImage` | `ImageHandler` | `Mapper` |
| `ImageButton` | `IImageButton` | `ImageButtonHandler` | `Mapper` |
| `IndicatorView` | `IIndicatorView` | `IndicatorViewHandler` | `Mapper` |
| `Label` | `ILabel` | `LabelHandler` | `Mapper` |
| `Line` | | `LineHandler` | `Mapper` |
| `Path` | | `PathHandler` | `Mapper` |
| `Picker` | `IPicker` | `PickerHandler` | `Mapper` |
| `Polygon` | | `PolygonHandler` | `Mapper` |
| `Polyline` | | `PolylineHandler` | `Mapper` |
| `ProgressBar` | `IProgress` | `ProgressBarHandler` | `Mapper` |
| `RadioButton` | `IRadioButton` | `RadioButtonHandler` | `Mapper` |
| `Rectangle` | | `RectangleHandler` | `Mapper` |
| `RefreshView` | `IRefreshView` | `RefreshViewHandler` | `Mapper` |
| `RoundRectangle` | | `RoundRectangleHandler` | `Mapper` |
| `ScrollView` | `IScrollView` | `ScrollViewHandler` | `Mapper` |
| `SearchBar` | `ISearchBar` | `SearchBarHandler` | `Mapper` |
| `Slider` | `ISlider` | `SliderHandler` | `Mapper` |
| `Stepper` | `IStepper` | `StepperHandler` | `Mapper` |
| `SwipeView` | `ISwipeView` | `SwipeViewHandler` | `Mapper` |
| `Switch` | `ISwitch` | `SwitchHandler` | `Mapper` |
| `TimePicker` | `ITimePicker` | `TimePickerHandler` | `Mapper` |
| `WebView` | `IWebView` | `WebViewHandler` | `Mapper` |

All handlers are in the `Microsoft.Maui.Handlers` namespace, with the following exceptions:

- `CarouselViewHandler` and `CollectionViewHandler` are in the `Microsoft.Maui.Controls.Handlers.Items` namespace.
- `LineHandler`, `PathHandler`, `PolygonHandler`, `PolylineHandler`, `RectangleHandler`, and `RoundRectangleHandler` are in the `Microsoft.Maui.Controls.Handlers` namespace.

The interfaces listed in the table above are in the `Microsoft.Maui` namespace.

<!-- Remove the text above once there are API docs that can be linked into -->