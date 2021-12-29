<template>
  <div class="right_main_under">
    <!-- 用户管理页面模块——列表 -->
    <el-row>
      <el-col :span="24"
        ><div class="grid-content bg-purple">
          <el-button type="primary" size="mini" plain class="left" @click="add"
            >添加</el-button
          >
        </div></el-col
      >
    </el-row>
    <el-row style="margin-top: 20px">
      <el-col :span="24">
        <el-table
          border
          ref="multipleTable"
          :data="tableData"
          style="width: 100%"
          max-height="650px"
          size="mini"
          class="tableStyle"
          empty-text="暂无数据"
        >
          <el-table-column prop="ID" label="序号" type="index">
            <template slot-scope="scope"
              ><span v-text="getIndex(scope.$index)"></span
            ></template>
          </el-table-column>
          <el-table-column prop="username" label="用户名"> </el-table-column>
          <el-table-column prop="password" label="密码"> </el-table-column>
          <el-table-column prop="roleName" label="角色名称"></el-table-column>
          <el-table-column prop="roleSign" label="角色标志"> </el-table-column>
          <el-table-column prop="updateTime" label="更新时间">
            <template slot-scope="scope">
              {{ time(scope.row.updateTime) }}
            </template>
          </el-table-column>
          <el-table-column prop="status" label="状态">
            <template slot-scope="scope">
              {{ scope.row.status == 1 ? "激活" : "未激活" }}
            </template>
          </el-table-column>
  <el-table-column prop="id" label="id" v-if="loadinglist">
            <template slot-scope="scope">
              {{ scope.row.id}}
            </template>
          </el-table-column>
          <el-table-column label="操作">
            <!-- <el-button size="mini" type="text">增</el-button> -->

            <template slot-scope="scope">
              <el-button size="mini" type="text" @click="xiugai(scope.row)"
                >修改</el-button
              >
              <el-button size="mini" type="text" @click="del(scope.row.id)">删除</el-button>
            </template>
          </el-table-column>
        </el-table>
        <div class="bottom">
          <div class="ss">
            <el-pagination
              @size-change="handleSizeChange"
              @current-change="handleCurrentChange"
              :current-page="mypageable.pageNum"
              :page-sizes="[10, 20, 30, 40]"
              :page-size="mypageable.pageSize"
              layout="total, sizes, prev, pager, next, jumper"
              :total="total"
              class="pagePagination pageRight"
            >
            </el-pagination>
          </div>
        </div>
      </el-col>
    </el-row>
    <!-- 新增 -->
    <el-dialog
      title="新增"
      :visible.sync="dialogVisible"
      width="35%"
      :before-close="handleClose"
      class="dialogInfo"
      :close-on-click-modal="true"
    >
      <el-form
        ref="newdomainSimpleVo"
        label-width="80px"
        :inline="true"
        :model="newdomainSimpleVo"
        size="mini"
      >
        <!-- 用户名 -->
        <el-form-item label="用户名：" prop="user">
          <el-input
            v-model="newdomainSimpleVo.user"
            placeholder="请输入用户名"
          ></el-input>
        </el-form-item>
        <!-- 密码 -->
        <el-form-item label="密码：" prop="pwd">
          <el-input
            v-model="newdomainSimpleVo.pwd"
            placeholder="请输入密码"
          ></el-input>
        </el-form-item>
        <el-form-item label="角色:">
             <el-select
            v-model="newdomainSimpleVo.role"
            placeholder="请选择角色"
          >
            <el-option
              v-for="(value, i) in newdomainSimpleVo.roleshuzu"
              :key="i"
              :value="i"
              :label="value"
              >{{ value }}</el-option
            >
          </el-select>
          <!-- <el-select v-model="newdomainSimpleVo.role" placeholder="请选择角色"> -->
            <!-- <el-option label="初审" value="2"></el-option>
            <el-option label="复审" value="3"></el-option>
            <el-option label="终审" value="4"></el-option>
            <el-option label="管理员" value="1"></el-option> -->
          <!-- </el-select> -->
        </el-form-item>
        <el-form-item label="数据标志:">
          <el-select
            v-model="newdomainSimpleVo.sjbzhi"
            placeholder="请选择数据标志"
          >
            <el-option
              v-for="(value, i) in newdomainSimpleVo.shujubiaoz"
              :key="i"
              :value="value"
              >{{ value }}</el-option
            >
          </el-select>
        </el-form-item>
        <!-- <el-form-item label="状态:">
          <el-select v-model="newdomainSimpleVo.radio" placeholder="请选择状态">
            <el-option label="激活" value="0"></el-option>
            <el-option label="未激活" value="1"></el-option>
          </el-select>
        </el-form-item> -->
        <br />
        <el-form-item label="状态">
          <el-radio v-model="newdomainSimpleVo.radio" label="1">激活</el-radio>
          <el-radio v-model="newdomainSimpleVo.radio" label="0"
            >未激活</el-radio
          >
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button type="primary" size="mini" @click="queding" plain
          >确 定</el-button
        >
        <el-button @click="quxiao" size="mini" plain>取 消</el-button>
      </span>
    </el-dialog>
    <!-- 修改 -->
    <el-dialog
      title="修改"
      :visible.sync="dialogVisible1"
      width="35%"
      :before-close="handleClose1"
      class="dialogInfo"
      :close-on-click-modal="true"
    >
      <el-form
        ref="newdomainSimpleVo"
        label-width="80px"
        :inline="true"
        :model="newdomainSimpleVoxiugai"
        size="mini"
      >
        <!-- 用户名 -->
        <el-form-item label="用户名：" prop="user">
          <el-input
            v-model="newdomainSimpleVoxiugai.userxiugai"
            placeholder="请输入用户名"
          ></el-input>
        </el-form-item>
        <!-- 密码 -->
        <el-form-item label="密码：" prop="pwd">
          <el-input
            v-model="newdomainSimpleVoxiugai.pwdxiugai"
            placeholder="请输入密码"
          ></el-input>
        </el-form-item>
        <el-form-item label="角色:">
          <el-select
            v-model="newdomainSimpleVoxiugai.rolexiugai"
            placeholder="请选择角色"
          >
        
            <el-option
              v-for="(value, i) in newdomainSimpleVoxiugai.roleshuzuxiugai"
              :key="i"
              :value="i"
              :label="value"
              >{{ value }}</el-option
            >
          </el-select>
       
        </el-form-item>
        <el-form-item label="数据标志:">
          <el-select
            v-model="newdomainSimpleVoxiugai.sjbzhixiugai"
            placeholder="请选择数据标志"
          >
            <el-option
              v-for="(value, i) in newdomainSimpleVoxiugai.shujubiaozxiugai"
              :key="i"
              :value="value"
              >{{ value }}</el-option
            >
          </el-select>
        </el-form-item>
        <br />
        <el-form-item label="状态:">
          <el-radio v-model="newdomainSimpleVoxiugai.radioxiugai" label="1"
            >激活</el-radio
          >
          <el-radio v-model="newdomainSimpleVoxiugai.radioxiugai" label="0"
            >未激活</el-radio
          >
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button type="primary" size="mini" @click="quedingxiugai" plain
          >确 定</el-button
        >
        <el-button @click="quxiaoxiugai" size="mini" plain>取 消</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
import dayjs from "dayjs";
export default {
  data() {
    return {
      // loading:false,
      sj: [1, 2, 3],
      value: [],
      loadinglist: false,
      shuju: [],
      tableData: [],
      newdomainSimpleVo: {
        user: "",
        pwd: "",
        shujubiaoz: [],
        radio: "1",
        role: "",
             roleshuzu:[],
        sjbzhi: "",
      },
      newdomainSimpleVoxiugai: {
        userxiugai: "",
        pwdxiugai: "",
        shujubiaozxiugai: [],
        radioxiugai: "1",
        rolexiugai: "",
        sjbzhixiugai: "",
       roleshuzuxiugai:[],
       idxiugai:'',
      },
      mypageable: {
        pageNum: 1,
        pageSize: 10,
      },
      total: 1,
      totalPages: "",
      dialogVisible: false,
      dialogVisible1: false,
    };
  },
  created() {
    this.newlist();
  },
  methods: {
    // 初始化列表
    async newlist() {
      let list = {
        myPage: {
          pageNum: this.mypageable.pageNum,
          pageSize: this.mypageable.pageSize,
        },
      };
      const { data: res } = await this.$http.post("/user/findAll", list);
      if (res.code == 200) {
        this.tableData = res.data.list;
        this.total = res.data.total;
        // console.log(res.data);
      }
    },
    // ——————————————————新增
    add() {
      this.sjbs();
      this.rolenew()
        this.dialogVisible = true;
    },
    // 获取数据标识
    async sjbs() {
      const { data: res } = await this.$http.get("/check/remarkGroup");
      if (res.code == 200) {
        this.newdomainSimpleVo.shujubiaoz = res.data;
        this.newdomainSimpleVoxiugai.shujubiaozxiugai=res.data
      
      }
    },
     // 获取角色
     async rolenew(){
       const {data:res}=await this.$http.get('role/findRoleName')
       if(res.code==200){
        //  console.log(res.data);
        this.newdomainSimpleVo.roleshuzu=res.data
            this.newdomainSimpleVoxiugai.roleshuzuxiugai=res.data
       }
     },
    handleClose() {
      this.dialogVisible = false;
    },
    async queding() {
      let list = {
        userEntity: {
          username: this.newdomainSimpleVo.user,
          password: this.newdomainSimpleVo.pwd,
          status: this.newdomainSimpleVo.radio,
          remark: this.newdomainSimpleVo.sjbzhi,
        },
        roleId: this.newdomainSimpleVo.role,
      };
      const { data: res } = await this.$http.post("/user/addUser", list);
      if (res.code == 200) {
        // console.log(res.data);
        this.$message(res.message);
        this.newlist();
        this.chongzhizinzeng();
        this.dialogVisible = false;
      } else {
        this.$message(res.message);
        this.chongzhizinzeng();
        this.dialogVisible = false;
      }
      // this.dialogVisible = false;
    },
    chongzhizinzeng() {
      this.newdomainSimpleVo.user = "";
      this.newdomainSimpleVo.pwd = "";
      this.newdomainSimpleVo.radio = 1;
      this.newdomainSimpleVo.sjbzhi = "";
      this.newdomainSimpleVo.role = "";
    },
    quxiao() {
      this.dialogVisible = false;
    },
    // ——————————————————新增
    // ——————————————————修改
    xiugai(val) {
      console.log(val);
      this.sjbs();
      this.rolenew();
      this.newdomainSimpleVoxiugai.idxiugai=val.id
      this.newdomainSimpleVoxiugai.userxiugai = val.username;
      this.newdomainSimpleVoxiugai.pwdxiugai = val.password;
      this.newdomainSimpleVoxiugai.rolexiugai = val.roleId.toString();
      this.newdomainSimpleVoxiugai.radioxiugai = val.status.toString();
      this.newdomainSimpleVoxiugai.sjbzhixiugai=val.remark
      this.dialogVisible1 = true;
    },
    handleClose1() {
      this.dialogVisible1 = false;
    },
    quxiaoxiugai() {
      this.dialogVisible1 = false;
    },
    async quedingxiugai() {
      let list = {
        userEntity: {
          username: this.newdomainSimpleVoxiugai.userxiugai,
          password: this.newdomainSimpleVoxiugai.pwdxiugai,
          status: this.newdomainSimpleVoxiugai.radioxiugai,
          remark: this.newdomainSimpleVoxiugai.sjbzhixiugai,
          id:this.newdomainSimpleVoxiugai.idxiugai,

        },
        roleId: this.newdomainSimpleVoxiugai.rolexiugai,
      };
      const { data: res } = await this.$http.post("/user/updateUser", list);
      if (res.code == 200) {
        // console.log(res.data);
        this.$message("修改成功")
        this.newlist();
        this.dialogVisible1 = false;
      }
    },
    // __________________修改
// ________________删除
async del(val){
const {data:res}=await this.$http.get('user/delUser',{
  params:{
    id:val
  }
})
if(res.code==200){
  // console.log(res.data);
  this.$message(res.message);
  this.newlist()
}
},
// ——————————————————————删除
    getIndex($index) {
      //$index为数据下标,对英序号要加一
      // console.log($index)
      return (
        (this.mypageable.pageNum - 1) * this.mypageable.pageSize + $index + 1
      );
    },
    getselect(val) {
      console.log(val);
      this.shuju = val;
    },
    handleSizeChange(val) {
      this.mypageable.pageSize = val;
      this.newlist();
    },
    handleCurrentChange(val) {
      this.mypageable.pageNum = val;
      this.newlist();
    },
    time(val) {
      return dayjs(val).format("YYYY-MM-DD  HH:mm:ss");
    },
  },
};
</script>

<style scoped lang='less'>
.right_main_under{
 margin: 20px 0 0 20px;
 box-sizing: border-box;
}
.bottom {
  width: 100%;
  height: 3.75rem /* 60/16 */ /* 40/16 */;
  .ss {
    float: right;
  }
}
</style>