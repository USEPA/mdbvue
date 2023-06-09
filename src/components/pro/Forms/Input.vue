<template>
  <component
    :key="componentKey"
    v-if="noWrapper"
    ref="input"
    :is="whatTagIsThis"
    :id="id"
    :class="inputClasses"
    :type="type"
    :step="step"
    :min="min"
    :max="max"
    :placeholder="placeholder"
    :disabled="disabled"
    :name="name"
    :required="required"
    :checked="inputChecked"
    :value="innerValue"
    :rows="rows"
    :readOnly="readOnly"
    :maxlength="maxlength"
    :autocomplete="autocomplete"
    :aria-label="label || ariaLabel || placeholder"
    :aria-describedby="ariaDescribedBy"
    :aria-labelledby="ariaLabelledBy"
    :aria-multiselectable="ariaMultiselectable"
    :aria-disabled="ariaDisabled"
    :aria-required="ariaRequired"
    :aria-haspopup="ariaHaspopup"
    :aria-expanded="ariaExpanded"
    :aria-owns="ariaOwns"
    :role="role"
    :autofocus="autofocus"
    @focus="focus"
    @blur="blur"
    @click="wave"
    @change="onChange"
    @input="onInput"
    >{{ whatTagIsThis === "textarea" && value }}</component
  >
  <div :class="wrapperClasses" v-else>
    <i v-if="icon" :class="iconClasses" />
    <div class="input-group-prepend" v-if="$slots.prepend" :id="prependSlotID">
      <slot name="prepend"></slot>
    </div>
    <component
      :key="componentKey"
      ref="input"
      :is="whatTagIsThis"
      :id="id"
      :class="inputClasses"
      :type="type"
      :step="step"
      :min="min"
      :max="max"
      :placeholder="placeholder"
      :disabled="disabled"
      :name="name"
      :required="required"
      :checked="inputChecked"
      :value="innerValue"
      :rows="rows"
      :maxlength="maxlength"
      :aria-label="label || ariaLabel || placeholder"
      :aria-describedby="ariaDescribedBy"
      :aria-labelledby="ariaLabelledBy"
      :aria-multiselectable="ariaMultiselectable"
      :aria-disabled="ariaDisabled"
      :aria-required="ariaRequired"
      :aria-haspopup="ariaHaspopup"
      :aria-expanded="ariaExpanded"
      :aria-owns="ariaOwns"
      :role="role"
      :readOnly="readOnly"
      :autocomplete="autocomplete"
      @focus="focus"
      :autofocus="autofocus"
      @blur="blur"
      @click="wave"
      @change="onChange"
      @input="onInput"
      >{{ whatTagIsThis === "textarea" && value }}</component
    >
    <label
      v-if="label"
      :class="labelClasses"
      @click="focus"
      ref="label"
      :for="id"
      >{{ label }}</label
    >
    <label
      v-if="isThisCheckboxLabeless"
      :class="labelClasses"
      @click="focus"
      ref="label"
      :for="id"
    />
    <slot></slot>
    <i v-if="datePicker" :class="[datePickerIcon, 'mdb-date-picker-icon']" :aria-haspopup="ariaHaspopup" @click="$emit('datePickerToggle')" @keydown.enter="$emit('datePickerToggle')" tabindex="0" />
    <div class="input-group-append" v-if="$slots.append" :id="appendSlotID">
      <slot name="append"></slot>
    </div>
    <div v-if="validFeedback" class="valid-feedback">
      {{ validFeedback }}
    </div>
    <div v-if="invalidFeedback" class="invalid-feedback">
      {{ invalidFeedback }}
    </div>
    <small v-if="small" class="form-text text-muted">{{ small }}</small>
    <span
      v-if="counter && isTouched"
      class="character-counter"
      style="float: right; font-size: 12px; height: 1px;"
      >{{ characters }}/{{ counterMaxValue }}</span
    >
  </div>
</template>

<script>
import { mdbInput } from "../../../mixins/mdbInput";

const Input = {
  props: {
    value: {
      type: [String, Number, Boolean, Array],
      default: ""
    },
    counter: {
      type: Boolean
    },
    counterMaxValue: {
      type: Number,
      default: 10
    },
    checkboxValue: {
      type: String
    },
    datePicker: {
      type: Boolean
    },
    datePickerIcon: {
      type: String
    }
  },
  data() {
    return {
      characters: 0
    };
  },
  computed: {
    inputChecked() {
      if (this.type === "checkbox") {
        if (Array.isArray(this.value)) {
          if (this.value.includes(this.checkboxValue)) {
            return true;
          }
          return false;
        }
        if (this.value === true || this.innerChecked === true) {
          return true;
        }
        return false;
      }
      if (this.type === "radio") {
        if (this.value === this.radioValue || this.innerChecked) {
          return true;
        }
        return false;
      }
      return false;
    },
    componentKey() {
      if (this.type === "checkbox" || this.type === "radio") {
        return Number(this.inputChecked);
      }

      return 0;
    },
    inputClasses() {
      return [
        "form-control",
        this.validation ? (this.isValid ? "is-valid" : "is-invalid") : false,
        this.customValidation
          ? this.isValid
            ? "is-valid"
            : "is-invalid"
          : false,
        this.size && "form-control-" + this.size,
        {
          "filled-in": this.filled,
          "with-gap": this.gap
        },
        this.type === "checkbox"
          ? this.gap
            ? false
            : "form-check-input"
          : false,
        this.type === "radio" ? "form-check-input" : false,
        this.type === "textarea" && !this.basic ? "md-textarea" : false,
        this.counter && this.characters > this.counterMaxValue && "invalid",
        this.inputClass
      ];
    }
  },
  methods: {
    onChange(e) {
      if (this.type === "checkbox") {
        if (Array.isArray(this.value)) {
          if (e.target.checked) {
            this.value.push(this.checkboxValue);
          } else {
            this.value.splice(this.value.indexOf(this.checkboxValue), 1);
          }
          this.$emit("change", this.value);
          this.$emit("input", this.value);
        } else {
          this.$emit("change", e.target.checked);
          this.$emit("input", e.target.checked);
          this.innerChecked = e.target.checked;
        }
      } else if (this.type === "radio") {
        this.innerChecked = e.target.checked;
        if (this.radioValue) {
          this.$emit("input", this.radioValue);
        }
      } else {
        this.$emit("change", e.target.value);
      }
    },
    onInput(e) {
      if (this.type !== "checkbox") {

        this.$emit("input", e.target.value);
        this.innerValue = e.target.value;
        this.characters = e.target.value.length;
      }

      if (this.type === 'text' || this.type === 'textarea') {
        const initialPosition = e.target.selectionStart;

        this.$nextTick(() => {
          e.target.setSelectionRange(initialPosition, initialPosition);
        });
      }
    }
  },
  mounted() {
    if (this.counter) {
      this.characters = this.value.length;
    }
  },
  watch: {
    innerValue(value) {
      this.innerChecked = value;

      if (this.type === "radio") {
        this.innerChecked = this.radioValue === value;
      }

      this.$forceUpdate();
    }
  },
  mixins: [mdbInput]
};

export default Input;
export { Input as mdbInput };
</script>

<style scoped>
.navbar .md-form {
  margin-top: 0;
  margin-bottom: 0;
}

.form-dark input[type="checkbox"]:checked + label:before {
  top: -4px;
  left: -3px;
  width: 12px;
  height: 22px;
  border-style: solid;
  border-width: 2px;
  border-color: transparent #00c851 #00c851 transparent;
  -webkit-transform: rotate(40deg);
  -ms-transform: rotate(40deg);
  transform: rotate(40deg);
  -webkit-transform-origin: 100% 100%;
  -ms-transform-origin: 100% 100%;
  transform-origin: 100% 100%;
}

.form-dark .font-small {
  font-size: 0.8rem;
}

.form-dark input[type="email"]:focus:not([readonly]) + label {
  color: #fff;
}

.form-dark input[type="checkbox"] + label:before {
  content: "";
  position: absolute;
  top: 0;
  left: 0;
  width: 17px;
  height: 17px;
  z-index: 0;
  border: 1.5px solid #fff;
  border-radius: 1px;
  margin-top: 2px;
  -webkit-transition: 0.2s;
  transition: 0.2s;
}

.form-dark input[type="checkbox"]:checked + label:before {
  top: -4px;
  left: -3px;
  width: 12px;
  height: 22px;
  border-style: solid;
  border-width: 2px;
  border-color: transparent #00c851 #00c851 transparent;
  -webkit-transform: rotate(40deg);
  -ms-transform: rotate(40deg);
  transform: rotate(40deg);
  -webkit-transform-origin: 100% 100%;
  -ms-transform-origin: 100% 100%;
  transform-origin: 100% 100%;
}

.form-dark input[type="password"]:focus:not([readonly]) {
  border-bottom: 1px solid #00c851;
  -webkit-box-shadow: 0 1px 0 0 #00c851;
  box-shadow: 0 1px 0 0 #00c851;
}

.form-dark input[type="email"]:focus:not([readonly]) {
  border-bottom: 1px solid #00c851;
  -webkit-box-shadow: 0 1px 0 0 #00c851;
  box-shadow: 0 1px 0 0 #00c851;
}

.form-dark [type="checkbox"] + label:before {
  top: 2px;
  width: 15px;
  height: 15px;
}

.md-form textarea ~ label.active {
  color: inherit;
}

.form-control.is-valid {
  border-color: #28a745;
  background-image: url("data:image/svg+xml,%3csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 8 8'%3e%3cpath fill='%2328a745' d='M2.3 6.73L.6 4.53c-.4-1.04.46-1.4 1.1-.8l1.1 1.4 3.4-3.8c.6-.63 1.6-.27 1.2.7l-4 4.6c-.43.5-.8.4-1.1.1z'/%3e%3c/svg%3e");
  background-repeat: no-repeat;
  background-position: center right calc(0.375em + 0.1875rem);
  background-size: calc(0.75em + 0.375rem) calc(0.75em + 0.375rem);
}

.form-control.is-invalid {
  border-color: #dc3545;
  background-image: url("data:image/svg+xml,%3csvg xmlns='http://www.w3.org/2000/svg' fill='%23dc3545' viewBox='-2 -2 7 7'%3e%3cpath stroke='%23dc3545' d='M0 0l3 3m0-3L0 3'/%3e%3ccircle r='.5'/%3e%3ccircle cx='3' r='.5'/%3e%3ccircle cy='3' r='.5'/%3e%3ccircle cx='3' cy='3' r='.5'/%3e%3c/svg%3E");
  background-repeat: no-repeat;
  background-position: center right calc(0.375em + 0.1875rem);
  background-size: calc(0.75em + 0.375rem) calc(0.75em + 0.375rem);
}

.md-bg .is-valid {
  /* background-color: blue!important; */
  background-position: 50% 100%, 50% 100% !important;
  /* background-image: url("data:image/svg+xml,%3csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 8 8'%3e%3cpath fill='%2328a745' d='M2.3 6.73L.6 4.53c-.4-1.04.46-1.4 1.1-.8l1.1 1.4 3.4-3.8c.6-.63 1.6-.27 1.2.7l-4 4.6c-.43.5-.8.4-1.1.1z'/%3e%3c/svg%3e")!important; */
}

.md-bg .is-valid:focus {
  background-image: linear-gradient(to bottom, #28a745, #28a745),
    linear-gradient(to bottom, #ced4da, #ced4da) !important;
  -webkit-box-shadow: none !important;
  box-shadow: none !important;
}

.md-bg .is-invalid {
  /* background-color: blue!important; */
  background-position: 50% 100%, 50% 100% !important;
}

.md-bg .is-invalid:focus {
  background-image: linear-gradient(to bottom, #dc3545, #dc3545),
    linear-gradient(to bottom, #ced4da, #ced4da) !important;
  -webkit-box-shadow: none !important;
  box-shadow: none !important;
}

.md-form .is-invalid .md-bg input {
  border-bottom: 1px solid red !important;
}

.md-form .is-valid .md-bg input {
  border-bottom: 1px solid #28a745 !important;
}

.mdb-date-picker-icon {
  position: absolute;
  right: 0;
  top: .6rem;
  right: .6rem;
  cursor: pointer;
}

.mdb-date-picker-icon:focus {
  color: #4285F4;
  outline: none;
}
</style>
