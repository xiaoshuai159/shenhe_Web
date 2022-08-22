<template>
  <div class="right_main_under">
    <el-form size="mini" label-width="80px" :inline="true">
      <el-row :gutter="20">
        <el-col :span="16"
          ><div class="grid-content bg-purple">
            <!-- 流程记录页面头部模块——域名 -->
            <el-form-item>
              <el-select
                v-model="form.name"
                placeholder="归属地"
                  clearable
                @clear="fushencity_clearFun(form.name)"
              >
                <el-option
                  v-for="(item, index) in selectDatacity.fushen"
                  :label="item.label"
                  :value="item.value"
                  :key="index"
                >
                </el-option>
              </el-select>
            </el-form-item>
            <!-- 涉诈类型 -->
            <el-form-item>
              <el-select
                v-model="form.chushen"
                placeholder="涉诈类型"
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
            <!-- 归属地 -->
            <!-- <el-form-item>
              <el-select
                v-model="form.fushen"
                placeholder="归属地"
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
            </el-form-item> -->
            <el-form-item>
              <el-select
                v-model="form.http"
                placeholder="协议类型"
                clearable
                @clear="fushen_clearFun(form.http)"
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
            <!-- 处置时间 -->
            <el-form-item>
              <el-date-picker
                v-model="form.dateValue_find"
                type="daterange"
                :change="dataCreate_change(form.dateValue_find)"
                range-separator="至"
                start-placeholder="开始日期"
                end-placeholder="结束日期"
                value-format="yyyy-MM-dd HH:mm:ss"
                :default-time="['00:00:00', '23:59:59']"
              >
              </el-date-picker>
            </el-form-item>
            <el-form-item>
              <el-input v-model="form.url" placeholder="请输入域名"></el-input>
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
            <el-button size="mini" type="success" plain @click="uploadwj"
              >上传</el-button
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
      <el-table-column prop="id" label="序号" type="index">
        <template slot-scope="scope"
          ><span v-text="getIndex(scope.$index)"></span
        ></template>
      </el-table-column>
      <el-table-column prop="createTime" label="处置时间" show-overflow-tooltip>
        <template slot-scope="scope">
          {{ time(scope.row.createTime) }}
        </template>
      </el-table-column>
      <el-table-column prop="urlTitle" label="网址标题" show-overflow-tooltip>
      </el-table-column>
      <el-table-column prop="url" label="URL" show-overflow-tooltip>
      </el-table-column>
      <!-- <el-table-column prop="urlHash" label="URL哈希值" show-overflow-tooltip>
      </el-table-column> -->
      <el-table-column prop="yjcType" label="yjc类别" show-overflow-tooltip>
      </el-table-column>
      <el-table-column prop="urlIp" label="网址对应IP" show-overflow-tooltip>
      </el-table-column>
      <el-table-column
        prop="domesticAndForeign"
        label="境内外"
        show-overflow-tooltip
      >
        <template slot-scope="scope">
          {{ scope.row.domesticAndForeign == -1 ? "境外" : "境内" }}
        </template>
      </el-table-column>
      <el-table-column prop="ranking" label="排名" show-overflow-tooltip>
        <template slot-scope="scope">
          {{ scope.row.rank == -1 ? "无排名" : scope.row.rank }}
        </template>
      </el-table-column>

      <el-table-column prop="screenName" label="截图名称" show-overflow-tooltip>
      </el-table-column>
      <el-table-column
        prop="yjcHttpType"
        label="yjc协议类型"
        show-overflow-tooltip
      >
      </el-table-column>
      <el-table-column prop="featureNum" label="访问量" show-overflow-tooltip>
      </el-table-column>
      <el-table-column prop="belong" label="数据归属地" show-overflow-tooltip>
      </el-table-column>
      <el-table-column prop="fraudType" label="涉诈类型" show-overflow-tooltip>
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

    <!-- 上传 -->
    <el-dialog
      :close-on-click-modal="false"
      title="文件上传"
      :visible.sync="shangchuan"
      width="20%"
      height="40%"
      :before-close="shangchuanclose"
      class="dialogInfo"
    >
      <div style="width: 100%">
        <el-upload
          ref="upload"
          class="upload-demo"
          accept=".xlsx"
          action="/treatment/uploadExcel"
          :before-remove="beforeRemove"
          :on-success="successSendFile"
          :on-exceed="handleExceed"
          multiple
          :limit="3"
        >
          <el-button size="mini" type="primary">点击上传</el-button>
          <div slot="tip" class="el-upload__tip">上传文件</div>
        </el-upload>
      </div>
      <span slot="footer" class="dialog-footer">
        <el-button type="primary" @click="shangchuan = false" size="mini"
          >取 消</el-button
        >
        <!-- <el-button type="primary" @click="wenjianshangchaun" size="mini"
          >确 定</el-button
        > -->
      </span>
    </el-dialog>
  </div>
</template>

<script>
import dayjs from "dayjs";
export default {
  data() {
    return {
      selectDatacity: {
        fushen: [],
      },
      shangchuan: false,
      whiteSearchList: {
        startCreateTime: null,
        endCreateTime: null,
      },
      form: {
        name: "",
        chushen: null,
        fushen: null,
        zhongshen: null,
        dateValue_find: null,
        http: null,
        url: null,
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
          { value: "kf_ds", label: "kf_ds" },
          { value: "kf_wl", label: "kf_wl" },
          { value: "kf_other", label: "kf_other" },
          { value: "gjf_mc", label: "gjf_mc" },
          { value: "gjf_ss", label: "gjf_ss" },
          { value: "gjf_etc", label: "gjf_etc" },
          { value: "gjf_other", label: "gjf_other" },
          { value: "sd", label: "sd" },
          { value: "dk_xyz", label: "dk_xyz" },
          { value: "dk_te", label: "dk_te" },
          { value: "dk_dk", label: "dk_dk" },
          { value: "dk_other", label: "dk_other" },
          { value: "jjgw", label: "jjgw" },
          { value: "szp_lc", label: "szp_lc" },
          {
            value: "szp_dubo",
            label: "szp_dubo",
          },
          { value: "szp_ty", label: "szp_ty" },
          { value: "szp_yx", label: "szp_yx" },
          {
            value: "ds_gw",
            label: "ds_gw",
          },
          { value: "ds_fw", label: "ds_fw" },
          { value: "ds_other", label: "ds_other" },
          { value: "jy_jr", label: "jy_jr" },
          { value: "jy_hl", label: "jy_hl" },
          { value: "jy_jy", label: "jy_jy" },
          { value: "jy_other", label: "jy_other" },
          { value: "zx_xyd", label: "zx_xyd" },
          { value: "zx_bljl", label: "zx_bljl" },
          { value: "zx_other", label: "zx_other" },
          { value: "mc_ld", label: "mc_ld" },
          { value: "mc_sr", label: "mc_sr" },
          { value: "mc_gz", label: "mc_gz" },
          { value: "mc_other", label: "mc_other" },
          { value: "yx_card", label: "yx_card" },
          { value: "yx_zhzb", label: "yx_zhzb" },
          { value: "yx_other", label: "yx_other" },
          { value: "other_zj", label: "other_zj" },
          { value: "other_zp", label: "other_zp" },
          { value: "other_jp", label: "other_jp" },
          { value: "other_tp", label: "other_tp" },
          { value: "app_ff", label: "app_ff" },
          { value: "xzym", label: "xzym" },
        ],
        fushen: [
          {
            value: "1",
            label: "Http+Ip/Url",
          },
          {
            value: "2",
            label: "Https+Url",
          },
          {
            value: "3",
            label: "Https+Ip",
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
    this.suoshudi();
    this.techlist();
  },
  methods: {
    //初始化列表
    async techlist() {
      let list = {
        myPage: {
          pageNum: this.mypageable.pageNum,
          pageSize: this.mypageable.pageSize,
        },
        createTimeStart: this.whiteSearchList.startCreateTime,
        createTimeEnd: this.whiteSearchList.endCreateTime,
        fraudType: this.form.chushen,
        belong: this.form.name,
        yjcHttpType: this.form.http,
        url: this.form.url,
      };
      const { data: res } = await this.$http.post(
        "/treatment/batchFindTreatment",
        list
      );
      if (res.code == 200) {
        // console.log(res.data);
        this.tableData = res.data.list;
        this.total = res.data.total;
      }
    },
    async suoshudi() {
      const { data: res } = await this.$http.get("/check/sourcesGroup");
      if (res.code == 200) {
        // console.log(  res.data);
        // this.selectDatacity.fushen = res.data;
        // this.selectDatacity.fushen=
        let reslist=[]
        for (const key in res.data) {
      reslist.push(res.data[key])
        }
            this.selectDatacity.fushen =reslist.map((item) => {
          return {
            value: item.mapName,
            label: item.mapName,
          };
        });

        // console.log(this.selectData.fushen);
      }
    },

    chaxun() {
      this.techlist();
    },
    chongzhi() {
      this.form.fushen = null;
      this.form.http = null;
      this.form.zhongshen = null;
      this.form.dateValue_find = null;
      this.whiteSearchList.startCreateTime = null;
      this.whiteSearchList.endCreateTime = null;
      this.mypageable.pageNum = 1;
      this.mypageable.pageSize = 10;
      this.form.chushen = null;
      this.form.url = null;
      this.form.name = "";
      this.techlist();
    },
    //精确导出

    async put() {
      this.loadingbuttext = "...加载中";
      this.loadingbut = true;
      let list = {
        myPage: {
          pageNum: this.mypageable.pageNum,
          pageSize: this.mypageable.pageSize,
        },
        createTimeStart: this.whiteSearchList.startCreateTime,
        createTimeEnd: this.whiteSearchList.endCreateTime,
        fraudType: this.form.chushen,
        belong: this.form.fushen,
        yjcHttpType: this.form.http,
      };

      const { data: res } = await this.$http.post(
        "/treatment/batchDownLoad",
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
    uploadwj() {
      // this.listTemplate.moban = null;
      this.file = [];
      this.shangchuan = true;
    },
    // 文件上传
    successSendFile(res) {
      // this.loading=true
      if (res.code == 200) {
        // setTimeout(() => {
        this.$message.success("上传成功");
        this.getTabData();
        this.$refs.upload.clearFiles();
        // }, 1000)
      } else {
        this.$message(res.message);
      }
    },
    //删除
    beforeRemove(file, fileList) {
      return this.$confirm(`确定移除 ${file.name}？`);
    },
    //上传
    handleExceed(files, fileList) {
      this.$message.warning(
        `当前限制选择 3 个文件，本次选择了 ${files.length} 个文件，共选择了 ${
          files.length + fileList.length
        } 个文件`
      );
    },
    //文件上传关闭
    shangchuanclose() {
      this.shangchuan = false;
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
        this.form.http = null;
      }
    },
    zhongshen_clearFun(val) {
      if (val == "") {
        this.form.zhongshen = null;
      }
    },
    fushencity_clearFun(val) {
      if (val == "") {
        this.form.name = null;
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
    dataCreate_change(val) {
      if (val && val != "") {
        this.whiteSearchList.startCreateTime = val[0];
        this.whiteSearchList.endCreateTime = val[1];
      } else {
        this.whiteSearchList.startCreateTime = null;
        this.whiteSearchList.endCreateTime = null;
      }
    },
    time(val) {
      if (val == null) {
        return "无";
      } else {
        return dayjs(val).format("YYYY-MM-DD");
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
.right_main_under {
  margin: 20px 0 0 20px;
  box-sizing: border-box;
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
/deep/ .el-upload--text {
  width: 100% !important;
}
</style>