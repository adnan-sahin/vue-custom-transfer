<template>
  <div class="custom-transfer_container">
    <div>
      <label>
        <input type="checkbox" v-model="isGroup" />
        <span> Group by resource type </span>
      </label>
    </div>
    <div class="custom-transfer">
      <div class="custom-transfer__source">
        <TransferTreeviewPanel
          ref="sourcePanel"
          panelName="SOURCE PANEL"
          :panelItems="sourceItems"
          @hasAnySelectedItem="(val) => (hasAnySelectedSourceItem = val)"
          @toggleParentItem="handleSourceToggleParentItem"
          @toggleSubParentItem="handleSourceToggleSubParentItem"
        />
      </div>
      <div class="custom-transfer__buttons">
        <button
          :disabled="!hasAnySelectedSourceItem"
          class="custom-transfer__button"
          @click.prevent="handleFromSourceToTarget"
        >
          <img
            :src="getImage('arrow-right.svg')"
            alt=""
            width="12px"
            height="12px"
          />
        </button>
        <button
          :disabled="!hasAnySelectedTargetItem"
          @click.prevent="handleFromTargetToSource"
          class="custom-transfer__button"
        >
          <img
            :src="getImage('arrow-left.svg')"
            alt=""
            width="12px"
            height="12px"
          />
        </button>
        <button
          :disabled="!sourceItems.length"
          class="custom-transfer__button"
          @click.prevent="handleFromAllSourceToTarget"
        >
          <img
            :src="getImage('arrow_double-right.svg')"
            alt=""
            width="12px"
            height="12px"
          />
        </button>
        <button
          :disabled="!targetItems.length"
          class="custom-transfer__button"
          @click.prevent="handleFromAllTargetToSource"
        >
          <img
            :src="getImage('arrow_double-left.svg')"
            alt=""
            width="12px"
            height="12px"
          />
        </button>
      </div>
      <div class="custom-transfer__target">
        <template v-if="isGroup">
          <TransferTreeviewPanel
            ref="targetPanel"
            panelName="TARGET PANEL"
            :panelItems="targetItems"
            @hasAnySelectedItem="(val) => (hasAnySelectedTargetItem = val)"
            @toggleParentItem="handleTargetToggleParentItem"
            @toggleSubParentItem="handleTargetToggleSubParentItem"
          />
        </template>
        <template v-else>
          <TransferListPanel
            ref="targetPanel"
            panelName="TARGET PANEL"
            :panelItems="targetItems"
            @hasAnySelectedItem="(val) => (hasAnySelectedTargetItem = val)"
          />
        </template>
      </div>
    </div>
  </div>
</template>

<script>
import TransferTreeviewPanel from './TransferTreeviewPanel';
import TransferListPanel from './TransferListPanel';
export default {
  components: { TransferTreeviewPanel, TransferListPanel },
  data() {
    return {
      isGroup: true,
      config: {
        sourcePanelName: 'SOURCE PANEL',
        targetPanelName: 'TARGET PANEL',
      },
      selectedSourceItems: [],
      selectedTargetItems: [],
      hasAnySelectedSourceItem: false,
      hasAnySelectedTargetItem: false,
      initialItems: [],
      sourceItems: [],
      targetItems: [],
      listTargetItems: [],
    };
  },
  methods: {
    handleSourceToggleParentItem(index) {
      let item = this.sourceItems[index];
      item.collapsed = !item.collapsed;
    },
    handleSourceToggleSubParentItem(parentIndex, subParentIndex) {
      let item = this.sourceItems[parentIndex].children[subParentIndex];
      item.collapsed = !item.collapsed;
    },
    handleTargetToggleParentItem(index) {
      let item = this.targetItems[index];
      item.collapsed = !item.collapsed;
    },
    handleTargetToggleSubParentItem(parentIndex, subParentIndex) {
      let item = this.targetItems[parentIndex].children[subParentIndex];
      item.collapsed = !item.collapsed;
    },
    getImage(name) {
      return require('@/assets/' + name);
    },

    handleFromSourceToTarget() {
      this.selectedSourceItems = this.$refs.sourcePanel.getSelectedItems();
      this.transferFromSourceToTarget();
    },
    handleFromTargetToSource() {
      this.selectedTargetItems = this.$refs.targetPanel.getSelectedItems();
      this.transferFromTargetToSource();
    },
    transferFromTargetToSource() {
      this.transferSelectedItems(
        this.selectedTargetItems,
        this.targetItems,
        this.sourceItems
      );
      this.hasAnySelectedTargetItem = false;
      this.selectedTargetItems = [];
    },
    transferFromSourceToTarget() {
      this.transferSelectedItems(
        this.selectedSourceItems,
        this.sourceItems,
        this.targetItems
      );
      this.hasAnySelectedSourceItem = false;
      this.selectedSourceItems = [];
    },
    transferSelectedItems(selectedItems, sourceItems, targetItems) {
      JSON.parse(JSON.stringify(selectedItems)).forEach(
        ({ grandParentItem, parentItem, childItem }) => {
          // if target has not grandParent item, It will add grandParent item
          if (!targetItems.find((item) => item.id == grandParentItem.id)) {
            grandParentItem.children = [];
            targetItems.push(grandParentItem);
          }
          let grandParentTarget = targetItems.find(
            (item) => item.id == grandParentItem.id
          );
          grandParentTarget.checked = false;

          if (grandParentItem && parentItem && childItem) {
            // add to target
            if (
              !grandParentTarget.children.find((p) => p.id == parentItem.id)
            ) {
              parentItem.children = [];
              grandParentTarget.children.push(parentItem);
            }
            let parentTarget = grandParentTarget.children.find(
              (p) => p.id == parentItem.id
            );
            parentTarget.checked = false;
            if (!parentTarget.children.find((p) => p.id == childItem.id)) {
              childItem.checked = false;
              parentTarget.children.push(childItem);
            }
            //start remove from source
            let grandParentSource = sourceItems.find(
              (item) => item.id == grandParentItem.id
            );
            let parentSource = grandParentSource.children.find(
              (p) => p.id == parentItem.id
            );
            let sourceIndex = parentSource.children.findIndex(
              (p) => p.id == childItem.id
            );
            if (sourceIndex > -1) {
              parentSource.children.splice(sourceIndex, 1);
              if (parentSource.children.length == 0) {
                grandParentSource.children.splice(
                  grandParentSource.children.findIndex(
                    (p) => p.id == parentItem.id
                  ),
                  1
                );
              }
              if (grandParentSource.children.length == 0) {
                sourceItems.splice(
                  sourceItems.find((item) => item.id == grandParentItem.id),
                  1
                );
              }
            }
            // end remove from source
          } else if (grandParentItem && parentItem) {
            // add to target
            if (
              !grandParentTarget.children.find((p) => p.id == parentItem.id)
            ) {
              parentItem.checked = false;
              grandParentTarget.children.push(parentItem);
            }

            //remove from source
            let grandParentSource = sourceItems.find(
              (p) => p.id == grandParentItem.id
            );
            let sourceIndex = grandParentSource.children.findIndex(
              (p) => p.id == parentItem.id
            );
            if (sourceIndex > -1) {
              grandParentSource.children.splice(sourceIndex, 1);
              if (grandParentSource.children.length == 0) {
                sourceItems.splice(
                  sourceItems.findIndex(
                    (item) => item.id == grandParentItem.id
                  ),
                  1
                );
              }
            }
          }
        }
      );
    },
    handleFromAllSourceToTarget() {
      this.selectedSourceItems = this.transferAllItems(this.sourceItems);
      this.transferFromSourceToTarget();
    },
    handleFromAllTargetToSource() {
      this.selectedTargetItems = this.transferAllItems(this.targetItems);
      this.transferFromTargetToSource();
    },
    transferAllItems(items) {
      let selectedItems = [];
      items.forEach((grandParent) => {
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
      });
      return selectedItems;
    },
  },
  created() {
    const mockData = [
      {
        id: 1,
        name: 'Grand Parent Menu 1',
        children: [
          {
            id: 1,
            name: 'Bridge Stand',
            children: [
              { id: 1, name: 'Child Menu', type: 'resource' },
              { id: 2, name: 'Child Menu' },
              { id: 3, name: 'Child Menu' },
              { id: 4, name: 'Child Menu' },
            ],
          },
          {
            id: 2,
            name: 'Parent Menu 2',
            children: [
              { id: 5, name: 'Child Menu' },
              { id: 6, name: 'Child Menu' },
              { id: 7, name: 'Child Menu' },
              { id: 8, name: 'Child Menu' },
            ],
          },
        ],
      },
      {
        id: 2,
        name: 'Grand Parent Menu 2',

        children: [
          {
            id: 1,
            name: 'Parent Menu 3',
            children: [
              { id: 9, name: 'Child Menu', type: 'resource' },
              { id: 10, name: 'Child Menu' },
              { id: 11, name: 'Child Menu' },
              { id: 12, name: 'Child Menu' },
              { id: 13, name: 'Child Menu' },
            ],
          },
          {
            id: 2,
            name: 'Parent Menu 4',
            children: [
              { id: 14, name: 'Child Menu', children: [] },
              { id: 15, name: 'Child Menu', children: [] },
              { id: 16, name: 'Child Menu', children: [] },
              { id: 17, name: 'Child Menu', children: [] },
              { id: 18, name: 'Child Menu', children: [] },
              { id: 19, name: 'Child Menu', children: [] },
              { id: 20, name: 'Child Menu', children: [] },
            ],
          },
        ],
      },
      {
        id: 0,
        name: 'Unplanned',
        children: [
          { id: 21, name: 'Stand', type: 'unit', children: [] },
          { id: 22, name: 'Gate', type: 'unit', children: [] },
          { id: 23, name: 'Counter', children: [] },
          { id: 24, name: 'Chute', children: [] },
        ],
      },
    ];
    this.sourceItems = mockData.map((grandParent) => {
      grandParent.checked = false;
      grandParent.collapsed = false;
      if (grandParent.children && grandParent.children.length > 0) {
        grandParent.children.map((parent) => {
          parent.checked = false;
          parent.collapsed = false;
          if (parent.children && parent.children.length > 0) {
            parent.children.map((child) => {
              child.checked = false;
              if (child.children && child.children.length == 0) {
                delete child.children;
              }
              return child;
            });
          } else if (parent.children && parent.children.length == 0) {
            delete parent.children;
          }
          return parent;
        });
      }
      return grandParent;
    });
  },
};
</script>

<style lang="scss">
.custom-transfer_container {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  .custom-transfer {
    font-family: 'Roboto', sans-serif;
    display: flex;
    &__buttons {
      display: flex;
      flex-direction: column;
      justify-content: center;
      margin: 5px 10px;
    }
    &__button {
      width: 30px;
      height: 30px;
      border: 1px solid #003a70;
      border-radius: 6px;
      margin: 5px 0;
      cursor: pointer;
      display: flex;
      align-items: center;
      justify-content: center;
      background: #fff;
      &:disabled {
        opacity: 0.2;
        cursor: not-allowed;
      }
    }
  }
}
</style>
