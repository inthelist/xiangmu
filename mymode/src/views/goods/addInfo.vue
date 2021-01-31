<template>
  <div>
    <el-dialog
      center
      :before-close="cancel"
      :title="addInfo.isAdd ? '添加商品' : '编辑商品'"
      :visible.sync="addInfo.isShow"
      @opened='createEditor'
    >
      <el-form :model="goodsForm" ref="ruleForm">
        <el-form-item
          prop="first_cateid"
          label="一级分类"
          :label-width="formLabelWidth"
        >
          <el-select
            v-model="goodsForm.first_cateid"
            @change="changeCate"
            placeholder="请选择"
          >
            <el-option
              v-for="item in getCateList"
              :key="item.id"
              :label="item.catename"
              :value="item.id"
            >
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item
          prop="second_cateid"
          label="二级分类"
          :label-width="formLabelWidth"
        >
          <el-select v-model="goodsForm.second_cateid" placeholder="请选择">
            <el-option
              v-for="item in secondCate"
              :key="item.id"
              :label="item.catename"
              :value="item.id"
            >
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item
          prop="specsid"
          label="商品规格"
          :label-width="formLabelWidth"
        >
          <el-select
            v-model="goodsForm.specsid"
            @change="changeSpecs"
            placeholder="请选择"
          >
            <el-option
              v-for="item in getSpecsList"
              :key="item.id"
              :label="item.specsname"
              :value="item.id"
            >
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item
          prop="specsattr"
          label="规格属性"
          :label-width="formLabelWidth"
        >
          <el-select multiple v-model="goodsForm.specsattr" placeholder="请选择">
            <el-option
              v-for="item in specsArr"
              :key="item"
              :label="item"
              :value="item"
            >
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item  label="商品描述"
          :label-width="formLabelWidth">
          <div id='editor'></div>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="cancel">取 消</el-button>
        <el-button v-if="addInfo.isAdd" @click="add()" type="primary"
          >确 定</el-button
        >
        <el-button v-else type="primary" @click="update('ruleForm')"
          >编 辑</el-button
        >
      </div>
    </el-dialog>
  </div>
</template>

<script>
//引入wangeditor
import E from 'wangeditor'
import { mapGetters, mapActions } from "vuex";
//引入封装好的接口模块
import { getGoodsAdd, getGoodsEdit, getGoodsInfo } from "../../util/axios";
export default {
  data() {
    return {
      goodsForm: {
        first_cateid: "", //一级分类编号
        second_cateid: "", //二级分类编号
        goodsname: "", //商品名称
        price: "", //商品价格
        market_price: "", //市场价格
        img: "", //商品图片（文件）
        description: "", //商品描述
        specsid: "", //商品规格编号
        specsattr: "", //商品规格属性
        isnew: 1, //是否新品 1是新品 2是否
        ishot: 1, //是否热卖推荐1是热卖 2是否
        status: 1 //1是正常2禁用
      },
      formLabelWidth: "120px",
      secondCate: [], //二级分类数据列表
      specsArr:[],//规格属性数组
      editor:null
    };
  },
  computed: {
    ...mapGetters({
      getCateList: "cate/getCateList", //分类列表
      getSpecsList: "specs/getSpecsList", //分类列表
    })
  },
  mounted() {
    //商品列表一触发就调用列表
    //分类列表行动
    this.getCateListAction();
    //规格列表行动
    this.getSpecsListAction();
  },
  props: ["addInfo"],
  methods: {
    //封装一个创建编辑的事件
    createEditor(){
      //实例化编辑器的构造函数
      this.editor = new E('#editor')
      //初始化创建
      this.editor.create()
      console.log(this.editor,'配置项');
      //清空多次实例化创建 置空
      //this.editor = this.editor.txt.html(this.goodsForm.description)
      // console.log(this.goodsForm.description,'this.goodsForm.descriptionthis.goodsForm.descriptionthis.goodsForm.description');
      //由于this.goodsForm.description下次赋值变成undefined所以直接置空
      this.editor.txt.html('')
    },
    //封装一个一级分类切换事件
    changeCate() {
      console.log(this.goodsForm.first_cateid, "一级分类id");
      //当v-model变化一级分类id和分类列表一致的时候，是否可以找到它所在的那条数据以及索引
      //findIndex
      // let index =  this.getCateList.findIndex((item)=>{
      //     //当条件匹配返回当前条件匹配的索引
      //     console.log(item,'哈哈哈哈哈');
      //      return this.goodsForm.first_cateid == item.id
      //   })
      let index = this.getCateList.findIndex(
        item => this.goodsForm.first_cateid == item.id
      );
      this.secondCate = this.getCateList[index].children;
   
        this.goodsForm.second_cateid=''
      
    },
    //封装一个规格属性列表切换事件
    changeSpecs(){
      console.log(this.goodsForm.specsid,'商品规格编号');
      let index = this.getSpecsList.findIndex(
        item => this.goodsForm.specsid == item.id
      )
      this.specsArr = this.getSpecsList[index].attrs
            console.log(this.specsArr, "规格属性");

      //当用户切换规格的时候，把双向数据绑定规格属性置空
      this.goodsForm.specsattr = ''
      console.log(this.goodsForm.specsattr,'商品规格属性的双向数据绑定');
    },
    ...mapActions({
      getCateListAction: "cate/getCateListAction",
      getSpecsListAction: "specs/getSpecsListAction",
      getGoodsListAction: "goods/getGoodsListAction",
      getCountAction: "goods/getCountAction"
    }),
    //封装一个取消事件
    cancel() {
      this.$emit("cancel", {
        isShow: false,
        isAdd: this.addInfo.isAdd
      });
      //调用重置事件
      this.reset();
    },
    //封装一个重置事件
    reset() {
      this.goodsForm = {
        roleid: "", //角色编号
        username: "", //管理员名称
        password: "", //密码
        status: 1 //1是正常2禁用
      };
        this.editor = this.editor.txt.html('')
    },
    //封装一个添加事件
    add(formName) {
      console.log(this.goodsForm.specsattr,'规格属性的多选项')
      //把富文本编辑器的内容直接赋值给 商品描述
      //this.editor.txt.html()  取出富文本编辑器的内容
      this.goodsForm.description = this.editor.txt.html()
      this.goodsForm.specsattr = this.goodsForm.specsattr ? this.goodsForm.specsattr.join(','):''
      console.log(this.goodsForm, "提交的表单体");
          //  添加方法执行，触发validate验证器
      this.$refs[formName].validate(valid => {
        if (valid) {
          //调取添加接口
          getGoodsAdd(this.goodsForm).then(res => {
            if (res.data.code == 200) {
              this.$message.success(res.data.msg);
              //关闭弹框并重置
              this.cancel();
              //重新获取列表
              this.getGoodsListAction();
              //重新调取下总数（当你添加成功，分页也要跟着变化，只有总数变化之后，分页才能跟着变化）
              this.getCountAction();
            } else {
              this.$message.error(res.data.msg);
            }
          });
        } else {
          console.log("error submit!!");
          return false;
        }
      });
    },
    //封装一个查询一条数据事件
    lookInfo(id) {
      //调取查询接口
     getGoodsInfo({ uid: id }).then(res => {
        console.log(res, "查询商品一条数据结果");
        if (res.data.code === 200) {
          this.goodsForm = res.data.list;
          this.goodsForm.uid = id;
        } else {
          this.$message.error(res.data.msg);
        }
      });
    },
    //封装一个修改事件
    update(formName) {
      //当前密码如果修改就是重新赋值即可，如果为空就是上一次的密码
      //修改方法执行，触发validate验证器
      this.$refs[formName].validate(valid => {
        if (valid) {
          //调取修改接口
          getGoodsEdit(this.goodsForm).then(res => {
            if (res.data.code == 200) {
              this.$message.success(res.data.msg);
              //关闭弹框并重置
              this.cancel();
              //重新获取列表
              this.getGoodsListAction();
              //重新调取下总数（当你添加成功，分页也要跟着变化，只有总数变化之后，分页才能跟着变化）
              this.getCountAction();
            } else {
              this.$message.error(res.data.msg);
            }
          });
        } else {
          console.log("error submit!!");
          return false;
        }
      });
    }
  }
};
</script>

<style lang="" scoped></style>
