<template>
  <div class="Informationall">
    <!-- <div class="Informationall-box"> -->
    <!-- <el-tabs @tab-click="handleClick" class="Informationall-box">
      <el-tab-pane label="通知" name="first">
        <div class="Informationall-list">
        </div>
      </el-tab-pane>
    </el-tabs> -->

    <sysadmins />
    <!-- </div> -->
  </div>
</template>

<script>
// import sysadmins from "../../components/sysadmin/sysadmins";
import sysadmins from '../../components/sysadmin/sysadmins.vue';
// import pagination from "../../components/pagination/pagination";
export default {
  data() {
    return {
      multipleSelection: [],
    };
  },
  methods: {
    handleClick() {},

    toggleSelection(rows) {
      if (rows) {
        rows.forEach((row) => {
          this.$refs.multipleTable.toggleRowSelection(row);
        });
      } else {
        this.$refs.multipleTable.clearSelection();
      }
    },
    handleSelectionChange(val) {
      this.multipleSelection = val;
    },
  },
  components: {
    sysadmins
    // pagination,
  },
};
</script>

<style>
.Informationall {
  width: 100%;
  min-height: 100%;
  padding: 25px;
  box-sizing: border-box;
}
.Informationall-box {
  width: 100%;
  min-height: 100%;
  border-radius: 5px;
  padding: 15px;
  box-sizing: border-box;
  background: #ffffff;
}
.Informationall-box .el-tabs__nav-scroll {
  justify-content: flex-start !important;
  padding-left: 15px;
  box-sizing: border-box;
}
.Informationall-list {
  width: 100%;
  height: auto;
  padding: 15px;
  box-sizing: border-box;
}
</style>