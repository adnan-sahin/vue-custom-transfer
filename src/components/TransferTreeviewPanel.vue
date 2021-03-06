<template>
  <div>
    <div class="transfer-panel">
      <div class="transfer-panel__header">
        {{ panelName }}
      </div>
      <div class="transfer-panel__body">
        <div class="items">
          <draggable
            v-bind="dragOptions"
            @start="drag = true"
            @end="drag = false"
            :move="onMoveCallback"
            :list="panelItems"
          >
            <div v-for="(item, index) in panelItems" :key="index">
              <div v-show="showGrandParent(item)" class="parent-items">
                <input
                  type="checkbox"
                  v-indeterminate="checkIndeterminate(item)"
                  v-model="item.checked"
                  @click="
                    handleChangeItem({
                      grandParentItem: item,
                    })
                  "
                />
                <label @click.prevent="toggleParentItem(index)">
                  <span> {{ item.name }} </span>
                  <img
                    :src="
                      item.collapsed
                        ? require('@/assets/down-arrow.svg')
                        : require('@/assets/up-arrow.svg')
                    "
                    alt=""
                    width="12px"
                    height="12px"
                  />
                </label>
              </div>
              <draggable
                v-bind="dragOptions"
                @start="drag = true"
                @end="drag = false"
                :move="onMoveCallback"
                v-model="item.children"
              >
                <div
                  v-show="!item.collapsed"
                  v-for="(subItem, subItemIndex) in item.children"
                  :key="subItemIndex"
                >
                  <div
                    v-show="
                      !subItem.children ||
                        (subItem.children && subItem.children.length > 0)
                    "
                    class="parent-items"
                    :style="{ paddingLeft: 15 + 'px' }"
                  >
                    <input
                      type="checkbox"
                      v-indeterminate="checkIndeterminate(subItem)"
                      v-model="subItem.checked"
                      @click="
                        handleChangeItem({
                          grandParentItem: item,
                          parentItem: subItem,
                        })
                      "
                    />
                    <label
                      @click.prevent="toggleSubParentItem(index, subItemIndex)"
                    >
                      <span>
                        {{ subItem.name }}
                      </span>
                      <img
                        v-if="subItem.children"
                        :src="
                          subItem.collapsed
                            ? require('@/assets/down-arrow.svg')
                            : require('@/assets/up-arrow.svg')
                        "
                        alt=""
                        width="12px"
                        height="12px"
                      />
                    </label>
                  </div>

                  <template v-if="subItem.children">
                    <draggable
                      v-bind="dragOptions"
                      v-model="subItem.children"
                      :move="onMoveCallback"
                      @start="drag = true"
                      @end="drag = false"
                    >
                      <div
                        :style="{ paddingLeft: 30 + 'px' }"
                        v-show="!subItem.collapsed"
                        class="child-items"
                        v-for="(childItem, childItemIndex) in subItem.children"
                        :key="childItemIndex"
                      >
                        <label>
                          <input
                            type="checkbox"
                            v-model="childItem.checked"
                            @click="
                              handleChangeItem({
                                grandParentItem: item,
                                parentItem: subItem,
                                childItem,
                              })
                            "
                          />
                          <span> {{ childItem.name }} {{ childItem.id }} </span>
                        </label>
                      </div>
                    </draggable>
                  </template>
                </div>
              </draggable>
            </div>
          </draggable>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import draggable from 'vuedraggable';
export default {
  name: 'TransferTreeviewPanel',
  components: { draggable },
  props: {
    panelName: { type: String },
    panelItems: { type: Array },
  },
  data() {
    return {
      drag: false,
    };
  },
  computed: {
    dragOptions() {
      return {
        animation: 200,
        group: 'description',
        disabled: false,
        ghostClass: 'ghost',
      };
    },
  },
  directives: {
    indeterminate: function(el, binding) {
      el.indeterminate = Boolean(binding.value);
    },
  },
  methods: {
    onMoveCallback(event, originalEvent) {
      return event.relatedContext.list.includes(event.draggedContext.element);
    },
    checkIndeterminate(item) {
      return (
        (item.children &&
          item.children.some((p) => p.checked) &&
          !item.children.every((p) => p.checked)) ||
        (item.children &&
          item.children
            .filter((p) => p.children && p.children.length > 0)
            .some((p) => p.children && p.children.some((p) => p.checked)) &&
          !item.children
            .filter((p) => p.children && p.children.length > 0)
            .every((p) => p.children && p.children.every((p) => p.checked)))
      );
    },
    showGrandParent(item) {
      return (
        item.children &&
        item.children.length > 0 &&
        item.children.some(
          (p) => !p.children || (p.children && p.children.length > 0)
        )
      );
    },
    toggleParentItem(index) {
      this.$emit('toggleParentItem', index);
    },
    toggleSubParentItem(index, subItemIndex) {
      this.$emit('toggleSubParentItem', index, subItemIndex);
    },
    handleChangeItem({ grandParentItem, parentItem, childItem }) {
      if (grandParentItem && parentItem && childItem) {
        let grandParent = this.panelItems.find(
          (p) => p.id == grandParentItem.id
        );
        let parent = grandParent.children.find((p) => p.id == parentItem.id);
        childItem.checked = !childItem.checked;
        parentItem.checked = parent.children.every((p) => p.checked);
        grandParentItem.checked = grandParentItem.children.every(
          (p) => p.checked
        );
      } else if (grandParentItem && parentItem && !childItem) {
        let grandParent = this.panelItems.find(
          (p) => p.id == grandParentItem.id
        );
        let parent = grandParent.children.find((p) => p.id == parentItem.id);
        parent.checked = !parent.checked;
        grandParent.checked = grandParent.children.every((p) => p.checked);
        if (parent.children) {
          parent.children.forEach((child) => {
            child.checked = parent.checked;
          });
        }
      } else if (grandParentItem && !parentItem && !childItem) {
        let grandParent = this.panelItems.find(
          (p) => p.id == grandParentItem.id
        );
        grandParent.checked = !grandParent.checked;
        grandParent.children.forEach((parent) => {
          parent.checked = grandParent.checked;
          if (parent.children) {
            parent.children.forEach((child) => {
              child.checked = grandParent.checked;
            });
          }
        });
      }
      this.$emit('hasAnySelectedItem', this.hasAnySelectedItem());
    },
    hasAnySelectedItem() {
      if (this.panelItems.some((p) => p.checked)) return true;
      for (let i = 0; i < this.panelItems.length; i++) {
        const grandParent = this.panelItems[i];
        if (grandParent.children) {
          if (grandParent.children.some((p) => p.checked)) return true;
          for (let j = 0; j < grandParent.children.length; j++) {
            const parent = grandParent.children[j];
            if (parent.children) {
              if (parent.children.some((p) => p.checked)) return true;
            } else if (parent.checked) return true;
          }
        } else if (grandParent.checked) return true;
      }
    },
    getSelectedItems() {
      let selectedItems = [];
      this.panelItems.forEach((grandParent) => {
        if (grandParent.checked) {
          grandParent.children.forEach((parent) => {
            if (parent.children) {
              parent.children.forEach((child) => {
                selectedItems.push({
                  grandParentItem: grandParent,
                  parentItem: parent,
                  childItem: child,
                });
              });
            } else {
              selectedItems.push({
                grandParentItem: grandParent,
                parentItem: parent,
              });
            }
          });
        } else {
          let checkedParents = grandParent.children.filter((p) => p.checked);
          checkedParents.forEach((parent) => {
            if (parent.children) {
              parent.children.forEach((child) => {
                selectedItems.push({
                  grandParentItem: grandParent,
                  parentItem: parent,
                  childItem: child,
                });
              });
            } else {
              selectedItems.push({
                grandParentItem: grandParent,
                parentItem: parent,
              });
            }
          });
          let unCheckedParents = grandParent.children.filter((p) => !p.checked);
          unCheckedParents.forEach((parent) => {
            if (parent.children) {
              parent.children
                .filter((p) => p.checked)
                .forEach((child) => {
                  selectedItems.push({
                    grandParentItem: grandParent,
                    parentItem: parent,
                    childItem: child,
                  });
                });
            } else if (parent.checked) {
              selectedItems.push({
                grandParentItem: grandParent,
                parentItem: parent,
              });
            }
          });
        }
      });
      return selectedItems;
    },
  },
  watch: {
    panelItems: {
      handler(newVal) {},
    },
  },
};
</script>

<style lang="scss" scoped>
.transfer-panel {
  width: 210px;
  border: 1px solid #b3c4d4;
  &__header {
    height: 30px;
    box-sizing: border-box;
    background: #e0e7ee;
    color: #003a70;
    padding: 7px 10px;
    font-size: 12px;
    border-top-left-radius: 2px;
    border-top-right-radius: 2px;
  }
  &__body {
    padding: 5px 0;
    overflow: auto;
    scrollbar-color: #b3c4d4 #b3c4d4;
    scrollbar-width: thin;
    text-align: left;

    &::-webkit-scrollbar {
      height: 100%;
      width: 6px;
    }
    &::-webkit-scrollbar-track {
      background: #e0e7ee;
    }
    &::-webkit-scrollbar-thumb {
      background-color: #b3c4d4;
      border-radius: 4px;
      border: 1px solid #b3c4d4;
    }
    height: 450px;
    .items {
      color: #003a70;
      font-size: 12px;
      .parent-items {
        box-sizing: border-box;
        border-bottom: 1px solid #e0e7ee;
        position: relative;
        height: 28px;
        display: flex;
        align-items: center;
        text-transform: uppercase;
        width: 100%;
        label {
          display: flex;
          align-items: center;
          width: 100%;
          position: relative;
          img {
            margin-right: 5px;
            margin-left: auto;
          }
        }
        span {
          font-weight: 700;
        }
      }
      .child-items {
        box-sizing: border-box;
        border-bottom: 1px solid #e0e7ee;
        height: 28px;
        display: flex;
        align-items: center;
        label {
          display: flex;
          align-items: center;
        }
      }
    }
  }

  .ghost {
    opacity: 0.5;
    background: #e0e7ee;
  }
}
</style>
