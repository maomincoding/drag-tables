<!--权限管理 -->
<template>
    <div
      class="main-box"
      v-loading="loadLoading"
      element-loading-text="数据加载中"
      element-loading-spinner="el-icon-loading"
    >
      <div class="main-l">
        <div class="top-name">
          <div class="top-box">
            <div class="top-count">
              {{
                initStatus.newGuestList ? guestData.length : newGuestList.length
              }}
            </div>
          </div>
          <p>游客</p>
        </div>
        <div class="utable-box">
          <el-table
            ref="guestData"
            :data="guestData"
            :row-key="getUserId"
            @row-click="guestDataSelect"
            style="width: 100%; border: 1px solid #002368; margin-bottom: 20px"
            @selection-change="selectionGuestChange"
          >
            <el-table-column type="selection" width="55"> </el-table-column>
            <el-table-column
              prop="name"
              label="姓名"
              align="center"
              show-overflow-tooltip
            ></el-table-column>
            <el-table-column
              prop="account"
              label="账号"
              align="center"
              show-overflow-tooltip
            >
            </el-table-column>
            <el-table-column
              prop="jobTit"
              label="职务"
              align="center"
              show-overflow-tooltip
              style="position: relative"
            >
              <template slot-scope="scope">
                <span>{{ scope.row.jobTit }}</span>
              </template>
            </el-table-column>
          </el-table>
        </div>
      </div>
      <div class="main-r">
        <div class="top-name">
          <p>管理员</p>
        </div>
        <el-table
          ref="managerData"
          :data="managerData"
          :row-key="getUserId"
          style="width: 100%; border: 1px solid #002368; margin-bottom: 10px"
        >
          <el-table-column
            prop="name"
            label="姓名"
            align="center"
            show-overflow-tooltip
          >
          </el-table-column>
          <el-table-column
            prop="account"
            label="账号"
            align="center"
            show-overflow-tooltip
          >
          </el-table-column>
          <el-table-column
            prop="jobTit"
            label="职务"
            align="center"
            style="position: relative"
            show-overflow-tooltip
          >
            <template slot-scope="scope">
              <span>{{ scope.row.jobTit }}</span>
            </template>
          </el-table-column>
        </el-table>
        <div class="top-name">
          <div class="top-box">
            <div class="top-count">
              {{
                initStatus.newOperatorList
                  ? operatorData.length
                  : newOperatorList.length
              }}
            </div>
          </div>
          <p>操作员</p>
        </div>
        <div class="table-b">
          <div class="table-box">
            <el-table
              ref="operatorData"
              class="table-l"
              :data="operatorData"
              :row-key="getUserId"
              style="margin-bottom: 20px"
              @row-click="operatorDataSelect"
              @selection-change="selectionOperatorChange"
            >
              <el-table-column type="selection" width="55"> </el-table-column>
              <el-table-column
                prop="name"
                label="姓名"
                align="center"
                show-overflow-tooltip
              >
              </el-table-column>
              <el-table-column
                prop="account"
                label="账号"
                align="center"
                show-overflow-tooltip
              >
              </el-table-column>
              <el-table-column
                prop="jobTit"
                label="职务"
                align="center"
                show-overflow-tooltip
              ></el-table-column>
              <el-table-column align="center" label="分配开关">
                <template>
                  <div class="breaker-btn"></div>
                </template>
              </el-table-column>
              <el-table-column
                align="center"
                label="分配场景"
                style="position: relative"
              >
                <template>
                  <div class="scene-btn"></div>
                </template>
              </el-table-column>
            </el-table>
          </div>
        </div>
        <div class="top-name">
          <div class="top-box">
            <div class="top-count">
              {{
                initStatus.newElectricianList
                  ? electricianData.length
                  : newElectricianList.length
              }}
            </div>
          </div>
          <p>电工</p>
        </div>
        <div class="table-b">
          <div class="table-box">
            <el-table
              ref="electricianData"
              :data="electricianData"
              :row-key="getUserId"
              @row-click="electricianDataSelect"
              class="table-l"
              @selection-change="selectionElectricianChange"
            >
              <el-table-column type="selection" width="55"> </el-table-column>
              <el-table-column
                prop="name"
                label="姓名"
                align="center"
                show-overflow-tooltip
              >
              </el-table-column>
              <el-table-column
                prop="account"
                label="账号"
                align="center"
                show-overflow-tooltip
              >
              </el-table-column>
              <el-table-column
                prop="jobTit"
                label="职务"
                align="center"
                show-overflow-tooltip
              ></el-table-column>
              <el-table-column align="center" label="分配开关">
                <template>
                  <div class="breaker-btn"></div>
                </template>
              </el-table-column>
              <el-table-column
                align="center"
                label="分配场景"
                style="position: relative"
              >
                <template>
                  <div class="scene-btn"></div>
                </template>
              </el-table-column>
            </el-table>
          </div>
        </div>
      </div>
      <el-dialog :visible.sync="passwordView"  width="30%">
        <el-form ref="form">
          <el-form-item>
            <el-input
              v-model="password"
              placeholder="请输入超级管理员密码"
              type="password"
              show-password
            ></el-input>
          </el-form-item>
        </el-form>
        <span slot="footer" class="dialog-footer">
          <el-button @click="passwordView = false">取 消</el-button>
          <el-button type="primary" @click="okChangeManager"
            >确 定</el-button
          >
        </span>
      </el-dialog>
    </div>
</template>

<script>
import Sortable from "sortablejs";
import { deepClone } from "./utils/index";
import tableData from "./utils/data";

export default {
  name: "Authority",
  data: () => ({
    passwordView: false,
    loadLoading: false,
    password: "",
    guestData: [],
    managerData: [],
    electricianData: [],
    operatorData: [],
    initStatus: {
      newGuestList: true,
      newManagerList: true,
      newOperatorList: true,
      newElectricianList: true,
    },
    fromItem: "",
    newGuestList: [],
    newManagerList: [],
    newOperatorList: [],
    newElectricianList: [],
    selectGuestList: [],
    selectOperatorList: [],
    selectElectricianList: [],
    managerOldIndex: 0,
  }),
  computed: {
    // ...mapGetters(['roles', 'account']),
  },
  watch: {
    passwordView: "watchPasswordView",
  },
  created() {
    // 定义静态数据
    this.obj = {
      newGuestList: ["guestData", 0],
      newManagerList: ["managerData", 3],
      newOperatorList: ["operatorData", 1],
      newElectricianList: ["electricianData", 2],
    };
    this.loadLoading = true;
    this.guestData = tableData.guestData;
    this.managerData = tableData.managerData;
    this.electricianData = tableData.electricianData;
    this.operatorData = tableData.operatorData;
    this.loadLoading = false;
  },
  mounted() {
    this.sortGuest();
    this.sortOperator();
    this.sortElectrician();
    this.sortManager();
  },
  methods: {
    watchPasswordView(val) {
      if (!val) {
        this.password = "";
      }
    },
    guestDataSelect(row) {
      row.flag = !row.flag;
      this.$refs.guestData.toggleRowSelection(row, row.flag);
    },
    operatorDataSelect(row) {
      row.flag = !row.flag;
      this.$refs.operatorData.toggleRowSelection(row, row.flag);
    },
    electricianDataSelect(row) {
      row.flag = !row.flag;
      this.$refs.electricianData.toggleRowSelection(row, row.flag);
    },
    // 确定超级管理员
    okChangeManager() {
      if (this.password.trim().length > 0) {
        const item = this[this.fromItem][this.managerOldIndex];
        if (item) {
          this.newManagerList = this.initStatus.newManagerList
            ? deepClone(this.managerData)
            : deepClone(this.newManagerList);
          this.newGuestList = this.initStatus.newGuestList
            ? deepClone(this.guestData)
            : deepClone(this.newGuestList);
          this.initStatus.newGuestList = false;
          this.initStatus.newManagerList = false;
          this.newManagerList = [item];
          this[this.fromItem].splice(this.managerOldIndex, 1);
          this.initStatus[this.fromItem] = false;

          if (this.managerData[0]) {
            const obj = deepClone(this.managerData[0]);
            this.newGuestList.push(obj);
            this.guestData.push(obj);
          }
          this.managerData = [item];
          switch (this.fromItem) {
            case "newGuestList":
              this.guestData = deepClone(this.newGuestList);
              break;
            case "newOperatorList":
              this.operatorData = deepClone(this.newOperatorList);
              break;
            case "newElectricianList":
              this.electricianData = deepClone(this.newElectricianList);
              break;
            default:
              break;
          }
          this.$message({
            message: "拖拽成功！",
            type: "success",
          });
          this.password = "";
          this.passwordView = false;
        } else {
          this.$message({
            message: "拖拽失败",
            type: "warning",
          });
          this.password = "";
          this.passwordView = false;
        }
      } else {
        this.$message({
          message: "请输入超级管理员密码",
          type: "warning",
        });
      }
    },
    // 获取userId
    getUserId(row) {
      return row.userId;
    },
    // 封装添加数据
    useAddNewData(evt, newData, oldData) {
      const item = this[this.fromItem][evt.oldIndex]; // 添加项
      const loading = this.$loading({
        lock: true,
        text: "加载中",
        spinner: "el-icon-loading",
        background: "rgba(0, 0, 0, 0.7)",
      });

      setTimeout(() => {
        loading.close();
        this.$message({
          message: "拖拽成功！",
          type: "success",
        });
      }, 1000);
      this[newData] = this.initStatus[newData]
        ? deepClone(oldData)
        : deepClone(this[newData]);
      this.initStatus[newData] = false;
      oldData.push(item);
      this[newData].push(item);
      this[this.fromItem].splice(evt.oldIndex, 1);
      this.$refs[this.obj[newData][0]].$el
        .querySelectorAll(".el-table__body-wrapper > table > tbody")[0]
        .removeChild(evt.item);
    },
    // 封装添加(多)数据
    useAddsNewData(evt, newData, oldData) {
      const arr = [];
      for (
        let index = 0;
        index < this[`select${this.fromItem.split("new")[1]}`].length;
        index++
      ) {
        const element = this[`select${this.fromItem.split("new")[1]}`][index];
        arr.push(element.userId);
      }
      const loading = this.$loading({
        lock: true,
        text: "加载中",
        spinner: "el-icon-loading",
        background: "rgba(0, 0, 0, 0.7)",
      });

      setTimeout(() => {
        loading.close();
        this.$message({
          message: "批量拖拽成功！",
          type: "success",
        });
      }, 1000);
      this[newData] = this.initStatus[newData]
        ? deepClone(oldData)
        : deepClone(this[newData]);
      this.initStatus[newData] = false;
      this[newData].push(...this[`select${this.fromItem.split("new")[1]}`]);
      this[this.obj[newData][0]].push(
        ...this[`select${this.fromItem.split("new")[1]}`]
      );
      this.useDel(
        this[`select${this.fromItem.split("new")[1]}`],
        this[this.obj[this.fromItem][0]]
      );
      this.$refs[this.obj[newData][0]].$el
        .querySelectorAll(".el-table__body-wrapper > table > tbody")[0]
        .removeChild(evt.item);
    },
    // 封装初始化数据
    useInitData(fromItem, oldData) {
      this.fromItem = fromItem;
      this[fromItem] = this.initStatus[fromItem]
        ? deepClone(oldData)
        : deepClone(this[fromItem]);
      this.initStatus[fromItem] = false;
    },
    // 批量删除(数组)
    useDel(data, currentData) {
      for (let i = 0; i < data.length; i++) {
        const element = data[i];
        for (let j = 0; j < currentData.length; j++) {
          const item = currentData[j];
          if (item === element) {
            currentData.splice(j, 1);
          }
        }
      }
    },
    // 删除(数组项)
    useDelItem(element, currentData) {
      for (let j = 0; j < currentData.length; j++) {
        const item = currentData[j];
        if (item === element) {
          currentData.splice(j, 1);
        }
      }
    },
    // 还原初始状态
    useReduction(i) {
      const arr = [
        {
          data: "guestData",
          sletData: "selectGuestList",
        },
        {
          data: "operatorData",
          sletData: "selectOperatorList",
        },
        {
          data: "electricianData",
          sletData: "selectElectricianList",
        },
      ];
      this.$refs[arr[i].data].clearSelection();
      this[arr[i].sletData] = [];
      this[arr[i].sletView] = false;
    },
    // 监听游客表格选择
    selectionGuestChange(val) {
      this.selectGuestList = val;
    },
    // 监听操作员表格选择
    selectionOperatorChange(val) {
      this.selectOperatorList = val;
    },
    // 监听电工表格选择
    selectionElectricianChange(val) {
      console.log(val);
      this.selectElectricianList = val;
    },
    // 拖拽游客
    sortGuest() {
      const el = this.$refs.guestData.$el.querySelectorAll(
        ".el-table__body-wrapper > table> tbody"
      )[0];
      Sortable.create(el, {
        ghostClass: "sortable-ghost",
        sort: false,
        animation: 150,
        group: {
          name: "person",
          pull: true,
          put: true,
        },
        setData: function (
          /** DataTransfer */ dataTransfer,
          /** HTMLElement*/ dragEl
        ) {
          dataTransfer.setData("Text", dragEl.textContent); // `dataTransfer` object of HTML5 DragEvent
        },
        onStart: () => {
          this.useInitData("newGuestList", this.guestData); // 初始化
        },
        onAdd: (evt) => {
          this.useReduction(0);
          if (this[`select${this.fromItem.split("new")[1]}`].length === 0) {
            this.useAddNewData(evt, "newGuestList", this.guestData);
          } else {
            this.useAddsNewData(evt, "newGuestList", this.guestData);
          }
        },
        onEnd: (ev) => {
          if (this[`select${this.fromItem.split("new")[1]}`].length !== 0) {
            this.useReduction(0);
            if (ev.to.outerText.indexOf("管理员") !== -1) {
              this.$nextTick(() => {
                this.newGuestList = deepClone(this.guestData);
                const data = deepClone(this.guestData);
                this.guestData = data;
              });
            } else {
              const data = deepClone(this.guestData);
              this.newGuestList = deepClone(this.guestData);
              this.guestData = [];
              this.$nextTick(() => {
                this.guestData = data;
              });
            }
          } else {
            this.$nextTick(() => {
              this.guestData = this.newGuestList;
            });
          }
        },
      });
    },
    // 拖拽操作员
    sortOperator() {
      const el = this.$refs.operatorData.$el.querySelectorAll(
        ".el-table__body-wrapper > table> tbody"
      )[0];
      Sortable.create(el, {
        ghostClass: "sortable-ghost",
        sort: false,
        animation: 150,
        group: {
          name: "person",
          pull: true,
          put: true,
        },
        setData: function (
          /** DataTransfer */ dataTransfer,
          /** HTMLElement*/ dragEl
        ) {
          dataTransfer.setData("Text", dragEl.textContent); // `dataTransfer` object of HTML5 DragEvent
        },
        onStart: () => {
          this.useInitData("newOperatorList", this.operatorData); // 初始化
        },
        onAdd: (evt) => {
          this.useReduction(1);
          if (this[`select${this.fromItem.split("new")[1]}`].length === 0) {
            this.useAddNewData(evt, "newOperatorList", this.operatorData);
          } else {
            this.useAddsNewData(evt, "newOperatorList", this.operatorData);
          }
        },
        onEnd: (ev) => {
          if (this[`select${this.fromItem.split("new")[1]}`].length !== 0) {
            this.useReduction(1);
            if (ev.to.outerText.indexOf("管理员") !== -1) {
              this.$nextTick(() => {
                this.newOperatorList = deepClone(this.operatorData);
                const data = deepClone(this.operatorData);
                this.operatorData = data;
              });
            } else {
              const data = deepClone(this.operatorData);
              this.newOperatorList = deepClone(this.operatorData);
              this.operatorData = [];
              this.$nextTick(() => {
                this.operatorData = data;
              });
            }
          } else {
            this.$nextTick(() => {
              this.operatorData = this.newOperatorList;
            });
          }
        },
      });
    },
    // 拖拽电工
    sortElectrician() {
      const el = this.$refs.electricianData.$el.querySelectorAll(
        ".el-table__body-wrapper > table> tbody"
      )[0];
      Sortable.create(el, {
        ghostClass: "sortable-ghost",
        sort: false,
        animation: 150,
        group: {
          name: "person",
          pull: true,
          put: true,
        },
        setData: function (
          /** DataTransfer */ dataTransfer,
          /** HTMLElement*/ dragEl
        ) {
          dataTransfer.setData("Text", dragEl.textContent); // `dataTransfer` object of HTML5 DragEvent
        },
        onStart: () => {
          this.useInitData("newElectricianList", this.electricianData); // 初始化
        },
        onAdd: (evt) => {
          this.useReduction(2);
          if (this[`select${this.fromItem.split("new")[1]}`].length === 0) {
            this.useAddNewData(evt, "newElectricianList", this.electricianData);
          } else {
            this.useAddsNewData(
              evt,
              "newElectricianList",
              this.electricianData
            );
          }
        },
        onEnd: (ev) => {
          if (this[`select${this.fromItem.split("new")[1]}`].length !== 0) {
            this.useReduction(2);
            if (ev.to.outerText.indexOf("管理员") !== -1) {
              this.$nextTick(() => {
                this.newElectricianList = deepClone(this.electricianData);
                const data = deepClone(this.electricianData);
                this.electricianData = data;
              });
            } else {
              const data = deepClone(this.electricianData);
              this.newElectricianList = deepClone(this.electricianData);
              this.electricianData = [];
              this.$nextTick(() => {
                this.electricianData = data;
              });
            }
          } else {
            this.$nextTick(() => {
              this.electricianData = this.newElectricianList;
            });
          }
        },
      });
    },
    // 拖拽管理员
    sortManager() {
      const el = this.$refs.managerData.$el.querySelectorAll(
        ".el-table__body-wrapper > table > tbody"
      )[0];
      Sortable.create(el, {
        ghostClass: "sortable-ghost",
        sort: false,
        animation: 150,
        group: {
          name: "person",
          pull: false,
          put: true,
        },
        setData: function (
          /** DataTransfer */ dataTransfer,
          /** HTMLElement*/ dragEl
        ) {
          dataTransfer.setData("Text", dragEl.textContent); // `dataTransfer` object of HTML5 DragEvent
        },
        onAdd: (evt) => {
          console.log(evt)
          switch (this.fromItem) {
            case "newGuestList":
              {
                const data = deepClone(this.guestData);
                this.guestData = [];
                this.$nextTick(() => {
                  this.guestData = data;
                });
              }
              break;
            case "newOperatorList":
              {
                const data = deepClone(this.operatorData);
                this.operatorData = [];
                this.$nextTick(() => {
                  this.operatorData = data;
                });
              }
              break;
            case "newElectricianList":
              {
                const data = deepClone(this.electricianData);
                this.electricianData = [];
                this.$nextTick(() => {
                  this.electricianData = data;
                });
              }
              break;
            default:
              break;
          }
          if (this[`select${this.fromItem.split("new")[1]}`].length < 2) {
            this.managerOldIndex = evt.oldIndex;
            this.passwordView = true;
          } else {
            this.$message({
              message: "批量失败！",
              type: "warning",
            });
          }
          this.$refs.managerData.$el
            .querySelectorAll(".el-table__body-wrapper > table > tbody")[0]
            .removeChild(evt.item);
        },
      });
    },
  },
};
</script>
<style lang="scss" scoped>
.top-name {
  padding: 10px;
  background: #032767;
  font-size: 14px;
  position: relative;
}
.top-name > p {
  color: #00a7ff;
  text-align: center;
}
.main-box {
  display: flex;
  padding: 10px 20px;
  justify-content: space-between;
  box-shadow: 0px 0 12px 1px inset #ccc;
}
.main-l {
  width: 48%;
  position: relative;
}
.main-r {
  width: 48%;
  position: relative;
}
.table-b {
  position: relative;
}
.allBtn {
  position: absolute;
  right: 10px;
  top: 5px;
  background: #0ea6e4;
  color: #fff;
  border: 0;
}
.isdel {
  text-align: center;
  font-size: 18px;
  color: #fff;
}

.top-box {
  display: flex;
  height: 30px;
  justify-content: space-between;
  align-items: center;
}

.top-count {
  color: #fff;
  font-size: 18px;
  margin-left:10px ;
}
.utable-box {
  overflow: auto;
  height: 700px;
}
.table-box {
  overflow: auto;
  height: 230px;
  margin-bottom: 10px;
  border: 1px solid #002368;
  .table-l {
    .breaker-btn {
      width: 22px;
      height: 18px;
      margin: 0 auto;
      background: red;
      cursor: pointer;
      &:hover {
        transform: scale(0.9);
      }
    }
    .scene-btn {
      width: 22px;
      height: 18px;
      margin: 0 auto;
      background: blue;
      cursor: pointer;
      &:hover {
        transform: scale(0.9);
      }
    }
  }
}
</style>
