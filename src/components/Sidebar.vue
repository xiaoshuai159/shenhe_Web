<template>
  <div class="sidebar">
    <el-menu
      router
      :default-active="$route.path"
      class="sidebar-el-menu"
      background-color="#545c64"
      text-color="#fff"
      active-text-color="rgb(69 125 187)"
    >
      <NavItem
        v-for="(v, index) in items"
        :key="index"
        :item="v"
        :path="v.name"
      />
    </el-menu>
  </div>
</template>

<script>
import NavItem from "./NavItem";

export default {
  data() {
    return {
      // defaultUrl: "/reviewed",
      pid: [],
      Newname: [],
      Newname1: [],
      items: [
        // {
        //   menuName: "待审核列表",
        //   menuUrl: "/reviewed",
        //   name: "reviewed",
        //   menuType: 1,
        // },
        // {
        //   menuName: "初审",
        //   menuUrl: "/Preliminaryexamination",
        //   name: "Preliminaryexamination",
        //   menuType: 1,
        // },
        // {
        //   menuName: "复审",
        //   menuUrl: "/reviewcase",
        //   name: "reviewcase",
        //   menuType: 1,
        // },
        // {
        //   menuName: "终审",
        //   menuUrl: "/finaljudgment",
        //   name: "finaljudgment",
        //   menuType: 1,
        // },
        // {
        //   menuName: "审核结果列表",
        //   menuUrl: "/reslist",
        //   name: "reslist",
        //   menuType: 1,
        // },
        // {
        //   menuName: "流程记录页面",
        //   menuUrl: "/technologicalprocess",
        //   name: "technologicalprocess",
        //   menuType: 1,
        // },
        // {
        //   menuName: "系统管理",
        //   menuUrl: "/xitong",
        //   name: "xitong",
        //   menuType: 0,
        //   children: [
        //      {
        //       menuName: "用户管理",
        //       menuUrl: "/user",
        //       name: "user",
        //       menuType: 1,
        //     },
        //     {
        //       menuName: "角色管理",
        //       menuUrl: "/Role",
        //       name: "Role",
        //       menuType: 1,
        //     },
        //       {
        //       menuName: "菜单管理",
        //       menuUrl: "/menu",
        //       name: "menu",
        //       menuType: 1,
        //     },
        //     {
        //       menuName: "人数配置页面",
        //       menuUrl: "/Numberallocation",
        //       name: "Numberallocation",
        //       menuType: 1,
        //     },
            
        //   ],
        // },
      ],
    };
  },
  components: {
    NavItem,
  },
  mounted() {
    // let href = window.location.href;
    // this.defaultUrl = href.split("/#")[1];
    this.qx();
  },
  watch: {
    $route: "getPath",
  },
  methods: {
    getPath() {
      this.defaultUrl = this.$route.path;
    },

    async qx() {
      const that = this;
      const { data: res } = await this.$http.post("/menu/queryUserMenuList");
      // console.log(res);
      if (res.code == 200) {
        // console.log(res.data);
        this.items = res.data;

        // =========================
        res.data.forEach((item) => {
          that.menuFir(item);
        });
        this.pid = this.getSetArr(this.pid);
        //   if (!window.sessionStorage.getItem("btn")) {
        //   window.sessionStorage.setItem("btn", this.Newname1);
        // }
        // console.log(this.pid);
        res.data.forEach((item) => {
          that.menuSec(item);
        });
        // console.log(res.data);
      }

      // console.log(res.data);
      // this.items = res.data;
    },
    getSetArr(arr) {
      return [...new Set(arr)];
    },
    //
    menuFir(data) {
      if (data.menuType != 1) {
        // data["isShow"] = true;
        if (data.children) {
          data.children.forEach((item) => {
            this.menuFir(item);
          });
        }
      } else {
        // data["isShow"] = false;
        this.pid.push(data.pid);
        this.Newname.push(data.name);
        this.Newname1 = this.Newname;
      }
    },

    menuSec(data) {
      var flag = false;
      for (var item in this.pid) {
        // console.log(item);
        // console.log(data.id,this.pid[item]);
        if (data.id != this.pid[item]) {
          flag++;
          continue;
        } else {
          data.menuType = 1;
          break;
        }
      }
      if (data.children) {
        data.children.forEach((item) => {
          this.menuSec(item);
        });
      } else {
        data.menuType = 1;
      }
    },
  },
  computed: {
    // onRoutes() {
    //   return this.$route.path.replace("/", "");
    // },
    // collapse() {
    //   return this.$store.state.collapse;
    // },
  },
};
</script>

<style scoped lang="less">
.sidebar {
  display: block;
  position: absolute;
  left: 0;
  top: 50px;
  bottom: 0;
  overflow-y: scroll;
}
.sidebar::-webkit-scrollbar {
  width: 0;
}
.sidebar-el-menu:not(.el-menu--collapse) {
  width: 8rem;
}
.sidebar > ul {
  height: 100%;
}
/deep/.el-menu-item,
/deep/.el-submenu__title {
  height: 67px;
  line-height: 70px;
}
/deep/.el-submenu__title {
}
// .el-submenu{
//   padding: 10px;
//     box-sizing: border-box;
// }
.el-submenu /deep/ img {
  box-sizing: border-box;
  width: 100%;
  height: 100%;
}
.el-menu-item /deep/ img {
  box-sizing: border-box;
  width: 100%;
  height: 100%;
}
</style>
