<script>
import VerticalScrollingContainer from "@/components/containers/VerticalScrollingContainer.vue";
import ListItem from "@/components/lists/RadioItem.vue";
import DropMenu from "@/components/design/DropMenu.vue";
//store focus on the first item in the list

export default {
  name: "ScrollingRadioList",
  emits: ["selection-changed", "menu-opened", "menu-selected"],
  components: { DropMenu, ListItem, VerticalScrollingContainer },

  props: {
    label: { type: String, default: "" },
    group: String,
    options: Array,
    selected: String,
    menu: Array,
    menu_id: String,

    max_height: { type: String, optional: true, default: "inherit" },
    padding: { type: String, required: false, default: "8px" },
    background: { type: String, required: false, default: "#3b413f" },
  },

  methods: {
    select(option) {
      this.$emit("selection-changed", option);
    },

    getUniqueId(name) {
      return this.group + "_" + name;
    },

    getButtonByRef(name) {
      return this.$refs[this.getUniqueId(name)][0];
    },

    getFirstButtonRef() {
      return this.$refs[this.getUniqueId(this.options[0].id)][0];
    },

    menuOpened(event, return_id, item) {
      // Trigger an event so anything upstream can handle the behaviour..
      this.$emit("menu-opened", event, return_id, item);

      // Trigger the menu open.
      this.$refs.contextMenu.showMenu(
        event,
        item,
        return_id,
        this.container.getMainRef().scrollTop
      );
    },

    getButtonId(value) {
      return (
        value
          .toLowerCase()
          .replace(" ", "_")
          .replace("(", "_")
          .replace(")", "_") +
        "_" +
        this.menu_id
      );
    },

    menuOptionClicked(event) {
      // We need to emit this up to whatever is putting us here :)
      this.$emit("menu-selected", event);
    },
  },

  data() {
    return {
      container: undefined,
      focus: undefined,
    };
  },

  mounted() {
    this.container = this.$refs.container;
    this.focus = this.getFirstButtonRef();
  },

  computed: {
    maxHeight() {
      return this.max_height;
    },
    isFocused() {
      //focus might change due to a keydown events on the listBox.
      //true or false refers to wether the item ref maches this.focus, which will be changed from the keyDown function.
      //returning true will add the focused class to the item.
      return this.$refs[this.focus.id][0] === this.focus;
    },
  },
};
</script>

<template>
  <VerticalScrollingContainer
    class="height"
    ref="container"
    role="radiogroup"
    :aria-label="label"
  >
    <ListItem
      v-for="option in options"
      :key="option.id"
      :id="getUniqueId(option.id)"
      :ref="getUniqueId(option.id)"
      :group="group"
      :text="option.label"
      :selected="selected === option.id"
      :disabled="option.disabled"
      :focused="options[0].id === option.id"
      @item-selected="select(option.id)"
      :icon="option.icon"
      :background="background"
      :padding="padding"
    >
      <template v-if="this.menu !== undefined" #right>
        <button
          :aria-label="`${option.label} Options`"
          :id="getButtonId(option.id)"
          aria-haspopup="menu"
          :aria-controls="this.menu_id"
          @click.prevent.stop="
            menuOpened($event, getButtonId(option.id), option.id)
          "
        >
          <span width="20px"
            ><font-awesome-icon icon="fa-solid fa-ellipsis-vertical"
          /></span>
        </button>
      </template>
    </ListItem>
    <slot></slot>
  </VerticalScrollingContainer>
  <DropMenu
    v-if="this.menu !== undefined"
    :options="this.menu"
    ref="contextMenu"
    @option-clicked="menuOptionClicked"
    :menu_id="this.menu_id"
  />
</template>

<style scoped>
.container .height {
  max-height: v-bind(maxHeight);
}

button {
  background-color: transparent;
  border: 0;
  padding: 6px;
  margin: 0;
}

button:focus {
  outline: none;
}
</style>
