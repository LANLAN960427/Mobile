<template>
  <!-- 合同编辑 -->
  <div class="contractwork">
    <van-cell-group>
      <div class="title-delivery">基本合同信息</div>
      <van-field v-model="cspList[1]" label="合同编号" disabled />
      <van-field v-model="cspList[2]" label="合同名称" placeholder="请输入合同名称" required />
      <van-field v-model="cspList[11]" label="合同金额" placeholder="请输入合同金额" type="number" />
      <van-cell-group class="tact-price">
        <span class="tact-label">税率(%)</span>
        <span class="tact-select">
          <van-stepper v-model="cspList[19]" required />
        </span>
      </van-cell-group>
      <van-field v-model="cspList[18]" label="合同含税金额" placeholder="请输入合同含税金额" required type="number" />
      <van-field v-model="cspList[3]" label="甲方" placeholder="请输入甲方" required />
      <van-field v-model="cspList[4]" label="乙方" placeholder="请输入乙方" required />
      <van-field v-model="cspList[10]" label="验收限期(天)" placeholder="请输入验收限期(天)" required />
      <van-cell-group class="con-price">
        <span class="con-label">交货时间</span>
        <span class="con-select" @click="showDateone=true">{{cspList[9]}}</span>
      </van-cell-group>
      <van-datetime-picker v-model="currentDate" v-show="showDateone" :min-date="new Date()" type="date" class="contract-date" @confirm="jiaohuoDate" @cancel="showDateone=false" />
      <van-field v-model="cspList[8]" label="交货地点" placeholder="请输入交货地点" />
      <van-cell-group class="con-price">
        <span class="con-label">付款方式</span>
        <span class="con-select" @click="paymentShow=true">{{payment}}</span>
        <van-popup v-model="paymentShow" position="bottom">
          <van-picker show-toolbar :columns="columns" @cancel="paymentShow=false" @confirm="onConfirm" />
        </van-popup>
      </van-cell-group>
      <van-cell-group>
        <div class="padding-vertical-sm text-xs">
          <span class="padding-left text-sm text-bold">付款说明：</span>
          <span class="padding-vertical-xs padding-horizontal-sm">
            第一种付款方式：按工程进度百分比方式付款：材料款按对应项目甲方付款进度支付。
            <br /> 第二种付款方式：材料供货进场并验收合格后一次性付完货款。
            <br /> 第三种付款方式：按预付款，到货款，质保金方式付
          </span>
        </div>
      </van-cell-group>
      <div v-if="payment === '第三种付款方式'">
        <van-field v-model="cspList[13]" label="订金付款期限(天)" placeholder="请输入订金付款期限(天)" required />
        <van-field v-model="cspList[15]" label="进场付款期限(天)" placeholder="请输入付款期限(天)" required />
        <van-field v-model="cspList[20]" label="保质付款期限(天)" placeholder="请输入保质付款期限(天)" required />
        <van-field v-model="cspList[14]" label="订金比例(%)" placeholder="请输入订金比例" required />
        <van-field v-model="cspList[16]" label="进场付款比例(%)" placeholder="请输入进场付款比例" required />
        <van-field v-model="cspList[21]" label="保质付款比例(%)" placeholder="请输入保质付款比例" required />
      </div>
      <div class="title-delivery">保质与违约</div>
      <van-field v-model="cspList[22]" label="保质期（天）" placeholder="请输入保质期（天）" type="number" />
      <van-cell-group class="con-price">
        <span class="con-label">保质开始时间</span>
        <span class="con-select" @click="showDate=true">{{cspList[23]}}</span>
      </van-cell-group>
      <van-datetime-picker v-model="currentDate" v-show="showDate" :min-date="new Date()" type="date" class="contract-date" @confirm="saveDate" @cancel="showDate=false" />
      <van-cell-group class="con-price">
        <span class="con-label">保质结束时间</span>
        <span class="con-select" @click="showDatatwo=true">{{cspList[24]}}</span>
      </van-cell-group>
      <van-datetime-picker v-model="currentDate" v-show="showDatatwo" :min-date="new Date()" type="date" class="contract-date" @confirm="jieshuDate" @cancel="showDatetwo=false" />
      <van-field v-model="cspList[25]" label="保证金比例(%)" placeholder="请输入保证金比例" />
      <van-field v-model="cspList[26]" label="保质期处理期限(天)" placeholder="请输入保质期处理期限" />
      <van-field v-model="cspList[27]" label="保质期处理备注" placeholder="请输入保质期处理备注" />
      <van-field v-model="cspList[28]" label="乙方违约(‰)" placeholder="请输入乙方违约" required type="number" />
      <van-field v-model="cspList[29]" label="甲方违约(‰)" placeholder="请输入甲方违约" required type="number" />
      <div class="title-delivery">甲方信息</div>
      <van-field v-model="cspList[31]" label="甲方(盖章签名处)" disabled />
      <van-field v-model="cspList[32]" label="甲方银行账号" disabled />
      <van-field v-model="cspList[33]" label="甲方开户行" disabled />
      <van-field v-model="cspList[34]" label="甲方纳税识别码" disabled />
      <van-field v-model="cspList[36]" label="甲方电话" disabled />
      <van-field v-model="cspList[37]" label="甲方代表人" placeholder="请输入甲方代表人" />
      <van-cell-group class="con-price">
        <span class="con-label">甲方签订日期</span>
        <span class="con-select" @click="showDatathree=true">{{cspList[38]}}</span>
      </van-cell-group>
      <van-datetime-picker v-model="currentDate" v-show="showDatathree" :min-date="new Date()" type="date" class="contract-date" @confirm="jiafangDate" @cancel="showDatathree=false" />
      <van-field v-model="cspList[35]" label="甲方地址" disabled />
      <div class="title-delivery">乙方信息</div>
      <van-field v-model="cspList[39]" label="乙方(盖章签名处)" disabled />
      <van-field v-model="cspList[40]" label="乙方银行账号" disabled />
      <van-field v-model="cspList[41]" label="乙方开户行" disabled />
      <van-field v-model="cspList[42]" label="乙方纳税识别码" disabled />
      <van-field v-model="cspList[44]" label="乙方电话" disabled />
      <van-field v-model="cspList[45]" label="乙方代表人" placeholder="请输入乙方代表人" />
      <van-cell-group class="con-price">
        <span class="con-label">乙方签订日期</span>
        <span class="con-select" @click="showDatafour=true">{{cspList[46]}}</span>
      </van-cell-group>
      <van-datetime-picker v-model="currentDate" v-show="showDatafour" :min-date="new Date()" type="date" class="contract-date" @confirm="yifangDate" @cancel="showDatafour=false" />
      <van-field v-model="cspList[43]" label="乙方地址" disabled />
    </van-cell-group>
    <div class="title-delivery">合同附件</div>
    <van-cell title="附件" is-link value="" @click="$router.push({name: 'contractAnnex'})" />
    <div class="button">
      <van-button size="large" type="primary" @click="submitWork">提交合同</van-button>
    </div>
  </div>
</template>
<script>
import computed from "../../../assets/js/computed.js";
import { conprice, contractInfo } from "../../../assets/js/api.js";
export default {
  data() {
    return {
      cspList: [],
      payment: "请选择付款方式",
      currentDate: new Date(),
      columns: ["第一种付款方式", "第二种付款方式", "第三种付款方式"],
      paymentShow: false,
      showDate: false, // 确认时间
      showDateone: false, //交货时间
      showDatatwo: false, //保质结束时间
      showDatathree: false, //甲方
      showDatafour: false //乙方
    };
  },
  computed,
  methods: {
    //付款方式
    onConfirm(res) {
      this.payment = res;
      this.paymentShow = false;
    },
    // 保质开始时间
    saveDate(val) {
      this.cspList[23] = this.$util.formatDate(val);
      this.showDate = false;
    },
    //交货时间
    jiaohuoDate(val) {
      this.cspList[9] = this.$util.formatDate(val);
      this.showDateone = false;
    },
    //保质结束时间
    jieshuDate(val) {
      this.cspList[24] = this.$util.formatDate(val);
      this.showDatatwo = false;
    },
    //甲方
    jiafangDate(val) {
      this.cspList[38] = this.$util.formatDate(val);
      this.showDatathree = false;
    },
    //乙方
    yifangDate(val) {
      this.cspList[46] = this.$util.formatDate(val);
      this.showDatafour = false;
    },
    conContract() {
      conprice.conContract(this.confirmParams[23]).then(res => {
        if (res && res.status === 1) {
          const sp = res.text.split("[[");
          const csp = sp[1].split(";");
          this.cspList = eval("[[" + csp[0])[0];

          if (this.cspList[9] && this.cspList[9] != "1900-01-01 00:00:00") {
            this.cspList[9] = this.$util.formatDate(this.cspList[9]);
          } else {
            this.cspList[9] = "请选择时间"; // 确认时间
          }
          if (this.cspList[23] && this.cspList[23] != "1900-01-01 00:00:00") {
            this.cspList[23] = this.$util.formatDate(this.cspList[23]);
          } else {
            this.cspList[23] = "请选择时间"; // 交货时间
          }
          if (this.cspList[24] && this.cspList[24] != "1900-01-01 00:00:00") {
            this.cspList[24] = this.$util.formatDate(this.cspList[24]);
          } else {
            this.cspList[24] = "请选择时间"; // 保质结束时间
          }
          if (this.cspList[38] && this.cspList[38] != "1900-01-01 00:00:00") {
            this.cspList[38] = this.$util.formatDate(this.cspList[38]);
          } else {
            this.cspList[38] = "请选择时间"; // 甲方
          }
          if (this.cspList[46] && this.cspList[46] != "1900-01-01 00:00:00") {
            this.cspList[46] = this.$util.formatDate(this.cspList[46]);
          } else {
            this.cspList[46] = this.$util.formatDate(new Date()); // 乙方
          }
          const i = this.cspList[12];
          this.payment = this.columns[i - 1];
        }
      });
    },
    // 提交
    submitWork() {
      const cspList = this.cspList;
      if (!cspList[2].trim()) {
        this.$toast.fail("请输入合同名称");
        return;
      } else if (!cspList[3].trim()) {
        this.$toast.fail("请输入甲方");
        return;
      } else if (!cspList[4].trim()) {
        this.$toast.fail("请输入乙方");
        return;
      } else if (cspList[11] === "") {
        this.$toast.fail("请输入合同金额");
        return;
      } else if (!cspList[18]) {
        this.$toast.fail("请输入正确的合同含税金额");
        return;
      } else if (!cspList[28]) {
        this.$toast.fail("请输入正确的乙方违约比例");
        return;
      } else if (!cspList[29]) {
        this.$toast.fail("请输入正确的甲方违约比例");
        return;
      } else if (this.payment == "第三种付款方式" && !cspList[13]) {
        this.$toast.fail("请输入订金付款期限");
        return;
      } else if (this.payment == "第三种付款方式" && !cspList[15]) {
        this.$toast.fail("请输入付款期限");
        return;
      } else if (this.payment == "第三种付款方式" && !cspList[20]) {
        this.$toast.fail("请输入保质付款期限");
        return;
      } else if (this.payment == "第三种付款方式" && !cspList[14]) {
        this.$toast.fail("请输入订金比例");
        return;
      } else if (this.payment == "第三种付款方式" && !cspList[16]) {
        this.$toast.fail("请输入进场付款比例");
        return;
      } else if (this.payment == "第三种付款方式" && !cspList[21]) {
        this.$toast.fail("请输入保质付款比例");
        return;
      }
      const xml = require("xml");
      const xmlString = xml({
        root: [
          {
            BC_SC_Order_Contract: [
              { _attr: { UpdateKind: "ukModify" } },
              { SC_Order_ContractOID: cspList[0] }
            ]
          },
          {
            BC_SC_Order_Contract: [
              { _attr: { UpdateKind: "" } },
              { SC_Order_ContractOID: "null" },
              { Contract_Name: cspList[2] },
              { Partner_Name: cspList[31] },
              { Supplier_Name: cspList[39] },
              { Deliver_Addr: cspList[8] },
              {
                Deliver_Time: cspList[9] != "请选择时间" ? cspList[9] : "null"
              },
              { Accept_Valid_Day: cspList[10] },
              { Contract_Amt: cspList[11] },
              { Pay_Mode: cspList[12] },
              { Signt_Valid_Day: cspList[13] },
              { Signt_Percent: cspList[14] },
              { In_Valid_Day: cspList[15] },
              { In_Pay_Percent: cspList[16] },
              { Ensure_Valid_Day: cspList[20] },
              { Ensure_Pay_Percent: cspList[21] },
              { Ensure_Day: cspList[22] },
              {
                Ensure_Begin_Date:
                  cspList[23] != "请选择时间" ? cspList[23] : "null"
              },
              {
                Ensure_End_Date:
                  cspList[24] != "请选择时间" ? cspList[24] : "null"
              },
              { Ensure_Amt_Percent: cspList[25] },
              { Ensure_Deal_Day: cspList[26] },
              { Ensure_Deal_Remark: cspList[27] },
              { Supplier_Breach: cspList[28] },
              { Partner_Breach: cspList[29] },
              { Partner_Behalf: cspList[37] },
              {
                Partner_Sign_Date:
                  cspList[38] != "请选择时间" ? cspList[38] : "null"
              },
              {
                Supplier_Sign_Date:
                  cspList[46] != "请选择时间" ? cspList[46] : "null"
              },
              { Supplier_Sign: cspList[45] },
              { Edit_Flag: this.$util.replacePos(cspList[50], 2, 1) },
              { SYS_LAST_UPD: new Date().Format("yyyy-MM-dd hh:mm:ss") }, // 最后修改日期
              { SYS_LAST_UPD_BY: this.userInfo.oid } // 最后修改用户
            ]
          }
        ]
      });

      this.$dialog
        .confirm({
          title: "提交",
          message: "确认提交该合同？"
        })
        .then(() => {
          contractInfo.saveContract(xmlString).then(res => {
            try {
              if (res.status === 1) {
                this.$toast.success({
                  forbidClick: true, // 禁用背景点击
                  message: "合同提交成功"
                });
                this.$nextTick().then(() => {
                  setTimeout(() => {
                    this.$router.go(-1);
                  }, 800);
                });
                return;
              }
              throw res.text;
            } catch (e) {
              this.$toast.fail(e);
            }
          });
        });
    }
  },
  mounted() {
    this.conContract();
  }
};
</script>
<style lang="less" scoped>
.contractwork {
  width: 100%;
  .title-delivery {
    font-size: 16px;
    padding: 10px;
    color: #00a0e9;
    background-color: #f7f7f7;
  }
  .con-price {
    display: flex;
    padding: 4px 15px;
    box-sizing: border-box;
    line-height: 32px;
    position: relative;
    background-color: #fff;
    font-size: 14px;
    overflow: hidden;
    .con-label {
      min-width: 130px;
      flex: 1;
    }
    .con-select {
      flex: 5;
    }
  }
  .contract-date {
    width: 100%;
    position: fixed;
    z-index: 9999;
    bottom: 0;
    padding-right: 30px;
  }
  .tact-price {
    display: flex;
    padding: 4px 15px;
    box-sizing: border-box;
    line-height: 32px;
    position: relative;
    background-color: #fff;
    font-size: 14px;
    overflow: hidden;
  }
  .tact-label {
    min-width: 129px;
    flex: 1;
  }
  .tact-select {
    flex: 5;
  }
  .button {
    width: 100%;
    display: flex;
    justify-content: center;
    margin: 20px 0;
    button {
      width: 95%;
      text-align: center;
    }
  }
}
</style>
<style lang="less">
.contractwork {
  .van-field .van-cell__title {
    color: black;
    max-width: 130px !important;
  }
}
</style>

