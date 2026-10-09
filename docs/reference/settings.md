---
title: Settings
---

The UiAutomator2 driver exposes various settings through Appium's [Settings API](https://appium.io/docs/en/latest/guides/settings/).

## `actionAcknowledgmentTimeout`

| Type | Default |
| -- | -- |
| `long` | `3000` |

Maximum number of milliseconds to wait for an acknowledgment of a generic UI Automator click action.
Calls [`Configurator.setActionAcknowledgmentTimeout()`](https://developer.android.com/reference/androidx/test/uiautomator/Configurator#setActionAcknowledgmentTimeout(long))
under the hood.

## `allowInvisibleElements`

| Type | Default |
| -- | -- |
| `boolean` | `false` |

Whether to include elements that are not visible to the user (whose `displayed` attribute is
`false`) in the XML source tree.

## `alwaysTraversableViewClasses`

| Type | Default |
| -- | -- |
| `string` | `""` |

Comma separated list of Android element classes, whose visible/invisible state should be ignored by
the UiAutomator2 server during the application source tree traversal.

The default logic for the UI tree traversal (controlled by the [`allowInvisibleElements`](#allowinvisibleelements)
setting) is to stop recursing when an invisible node is found. However, with certain Jetpack
Compose classes (e.g. `androidx.compose.ui.viewinterop.ViewFactoryHolder`), invisible parent nodes
may have visible child nodes. See [this Google issue](https://issuetracker.google.com/issues/354958193)
and [this UiAutomator2 server issue](https://github.com/appium/appium-uiautomator2-server/issues/709)
for more details.

This setting only affects the XPath lookup and the page source buildup. The list of classes may
also include glob patterns, e.g. `androidx.compose.ui.viewinterop.*,android.widget.ImageButton`.

Available since driver version 5.0.3.

## `currentDisplayId`

| Type | Default |
| -- | -- |
| `integer` | `0` ([`Display.DEFAULT_DISPLAY`](https://developer.android.com/reference/android/view/Display#DEFAULT_DISPLAY)) |

Identifier of the display that should be used for driver actions such as retrieving screenshot,
finding elements, etc. Available values can be retrieved using the [`mobile: listDisplays`](./execute-methods.md#mobile-listdisplays)
execute method, or by running `adb shell dumpsys display` (search for `mDisplayId`).

If set to `-1`, the display is reset to the default one.

Refer to the [Multi-Window Testing guide](../guides/multiwindow.md) for detailed usage information.

## `deferAccessibilityCacheReset`

| Type | Default |
| -- | -- |
| `boolean` | `false` |

Whether the UiAutomator2 server should clear the accessibility cache for element/source lookups
only if a relevant UI-change `AccessibilityEvent` has been observed since the last reset. By default,
the cache is cleared for every element/source lookup, which prevents the cache from going stale,
but may result in significant memory usage under sustained lookups.

See [this UiAutomator2 server issue](https://github.com/appium/appium-uiautomator2-server/issues/774)
for more details.

Available since driver version 8.1.0.

## `disableIdLocatorAutocompletion`

| Type | Default |
| -- | -- |
| `boolean` | `false` |

Whether to disable automatic prefixing of `resource-id`-based element selectors with the
application package name.

Internal Android standards expect each element resource identifier to be prefixed with the `<packageName>:id/`
string. This is done to ensure uniqueness of each identifier. However, this rule is not strictly
enforced, and different development frameworks either ignore this or leave this behavior up to the
developer: for example, in Jetpack Compose, using the [`testTag` modifier attribute](https://developer.android.com/reference/kotlin/androidx/compose/ui/platform/package-summary#(androidx.compose.ui.Modifier).testTag(kotlin.String))
with [`testTagsAsResourceId`](https://developer.android.com/reference/kotlin/androidx/compose/ui/semantics/package-summary#(androidx.compose.ui.semantics.SemanticsPropertyReceiver).testTagsAsResourceId())
allows setting an arbitrary string without the prefix rule.
[Interoperability with UiAutomator](https://developer.android.com/develop/ui/compose/testing/interoperability#uiautomator-interop)
also explains how to set it.

By default, the driver adds the above prefix automatically to all unprefixed `resource-id` locators,
but this setting allows disabling this behavior.

## `elementResponseAttributes`

| Type | Default |
| -- | -- |
| `string` | `name,text` |

Comma-separated list of element attribute names to be included into element search responses. The
following values are supported: `name`, `text`, `rect`, `enabled`, `displayed`, `selected`, `attribute/<element_attribute_name>`.

Has no effect if the [`shouldUseCompactResponses`](#shouldusecompactresponses) setting is set to `true`.

## `enableMultiWindows`

| Type | Default |
| -- | -- |
| `boolean` | `false` |

Whether to include all windows that the user can interact with (e.g. an on-screen keyboard) while
building the application page source. By default, only the focused active application window is
included in the page source.

Refer to the [Multi-Window Testing guide](../guides/multiwindow.md) for detailed usage information.

## `enableNotificationListener`

| Type | Default |
| -- | -- |
| `boolean` | `true` |

Whether to enable the listener for new toast notifications and allow toast message texts to be
included into the application page source. By default, this listener is enabled and tracks toast
messages for up to `3500` ms after their expiration.

## `enableTopmostWindowFromActivePackage`

| Type | Default |
| -- | -- |
| `boolean` | `false` |

Whether to select the application window with the highest Z-order as the active window. By default,
the driver selects the focused window, which generally also has the highest Z-order, but this may
not be the case for multi-window apps, or devices with multiple displays.

Refer to the [Multi-Window Testing guide](../guides/multiwindow.md) for detailed usage information.

## `enforceXPath1`

| Type | Default |
| -- | -- |
| `boolean` | `false` |

Whether to enforce usage of XPath1 for XPath-based element lookup.

The default built-in XPath2 interpreter is based on the [PsychoPath](https://wiki.eclipse.org/PsychoPathXPathProcessor)
implementation, which is identical to an XPath1 interpreter for most cases, but more sophisticated
locators may run into [issues](https://github.com/appium/appium/issues/16142) due to different
behavior. This setting can be used to try to workaround such issues by enforcing XPath1 usage
(whose implementation is a part of the Android platform itself).

This setting is primarily relevant for driver versions 7.5.1 or earlier, since the issues that
resulted in the introduction of this setting were solved in version 7.5.2, as a result of vendoring
the PsychoPath library.

## `ignoreUnimportantViews`

| Type | Default |
| -- | -- |
| `boolean` | `false` |

Whether to enable compression of the application source layout hierarchy.

If enabled, the layout hierarchy derived from the accessibility framework will only contain nodes
that are important for UI Automator testing. Any unnecessary surrounding layout nodes that make
viewing and searching the hierarchy inefficient are removed.

## `includeA11yActionsInPageSource`

| Type | Default |
| -- | -- |
| `boolean` | `false` |

Whether to include the `actions` element attribute in the application source. This attribute value
could be huge if elements in the page source have a lot of actions, which could affect the
performance of page source generation.

Available since driver version 4.0.0.

## `includeExtraRenderingInfo`

| Type | Default |
| -- | -- |
| `boolean` | `false` |

Whether to include the `text-size` and `text-unit` element attributes in the application source.
Retrieval of these attributes has a slight effect on the performance of page source generation. 

Available since driver version 6.7.10.

## `includeExtrasInPageSource`

| Type | Default |
| -- | -- |
| `boolean` | `false` |

Whether to include the `extras` element attribute in the application source. This attribute value
could be huge if elements in the page source have a lot of extras, which could affect the
performance of page source generation.

Available since driver version 2.1.0.

## `keyInjectionDelay`

| Type | Default |
| -- | -- |
| `integer` | `0` |

Delay in milliseconds between key presses, used when inputting text.

## `limitXPathContextScope`

| Type | Default |
| -- | -- |
| `boolean` | `true` |

Whether to limit the context scope of XPath-based element lookups to the parent element.

Due to historical reasons, by default, the driver limits the scope of element context-based
searches to the parent element. This means that a request like
`findElement(By.xpath, "//root").findElement(By.xpath, "./..")` would always fail, because the
driver only collects descendants of the `root` element. Disabling this setting causes the
retrieved page source to includes the entire page source, resulting in the  aforementioned query to
no longer fail.

Disabling this setting nonetheless requires caution with other context-based queries - for example,
a request like `findElement(By.xpath, "//root").findElement(By.xpath, "//element")` would ignore
the current context and search for `element` through the whole page source. In such cases it is
recommended to use the dot (`.`) notation: `findElement(By.xpath, "//root").findElement(By.xpath, ".//element")`.

## `mapTestTagToResourceId`

| Type | Default |
| -- | -- |
| `boolean` | `false` |

Whether to map the [`testTag`](https://developer.android.com/reference/kotlin/androidx/compose/ui/platform/package-summary#(androidx.compose.ui.Modifier).testTag(kotlin.String))
semantic property of Jetpack Compose elements onto their `resource-id` attribute.

Enabling this setting mirrors the behavior of Compose's own [`testTagsAsResourceId`](https://developer.android.com/reference/kotlin/androidx/compose/ui/semantics/package-summary#(androidx.compose.ui.semantics.SemanticsPropertyReceiver).testTagsAsResourceId()),
which can otherwise only be toggled from within the app's own composable tree. The mapping is
consistently applied to `getAttribute`, page source/XPath generation, and `id`-based element
lookups. Unlike the [`disableIdLocatorAutocompletion`](#disableidlocatorautocompletion) setting,
bare `testTag` values are matched as-is, without automatically prepending the application package
name.

Available since driver version 8.4.0.

## `mjpegBilinearFiltering`

## `mjpegScalingFactor`

## `mjpegServerFramerate`

## `mjpegServerPort`

## `mjpegServerScreenshotQuality`

## `normalizeTagNames`

## `scrollAcknowledgmentTimeout`

## `serverPort`

## `shouldUseCompactResponses`

## `shutdownOnPowerDisconnect`

## `simpleBoundsCalculation`

## `snapshotMaxDepth`

## `trackScrollEvents`

## `useResourcesForOrientationDetection`

## `waitForIdleTimeout`

## `waitForSelectorTimeout`

## `wakeLockTimeout`
