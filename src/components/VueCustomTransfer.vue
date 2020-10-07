<template>
  <div>
    <label>
      <input type="checkbox" v-model="isGroup" />
      Group
    </label>
    <div class="custom-transfer">
      <div class="transfer-panel custom-transfer__source">
        <div class="transfer-panel__header">
          {{ config.sourcePanelName }}
        </div>
        <div class="transfer-panel__body">
          <div class="menu">
            <div v-for="(menu, index) in sourceMenu" :key="index">
              <div class="parent-menu">
                <input type="checkbox" v-model="menu.checked" />
                <label @click.prevent="toggleParentMenu(index)">
                  <span>
                    {{ menu.name }}
                  </span>
                  <img
                    :src="
                      menu.collapsed
                        ? require('@/assets/down-arrow.svg')
                        : require('@/assets/up-arrow.svg')
                    "
                    alt=""
                    width="12px"
                    height="12px"
                  />
                </label>
              </div>
              <div
                v-show="!menu.collapsed"
                v-for="(submenu, subMenuIndex) in menu.children"
                :key="subMenuIndex"
              >
                <div class="parent-menu" :style="{ paddingLeft: 15 + 'px' }">
                  <input type="checkbox" v-model="submenu.checked" />
                  <label
                    @click.prevent="toggleSubParentMenu(index, subMenuIndex)"
                  >
                    <span>
                      {{ submenu.name }}
                    </span>
                    <img
                      v-if="submenu.children"
                      :src="
                        submenu.collapsed
                          ? require('@/assets/down-arrow.svg')
                          : require('@/assets/up-arrow.svg')
                      "
                      alt=""
                      width="12px"
                      height="12px"
                    />
                  </label>
                </div>

                <template v-if="submenu.children">
                  <div
                    :style="{ paddingLeft: 30 + 'px' }"
                    v-show="!submenu.collapsed"
                    class="child-menu"
                    v-for="(childSubMenu,
                    childSubMenuIndex) in submenu.children"
                    :key="childSubMenuIndex"
                  >
                    <label>
                      <input type="checkbox" v-model="childSubMenu.checked" />
                      <span>
                        {{ childSubMenu.name }}
                      </span>
                    </label>
                  </div>
                </template>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="transfer-panel custom-transfer__target">
        <div class="transfer-panel__header">
          {{ config.targetPanelName }}
        </div>
        <div class="transfer-panel__body"></div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      isGroup: false,
      config: {
        sourcePanelName: 'SOURCE PANEL',
        targetPanelName: 'TARGET PANEL',
      },
      sourceMenu: [
        {
          name: 'Grand Parent Menu 1',
          checked: false,
          collapsed: false,
          children: [
            {
              name: 'Parent Menu 1',
              checked: false,
              collapsed: false,
              children: [
                { id: 1, name: 'Sub Menu', checked: false },
                { id: 2, name: 'Sub Menu', checked: false },
                { id: 3, name: 'Sub Menu', checked: false },
                { id: 4, name: 'Sub Menu', checked: false },
              ],
            },
            {
              name: 'Parent Menu 2',
              checked: false,
              collapsed: false,
              children: [
                { id: 5, name: 'Sub Menu', checked: false },
                { id: 6, name: 'Sub Menu', checked: false },
                { id: 7, name: 'Sub Menu', checked: false },
                { id: 8, name: 'Sub Menu', checked: false },
              ],
            },
          ],
        },
        {
          name: 'Grand Parent Menu 2',
          checked: false,
          collapsed: false,
          children: [
            {
              name: 'Parent Menu 3',
              checked: false,
              collapsed: false,
              children: [
                { id: 9, name: 'Sub Menu', checked: false },
                { id: 10, name: 'Sub Menu', checked: false },
                { id: 11, name: 'Sub Menu', checked: false },
                { id: 12, name: 'Sub Menu', checked: false },
                { id: 13, name: 'Sub Menu', checked: false },
              ],
            },
            {
              name: 'Parent Menu 4',
              checked: false,
              collapsed: false,
              children: [
                { id: 14, name: 'Sub Menu', checked: false },
                { id: 15, name: 'Sub Menu', checked: false },
                { id: 16, name: 'Sub Menu', checked: false },
                { id: 17, name: 'Sub Menu', checked: false },
                { id: 18, name: 'Sub Menu', checked: false },
                { id: 19, name: 'Sub Menu', checked: false },
                { id: 20, name: 'Sub Menu', checked: false },
              ],
            },
          ],
        },
        {
          name: 'Parent Menu 5',
          checked: false,
          collapsed: false,
          children: [
            { id: 21, name: 'Sub Menu', checked: false },
            { id: 22, name: 'Sub Menu', checked: false },
            { id: 23, name: 'Sub Menu', checked: false },
            { id: 24, name: 'Sub Menu', checked: false },
          ],
        },
      ],
    };
  },
  methods: {
    toggleParentMenu(index) {
      let menu = this.sourceMenu[index];
      menu.collapsed = !menu.collapsed;
    },
    toggleSubParentMenu(parentIndex, subParentIndex) {
      debugger;
      let menu = this.sourceMenu[parentIndex].children[subParentIndex];
      menu.collapsed = !menu.collapsed;
      debugger;
    },
  },
};
</script>

<style lang="scss" scoped>
.custom-transfer {
  font-family: 'Roboto', sans-serif;
  display: flex;
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
      .menu {
        color: #003a70;
        font-size: 12px;
        .parent-menu {
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
      }
    }
  }
  &__source {
    margin-right: 10px;
  }
}
</style>
