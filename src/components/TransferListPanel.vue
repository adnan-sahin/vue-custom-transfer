<template>
  <div class="transfer-panel">
    <div class="transfer-panel__header">
      {{ panelName }}
    </div>
    <div class="transfer-panel__body">
      <div class="panel-list">
        <draggable
          v-bind="dragOptions"
          @start="drag = true"
          @end="drag = false"
          v-model="listItems"
        >
          <div
            v-for="(item, index) in listItems"
            :key="index"
            class="panel-list-item"
          >
            <label>
              <input
                type="checkbox"
                v-model="item.checked"
                @click="handleChangeItem(item)"
              />
              <span> {{ item.name }} {{ item.id }} </span>
            </label>
          </div>
        </draggable>
      </div>
    </div>
  </div>
</template>

<script>
import draggable from 'vuedraggable';
export default {
  name: 'TransferListPanel',
  components: { draggable },
  props: {
    panelName: { type: String },
    panelItems: { type: Array },
  },
  data() {
    return {
      drag: false,
      listItems: [],
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
  watch: {
    panelItems: {
      handler(val) {
        debugger;
        let selectedItems = [];
        val.forEach((grandParent) => {
          if (grandParent.children) {
            grandParent.children.forEach((parent) => {
              if (parent.children && parent.children.length > 0) {
                selectedItems.push(...parent.children);
              } else if (!parent.children) {
                selectedItems.push(parent);
              }
            });
          } else {
            selectedItems.push(grandParent);
          }
        });
        this.listItems = JSON.parse(JSON.stringify(selectedItems));
      },
      deep: true,
      immediate: true,
    },
  },
  methods: {
    handleChangeItem(item) {
      item.checked = !item.checked;
      this.$emit('hasAnySelectedItem', this.hasAnySelectedItem());
    },
    hasAnySelectedItem() {
      return this.listItems.some((p) => p.checked);
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
    .panel-list {
      color: #003a70;
      font-size: 12px;
      &-item {
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
