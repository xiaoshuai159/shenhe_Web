<template>
    <div class="right_main_under">
      <el-form size="mini" label-width="80px" :inline="true" >
        <el-row :gutter="20">
          <el-col :span="18"
            ><div class="grid-content bg-purple">             
                <el-form-item>
                  <el-date-picker
                    v-model="form.datetime"
                    type="daterange"
                    :change="dataCreate_change(form.datetime)"
                    range-separator="至"
                    start-placeholder="开始日期"
                    end-placeholder="结束日期"
                    value-format="yyyy-MM-dd"
                    :clearable="false"
                  >
                  </el-date-picker>
                </el-form-item>
              <!-- 流程记录页面头部模块——域名 -->
              <el-form-item>
                <el-input v-model="form.domain" placeholder="请输入域名"></el-input>
              </el-form-item>
              <el-form-item>
                <el-select
                  v-model="form.chengshi"
                  placeholder="城市"
                  clearable
                  @clear="chengshi_clearFun(form.chengshi)"
                >
                  <el-option
                    v-for="item in selectData.chengshi"
                    :key="item.id"
                    :label="item.name"
                    :value="item.id"
                  >
                  </el-option>
                </el-select>
              </el-form-item>
              <!-- 流程记录页面头部模块——初审 -->
              <el-form-item>
                <el-select
                  v-model="form.shenhe"
                  placeholder="审核状态"
                  clearable
                  @clear="chushen_clearFun(form.shenhe)"
                >
                  <el-option
                    v-for="item in selectData.shenhe"
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
                  v-model="form.chuzhi"
                  placeholder="处置状态"
                  clearable
                  @clear="fushen_clearFun(form.chuzhi)"
                >
                  <el-option
                    v-for="item in selectData.chuzhi"
                    :key="item.value"
                    :label="item.label"
                    :value="item.value"
                  >
                  </el-option>
                </el-select>
              </el-form-item>
              </div
          ></el-col>
          <el-col :span="6"
            ><div class="grid-content bg-purple" style="float:right">
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
        max-height="550px"
        size="mini"
        class="tableStyle"
        empty-text="暂无数据"
        v-loading="loading"
      >
        <el-table-column prop="id" label="序号" type="index" min-width="5%">
          <template slot-scope="scope"
            ><span v-text="getIndex(scope.$index)"></span
          ></template>
        </el-table-column>
        <el-table-column prop="createTime" label="入库时间" show-overflow-tooltip sortable min-width="15%">
          <template slot-scope="scope">
            {{ time(scope.row.createTime) }}
          </template>
        </el-table-column>
        <el-table-column prop="url" label="域名" show-overflow-tooltip min-width="15%">
        </el-table-column>
        <el-table-column prop="source" label="城市" min-width="8%"> </el-table-column>
        
        <el-table-column prop="auditStatusName" label="审核状态" min-width="8%">
          <template slot-scope="scope">
            {{ scope.row.auditStatusName }}
          </template>
        </el-table-column>
        <el-table-column prop="treatStatusName" label="处置状态" min-width="5%">
          <template slot-scope="scope">
            {{ scope.row.treatStatusName }}
          </template>
        </el-table-column>
        <el-table-column prop="reason" label="原因" min-width="15%">
          <template slot-scope="scope">
            {{ scope.row.reason }}
          </template>
        </el-table-column>
        <el-table-column prop="auditResultName" label="审核结果" min-width="15%">
          <template slot-scope="scope">
            {{ scope.row.auditResultName }}
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
            :page-sizes="[10, 20, 30, 40, 50]"
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
        loading: false,
        form: {
          domain: null,
          shenhe: null,
          chuzhi: null,
          chengshi:null,
          datetime:[
            dayjs().subtract(1, 'week').format("YYYY-MM-DD") ,dayjs(new Date()).format("YYYY-MM-DD")
          ],
          username: null,
        },
        whiteSearchList: {
          startCreateTime:dayjs().subtract(1, 'week').format("YYYY-MM-DD") ,
          endCreateTime: dayjs(new Date()).format("YYYY-MM-DD")
        },
        tableData: [
        ],
        mypageable: {
          pageNum: 1,
          pageSize: 50,
        },
        total: 1,
        totalPages: "",
        selectData: {
          type: [],
          chengshi: [
            
          ],
          shenhe: [
            {
              value: '0',
              label: "待审核",
            },
            {
              value: '1',
              label: "审核中",
            },
            {
              value: '2',
              label: "已审核",
            },
          ],
          chuzhi: [
          {
              value: "0",
              label: "否",
            },
            {
              value: "1",
              label: "是",
            },
            
          ],
        },
        loadingbuttext: "导出",
        loadingbut: false,
      };
    },
    created() {
    //   this.form.username=JSON.parse(window.sessionStorage.getItem('one'))
    //   this.techlist();
      this.suoshudi()
    },
    methods: {
      // 城市下拉框数据
      async suoshudi() {
        this.loading = true
        const promise1 =  this.$http.get("/dictionary/datasource");
        // const promise2 =  this.$http.get("dictionary/fraudType",{isBig:true})
        // const [data1,data2] = await Promise.all([promise1,promise2])
        const { data:res } = await promise1
        if(res.code === 200){
          this.selectData.chengshi = res.data
        }
        // if(data1.data.code === 200 && data2.data.code === 200){
        //   this.selectData.fushen = data1.data.data
        //   this.selectData.shezha = data2.data.data
        //   for(let i in data1.data.data){
        //     if(data1.data.data[i].name==="上海"){
        //       this.newdomainSimpleVo.guishud = data1.data.data[i]
        //       break
        //     }else{
        //       this.newdomainSimpleVo.guishud = null
        //     }
        //   }
          
        // }
        this.techlist()
      },
      //初始化列表
      async techlist() {
        this.loading = true
        let list = {
          page: this.mypageable.pageNum,
          pageSize: this.mypageable.pageSize,
          url: this.form.domain,
          start: this.whiteSearchList.startCreateTime,
          end: this.whiteSearchList.endCreateTime,
          source: this.form.chengshi,
          auditStatus: this.form.shenhe,
          treatStatus: this.form.chuzhi,
        };
        const { data: res } = await this.$http.get("/originDomain/query", {params:list});
        if (res.code == 200) {
          // console.log(res.data);
          this.tableData = res.dataList;
          this.total = res.totalSum;
          this.loading = false
        }else{
          this.$message(res.message)
          this.loading = false
        }
      },
      chaxun() {
        this.mypageable.pageNum = 1;
        this.techlist();
      },
      chongzhi() {
        this.form.chuzhi = null;
        this.mypageable.pageNum = 1;
        this.mypageable.pageSize = 50;
        this.form.shenhe = null;
        this.form.username = null;
        this.form.chengshi = null
        this.form.domain = null
        this.whiteSearchList.startCreateTime = dayjs().subtract(1, 'week').format("YYYY-MM-DD");
        this.whiteSearchList.endCreateTime = dayjs(new Date()).format("YYYY-MM-DD");
        this.form.datetime=[
            dayjs().subtract(1, 'week').format("YYYY-MM-DD") ,dayjs(new Date()).format("YYYY-MM-DD")
          ],
           this.form.username=JSON.parse(window.sessionStorage.getItem('one'))
        this.techlist();
      },
      //精确导出
  
      async put() {
        this.loadingbuttext = "...加载中";
        this.loadingbut = true;
        let list = {
          url: this.form.domain,
          start: this.whiteSearchList.startCreateTime,
          end: this.whiteSearchList.endCreateTime,
          source: this.form.chengshi,
          auditStatus: this.form.shenhe,
          treatStatus: this.form.chuzhi,
        };
        if(this.tableData.length==0){
          this.$message("当前数据为空，无法导出！")
          this.loadingbuttext = "导出";
          this.loadingbut = false;
          return false
        }
        const res = await this.$http({
        methods:'get',
        url:"/originDomain/export",
        responseType: "blob",
        params:list
      })
      if (res) {
        this.loadingbuttext = "导出";
        this.loadingbut = false;
        // console.log(res.data);
           let binaryData = [];
        binaryData.push(res.data);
        var _url=window.URL.createObjectURL(new Blob(binaryData, {type: "application/vnd.ms-excel"}))
        const a = document.createElement("a");
        // a.download = name;
        a.href = _url
        a.setAttribute('download', decodeURI(res.headers['content-disposition'].split('=')[1]));
        document.body.appendChild(a);
        a.click()
        a.remove()
        this.$message('导出成功');
      } else {
        this.$message('导出失败');
      }
      },
  
      getIndex($index) {
        //$index为数据下标,对英序号要加一
        // console.log($index)
        return (
          (this.mypageable.pageNum - 1) * this.mypageable.pageSize + $index + 1
        );
      },
      type_clearFun(val) {
        if (val == "") {
          this.form.username = null;
        }
      },
      chushen_clearFun(val) {
        if (val == "") {
          this.form.shenhe = null;
        }
      },
      fushen_clearFun(val) {
        if (val == "") {
          this.form.chuzhi = null;
        }
      },
      handleSizeChange(val) {
        this.mypageable.pageSize = val;
        this.techlist();
      },
      handleCurrentChange(val) {
        this.mypageable.pageNum = val;
        this.techlist();
      },
      time(val) {
        if (val == null) {
          return "无";
        } else {
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
      dataCreate_change(val) {
        if (val && val != "") {
          this.whiteSearchList.startCreateTime = val[0];
          this.whiteSearchList.endCreateTime = val[1];
        } else {
          this.whiteSearchList.startCreateTime = null;
          this.whiteSearchList.endCreateTime = null;
        }
      },
    },
  };
  </script>
  
  <style scoped lang='less'>
  .right_main_under {
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
    // width: 300px;
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