<template>
  <div>
    <label>
      <input type="checkbox" v-model="isGroup" />
      Group
    </label>
    <div class="custom-transfer">
      <div class="custom-transfer__source">
        <TransferPanel
          panelType="source"
          panelName="SOURCE PANEL"
          :panelItems="sourceItems"
          @changeSelectedItems="handleChangeSelectedSourceItems"
          @toggleParentItem="handleSourceToggleParentItem"
          @toggleSubParentItem="handleSourceToggleSubParentItem"
        />
      </div>
      <div class="custom-transfer__buttons">
        <button
          :disabled="selectedSourceItems.length == 0"
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
          :disabled="selectedTargetItems.length == 0"
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
        <TransferPanel
          panelType="target"
          panelName="TARGET PANEL"
          :panelItems="targetItems"
          @changeSelectedItems="handleChangeSelectedTargetItems"
          @toggleParentItem="handleTargetToggleParentItem"
          @toggleSubParentItem="handleTargetToggleSubParentItem"
        />
      </div>
    </div>
  </div>
</template>

<script>
import TransferPanel from './TransferPanel';
export default {
  components: { TransferPanel },
  data() {
    return {
      isGroup: false,
      config: {
        sourcePanelName: 'SOURCE PANEL',
        targetPanelName: 'TARGET PANEL',
      },
      selectedSourceItems: [],
      selectedTargetItems: [],
      initialItems: [],
      sourceItems: [
        {
          id: 1,
          name: 'Grand Parent Menu 1',
          checked: false,
          collapsed: false,
          children: [
            {
              id: 1,
              name: 'Parent Menu 1',
              checked: false,
              collapsed: false,
              children: [
                { id: 1, name: 'Child Menu', checked: false },
                { id: 2, name: 'Child Menu', checked: false },
                { id: 3, name: 'Child Menu', checked: false },
                { id: 4, name: 'Child Menu', checked: false },
              ],
            },
            {
              id: 2,
              name: 'Parent Menu 2',
              checked: false,
              collapsed: false,
              children: [
                { id: 5, name: 'Child Menu', checked: false },
                { id: 6, name: 'Child Menu', checked: false },
                { id: 7, name: 'Child Menu', checked: false },
                { id: 8, name: 'Child Menu', checked: false },
              ],
            },
          ],
        },
        {
          id: 2,
          name: 'Grand Parent Menu 2',
          checked: false,
          collapsed: false,
          children: [
            {
              id: 1,
              name: 'Parent Menu 3',
              checked: false,
              collapsed: false,
              children: [
                { id: 9, name: 'Child Menu', checked: false },
                { id: 10, name: 'Child Menu', checked: false },
                { id: 11, name: 'Child Menu', checked: false },
                { id: 12, name: 'Child Menu', checked: false },
                { id: 13, name: 'Child Menu', checked: false },
              ],
            },
            {
              id: 2,
              name: 'Parent Menu 4',
              checked: false,
              collapsed: false,
              children: [
                { id: 14, name: 'Child Menu', checked: false },
                { id: 15, name: 'Child Menu', checked: false },
                { id: 16, name: 'Child Menu', checked: false },
                { id: 17, name: 'Child Menu', checked: false },
                { id: 18, name: 'Child Menu', checked: false },
                { id: 19, name: 'Child Menu', checked: false },
                { id: 20, name: 'Child Menu', checked: false },
              ],
            },
          ],
        },
        {
          id: 4,
          name: 'Parent Menu 5',
          checked: false,
          collapsed: false,
          children: [
            { id: 21, name: 'Child Menu', checked: false },
            { id: 22, name: 'Child Menu', checked: false },
            { id: 23, name: 'Child Menu', checked: false },
            { id: 24, name: 'Child Menu', checked: false },
          ],
        },
      ],
      targetItems: [
        {
          id: 1,
          name: 'Grand Parent Menu 1',
          checked: false,
          collapsed: false,
          children: [
            {
              id: 1,
              name: 'Parent Menu 1',
              checked: false,
              collapsed: false,
              children: [],
            },
            {
              id: 2,
              name: 'Parent Menu 2',
              checked: false,
              collapsed: false,
              children: [],
            },
          ],
        },
        {
          id: 2,
          name: 'Grand Parent Menu 2',
          checked: false,
          collapsed: false,
          children: [
            {
              id: 1,
              name: 'Parent Menu 3',
              checked: false,
              collapsed: false,
              children: [],
            },
            {
              id: 2,
              name: 'Parent Menu 4',
              checked: false,
              collapsed: false,
              children: [],
            },
          ],
        },
        {
          id: 4,
          name: 'Parent Menu 5',
          checked: false,
          collapsed: false,
          children: [],
        },
      ],
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
    handleChangeSelectedSourceItems(items) {
      this.selectedSourceItems = items;
    },
    handleChangeSelectedTargetItems(items) {
      this.selectedTargetItems = items;
    },
    handleFromSourceToTarget() {
      this.selectedSourceItems.forEach(
        ({ grandParentItem, parentItem, childItem }) => {
          if (grandParentItem && parentItem && childItem) {
            let grandParentTarget = this.targetItems.find(
              (item) => item.id == grandParentItem.id
            );
            let parentTarget = grandParentTarget.children.find(
              (p) => p.id == parentItem.id
            );
            if (!parentTarget.children.find((p) => p.id == childItem.id)) {
              parentTarget.children.push(
                Object.assign({}, { ...childItem, checked: false })
              );
            }
            let grandParentSource = this.sourceItems.find(
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
            }
          } else if (grandParentItem && parentItem) {
            let grandParentTarget = this.targetItems.find(
              (p) => p.id == grandParentItem.id
            );

            if (
              !grandParentTarget.children.find((p) => p.id == parentItem.id)
            ) {
              grandParentTarget.children.push(
                Object.assign({}, { ...parentItem, checked: false })
              );
            }
            let grandParentSource = this.sourceItems.find(
              (p) => p.id == grandParentItem.id
            );
            let sourceIndex = grandParentSource.children.findIndex(
              (p) => p.id == parentItem.id
            );
            if (sourceIndex > -1) {
              grandParentSource.children.splice(sourceIndex, 1);
            }
          }
        }
      );
      this.selectedSourceItems = [];
    },
    handleFromTargetToSource() {
      this.selectedTargetItems.forEach(
        ({ grandParentItem, parentItem, childItem }) => {
          if (grandParentItem && parentItem && childItem) {
            let grandParentSource = this.sourceItems.find(
              (item) => item.id == grandParentItem.id
            );
            let parentSource = grandParentSource.children.find(
              (p) => p.id == parentItem.id
            );
            if (!parentSource.children.find((p) => p.id == childItem.id)) {
              parentSource.children.push(
                Object.assign({}, { ...childItem, checked: false })
              );
            }
            let grandParentTarget = this.targetItems.find(
              (item) => item.id == grandParentItem.id
            );
            let parentTarget = grandParentTarget.children.find(
              (p) => p.id == parentItem.id
            );
            let targetIndex = parentTarget.children.findIndex(
              (p) => p.id == childItem.id
            );
            if (targetIndex > -1) {
              parentTarget.children.splice(targetIndex, 1);
            }
          } else if (grandParentItem && parentItem) {
            let grandParentSource = this.sourceItems.find(
              (p) => p.id == grandParentItem.id
            );

            if (
              !grandParentSource.children.find((p) => p.id == parentItem.id)
            ) {
              grandParentSource.children.push(
                Object.assign({}, { ...parentItem, checked: false })
              );
            }
            let grandParentTarget = this.targetItems.find(
              (p) => p.id == grandParentItem.id
            );
            let targetIndex = grandParentTarget.children.findIndex(
              (p) => p.id == parentItem.id
            );
            if (targetIndex > -1) {
              grandParentTarget.children.splice(targetIndex, 1);
            }
          }
        }
      );
      this.selectedTargetItems = [];
    },
    handleFromAllSourceToTarget() {
      this.selectedSourceItems = this.transferAllItems(this.sourceItems);
      this.handleFromSourceToTarget();
    },
    handleFromAllTargetToSource() {
      this.selectedTargetItems = this.transferAllItems(this.targetItems);
      this.handleFromTargetToSource();
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
};
</script>

<style lang="scss">
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
</style>
