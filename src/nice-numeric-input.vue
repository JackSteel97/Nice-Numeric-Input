
<template>
    <div class="input-wrapper" :class="[noControls ? '' : 'controls', isError ? 'error' : '', wrapperClass]">
        <label v-if="!hideLabel"
               :id="labelId"
			   :for="id"
			   class="input-label"
               :class="labelClass">
            {{label}}
        </label>
        <button v-if="!noControls"
                @click="decrease"
				class="left-control"
				:class="[changeButtonClass, decreaseButtonClass]"
                :disabled="disabled || !canDecrease"
                :title="decreaseTitle">
            {{internalDecreaseText}}
        </button>
        <input ref="numberInput"
               @input="handleInput"
			   @change="handleChange"
               @paste="handlePaste"
               :value="displayString"
               :class="[noControls ? 'no-controls-input' : 'double-controls-input', inputClass]"
			   :id="id"
               :name="name"
			   :disabled="disabled"
               type="text"
               :placeholder="placeholder"
               :aria-labelledby="!hideLabel ? labelId : false"
               :aria-label="hideLabel ? label : false" />
        <button v-if="!noControls"
                @click="increase"
				class="right-control"
				:class="[changeButtonClass, increaseButtonClass]"
                :disabled="disabled || !canIncrease"
                :title="increaseTitle">
            {{internalIncreaseText}}
        </button>
    </div>
</template>


<script lang="ts">
	import Vue from "vue";

	export default Vue.extend({
		props: {
			value: { type: Number, default: 0 },
			id: { type: String, default: "nice-numeric-input" },
			name: { type: String, default: "nice-numeric-input" },
			label: { type: String, required: true },
			placeholder: { type: String, default: "" },
			step: { type: Number, default: 1 },
			min: { type: Number, default: Number.NEGATIVE_INFINITY },
			max: { type: Number, default: Number.POSITIVE_INFINITY },
			isValid: { type: Boolean, default: false, required: false },
			disabled: { type: Boolean, default: false },
			locale: { type: String, default: null },
			currency: { type: String, default: null },
			minDecimalPlaces: { type: Number, default: 0 },
			maxDecimalPlaces: { type: Number, default: 2 },
			integerOnly: { type: Boolean, default: false },
			noControls: { type: Boolean, default: false },
			hideLabel: { type: Boolean, default: false },
			decreaseTitle: { type: String, default: "Increase" },
			increaseTitle: { type: String, default: "Decrease" },
			increaseText: { type: String, default: "+" },
			decreaseText: { type: String, default: "-" },
			superIncreaseText: { type: String, default: "++" },
			superDecreaseText: { type: String, default: "--" },
			ultraIncreaseText: { type: String, default: "+++" },
			ultraDecreaseText: { type: String, default: "---" },
			superStep: { type: Number, default: 10 },
			ultraStep: { type: Number, default: 100 },
			labelClass: { type: String, default: null },
			inputClass: { type: String, default: null },
			decreaseButtonClass: { type: String, default: null },
			increaseButtonClass: { type: String, default: null },
			wrapperClass: { type: String, default: null },
			superStepClass: { type: String, default: "" },
			ultraStepClass: { type: String, default: "" }
		},

		data: () => {
			return {
				internalValue: null as (number | null),
				ctrlActive: false,
				shiftActive: false,
				internalLocale: "en-US"
			}
		},

		computed: {
			labelId(): string {
				return this.id + '-label'
			},
			canIncrease(): boolean {
				return this.internalValueIsNotDefined || (this.internalValue + this.internalStep) <= this.max;
			},
			canDecrease(): boolean {
				return this.internalValueIsNotDefined || (this.internalValue - this.internalStep) >= this.min;
			},
			displayString(): string {
				if (this.internalValueIsNotDefined) {
					if (this.value) {
						this.setInternalValue(this.value);
					} else {
						this.setToDefaultValue();
					}
				}
				let minDecimals = 0, maxDecimals = 0;
				if (!this.integerOnly) {
					minDecimals = this.minDecimalPlaces;
					maxDecimals = this.maxDecimalPlaces;
				}
				return this.internalValue.toLocaleString(this.internalLocale, {
					style: this.currency ? "currency" : "decimal",
					currency: this.currency || undefined,
					minimumFractionDigits: minDecimals,
					maximumFractionDigits: maxDecimals
				});
			},
			internalValueIsNotDefined(): boolean {
				return this.internalValue == null || Number.isNaN(this.internalValue);
			},
			isValidComputed: {
				get(): boolean {
					return this.isValid;
				},
				set(val: boolean) {
					this.$emit('update:isValid', val);
				}
			},
			isError(): boolean {
				let error = false;
				if (this.internalValue == null || this.internalValue > this.max || this.internalValue < this.min) {
					error = true;
				}
				this.isValidComputed = !error;
				return error;
			},
			isUltraChangeActive() {
				return this.ctrlActive && this.shiftActive;
			},
			isSuperChangeActive() {
				// Equivalent of ctrlActive XOR shiftActive for booleans.
				return this.ctrlActive != this.shiftActive;
			},
			internalIncreaseText() {
				if (this.isUltraChangeActive) {
					return this.ultraIncreaseText;
				} else if (this.isSuperChangeActive) {
					return this.superIncreaseText;
				} else {
					return this.increaseText;
				}
			},
			internalDecreaseText() {
				if (this.isUltraChangeActive) {
					return this.ultraDecreaseText;
				} else if (this.isSuperChangeActive) {
					return this.superDecreaseText;
				} else {
					return this.decreaseText;
				}
			},
			changeButtonClass() {
				if (this.isUltraChangeActive) {
					return this.ultraStepClass || "much-smaller-padding";
				} else if (this.isSuperChangeActive) {
					return this.superStepClass || "smaller-padding";
				} else {
					return "";
				}
			},
			internalStep() {
				if (this.isUltraChangeActive) {
					return this.ultraStep;
				} else if (this.isSuperChangeActive) {
					return this.superStep;
				} else {
					return this.step;
				}
			}
		},

		methods: {
			getDefaultValue(): number {
				if (this.min != Number.NEGATIVE_INFINITY) {
					return this.min;
				}
				return 0;
			},
			handlePaste(e: ClipboardEvent): void {
				const clipboardData = e.clipboardData || (window as any).clipboardData;
				const pastedData = clipboardData.getData("Text");
				if (pastedData) {
					e.stopPropagation();
					e.preventDefault();
					this.valueChanged(pastedData, null);
				}
			},
			handleInput(e: InputEvent): void {
				let target = <HTMLInputElement>e.target;
				this.valueChanged(target.value, e.data);
			},
			handleChange(e: Event): void {
				let target = <HTMLInputElement>e.target;
				this.valueChanged(target.value, null, true);
			},
			valueChanged(newValue: string, newInput: string, strictValidation: boolean = false, possibleRecurse: boolean = true): void {
				const decimalNumbersRegex = /[+-]?\d+(\.\d+)?/g;

				const normalisedInput = this.normaliseInput(newValue, this.internalLocale);
				// Match to find any numbers.
				const matches = normalisedInput.match(decimalNumbersRegex);
				let result = null;
				let isValidNonNumeric = false;


				if (!strictValidation && (this.isEmptyInput(newValue) || this.isStartingSignedInput(newValue) || this.isAddingDecimalPlaces(newValue))) {
					isValidNonNumeric = true;
				} else if (matches != null && matches.length > 0) {
					// Parse the first match.
					result = parseFloat(matches[0]);
				} else {
					if (possibleRecurse && newValue.length > 0) {
						this.valueChanged(`${newValue[0]}`, newInput, strictValidation, false);
						return;
					}
					// Manually clear the invalid input to cover edge cases where computed properties don't update because the internal value hasn't changed value.
					(<HTMLInputElement>this.$refs.numberInput).value = null;
				}

				// Don't reset to 0 when we have a valid non-numeric edge case.
				if (!isValidNonNumeric) {
					if (this.integerOnly) {
						result = Math.round(result);
					}
					if (newInput === "-") {
						result = -result;
					} else if (newInput === "+") {
						result = Math.abs(result);
					}
					this.setInternalValue(result);
				}
			},
			normaliseInput(value: string, locale: string): string {
				const example = Intl.NumberFormat(locale).format(1.1);
				const cleanRegExp = new RegExp(`[^-+0-9${example.charAt(1)}]`, "g");
				const cleanValue = value.replace(cleanRegExp, "");
				const normalised = cleanValue.replace(example.charAt(1), ".");

				return normalised;
			},
			isStartingSignedInput(input: string): boolean {
				return input.length === 1 && (input === "+" || input === "-");
			},
			isEmptyInput(input: string): boolean {
				return input.length === 0;
			},
			isAddingDecimalPlaces(input: string): boolean {
				return input.endsWith(".") || input.endsWith(",") || input.endsWith(" ");
			},
			increase(): void {
				if (this.canIncrease) {
					if (this.internalValueIsNotDefined) {
						this.setToDefaultValue();
					}
					this.setInternalValue(this.internalValue + this.internalStep);
				}
			},
			decrease(): void {
				if (this.canDecrease) {
					if (this.internalValueIsNotDefined) {
						this.setToDefaultValue();
					}
					this.setInternalValue(this.internalValue - this.internalStep);
				}
			},
			setToDefaultValue(): void {
				let newVal = 0;
				if (this.min != Number.NEGATIVE_INFINITY) {
					newVal = this.min;
				}
				this.setInternalValue(newVal);
			},
			setInternalValue(val: number): void {
				// Wipe out the value to force an update even if the value hasn't changed - ensures extra characters that don't affect the parsed value are removed from display.
				this.internalValue = null;
				this.internalValue = val;
				this.$emit('input', this.internalValue);
			},
			keychange(e: KeyboardEvent) {
				this.ctrlActive = e.ctrlKey;
				this.shiftActive = e.shiftKey;
			},
		},

		created() {
			if (this.locale === null) {
				if (typeof window !== 'undefined' && window) {
					this.internalLocale = window.navigator.language;
					document.addEventListener("keydown", this.keychange);
					document.addEventListener("keyup", this.keychange);
				}
			} else {
				this.internalLocale = this.locale;
			}

			if (Vue.config.devtools) {
				// Validate props that depend on each other in development mode.
				if (this.min > this.max) {
					console.error(`nice-numeric-input Prop Error: Min [${this.min}] cannot be greater than Max [${this.max}]`)
				}
				if (this.$listeners && !this.$listeners.input) {
					console.warn(`nice-numeric-input Warning: There is no input event listener attached, use v-model or bind one directly to the input event.`)
				}
			}
			
		},

		beforeDestroy() {
			document.removeEventListener('keydown', this.keychange);
			document.removeEventListener('keyup', this.keychange);
		},

		watch: {
			value(newVal: number) {
				this.internalValue = newVal;
			}
		},
	})
</script>

<style scoped>
	.input-wrapper {
		position: relative;
		font-weight: 400;
		font-style: normal;
		display: -webkit-box;
		display: -ms-flexbox;
		display: flex;
		color: rgba(0, 0, 0, 0.9);
	}

	.input-wrapper > input {
		width: 100%;
		margin: 0em;
		max-width: 100%;
		-webkit-box-flex: 1;
		-ms-flex: 1 0 auto;
		flex: 1 0 auto;
		outline: none;
		-webkit-tap-highlight-color: transparent;
		text-align: left;
		line-height: 1.2em;
		font-family: "Helvetica Neue", Arial, Helvetica, sans-serif;
		padding: 0.66em 1em;
		background: #fff;
		border: 1px solid rgba(34, 36, 38, 0.2);
		color: rgba(0, 0, 0, 0.9);
		border-radius: 0.3rem;
		-webkit-transition: border-color 0.1s ease, -webkit-box-shadow 0.1s ease;
		transition: border-color 0.1s ease, -webkit-box-shadow 0.1s ease;
		transition: box-shadow 0.1s ease, border-color 0.1s ease;
		transition: box-shadow 0.1s ease, border-color 0.1s ease,
			-webkit-box-shadow 0.1s ease;
		-webkit-box-shadow: none;
		box-shadow: none;
	}

	.input-wrapper.disabled,
	.input-wrapper input[disabled] {
		opacity: 0.4;
	}

	.input-wrapper.disabled > input {
		pointer-events: none;
	}

	.input-wrapper > input:active {
		border-color: rgba(0, 0, 0, 0.4);
		background: #fafafa;
	}

	.input-wrapper > input:focus {
		border-color: #85b7d9;
		background: #fff;
		color: rgba(0, 0, 0, 0.8);
	}

	.input-wrapper.error > input {
		background-color: #ffd7d7;
		border-color: #dba8a8;
		color: #9b2d2b;
	}

	.input-wrapper > input::-webkit-input-placeholder {
		color: rgba(191, 191, 191, 0.87);
	}

	.input-wrapper > input::-moz-placeholder {
		color: rgba(191, 191, 191, 0.87);
	}

	.input-wrapper > input:-ms-input-placeholder {
		color: rgba(191, 191, 191, 0.87);
	}

	.input-wrapper.error > input::-webkit-input-placeholder {
		color: #e7bdbc;
	}

	.input-wrapper.error > input::-moz-placeholder {
		color: #e7bdbc;
	}

	.input-wrapper.error > input::-ms-input-placeholder {
		color: #e7bdbc !important;
	}

	.input-wrapper.error > input:focus::-webkit-input-placeholder {
		color: #da9796;
	}

	.input-wrapper.error > input:focus::-moz-placeholder {
		color: #da9796;
	}

	.input-wrapper.error > input:focus::-ms-input-placeholder {
		color: #da9796 !important;
	}

	.input-label {
		-webkit-box-flex: 0;
		-ms-flex: 0 0 auto;
		flex: 0 0 auto;
		margin: 0;
		font-size: 1em;
		padding-right: 1em;
		font-weight: bold;

		display: -webkit-box;
		display: -ms-flexbox;
		display: flex;
		-webkit-box-align: center;
		-ms-flex-align: center;
		align-items: center;
	}

	button {
		cursor: pointer;
		display: inline-block;
		min-height: 1em;
		outline: none;
		border: none;
		vertical-align: baseline;
		background: #e0e1e2 none;
		color: rgba(0, 0, 0, 0.7);
		font-family: "Helvetica Neue", Arial, Helvetica, sans-serif;
		margin: 0em 0.25em 0em 0em;
		padding: 0.75em 1.5em 0.75em;
	}

	button.smaller-padding {
		padding: 0.75em 1.25em 0.75em;
	}

	button.much-smaller-padding {
		padding: 0.75em 1em 0.75em;
	}

	button:hover {
		background-color: #cacbcd;
		-webkit-box-shadow: 0px 0px 0px 1px transparent inset,
			0px 0em 0px 0px rgba(34, 36, 38, 0.15) inset;
		box-shadow: 0px 0px 0px 1px transparent inset,
			0px 0em 0px 0px rgba(34, 36, 38, 0.15) inset;
		color: rgba(0, 0, 0, 0.8);
	}

	button:focus {
		background-color: #cacbcd;
		color: rgba(0, 0, 0, 0.8);
		background-image: "" !important;
		-webkit-box-shadow: "" !important;
		box-shadow: "" !important;
	}

	button:active {
		background-color: #babbbc;
		background-image: "";
		color: rgba(0, 0, 0, 0.9);
		-webkit-box-shadow: 0px 0px 0px 1px transparent inset, none;
		box-shadow: 0px 0px 0px 1px transparent inset, none;
	}

	button:disabled {
		cursor: default;
		opacity: 0.4 !important;
		-webkit-box-shadow: none !important;
		box-shadow: none !important;
		pointer-events: none !important;
	}

	.input-wrapper.controls > button {
		display: -webkit-box;
		display: -ms-flexbox;
		display: flex;
		-webkit-box-align: center;
		-ms-flex-align: center;
		-webkit-box-flex: 0;
		-ms-flex: 0 0 auto;
		flex: 0 0 auto;
		margin: 0;
		text-transform: none;
		text-shadow: none;
		font-weight: bold;
		line-height: 1em;
		font-style: normal;
		text-align: center;
		text-decoration: none;
		-webkit-box-shadow: 0px 0px 0px 1px transparent inset,
			0px 0em 0px 0px rgba(34, 36, 38, 0.15) inset;
		box-shadow: 0px 0px 0px 1px transparent inset,
			0px 0em 0px 0px rgba(34, 36, 38, 0.15) inset;
		-webkit-user-select: none;
		-moz-user-select: none;
		-ms-user-select: none;
		user-select: none;
		-webkit-transition: opacity 0.1s ease, background-color 0.1s ease,
			color 0.1s ease, background 0.1s ease, -webkit-box-shadow 0.1s ease;
		transition: opacity 0.1s ease, background-color 0.1s ease, color 0.1s ease,
			background 0.1s ease, -webkit-box-shadow 0.1s ease;
		transition: opacity 0.1s ease, background-color 0.1s ease, color 0.1s ease,
			box-shadow 0.1s ease, background 0.1s ease;
		transition: opacity 0.1s ease, background-color 0.1s ease, color 0.1s ease,
			box-shadow 0.1s ease, background 0.1s ease, -webkit-box-shadow 0.1s ease;
		-webkit-tap-highlight-color: transparent;
	}

	button.left-control {
		border-radius: 3px 0px 0px 3px;
	}

	button.right-control {
		border-radius: 0px 3px 3px 0px;
	}

	.double-controls-input {
		border-radius: 0px !important;
	}

	.no-controls-input {
		border-radius: 3px !important; /* TODO: Do I need this? */
		border-left-color: rgba(34, 36, 38, 0.15) !important;
	}
</style>