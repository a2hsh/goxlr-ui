<template>
  <div
    :id="id"
    class="wrapper"
    role="listitem"
    :aria-labelledby="`label_${id}`"
    :aria-selected="selected"
    :tabindex="focused ? 0 : -1"
    :aria-disabled="disabled"
  >
    <label
      ref="label"
      :id="`label_${id}`"
      :for="id"
      :class="{ selected: this.selected, disabled: this.disabled }"
    >
      <font-awesome-icon v-if="icon !== undefined" :icon="icon" />
      {{ text }}
    </label>
  </div>
  <div
    ref="right_ref"
    class="right_side"
    :class="{ selected: this.selected, disabled: this.disabled }"
  >
    <slot name="right"></slot>
  </div>
</template>

<script>
import { FontAwesomeIcon } from "@fortawesome/vue-fontawesome";

export default {
  components: { FontAwesomeIcon },
  emits: ["item-selected", "item-focused"],
  name: "ListItem",

  props: {
    text: { type: String, required: true },
    icon: { type: String, required: false },
    id: { type: String, required: true },
    group: { type: String, required: true },

    selected: { type: Boolean, required: false, default: false },
    disabled: { type: Boolean, required: false, default: false },
    focused: { type: Boolean, required: false, default: false },

    padding: { type: String, required: false, default: "8px" },
    background: { type: String, required: false, default: "#3b413f" },
  },

  data: function () {
    return {
      local_selected: false,
    };
  },

  methods: {
    change() {
      this.$emit("item-selected", this.id);
    },

    isSelected() {
      if (this.$refs.check === undefined) {
        console.log("No Ref.");
        return false;
      }
      return this.selected;
    },

    focus() {
      this.$refs.label.focus();
    },
  },

  computed: {
    right_width: function () {
      return this.$refs.right_ref.clientWidth + "px";
    },
  },
};
</script>

<style scoped>
.wrapper {
  display: flex;
  flex-direction: row;
}

/* We're going to try and make the label look and behave like a legacy 'Button' */
label {
  display: block;
  box-sizing: border-box;

  width: calc(100% - 12px - v-bind(right_width));
  margin: auto 0 auto auto;
  background-color: v-bind(background);

  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;

  padding: v-bind(padding);
  text-align: left;
  color: #fff;
}

.right_side {
  background-color: #3b413f;
  margin-right: auto;
  color: #fff;
}

:slotted(.right_side > *) {
  color: #fff;
}

:slotted(.right_side.selected > *) {
  color: #353937;
}

.right_side.selected {
  background-color: #59b1b6;
  color: #353937;
}

label.selected {
  background-color: #59b1b6;
  color: #353937;
}

label:not(.selected):focus-within {
  background-color: #49514e;
}

label:not(.selected):hover,
label:not(.selected):hover + div {
  background-color: #49514e;
}

label.disabled,
label.disabled + div {
  background-color: #383d3b;
  color: #959796;
}
</style>
