<template>
  <div class="right_main_under">
    <el-form size="mini" label-width="80px" class="form">
      <el-form-item label="时间：" v-if="loadingxuanze">
        <!-- //复审模块--时间 -->
        <el-date-picker
          v-model="newdomainSimpleVo.dateValue_find"
          type="daterange"
          :change="dataCreate_change(newdomainSimpleVo.dateValue_find)"
          range-separator="至"
          start-placeholder="开始日期"
          end-placeholder="结束日期"
          value-format="yyyy-MM-dd HH:mm:ss"
          :default-time="['00:00:00', '23:59:59']"
        >
        </el-date-picker>
        <div class="btn">
          <el-button size="mini" type="primary" plain>查询</el-button>
          <el-button size="mini" type="success" plain>重置</el-button>
        </div>
      </el-form-item>
      <div class="return" v-if="listloadingurl">
        <el-button
          class="btn1"
          size="mini"
          type="success"
          plain
          @click="fanhui"
        >
          <i class="el-icon-back"></i> 返回上一层
        </el-button>
      </div>
    </el-form>
    <!-- //复审模块——日期列表 -->
    <div class="bottomlist">
      <div class="datalist">
        <div v-if="listloading" class="leftlist">
          <el-table
            border
            ref="multipleTable"
            :data="tableData"
            style="width: 100%"
            max-height="650px"
            size="mini"
            class="tableStyle1"
            empty-text="暂无数据"
          >
            <el-table-column prop="ID" label="序号" type="index" min-width="5%">
              <template slot-scope="scope"
                ><span v-text="getIndex(scope.$index)"></span
              ></template>
            </el-table-column>
            <el-table-column prop="date" label="日期" min-width="75%">
            </el-table-column>
            <el-table-column label="操作" min-width="20%">
              <template slot-scope="scope">
                <el-button
                  size="mini"
                  type="text"
                  @click="search(scope.row.date)"
                  >详情</el-button
                >
              </template>
            </el-table-column>
          </el-table>
          <div class="bottom">
            <el-pagination
              @current-change="handleCurrentChange"
              :current-page="mypageable.pageNum"
              :page-size="mypageable.pageSize"
              layout="total, prev, pager, next, jumper"
              :total="total"
              class="pagePagination pageRight"
              small
            >
            </el-pagination>
          </div>
        </div>
        <!-- 复审模块——详情url列表 -->
        <el-tabs
          v-model="activeName"
          @tab-click="handleClick"
          v-if="listloadingurl"
        >
          <el-tab-pane label="待审核" name="0">
            <!-- 初申模块——详情url列表 -->
            <div class="leftlist">
              <el-table
                border
                ref="multipleTable"
                :data="tableData1"
                style="width: 100%"
                max-height="650px"
                size="mini"
                class="tableStyle1"
                empty-text="暂无数据"
              >
                <el-table-column
                  prop="ID"
                  label="序号"
                  type="index"
                  min-width="5%"
                >
                  <template slot-scope="scope"
                    ><span v-text="getIndex1(scope.$index)"></span
                  ></template>
                </el-table-column>
                <el-table-column prop="url" label="URL" min-width="75%">
                  <template slot-scope="scope">
                    <a :href="scope.row.url" target="_blank" class="urlcolor">{{
                      scope.row.url
                    }}</a>
                  </template>
                </el-table-column>
                <el-table-column label="操作" min-width="20%">
                  <template slot-scope="scope">
                    <el-button
                      size="mini"
                      type="text"
                      @click="chakan(scope.row)"
                      >查看</el-button
                    >
                  </template>
                </el-table-column>
              </el-table>
              <div class="bottom">
                <el-pagination
                  @current-change="handleCurrentChangeurl"
                  :current-page="mypageableurl.pageNumurl"
                  :page-size="mypageableurl.pageSizeurl"
                  layout="total, prev, pager, next, jumper"
                  :total="totalurl"
                  class="pagePagination pageRight"
                  small
                >
                </el-pagination>
              </div></div
          ></el-tab-pane>
          <el-tab-pane label="已审核" name="1">
            <!-- 初申模块——详情url列表 -->
            <div class="leftlist">
              <el-table
                border
                ref="multipleTable"
                :data="tableData1wei"
                style="width: 100%"
                max-height="650px"
                size="mini"
                class="tableStyle1"
                empty-text="暂无数据"
              >
                <el-table-column
                  prop="ID"
                  label="序号"
                  type="index"
                  min-width="5%"
                >
                  <template slot-scope="scope"
                    ><span v-text="getIndex2(scope.$index)"></span
                  ></template>
                </el-table-column>
                <el-table-column prop="url" label="URL" min-width="75%">
                  <template slot-scope="scope">
                    <a :href="scope.row.url" target="_blank" class="urlcolor">{{
                      scope.row.url
                    }}</a>
                  </template>
                </el-table-column>
                <el-table-column label="操作" min-width="20%">
                  <template slot-scope="scope">
                    <el-button
                      size="mini"
                      type="text"
                      @click="chakan1(scope.row)"
                      >查看</el-button
                    >
                  </template>
                </el-table-column>
              </el-table>
              <div class="bottom">
                <el-pagination
                  @current-change="handleCurrentChangeurl1"
                  :current-page="mypageableurl1.pageNumurl1"
                  :page-size="mypageableurl1.pageSizeurl1"
                  layout="total, prev, pager, next, jumper"
                  :total="totalurl1"
                  class="pagePagination pageRight"
                  small
                >
                </el-pagination>
              </div></div
          ></el-tab-pane>
        </el-tabs>
        <!-- //审核状态 -->
        <div class="leftbtoom" v-if="listloadingright">
          <h4>初审结果</h4>
          <table border="1" width="90%">
            <tr>
              <th>初审时间</th>
              <th>初审结果</th>
            </tr>
            <tr>
              <td>{{ time(this.form.chutime) }}</td>
              <td>{{ status(this.form.chuerr) }}</td>
            </tr>
          </table>
        </div>
      </div>
      <!--复审模块- 右url详情 -->
      <div class="listurl" v-if="listloadingright">
        <el-form size="mini" label-width="80px">
          <el-row>
            <el-col :span="24"
              ><div class="grid-content bg-purple-dark">
                <el-form-item label="域名：" class="inner">
                  <el-input v-model="form.yuming" disabled></el-input>
                </el-form-item></div
            ></el-col>
          </el-row>
          <el-row>
            <el-col :span="24"
              ><div class="grid-content bg-purple-dark">
                <el-form-item label="IP：" class="inner">
                  <el-tooltip
                    class="item"
                    effect="dark"
                    :content="form.ip"
                    placement="bottom"
                  >
                    <el-input v-model="form.ip" disabled></el-input>
                  </el-tooltip>
                </el-form-item></div
            ></el-col>
          </el-row>
          <el-row type="flex" class="row-bg" justify="space-between">
            <el-col :span="6"
              ><div class="grid-content bg-purple">
                <el-form-item label="来源：">
                  <el-input v-model="form.laiyuan" disabled></el-input>
                </el-form-item></div
            ></el-col>
            <el-col :span="6"
              ><div class="grid-content bg-purple-light">
                <el-form-item label="标题：">
                   <el-tooltip
                    class="item"
                    effect="dark"
                    :content="form.title"
                    placement="bottom"
                  >
                  <el-input v-model="form.title" disabled></el-input>
                    </el-tooltip>
                </el-form-item></div
            ></el-col>
            <el-col :span="6"
              ><div class="grid-content bg-purple">
                <el-form-item label="原因：">
                  <el-input v-model="form.question" disabled></el-input>
                </el-form-item></div
            ></el-col>
          </el-row>
          <el-row type="flex" class="row-bg" justify="space-between">
            <el-col :span="6"
              ><div class="grid-content bg-purple">
                <el-form-item label="特征：">
                  <el-input v-model="form.tezheng" disabled></el-input>
                </el-form-item></div
            ></el-col>
            <el-col :span="6"
              ><div class="grid-content bg-purple-light">
                <el-form-item label="类型：">
                  <el-input v-model="form.type" disabled></el-input>
                </el-form-item></div
            ></el-col>
            <el-col :span="6"
              ><div class="grid-content bg-purple">
                <el-form-item label="访问量：">
                  <el-input v-model="form.fangwenliang" disabled></el-input>
                </el-form-item></div
            ></el-col>
          </el-row>
          <el-row type="flex" class="row-bg" justify="space-between">
            <el-col :span="6"
              ><div class="grid-content bg-purple-light">
                <el-form-item label="排名：">
                  <el-input v-model="form.paiming" disabled></el-input>
                </el-form-item></div
            ></el-col>
            <el-col :span="6"
              ><div class="grid-content bg-purple">
                <el-form-item label="备案：">
                  <el-tooltip
                    class="item"
                    effect="dark"
                    :content="form.jingneiwai"
                    placement="bottom"
                  >
                    <el-input v-model="form.jingneiwai" disabled></el-input>
                  </el-tooltip>
                </el-form-item></div
            ></el-col>
            <el-col :span="6"
              ><div class="grid-content bg-purple"></div
            ></el-col>
          </el-row>
        </el-form>
        <!--复审模块- 右url图片 -->
        <div class="images">
          <el-image
            :src="this.form.img"
            :preview-src-list="this.form.srcList"
            class="img"
            title="pc"
          >
            <div slot="error" class="image-slot">
              <i class="el-icon-picture-outline" style="margin: 40%">无图片</i>
            </div>
          </el-image>
          <el-image
            :src="this.form.img1"
            :preview-src-list="this.form.srcList1"
            class="img1"
            title="移动端"
          >
            <div slot="error" class="image-slot">
              <i class="el-icon-picture-outline" style="margin: 40%">无图片</i>
            </div>
          </el-image>
          <!-- <img :src="this.form.img" alt="" class="img" title="pc" />
          <img :src="this.form.img1" alt="" class="img1" title="移动端" /> -->
        </div>
        <!--复审模块- 右url——radio -->
        <div class="botcheck">
          <el-radio-group v-model="radio">
            <el-radio :label="1">是</el-radio>
            <el-radio :label="2">否</el-radio>
            <el-radio :label="3">不确定</el-radio>
            <el-button size="mini" @click="tijiao" v-show="radioloading"
              >提交</el-button
            >
          </el-radio-group>
        </div>
        <!--复审模块- 右url——上一页下一页 -->
        <div class="leftshang">
          <i class="el-icon-arrow-left" @click="shangyiye" v-if="shangxian"></i>
        </div>
        <div class="leftxia">
          <i class="el-icon-arrow-right" @click="xiayiye" v-if="xiaxian"></i>
        </div>
      </div>
      <span
        class="guishudi"
        v-if="guishudiLoading"
        :title="this.gushudi"
        @click="qiehuanguishudi"
      >
        归属地:{{ this.gushudi }}
      </span>
      <span class="wenzi" v-if="wenzi123">
        当前日期 <span class="te">{{ this.mapName }}</span >待处置量为：<span class="te">{{ this.nums }}</span>条
      </span>
    </div>

    <el-dialog
      title="选择归属地"
      :visible.sync="dialogVisible"
      width="30%"
      :before-close="handleClose"
        :showClose="false"
    >
      <el-radio-group v-model="radio123">
        <el-radio
          v-for="(item, index) in this.shushoudione"
          :label="item.sources"
          :value="item.mapName"
          :key="index"
          >{{ item.mapName }}</el-radio
        >
      </el-radio-group>
      <span slot="footer" class="dialog-footer">
        <el-button type="primary" plain @click="errer1">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
import dayjs from "dayjs";
import Numlist from "@/utils/page.js";
import getIndexres from "@/utils/Indexxia.js";
export default {
  name: "chushen",
  data() {
    return {
      gushudi: "",
      guishudiLoading: false,
      nums: 0,
      mapName: "",
      wenzi123: false,
      radioloading: false,
      loadingxuanze: false,
      activeName: "0",
      newdomainSimpleVo: {
        dateValue_find: null, //时间
      },
      whiteSearchList: {
        startCreateTime: "",
        endCreateTime: "",
      },
      mypageable: {
        pageNum: 1,
        pageSize: 10,
      },
      total: 1,
      totalPages: "",
      mypageableurl: {
        pageNumurl: 1,
        pageSizeurl: 10,
      },
      totalurl: 1,
      totalPagesurl: "",
      mypageableurl1: {
        pageNumurl1: 1,
        pageSizeurl1: 10,
      },
      totalurl1: 1,
      totalPagesurl1: "",
      tableData: [
    
      ],
      tableData1: [
      
      ],
      tableData1wei: [
     
      ],
      form: {
        yuming: "",
        laiyuan: "",
        title: "",
        question: "",
        tezheng: "",
        type: "",
        fangwenliang: "",
        ip: "",
        paiming: "",
        jingneiwai: "",
        img: "",
        img1: "",
        srcList: [],
        srcList1: [],
        // shenhezhuangtai: "",
        chuname: "",
        chutime: "",
        chuerr: "",
      },
      radio: "",
      listloading: true,
      listloadingurl: false,
      listloadingright: false,
      searchurl: "",
      sId: "",
      sId1: "",
      num: [],
      newnum: [],
      num1: [],
      newnum1: [],
      shangxian: true,
      xiaxian: true,
      newres: "",
      newres1: "",
      yinewres: "",
      yinewres1: "",
      shushoudione: [],
      suoshudiname: "",
      processid: "",
      radio123: "",
      dialogVisible: true,
    };
  },
  created() {
    this.listtime();
    this.suoshudi();
  },
  methods: {
       qiehuanguishudi(){
      this.dialogVisible=true
      this.listloading = true;
      this.listloadingurl = false;
      this.wenzi123=false;
      this.listloadingright = false;
    },
    errer1() {
           if(this.radio123==""){
        this.$message('请选择归属地')
      }else{
          this.mypageableurl.pageNumurl =1;
              this.mypageableurl1.pageNumurl1 =1;
      this.dialogVisible = false;
      this.gsd();
      this.guishudiLoading = true;
      }
    },
    gsd() {
      // console.log( this.shushoudione);
      for (const key in this.shushoudione) {
        //  console.log(key,this.shushoudione[key]);
        if (this.radio123 == this.shushoudione[key].sources) {
          this.gushudi = this.shushoudione[key].mapName;
        }
      }
    },
    //初始化待处置条数
    async daichuzhilist() {
      const num = this.radio123;
      // console.log(num);
      const num1 = parseInt(num);

      const { data: res } = await this.$http.get("/check/checkNums", {
        params: {
          sources: num1,checkTime:this.searchurl
        },
      });
      if (res.code == 200) {
        // console.log(res.data);
             this.wenzi123 = true;
        this.nums = res.data.nums;
        this.mapName = res.data.mapName;
      }
    },
    //初始化时间
    async listtime() {
      let list = {
        around: "",
        endCheckTime: this.whiteSearchList.endCreateTime,
        startCheckTime: this.whiteSearchList.startCreateTime,
        features: 0,
        fraudType: "",
        myPage: {
          pageNum: this.mypageable.pageNum,
          pageSize: this.mypageable.pageSize,
        },
        ranking: "",
      };
      const { data: res } = await this.$http.post("/checking/dateGroup", list);
      if (res.code == 200) {
        // console.log(res.data);
        // let list = [];
        // list = res.data.list.map((item) => ({ date: item }));
        this.tableData = res.data.list;
        this.totalPages = res.data.totalPages;
        this.total = res.data.total;
      }
    },

    handleClick(tab, event) {
      // console.log(tab, event);
      if (tab.name == 1) {
        // this.activeName=1
        // console.log('已审核');
        this.mypageableurl1.pageNumurl1 = 1;
        this.xqyi();
        this.listloadingright = false;
      } else if (tab.name == 0) {
        // console.log('待审核');
        this.mypageableurl.pageNumurl = 1;
        this.xq();
        this.listloadingright = false;
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
    getIndex($index) {
      //$index为数据下标,对英序号要加一
      // console.log($index)
      return (
        (this.mypageable.pageNum - 1) * this.mypageable.pageSize + $index + 1
      );
    },
    getIndex1($index) {
      //$index为数据下标,对英序号要加一
      // console.log($index)
      return (
        (this.mypageableurl.pageNumurl - 1) * this.mypageableurl.pageSizeurl +
        $index +
        1
      );
    },
    getIndex2($index) {
      //$index为数据下标,对英序号要加一
      // console.log($index)
      return (
        (this.mypageableurl1.pageNumurl1 - 1) *
          this.mypageableurl1.pageSizeurl1 +
        $index +
        1
      );
    },
    //详情
    search(val) {
      // console.log(val);
      this.listloading = false;
      this.listloadingurl = true;
      
  
      this.searchurl = val;
      this.activeName = "0";
          this.daichuzhilist()
      this.xq();
    },
    // 日期操作——详情
    async xq() {
      this.num = [];
      this.newnum = [];
      let list = {
        checkStatus: "0",
        checkTime: this.searchurl,
        sources: parseInt(this.radio123),
        myPage: {
          pageNum: this.mypageableurl.pageNumurl,
          pageSize: this.mypageableurl.pageSizeurl,
        },
      };
      // console.log(list);
      const { data: res } = await this.$http.post(
        "/recheck/queryUrlListByDate",
        list
      );
      if (res.code == 200) {
        for (var i = 0; i < res.data.list.length; i++) {
          this.num.push(Numlist(res.data.list[i].id));
          // console.log(this.num);
          // console.log(res.data.list[i]);
        }
        // console.log(res.data);
        this.newnum = this.num;
        this.tableData1 = res.data.list;
        this.totalurl = res.data.total;
      } else if (res.code == 500) {
        this.$message(res.message);
        this.fanhui();
      }
    },
    async xqyi() {
      this.num1 = [];
      this.newnum1 = [];
      // this.xqurl=val
      let list = {
        checkStatus: "1",
        checkTime: this.searchurl,
        sources: parseInt(this.radio123),
        myPage: {
          pageNum: this.mypageableurl1.pageNumurl1,
          pageSize: this.mypageableurl1.pageSizeurl1,
        },
      };
      // console.log(list);
      // console.log(list);
      const { data: res } = await this.$http.post(
        "/recheck/queryUrlListByDate",
        list
      );
      if (res.code == 200) {
        for (var i = 0; i < res.data.list.length; i++) {
          this.num1.push(Numlist(res.data.list[i].id));
          // console.log(this.num);
          // console.log(res.data.list[i]);
        }
        // console.log(this.num.length);
        // console.log(res.data);
        this.newnum1 = this.num1;
        this.tableData1wei = res.data.list;
        this.totalurl1 = res.data.total;
      } else if (res.code == 500) {
        this.$message(res.message);
        this.fanhui();
      }
    },
    //查看
    chakan(val) {
      this.shangxian = true;
      this.xiaxian = true;
      this.sId = val.id;
      this.radio = "";

      let res = getIndexres(this.num, this.sId);

      if (res == 0) {
        this.shangxian = false;
      } else if (res == this.num.length - 1) {
        // console.log(this.num.length);
        this.xiaxian = false;
      } else if (res > 0 && res < this.num.length) {
        this.shangxian = true;
        this.xiaxian = true;
      }

      this.listloadingright = true;
      this.listloadingurl = true;
      this.wenzi123 = true;
      this.chakanxiangq(this.sId);
    },
    chakan1(val) {
      this.shangxian = true;
      this.xiaxian = true;
      this.sId1 = val.id;
      // console.log(this.sId1);
      this.radio = "";
      // console.log(this.sId);
      // let res = this.num.indexOf(this.sId);

      let res = getIndexres(this.num1, this.sId1);
      if (res == 0) {
        this.shangxian = false;
      } else if (res == this.num1.length - 1) {
        this.xiaxian = false;
      } else if (res > 0 && res < this.num1.length) {
        this.shangxian = true;
        this.xiaxian = true;
      }

      // console.log(res);
      // console.log(this.num);

      this.listloadingright = true;
      this.listloadingurl = true;
      this.wenzi123 = true;
      this.chakanxiangq(this.sId1);
      // console.log(this.sId);
    },
    async chakanxiangq(n) {
      this.form.srcList = [];
      this.form.srcList1 = [];
      this.radio = "";
      const { data: res } = await this.$http.get("/finalcheck/getOrigintData", {
        params: {
          id: n,
        },
      });

      if (res.code == 200) {
        if (res.data.originData.checkResult2 != null) {
          this.radio = res.data.originData.checkResult2;
          this.radioloading = false;
        } else {
          this.radioloading = true;
        }
        // console.log(res.data);
        //  //域名
        this.form.yuming = res.data.originData.url;
        // //来源
        this.form.laiyuan = res.data.sources;
        // //标题
        this.form.title = res.data.originData.urlTitle;
        // //原因
        this.form.question = res.data.originData.reason;
        // //特征
        this.form.tezheng = res.data.originData.features;
        // //类型
        this.form.type = res.data.fraudType;
        // //访问量
        this.form.fangwenliang = res.data.originData.visits;
        // //IP
        this.form.ip = res.data.originData.ipAround;
        // //排名
        this.form.paiming = res.data.originData.ranking;
        // //境内外
        this.form.jingneiwai = res.data.originData.record;
        // //图片
        this.form.img = res.data.pcPicture;
        this.form.img1 = res.data.phonePicture;
        this.form.srcList.push(res.data.pcPicture);
        this.form.srcList1.push(res.data.phonePicture);
        // 审核状态
        this.form.shenhezhuangtai = res.data.originData.status;
        //id
        this.form.id = res.data.originData.id;
        //初审时间
        this.form.chutime = res.data.checkInfo.startCheck1Time;
        //初审结果
        this.form.chuerr = res.data.checkInfo.startCheck1Result;
      }
    },
    //上一页
    shangyiye() {
      if (this.activeName == 0) {
        this.newres = getIndexres(this.num, this.sId);
        this.newres1 = parseInt(this.newres) - 1;
        if (this.num[this.newres1] == undefined) {
          this.newres1 = 0;
          this.shangxian = false;
          this.$message("当前是列表第一条");
        } else if (this.newres1 >= 0 && this.newres1 <= this.num.length - 1) {
          this.shangxian = true;
          this.xiaxian = true;
          this.chakanxiangq(parseInt(this.num[this.newres1]));
        }
        this.sId = this.num[this.newres1];
      } else if (this.activeName == 1) {
        // console.log(this.sId1);
        this.yinewres = getIndexres(this.num1, this.sId1);
        // console.log(this.yinewres);
        this.yinewres1 = parseInt(this.yinewres) - 1;
        if (this.num1[this.yinewres1] == undefined) {
          this.yinewres1 = 0;
          this.shangxian = false;
          this.$message("当前是列表第一条");
        } else if (
          this.yinewres1 >= 0 &&
          this.yinewres1 <= this.num1.length - 1
        ) {
          this.shangxian = true;
          this.xiaxian = true;
          this.chakanxiangq(parseInt(this.num1[this.yinewres1]));
        }
        this.sId1 = this.num1[this.yinewres1];
      }
      // if (this.newres1 == -1) {
      //   this.newres1 = this.newres1 + 1;
      // }

      //  console.log(this.sId);
    },
    //下一页
    xiayiye() {
      if (this.activeName == 0) {
        this.newres = getIndexres(this.num, this.sId);
        this.newres1 = parseInt(this.newres) + 1;
        // if (this.newres1 == 10) {
        //   this.newres1 = this.newres1 - 1;
        // }

        if (this.num[this.newres1] == undefined) {
          this.newres1 = this.num.length - 1;
          this.xiaxian = false;
          this.$message("当前列表最后一条");
        } else if (this.newres1 > 0 || this.newres1 < this.num.length - 1) {
          this.shangxian = true;
          this.xiaxian = true;
          this.chakanxiangq(parseInt(this.num[this.newres1]));
        }
        this.sId = this.num[this.newres1];
      } else if (this.activeName == 1) {
        // console.log(this.num1);
        // console.log(this.sId1);
        this.yinewres = getIndexres(this.num1, this.sId1);
        // console.log(this.yinewres);
        this.yinewres1 = parseInt(this.yinewres) + 1;
        //  console.log(this.yinewres1);
        // if (this.newres1 == 10) {
        //   this.newres1 = this.newres1 - 1;
        // }

        if (this.num1[this.yinewres1] == undefined) {
          this.yinewres1 = this.num1.length - 1;
          this.xiaxian = false;
          this.$message("当前列表最后一条");
        } else if (
          this.yinewres1 > 0 ||
          this.yinewres1 < this.num1.length - 1
        ) {
          this.shangxian = true;
          this.xiaxian = true;
          this.chakanxiangq(parseInt(this.num1[this.yinewres1]));
        }
        this.sId1 = this.num1[this.yinewres1];
        // console.log(this.sId1);
      }
    },
    //提交
    async tijiao() {
      let list = {
        id: this.form.id,
        againCheckResult: this.radio,
        url: this.form.yuming,
      };
      const { data: res } = await this.$http.post(
        "/recheck/updateReCheckResult",
        list
      );
      if (res.code == 200) {
        // console.log(res);
        this.$message(res.data);

        // setTimeout(() => {
        //   console.log(this.newnum, this.sId);
        //   let numnet = getIndexres(this.newnum, this.sId);
        //   console.log(this.numnet);
        //   if (numnet == this.newnum.length - 1) {
        //     this.$message("最后一条");
        //   } else {
        //     let numnet1 = parseInt(this.newnum[parseInt(numnet) + 1]);
        //     // console.log(typeof(numnet1));
        //     this.sId = parseInt(this.newnum[parseInt(numnet) + 1]);
        //     // console.log(this.sId);
        //     this.chakanxiangq(numnet1);
        //     this.xq();
        //   }
        //   //
        // }, 1000);
        // setTimeout(() => {
        //   // this.xqyi();
        //   let yinumnet = getIndexres(this.newnum1, this.sId1);
        //   if (yinumnet == this.newnum1.length - 1) {
        //     this.$message("页面最后一条");
        //   } else {
        //     let yinumnet1 = parseInt(this.newnum1[parseInt(yinumnet) + 1]);
        //     this.sId1 = parseInt(this.newnum1[parseInt(yinumnet) + 1]);
        //     this.chakanxiangq(yinumnet1);
        //     this.xqyi();
        //   }
        // }, 1500);

        if (this.radio != "") {
          this.radioloading = false;
        }
      }
    },
    //返回
    fanhui() {
      this.listloading = true;
      this.listloadingurl = false;
      this.wenzi123 = false;
      this.listloadingright = false;
    },
    handleCurrentChange(val) {
      this.mypageable.pageNum = val;
      this.listtime();
    },
    handleCurrentChangeurl(val) {
      this.mypageableurl.pageNumurl = val;
      this.xq();
    },
    handleCurrentChangeurl1(val) {
      // console.log(val);
      this.mypageableurl1.pageNumurl1 = val;
      this.xqyi();
    },

    // 所属地
    async suoshudi() {
      const { data: res } = await this.$http.get("/check/sourcesGroup");
      if (res.code == 200) {
        // console.log(res.data);
        // for(var i=0;i<res.data.length;i++){
        //    this.shushoudi.push(res.data[i])
        // }

        this.shushoudione = res.data;
      }
    },
    handleClose() {
      this.$message("请选择归属地");
    },
    time(val) {
      return dayjs(val).format("YYYY-MM-DD  HH:mm:ss");
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
    type(val) {
      if (val == "dk") {
        return "贷款";
      } else if (val == "lc") {
        return "理财";
      } else if (val == "gjf") {
        return "公检法";
      } else if (val == "gw") {
        return "网络购物";
      } else if (val == "qt") {
        return "其他类型";
      } else if (val == "normal") {
        return "正常网站";
      } else if (val == "sd") {
        return "刷单";
      }
    },
  },
};
</script>

<style  scoped lang='less'>
.right_main_under {
  margin: 20px 0 0 20px;
  box-sizing: border-box;
}
.el-row {
  margin-bottom: 0px;
  &:last-child {
    margin-bottom: 0;
  }
}
.el-col-6 {
  width: 34%;
}
.el-col {
  border-radius: 4px;
}

.grid-content {
  border-radius: 4px;
  min-height: 24px;
}
.row-bg {
  padding: 1px 0px;
}

.btn {
  display: inline-block;
  margin-left: 20px;
}
.bottom {
  width: 37%;
  height: 3.75rem /* 60/16 */ /* 40/16 */;
  // text-align: right;
  float: left;
  // margin-right: 20rem;
}
.btn1 {
  float: right;
  font-size: 12px;
}
.form {
  position: relative;
}
.return {
  display: inline-block;
  position: absolute;
  z-index: 200;
  // float: right;
  margin-top: 10px;
  right: 20px;
}
.xiangqingbtn {
  font-size: 12px;
}
// .urlcolor:visited {
//   color: #909090;
//   text-decoration: underline;
// }
a {
  text-decoration: underline;
}
.urlcolor:visited {
  color: #0000ff;
  text-decoration: none;
}
// .urlcolor:visited {
//   color: #0000ff;
//   text-decoration: underline;
// }
.urlcolor:active {
  color: red;
  text-decoration: none;
}
/deep/ .el-table--mini td,
.el-table--mini th {
  padding: 5px 0;
}
.bottomlist {
  // width: 100%;
  width: 98%;
  height: 100%;
  position: relative;
}
.wenzi {
  position: absolute;
  // color: #000;
  font-size: 14px;
  bottom: 0;
  left: 150px;
}
.guishudi {
  position: absolute;
  // color: #000;
  font-size: 15px;
  bottom: 0;
  left: 0;
  cursor: pointer;
}
.datalist {
  width: 38%;
  float: left;
}
.leftlist {
  width: 100%;
}
.listurl {
  margin-top: 4%;
  float: right;
  width: 60%;
  height: 93%;
  // border: 1px solid red;
  position: relative;
}
.leftbtoom {
  width: 100%;
  height: 200px;
  margin-top: 0px;
  // border: 1px solid red;
  box-sizing: border-box;
  padding: 20px;
}
h4 {
  margin-bottom: 5px;
}
table {
  border-collapse: collapse;
  th,
  td {
    text-align: center;
  }
}
.leftshang {
  position: absolute;
  top: 50%;
  left: 5px;
  font-size: 40px;
  cursor: pointer;
}
.leftxia {
  position: absolute;
  top: 50%;
  right: 5px;
  font-size: 40px;
  cursor: pointer;
}
/deep/ .el-input--mini .el-input__inner {
  // width: 220px;
  width: 84%;
}
/deep/ .inner .el-input--mini .el-input__inner {
  width: 96%;
}
.images {
  width: 90%;
  height: 400px;
  // border: 1px solid blue;
  // box-sizing: border-box;
  margin: 0 auto;
  // padding: 0px 50px;
}
.botcheck {
  width: 45%;
  height: 60px;
  // border: 1px solid red;
  padding: 20px 10px;
  box-sizing: border-box;
  margin: 0 auto;
}
.img {
  margin-top: 5%;
  padding: 10px 0;
  width: 47%;
  height: 85%;
  margin-left: 1%;
  margin-right: 2%;
  border: 5px solid #ccc;
  box-sizing: border-box;
  cursor: pointer;
}
.img1 {
  padding: 10px 0;
  width: 47%;
  height: 85%;
  margin-left: 2%;
  margin-right: 1%;
  border: 5px solid #ccc;
  box-sizing: border-box;
  cursor: pointer;
}
/deep/ .el-input.is-disabled .el-input__inner {
  color: #000;
}
.te{
  color: red;
}
</style>