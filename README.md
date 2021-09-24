# Nice-Numeric-Input
![npm](https://img.shields.io/npm/v/nice-numeric-input?style=flat-square)
![npm bundle size](https://img.shields.io/bundlephobia/minzip/nice-numeric-input?label=Zipped%20Size&style=flat-square)
![NPM](https://img.shields.io/npm/l/nice-numeric-input?label=License&style=flat-square)
![npm peer dependency version](https://img.shields.io/npm/dependency-version/nice-numeric-input/peer/vue?style=flat-square)
![npm](https://img.shields.io/npm/dw/nice-numeric-input?style=flat-square)

Nice-Numeric-Input is a modern, rich featured and highly customisable numeric input built on Vue. Capable of formatting as the user types, including currency formatting. With no extra dependencies other than Vue itself.

## Demo
<a href="https://codepen.io/Jsteel97/pen/YzQLZeP" target="_blank">Basic Demo</a>

![nice-numeric-input-demo-gif](https://user-images.githubusercontent.com/13905557/134671516-17bca2cb-4cb8-41c1-b3cc-9db345506e79.gif)


## Getting Started
### Installation
```bash
> npm install nice-numeric-input
```

### Usage
**Import**
```ts
import NiceNumericInput from 'nice-numeric-input'
```

**Register**
```ts
export default Vue.extend({
    //...
    components: { NiceNumericInput }
    //...
});
```

For a full list of props see the [Reference Props](#props) section.
```html
<nice-numeric-input v-model="cashValue"
                    placeholder="Enter a cash value"
                    label="Cash"
                    name="example"
                    currency="GBP"
                    locale="en-GB"
                    :max="100"
                    :min="0" />
```


## Reference
### Props
| Prop                | Type      | Required | Default Value          | Notes                                                                                                         |
|---------------------|-----------|----------|------------------------|---------------------------------------------------------------------------------------------------------------|
| value               | `Number`  | Yes      | `0`                    | Use `v-model` to get two-way binding                                                                          |
| id                  | `String`  | No       | `"nice-numeric-input"` |                                                                                                               |
| name                | `String`  | No       | `"nice-numeric-input"` |                                                                                                               |
| label               | `String`  | Yes      |                        | Required for accessibility, use `hide-label` to remove the visual label                                       |
| placeholder         | `String`  | No       |                        |                                                                                                               |
| step                | `Number`  | No       | `1`                    | The amount to increase or decrease when using the step buttons                                                |
| min                 | `Number`  | No       | `NEGATIVE_INFINITY`    | Anything entered below this will fail the `isValid` check                                                     |
| max                 | `Number`  | No       | `POSITIVE_INFINITY`    | Anything entered above this will fail the `isValid` check                                                     |
| isValid             | `Boolean` | No       |                        | Basic internal validation, use the `.sync` modifier to get changes                                            |
| disabled            | `Boolean` | No       | `false`                | Disable the entire component                                                                                  |
| locale              | `String`  | No       | `window.navigator.language \|\| "en-US"`   | Standard [BCP 47](https://tools.ietf.org/rfc/bcp/bcp47.txt) language tag                                      |
| currency            | `String`  | No       | `null`                 | When `null` currency formatting is turned off. Otherwise it can be any currency code e.g. `USD`, `EUR`, `JPY` |
| minDecimalPlaces    | `Number`  | No       | `0`                    |                                                                                                               |
| maxDecimalPlaces    | `Number`  | No       | `2`                    |                                                                                                               |
| integerOnly         | `Boolean` | No       | `false`                | Prevents decimal numbers being entered, entries are rounded to the nearest integer                            |
| noControls          | `Boolean` | No       | `false`                | Removes the step buttons                                                                                      |
| hideLabel           | `Boolean` | No       | `false`                | Removes the visual label                                                                                      |
| decreaseTitle       | `String`  | No       | `"Decrease"`           | The button title for accessibility                                                                            |
| increaseTitle       | `String`  | No       | `"Increase"`           | The button title for accessibility                                                                            |
| increaseText        | `String`  | No       | `"+"`                  | The text displayed on the increase step button                                                                |
| decreaseText        | `String`  | No       | `"-"`                  | The text displayed on the decrease step button                                                                |
| superIncreaseText   | `String`  | No       | `"++"`                 | The text displayed on the increase step button when super step is active (<kbd>Ctrl</kbd> or <kbd>Alt</kbd>)  |
| superDecreaseText   | `String`  | No       | `"--"`                 | The text displayed on the decrease step button when super step is active (<kbd>Ctrl</kbd> or <kbd>Alt</kbd>)  |
| ultraIncreaseText   | `String`  | No       | `"+++"`                | The text displayed on the increase step button when ultra step is active (<kbd>Ctrl</kbd> + <kbd>Alt</kbd>)   |
| ultraDecreaseText   | `String`  | No       | `"---"`                | The text displayed on the decrease step button when ultra step is active (<kbd>Ctrl</kbd> + <kbd>Alt</kbd>)   |
| superStep           | `Number`  | No       | `10`                   | The amount to change during a super step                                                                      |
| ultraStep           | `Number`  | No       | `100`                  | The amount to change during an ultra step                                                                     |
| labelClass          | `String`  | No       |                        | A custom class to apply to the visual label                                                                   |
| inputClass          | `String`  | No       |                        | A custom class to apply to the input field                                                                    |
| decreaseButtonClass | `String`  | No       |                        | A custom class to apply to the decrease step button                                                           |
| increaseButtonClass | `String`  | No       |                        | A custom class to apply to the increase step button                                                           |
| wrapperClass        | `String`  | No       |                        | A custom class to apply to the top level component element                                                    |
| superStepClass      | `String`  | No       |                        | A custom class to apply to both step buttons when super step is active                                        |
| ultraStepClass      | `String`  | No       |                        | A custom class to apply to both step buttons when ultra step is active                                        |

---
### Events
| Event               | Emitted Type  | Notes                                                                                                                    |
|---------------------|---------------|--------------------------------------------------------------------------------------------------------------------------|
| input               | `Number`      | Used automatically by a `v-model` binding. Can be bound manually, fires when the value is changed                        |
| update:isValid      | `Boolean`     | Used automatically by the `.sync` modifier on `isValid` prop. Can be bound manually, fires when the valid value changes. |


# Support This Project
[![ko-fi](https://ko-fi.com/img/githubbutton_sm.svg)](https://ko-fi.com/R6R8NDIH)

BTC: bc1qpss998j2j06fmdrycn4fvh3vrdu7vttkjsngwd

ETH: 0xab04C5D9Dd7145fFf2dD94db7D7A50ae7Ce4Ab25

ADA: addr1qxhk3mptca7l0qk2p7hhh5tp4qfqvqj5tpj5vcgkcy5zx6d0drkzh3ma77pv5ra000gkr2qjqcp9gkr9ges3dsfgyd5srdnl4c
