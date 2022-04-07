<template>
  <div class="right_main_under">
    <el-form size="mini" label-width="80px" :inline="true">
      <el-row :gutter="20">
        <el-col :span="16"
          ><div class="grid-content bg-purple">
            <!-- 流程记录页面头部模块——域名 -->
            <el-form-item>
              <el-input v-model="form.name" placeholder="请输入域名"></el-input>
            </el-form-item>
            <!-- 流程记录页面头部模块——初审 -->
            <el-form-item>
              <el-select
                v-model="form.chushen"
                placeholder="请选择初审结果"
                clearable
                @clear="chushen_clearFun(form.chushen)"
              >
                <el-option
                  v-for="item in selectData.chushen"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                >
                </el-option>
              </el-select>
            </el-form-item>
            <!-- 流程记录页面头部模块——复审 -->
            <el-form-item>
              <el-select
                v-model="form.fushen"
                placeholder="请选择复审结果"
                clearable
                @clear="fushen_clearFun(form.fushen)"
              >
                <el-option
                  v-for="item in selectData.fushen"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                >
                </el-option>
              </el-select>
            </el-form-item>
            <!-- 流程记录页面头部模块——终审 -->
            <el-form-item>
              <el-select
                v-model="form.zhongshen"
                placeholder="请选择终审结果"
                clearable
                @clear="zhongshen_clearFun(form.zhongshen)"
              >
                <el-option
                  v-for="item in selectData.zhongshen"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                >
                </el-option>
              </el-select>
            </el-form-item></div
        ></el-col>
        <el-col :span="8"
          ><div class="grid-content bg-purple">
            <!-- 流程记录页面头部模块——button -->
            <el-button size="mini" type="primary" plain @click="chaxun"
              >查询</el-button
            >
            <el-button size="mini" type="success" plain @click="chongzhi"
              >重置</el-button
            >
            <!-- 导出 -->
            <el-button
              size="mini"
              type="info"
              plain
              @click="put"
              :loading="loadingbut"
              >{{ loadingbuttext }}</el-button
            >
          </div></el-col
        >
      </el-row>
    </el-form>

    <!-- 流程记录页面模块——列表 -->
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
      <el-table-column prop="url" label="URL" show-overflow-tooltip>
      </el-table-column>
      <el-table-column prop="startCheck1Name" label="初审人"> </el-table-column>
      <el-table-column prop="startCheck1Time" label="初审时间" sortable>
        <template slot-scope="scope">
          {{ time(scope.row.startCheck1Time) }}
        </template>
      </el-table-column>
      <el-table-column prop="startCheck1Result" label="初审结果">
        <template slot-scope="scope">
          {{ status(scope.row.startCheck1Result) }}
        </template>
      </el-table-column>
      <el-table-column prop="againCheck1Name" label="复审人"> </el-table-column>
      <el-table-column prop="againCheck1Time" label="复审时间" sortable>
        <template slot-scope="scope">
          {{ time(scope.row.againCheck1Time) }}
        </template>
      </el-table-column>
      <el-table-column prop="againCheck1Result" label="复审结果">
        <template slot-scope="scope">
          {{ status(scope.row.againCheck1Result) }}
        </template>
      </el-table-column>

      <el-table-column prop="endCheckName" label="终审人"> </el-table-column>
      <el-table-column prop="endCheckTime" label="终审时间" sortable>
        <template slot-scope="scope">
          {{ time(scope.row.endCheckTime) }}
        </template>
      </el-table-column>
      <el-table-column prop="endCheckResult" label="终审结果">
        <template slot-scope="scope">
          {{ status(scope.row.endCheckResult) }}
        </template>
      </el-table-column>
      <!-- <el-table-column prop="url9" label="备注"> </el-table-column> -->
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
  </div>
</template>

<script>
import dayjs from "dayjs";
export default {
  data() {
    return {
      form: {
        name: "",
        chushen: null,
        fushen: null,
        zhongshen: null,
      },
      tableData: [
        // {
        //   url: "www.baid.com",
        //   url1: "发财网",
        //   url2: "诈骗",
        //   url3: "2021-11-11",
        //   url4: "xxx.xx.xx.xx",
        //   url5: "sd",
        //   url6: "境外",
        //   url7: "无",
        //   url8: "200",
        //   url9: "1",
        // },
      ],
      mypageable: {
        pageNum: 1,
        pageSize: 10,
      },
      total: 1,
      totalPages: "",
      selectData: {
        chushen: [
          {
            value: "1",
            label: "是",
          },
          {
            value: "2",
            label: "否",
          },
          {
            value: "3",
            label: "不确定",
          },
        ],
        fushen: [
          {
            value: "1",
            label: "是",
          },
          {
            value: "2",
            label: "否",
          },
          {
            value: "3",
            label: "不确定",
          },
        ],
        zhongshen: [
          {
            value: "1",
            label: "是",
          },
          {
            value: "2",
            label: "否",
          },
          {
            value: "3",
            label: "不确定",
          },
        ],
      },
        loadingbuttext: "导出",
             loadingbut: false,
    };
  },
  created() {
    this.techlist();
  },
  methods: {
    //初始化列表
    async techlist() {
      let list = {
        againCheckResult: this.form.fushen,
        endCheckResult: this.form.zhongshen,
        myPage: {
          pageNum: this.mypageable.pageNum,
          pageSize: this.mypageable.pageSize,
        },
        startCheckResult: this.form.chushen,
        url: this.form.name,
      };
      const { data: res } = await this.$http.post("/process/findAll", list);
      if (res.code == 200) {
        // console.log(res.data);
        this.tableData = res.data.list;
        this.total = res.data.total;
      }
    },
    chaxun(){
      this.techlist()
    },
    chongzhi(){
      this.form.fushen=null;
      this.form.zhongshen=null;
      this.mypageable.pageNum=1;
      this.mypageable.pageSize=10;
      this.form.chushen=null
      this.form.name=""
        this.techlist()
    },
    //精确导出

    async put() {
      this.loadingbuttext = "...加载中";
      this.loadingbut = true;
     let list = {
        againCheckResult: this.form.fushen,
        endCheckResult: this.form.zhongshen,
        myPage: {
          pageNum: this.mypageable.pageNum,
          pageSize: this.mypageable.pageSize,
        },
        startCheckResult: this.form.chushen,
        url: this.form.name,
      };

      const { data: res } = await this.$http.post(
        "/process/exportCheckInfoList",
        list
      );
      if (res.code == 200) {
        this.loadingbuttext = "导出";
        this.loadingbut = false;
        // console.log(res.data);
        let newurl = res.data.url;
        let eleLink = document.createElement("a");
        eleLink.download = name;
        // const down = window.location.origin
        // eleLink.href = "http://172.31.1.61:8080" + newurl;
        // const down = window.location.origin
        eleLink.href = newurl;
        // console.log(eleLink);
        eleLink.click();
        eleLink.remove();
          this.$message(res.message);
      } else {
        this.$message(res.message);
      }
    },

    getIndex($index) {
      //$index为数据下标,对英序号要加一
      // console.log($index)
      return (
        (this.mypageable.pageNum - 1) * this.mypageable.pageSize + $index + 1
      );
    },
    chushen_clearFun(val) {
      if (val == "") {
        this.form.chushen = null;
      }
    },
    fushen_clearFun(val) {
      if (val == "") {
        this.form.fushen = null;
      }
    },
    zhongshen_clearFun(val) {
      if (val == "") {
        this.form.zhongshen = null;
      }
    },
    handleSizeChange(val) {
      this.mypageable.pageSize = val;
      this.techlist()
    },
    handleCurrentChange(val) {
      this.mypageable.pageNum = val;
            this.techlist()
    },
    time(val) {
      if(val==null){
        return '无'
      }else{
 return dayjs(val).format("YYYY-MM-DD  HH:mm:ss");
      }
     
    },
    status(val) {
      if (val == "1") {
        return "是";
      } else if (val == "2") {
        return "否";
      } else if (val == "3") {
        return "不确定";
      }
    },
  },
};
</script>

<style scoped lang='less'>
.right_main_under{
//  margin: 20px 0 0 20px;
//  box-sizing: border-box;
}
.el-row {
  margin-bottom: 20px;
  &:last-child {
    margin-bottom: 0;
  }
}
.el-col {
  border-radius: 4px;
}

.grid-content {
  border-radius: 4px;
  min-height: 36px;
}
/deep/ .el-input--mini .el-input__inner {
  width: 210px;
}
/deep/ .el-form-item:first-child {
  width: 300px;
  .el-form-item__content {
    width: 100%;
    .el-input__inner {
      width: 100%;
    }
  }
}
.bottom {
  width: 100%;
  height: 3.75rem /* 60/16 */ /* 40/16 */;
  .ss {
    float: right;
  }
}
</style>