<template>
  <div>
    <el-container>
      <el-header>
        XXX大型后台管理系统
      </el-header>
      <el-container>
        <el-aside>
          <vanv></vanv>
        </el-aside>
        <el-main>
          <router-view></router-view>
        </el-main>
      </el-container>
    </el-container>
  </div>
</template>

<script>
import vanv from '../components/vNav'
export default {
 data() {
 return {

 };
 },
 components:{
  vanv
 }
};
</script>

<style lang="stylus" scoped>
@import '../stylus/index.styl'
.el-header
    background-color $secondBgColor
.el-aside
    width 220px !important
</style>
