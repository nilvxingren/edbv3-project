<!--表格树模板 贾南风-->
<template>
  <el-table :data="formatData" :row-style="showRow" v-bind="$attrs" tooltip-effect="dark">
    <!--<el-table-column v-if="columns.length===0" width="30">
      <template slot-scope="scope">
        {{scope.$index}}
      </template>
    </el-table-column>-->
    <el-table-column v-if="columns.length===0" label="权限树" min-width="160" :show-overflow-tooltip="true">
      <template slot-scope="scope">
        <span v-for="space in scope.row._level" class="ms-tree-space" :key="space"></span>
        <span class="tree-ctrl" v-if="iconShow(0,scope.row)" @click="toggleExpanded(scope.$index)">
          <i v-if="!scope.row._expanded" class="el-icon-caret-right" style="font-size:20px;"></i>
          <i v-else class="el-icon-caret-bottom" style="font-size:20px;"></i>
        </span>
        {{scope.row.privilegeName}}
      </template>
    </el-table-column>
    <el-table-column v-else v-for="(column, index) in columns" :key="column.value" :label="column.text" :width="column.width" min-width="110">
      <template slot-scope="scope">
        <span v-if="index === 0" v-for="space in scope.row._level" class="ms-tree-space" :key="space"></span>
        <span class="tree-ctrl" v-if="iconShow(index,scope.row)" @click="toggleExpanded(scope.$index)">
          <i v-if="!scope.row._expanded" class="el-icon-plus"></i>
          <i v-else class="el-icon-minus"></i>
        </span>
        {{scope.row[column.value]}}
      </template>
    </el-table-column>
    <slot></slot>
  </el-table>
</template>

<script>

import treeToArray from './eval'
export default {
  name: 'treeTable',
  props: {
    a: 1,
    data: {
      type: [Array, Object],
      required: true
    },
    columns: {
      type: Array,
      default: () => []
    },
    evalFunc: Function,
    evalArgs: Array,
    expandAll: {
      type: Boolean,
      default: false
    }
  },
  computed: {
    // 格式化数据源
    formatData: function() {
      let tmp
      if (!Array.isArray(this.data)) {
        tmp = [this.data]
      } else {
        tmp = this.data
      }
      const func = this.evalFunc || treeToArray
      const args = this.evalArgs ? Array.concat([tmp], this.evalArgs) : [tmp, this.expandAll];
      return func.apply(null, args)
    }
  },
  methods: {
    showRow: function(row) {
      const show = (row.row.parent ? (row.row.parent._expanded && row.row.parent._show) : true);
      row.row._show = show;
      return show ? '' : 'display:none;'
    },
    // 切换下级是否展开
    toggleExpanded: function(trIndex) {
      const record = this.formatData[trIndex];
      record._expanded = !record._expanded;
    },
    // 图标显示
    iconShow(index, record) {
      return (index === 0 && record.nodes && record.nodes.length > 0)
    }
  },
  mounted(){
    console.log(this.columns);
  }
}
</script>

<style scoped>
  .ms-tree-space {
    position: relative;
    top: 1px;
    display: inline-block;
    font-style: normal;
    font-weight: 400;
    line-height: 1;
    width: 18px;
    height: 14px;
  }
  .ms-tree-space::before {
      content: ""
    }
  .processContainer{
    width: 100%;
    height: 100%;
  }
  table td {
    line-height: 26px;
  }

  .tree-ctrl{
    position: relative;
    cursor: pointer;
    color: #2196F3;
    margin-left: -18px;
  }
</style>
