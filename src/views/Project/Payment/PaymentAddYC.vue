<template>
  <!-- 新增预存列表 -->
  <div class="task" :style="buttonValue.length > 0 ? 'padding-bottom: 75px;' : ''">
    <div class="task-data">
      <van-cell-group>
        <div class="task-title">基础信息</div>
        <van-field :value="data[1] || '系统生成'" label="单号" :disabled="true" />
        <van-field :value="data[27]" label="工程编号" :disabled="true" />
        <div class="van-cell van-cell--required van-field">
          <div class="van-cell__title">工程名称</div>
          <div class="van-cell__value flex-between">
            <span :class="edit ? 'text-truncate text-left text-gray' : 'text-truncate text-left'">{{data[28] || '请选择工程项目'}}</span>
            <van-button type="primary" size="mini" @click="projectShow=true" v-if="!edit">选择</van-button>
          </div>
        </div>
        <van-field :value="$util.formatDate(data[10]) || '请选择汇款日期'" label="汇款日期：" required readonly @click="showDate" />
        <van-datetime-picker v-model="currentDate" title="汇款日期" v-show="dateShow" :min-date="new Date()" type="date" class="task-date" @confirm="saveDate" @cancel="dateShow=false" />
        <van-field v-model="data[9]" label="汇款金额(￥)：" type="number" required placeholder="请输入汇款金额" />
        <van-field v-model="data[12]" label="银行账号：" required placeholder="请输入银行账号" />
        <van-field v-model="data[11]" label="开户行：" required placeholder="请输入开户行" />
        <van-field v-model="data[13]" label="经手人：" required placeholder="请输入经手人" />
        <van-field v-model="data[16]" label="汇款说明：" required placeholder="请输入汇款说明" />

        <!--资金凭证-->
        <div class="task-title">资金凭证</div>
        <van-cell-group class="task-upload">
          <div class="task-content">
            <van-uploader class="task-imgage" :after-read="onReadFile1" accept="image/jpeg, image/png" :max-size="2097152" @oversize="$toast.fail('图片大小不能超过2M')">
              <img :src="image1" alt="资金凭证1" v-if="image1">
              <div class="content-upload" v-else>
                <van-icon name="photograph" />
                <span>点击上传凭证</span>
              </div>
            </van-uploader>
            <van-uploader class="task-imgage" :after-read="onReadFile2" accept="image/jpeg, image/png" :max-size="2097152" @oversize="$toast.fail('图片大小不能超过2M')">
              <img :src="image2" alt="资金凭证2" v-if="image2">
              <div class="content-upload" v-else>
                <van-icon name="photograph" />
                <span>点击上传凭证</span>
              </div>
            </van-uploader>
          </div>
        </van-cell-group>

        <!--制单信息-->
        <div class="task-title">制单信息</div>
        <van-field :value="data[29] || userInfo.name" label="制单人：" disabled />
        <van-field :value="$util.formatDate(data[17]) || new Date().Format('yyyy-MM-dd')" label="制单日期：" disabled />
        <van-field :value="$util.formatDate(data[18]) || new Date().Format('yyyy-MM-dd')" label="修改日期" disabled v-if="data[18] && data[18] != '1900-01-01 00:00:00'" />
      </van-cell-group>
      <div class="payment-button" v-if="buttonValue.length > 0">
        <div class="button-value" v-for="(item,index) in buttonValue" :key="index" v-if="item.Allowvisible === '1'">
          <van-button @click="onSave" v-if="item.text === '保存'" :disabled="item.Enabled !== '1'">保存</van-button>
          <van-button type="primary" @click="onSubmit" v-if="item.text === '提交'" :disabled="item.Enabled !== '1'">提交</van-button>
        </div>
      </div>
    </div>

    <!--项目列表-->
    <van-popup v-model="projectShow" position="right">
      <div class="supplier">
        <div class="supplier-item" v-for="(item,index) in projectList" :key="index" @click="currProject=item">
          <!--标题-->
          <div class="item-title">
            <span class="title">{{item.ProjectName}}</span>
            <span class="icon">
              <van-icon name="success" color="#00A0E9" v-if="item.SC_ProjectOID === currProject.SC_ProjectOID" />
            </span>
          </div>
          <!--内容-->
          <div class="item-content">
            <div class="content-row">
              <span class="row-left">联系人：{{item.Contact}}</span>
              <span class="row-right text-right">联系电话：{{item.Telephone}}</span>
            </div>
          </div>
        </div>
        <div class="screen-button">
          <van-button type="primary" size="large" @click.stop="onConfrimProItem">确 定</van-button>
        </div>
      </div>
    </van-popup>
  </div>
</template>
<script>
import computed from "../../../assets/js/computed.js";
import { index, financial, project } from "../../../assets/js/api.js";

export default {
  data() {
    return {
      edit: false,
      dateShow: false,
      currentDate: new Date(),
      data: [],
      names: [],
      image1: "",
      image2: "",
      images: [],
      businessKey: "",
      projectShow: false,
      projectList: [],
      currProject: [],
      buttonValue: []
    };
  },
  methods: {
    // 确认项目
    onConfrimProItem() {
      this.data[27] = this.currProject.ProjectNo;
      this.data[28] = this.currProject.ProjectName;
      this.projectShow = false;
    },
    // 获取项目列表
    getProject() {
      const params = {
        oid: this.userInfo.oid,
        type: 1
      };
      project.getProjectList(params).then(res => {
        if (res.status) {
          const sp = res.text.split(";");
          this.projectList = eval(sp[0]);
        }
      });
    },
    onReadFile1(file) {
      this.names[0] = file.file.name;
      this.image1 = file.content;
      this.images[0] = file.content.split(",")[1];
    },
    onReadFile2(file) {
      this.names[1] = file.file.name;
      this.image2 = file.content;
      this.images[1] = file.content.split(",")[1];
    },
    // 显示时间选择
    showDate() {
      this.currentDate = this.data[10] || new Date();
      this.dateShow = true;
    },
    // 确认时间
    saveDate(val) {
      this.data[10] = this.$util.formatDate(val, "yyyy-MM-dd hh:mm:ss");
      this.dateShow = false;
    },
    //保存先获取单号
    onSave() {
      if (!this.data[10] || this.data[10] == "1900-01-01 00:00:00") {
        this.$toast.fail("请选择汇款日期");
        return;
      }
      if (!this.data[9] || isNaN(this.data[9])) {
        this.$toast.fail("请输入正确汇款金额");
        return;
      }
      if (!this.data[12]) {
        this.$toast.fail("请输入银行账号");
        return;
      }
      if (!this.data[11]) {
        this.$toast.fail("请输入开户行");
        return;
      }
      if (!this.data[13]) {
        this.$toast.fail("请输入经手人");
        return;
      }
      if (!this.data[16]) {
        this.$toast.fail("请输入汇款说明");
        return;
      }
      if (!this.image1 && !this.image2) {
        this.$toast.fail("请上传支付凭证");
        return;
      }
      let img = [];
      const params = {
        bpoName: "BPO_Start_YC_InOutFormService",
        UserPhoto: this.images.join(","),
        PhotoName: this.names.join(",")
      };
      financial.uploadImage(params).then(result => {
        try {
          if (result.status == 1) {
            img = result.text.split(",");
            // 修正资金凭证1
            if (img.length > 0 && img[0].length <= 33) {
              img[0] = "null";
            }
          }
          financial.getMemorySheetNo("YC").then(ress => {
            if (ress && ress.status === 1) {
              const xml = require("xml");
              if (this.businessKey == "") {
                const uuidv1 = require("uuid/v1");
                this.businessKey = uuidv1();
              }
              let xmlString = "";
              if (this.edit) {
                xmlString = xml({
                  BC_SC_Money_InOut: [
                    { _attr: { UpdateKind: "ukModify" } },
                    { SC_Money_InOutOID: this.businessKey }
                  ]
                });
              }
              xmlString += xml({
                BC_SC_Money_InOut: [
                  { _attr: { UpdateKind: this.edit ? "" : "ukInsert" } },
                  { SC_Money_InOutOID: this.edit ? "null" : this.businessKey },
                  { InOut_SheetNO: this.data[1] || ress.text },
                  { ProjectID: this.currProject.SC_ProjectOID },
                  { PartnerID: this.currProject.PartnerID },
                  { DemandID: this.userId.UCML_OrganizeOID },
                  { Sheet_Type: "YC" },
                  { Approve_Flag: 0 },
                  { InOut_Date: this.data[10] },
                  { InOut_Amt: this.data[9] },
                  { Bank_Account: this.data[12] },
                  { Bank_Name: this.data[11] },
                  { Operator: this.data[13] },
                  { Remark: this.data[16] },
                  { Certificate1: img[0] || "null" },
                  { Certificate2: img[1] || "null" },
                  { SYS_CreatedBy: this.userId.UCML_UserOID },
                  { SYS_POSTN: this.userId.UCML_PostOID },
                  { SYS_DIVISION: this.userId.UCML_DivisionOID },
                  { SYS_ORG: this.userId.UCML_OrganizeOID },
                  { EmployeeName: this.userId.PersonName },
                  {
                    SYS_Created:
                      this.data[17] || new Date().Format("yyyy-MM-dd hh:mm:ss")
                  },
                  { SYS_LAST_UPD: new Date().Format("yyyy-MM-dd hh:mm:ss") },
                  { SYS_LAST_UPD_BY: this.userInfo.oid }
                ]
              });
              xmlString = "<root>" + xmlString + "</root>";
              financial.preMemoryConservation(xmlString).then(res => {
                if (res.status === 1) {
                  this.edit = true;
                  this.$toast.success("保存成功");
                  return;
                }
                throw res.text;
              });
            } else {
              throw ress.text;
            }
          });
        } catch (e) {
          this.$toast.fail(e);
        }
      });
    },
    onSubmit() {
      try {
        financial
          .submitPremomery(this.businessKey, this.currProject.DemandID)
          .then(result => {
            if (result.status === 1) {
              financial.conservationSubmit(this.businessKey).then(res => {
                if (res.status === 1) {
                  this.$toast.success({
                    forbidClick: true, // 禁用背景点击
                    message: "提交成功"
                  });
                  this.$nextTick().then(() => {
                    setTimeout(() => {
                      this.$router.go(-1);
                    }, 800);
                  });
                } else {
                  this.$toast.fail(res.text);
                }
              });
            } else {
              this.$toast.fail("提交失败，请先保存内容再提交");
            }
          });
      } catch (e) {
        this.$toast.fail(e);
        console.log(e);
      }
    },
    // 获取数据
    pageInit() {
      if (this.taskParams) {
        financial.getTaskYCInfo(this.taskParams).then(result => {
          try {
            if (result && result.status === 1) {
              let sp = result.text.split(";");
              this.data = eval(sp[0].split("=")[1])[0];
              this.businessKey = this.data[0];
              this.edit = true;
              this.image1 = this.data[14].replace("~", this.servePath);
              this.image2 = this.data[15].replace("~", this.servePath);
            }
            if (!this.data[10] || this.data[10] == "1900-01-01 00:00:00") {
              this.data[10] = new Date().Format("yyyy-MM-dd hh:mm:ss");
            }
          } catch (e) {
            console.log(e);
          }
        });
      }
      this.getProject();

      index
        .getAppletButton(this.userId.UCML_UserOID, "BPO_Start_YC_InOutForm")
        .then(res => {
          if (res.status) {
            this.buttonValue = JSON.parse(res.text);
          }
        });
    }
  },
  computed,
  mounted() {
    this.pageInit();
  }
};
</script>
<style lang="less" scoped>
.task {
  width: 100%;
  overflow: hidden !important;
  .task-data {
    width: 100%;
    height: 100%;
    overflow-x: hidden;
    overflow-y: auto;
    .payment-button {
      width: 100%;
      padding: 5px;
      position: fixed;
      bottom: 0;
      background-color: #fff;
      .button-value {
        display: inline-block;
        button {
          width: 191.5px;
          margin: 5px;
        }
      }
    }

    .task-title {
      font-size: 16px;
      padding: 10px;
      color: #00a0e9;
      background-color: #f7f7f7;
    }

    .task-upload {
      padding: 15px 0;
      .task-content {
        display: flex;
        justify-content: space-around;
        .task-imgage {
          width: 120px;
          height: 120px;
          border: 1px solid #ddd;
          img {
            width: 100%;
            height: 100%;
          }
          .content-upload {
            width: 100%;
            height: 100%;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            i {
              font-size: 30px;
            }
            span {
              color: #666;
              font-size: 12px;
              padding-top: 10px;
            }
          }
        }
      }
    }
    .task-date {
      width: 100%;
      position: fixed;
      z-index: 9999;
      bottom: 0;
    }
  }
  /* 选择项目 */
  .van-popup--right {
    width: 90%;
    height: 100%;
    .supplier {
      height: 100%;
      overflow-y: auto;
      -webkit-overflow-scrolling: touch;
      background-color: #f6f6f6;
      padding-bottom: 50px;
      .supplier-item {
        background-color: #fff;
        padding: 5px 10px;
        margin-bottom: 10px;
        .item-title {
          height: 40px;
          border-bottom: 1px solid #f6f6f6;
          display: flex;
          align-items: center;
          justify-content: space-between;
          .title {
            font-weight: 600;
            font-size: 16px;
          }
          .option {
            padding: 10px 15px;
            font-size: 12px;
            color: #00a0e9;
            text-decoration: underline;
          }
        }
        .item-content {
          font-size: 12px;
          color: #666;
          padding: 5px 0;
          .content-row {
            padding: 5px 0;
            display: flex;
            align-items: center;
            justify-content: space-between;
            .row-left {
              flex: 1;
            }
            .row-right {
              flex: 1;
            }
          }
        }
      }
      .screen-button {
        position: fixed;
        left: 0;
        right: 0;
        bottom: 0;
        text-align: center;
      }
    }
  }
}
</style>
<style lang="less">
.task {
  .van-field .van-cell__title {
    max-width: 100px;
  }
}
</style>
