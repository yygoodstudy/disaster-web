<template>
  <div class="block">
    <el-pagination
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
      :current-page="currentPage4"
      :page-sizes="[20, 40, 80, 100]"
      :page-size="100"
      layout="total, sizes, prev, pager, next, jumper"
      :total="pageTotal"
    >
    </el-pagination>
  </div>
</template>

<script>
export default {
  methods: {
    handleSizeChange(val) {
      console.log(`每页 ${val} 条`);
    },
    handleCurrentChange(val) {
      console.log(`当前页: ${val}`);
    },
  },
  data() {
    return {
      currentPage4: 1, // 默认显示第一页
    };
  },
};
</script>

<style>
</style>