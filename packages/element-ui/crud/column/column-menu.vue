<template>
  <!-- 操作栏 -->
  <el-table-column prop="menu"
                   :class-name="crud.tableOption.menuClassName"
                   :label-class-name="crud.tableOption.menuLabelClassName"
                   :fixed="validData(crud.tableOption.menuFixed,config.menuFixed)"
                   v-if="validData(crud.tableOption.menu,config.menu)&&crud.getPermission('menu')"
                   :label="crud.tableOption.menuTitle || t('crud.menu')"
                   :align="crud.tableOption.menuAlign || config.menuAlign"
                   :header-align="crud.tableOption.menuHeaderAlign || config.menuHeaderAlign"
                   :width="crud.isMobile?(crud.tableOption.menuXsWidth || config.menuXsWidth):( crud.tableOption.menuWidth || config.menuWidth)">
    <template #header="scope">
      <slot name="menu-header"
            v-bind="scope"
            :size="crud.size"
            v-if="crud.getSlotName({prop:'menu'},'H',crud.$slots)"></slot>
      <span v-else>{{crud.tableOption.menuTitle || t('crud.menu')}}</span>
    </template>
    <template #="{row,$index}">
      <div :class="b('menu')">
        <el-dropdown v-if="isMenu"
                     :size="crud.size">
          <el-button text
                     :size="crud.size">
            {{ crud.tableOption.menuBtnTitle || t('crud.menuBtn')}}
            <el-icon class='el-icon--right"'>
              <el-icon-arrow-down />
            </el-icon>
          </el-button>
          <template #dropdown>
            <el-dropdown-menu>
              <el-dropdown-item :icon="crud.getBtnIcon('viewBtn')"
                                :class="b('viewBtn')"
                                v-if="validData(crud.tableOption.viewBtn,config.viewBtn)"
                                v-permission="crud.getPermission('viewBtn',row,$index)"
                                @click="crud.rowView(row,$index)">{{crud.menuIcon('viewBtn')}}</el-dropdown-item>
              <el-dropdown-item :icon="crud.getBtnIcon('copyBtn')"
                                :class="b('copyBtn')"
                                v-if="validData(crud.tableOption.copyBtn,config.copyBtn)"
                                v-permission="crud.getPermission('copyBtn',row,$index)"
                                @click="crud.rowCopy(row)">{{crud.menuIcon('copyBtn')}}</el-dropdown-item>
              <el-dropdown-item :icon="crud.getBtnIcon('editBtn')"
                                :class="b('editBtn')"
                                v-if="validData(crud.tableOption.editBtn,config.editBtn)"
                                v-permission="crud.getPermission('editBtn',row,$index)"
                                @click="crud.rowEdit(row,$index)">{{crud.menuIcon('editBtn')}}</el-dropdown-item>
              <el-dropdown-item :icon="crud.getBtnIcon('delBtn')"
                                :class="b('delBtn')"
                                v-if="validData(crud.tableOption.delBtn,config.delBtn)"
                                v-permission="crud.getPermission('delBtn',row,$index)"
                                @click="crud.rowDel(row,$index)">{{crud.menuIcon('delBtn')}}</el-dropdown-item>
              <slot name="menuBtn"
                    :row="row"
                    :type="menuText('primary')"
                    :disabled="crud.btnDisabled"
                    :size="crud.size"
                    :index="$index"></slot>
            </el-dropdown-menu>
          </template>

        </el-dropdown>
        <template v-else-if="['button','text','icon'].includes(menuType)">
          <template v-if="validData(crud.tableOption.cellBtn,config.cellBtn)">
            <el-button :type="menuText('primary')"
                       :class="b('editBtn')"
                       :text="isTextMenu"
                       :icon="crud.getBtnIcon('editBtn')"
                       :size="crud.size"
                       :disabled="crud.btnDisabledList[$index]"
                       @click.stop="crud.rowCell(row,$index)"
                       v-if="validData(crud.tableOption.editBtn,config.editBtn)&&!row.$cellEdit"
                       v-permission="crud.getPermission('editBtn',row,$index)">
              <template v-if="!isIconMenu">
                {{crud.menuIcon('editBtn')}}
              </template>
            </el-button>
            <el-button :type="menuText('primary')"
                       :class="b('saveBtn')"
                       :text="isTextMenu"
                       :icon="crud.getBtnIcon('saveBtn')"
                       :size="crud.size"
                       :disabled="crud.btnDisabledList[$index]"
                       @click.stop="crud.rowCell(row,$index)"
                       v-else-if="validData(crud.tableOption.saveBtn,config.saveBtn)&&row.$cellEdit"
                       v-permission="crud.getPermission('saveBtn',row,$index)">
              <template v-if="!isIconMenu">
                {{crud.menuIcon('saveBtn')}}
              </template>
            </el-button>
            <el-button :type="menuText('primary')"
                       :class="b('cancelBtn')"
                       :text="isTextMenu"
                       :icon="crud.getBtnIcon('cancelBtn')"
                       :size="crud.size"
                       :disabled="crud.btnDisabledList[$index]"
                       @click.stop="crud.rowCancel(row,$index)"
                       v-if="row.$cellEdit">
              <template v-if="!isIconMenu">
                {{crud.menuIcon('cancelBtn')}}
              </template>
            </el-button>
          </template>
          <el-button :type="menuText('primary')"
                     :class="b('viewBtn')"
                     :text="isTextMenu"
                     :icon="crud.getBtnIcon('viewBtn')"
                     :size="crud.size"
                     :disabled="crud.btnDisabled"
                     @click.stop="crud.rowView(row,$index)"
                     v-permission="crud.getPermission('viewBtn',row,$index)"
                     v-if="validData(crud.tableOption.viewBtn,config.viewBtn)">
            <template v-if="!isIconMenu">
              {{crud.menuIcon('viewBtn')}}
            </template>
          </el-button>
          <el-button :type="menuText('primary')"
                     :class="b('copyBtn')"
                     :text="isTextMenu"
                     :icon="crud.getBtnIcon('copyBtn')"
                     :size="crud.size"
                     :disabled="crud.btnDisabled"
                     @click.stop="crud.rowCopy(row)"
                     v-permission="crud.getPermission('copyBtn',row,$index)"
                     v-if="validData(crud.tableOption.copyBtn,config.copyBtn)">
            <template v-if="!isIconMenu">
              {{crud.menuIcon('copyBtn')}}
            </template>
          </el-button>
          <el-button :type="menuText('primary')"
                     :class="b('editBtn')"
                     :text="isTextMenu"
                     :icon="crud.getBtnIcon('editBtn')"
                     :size="crud.size"
                     :disabled="crud.btnDisabled"
                     @click.stop="crud.rowEdit(row,$index)"
                     v-permission="crud.getPermission('editBtn',row,$index)"
                     v-if="validData(crud.tableOption.editBtn,config.editBtn)&&!crud.tableOption.cellBtn">
            <template v-if="!isIconMenu">
              {{crud.menuIcon('editBtn')}}
            </template>
          </el-button>
          <el-button :type="menuText('primary')"
                     :class="b('delBtn')"
                     :text="isTextMenu"
                     :icon="crud.getBtnIcon('delBtn')"
                     :size="crud.size"
                     :disabled="crud.btnDisabled"
                     @click.stop="crud.rowDel(row,$index)"
                     v-permission="crud.getPermission('delBtn',row,$index)"
                     v-if="validData(crud.tableOption.delBtn,config.delBtn) && !row.$cellEdit">
            <template v-if="!isIconMenu">
              {{crud.menuIcon('delBtn')}}
            </template>
          </el-button>

        </template>
        <slot name="menu"
              :row="row"
              :type="menuText('primary')"
              :disabled="crud.btnDisabled"
              :size="crud.size"
              :index="$index"></slot>
      </div>
    </template>
  </el-table-column>
</template>

<script>
import create from "core/create";
import locale from "core/locale";
import permission from 'common/directive/permission';
import config from "../config.js";
export default create({
  name: "crud",
  data () {
    return {
      config: config,
    }
  },
  mixins: [locale],
  inject: ["crud"],
  directives: {
    permission
  },
  computed: {
    menuType () {
      return this.crud.tableOption.menuType || this.$AVUE.menuType;
    },
    isIconMenu () {
      return this.menuType === "icon"
    },
    isTextMenu () {
      return this.menuType === "text"
    },
    isMenu () {
      return this.menuType === "menu"
    },
  },
  methods: {
    menuText (value) {
      return value;
    },
  }
})
</script>