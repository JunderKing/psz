<template>
  <div class="candy-order-confirm">
    <div class="title-area">
      <span>订单详情</span>
    </div>
    <div class="content-area">
      <div class="info-area">
        <div class="info-row">
          <span class="title">项目：</span>
          <div class="content-box">
            <img :src="depositBoxData.logoUrl" alt="">
            <div class="info-box">
              <span class="title">{{ depositBoxData.tokenSymbol }}</span>
              <span class="text">{{ depositBoxData.tokenName }}</span>
            </div>
          </div>
        </div>
        <div class="info-row">
          <div class="info-item">
            <span class="title">充值数量：</span>
            <span class="content">{{ depositBoxData.orderAmount }}枚</span>
          </div>
          <div class="info-item">
            <span class="title">锁仓期：</span>
            <span class="content">{{ depositBoxData.lockTime }}个月</span>
          </div>
          <div class="info-item">
            <span class="title">回报：</span>
            <span class="content">{{depositBoxData.interestRate * depositBoxData.orderAmount}}枚</span>
          </div>
        </div>
        <div class="info-row">
          <span class="title">接收地址：</span>
          <span class="content">{{ depositBoxData.toAddr }}</span>
        </div>
        <div class="info-row">
          <span class="title">您的地址：</span>
          <span class="content">{{ depositBoxData.fromAddr }}</span>
        </div>
      </div>
      <div class="table-box">
        <span class="title">以下为系统自动检测到的交易记录，请勾选此笔订单相关的交易记录进行确认！</span>
        <table>
          <tr>
            <th>充值数量</th>
            <th>交易时间</th>
            <th>交易哈希</th>
            <th>操作</th>
          </tr>
          <tr v-for="(record, index) in recordList" :key="index">
            <td>{{ record.txAmount }}</td>
            <td>{{ record.txTime }}</td>
            <td><a :href="'https://etherscan.io/tx/' + record.txHash" target="_blank">{{ getShortStr(record.txHash, 12) }}</a></td>
            <td><input @change="changeCheck($event, record.id)" type="checkbox"></td>
          </tr>
        </table>
        <div class="btn" @click="confirmTx">
          <span>确认完成</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      checkBox: '',
      recordList: [],
      depositBoxData: {},
      recordIdList: []
    }
  },
  mounted () {
    this.depositBoxData = this.$route.query
    this.updTxRecord()
  },
  methods: {
    confirmTx () {
      this.$http.post('/api/confirmDepositTx', {
        depositOrderId: this.depositBoxData.depositOrderId,
        txRecordIdList: this.recordIdList
      }).then((res) => {
        if (res.data.errcode === 0) {
          this.$router.push('/candyRoom/myCandyOrder')
        } else {
          alert(res.data.errmsg)
        }
      })
    },
    updTxRecord () {
      this.$http.post('/api/getOrderTxRecordList', {
        depositOrderId: this.depositBoxData.depositOrderId
      }).then((res) => {
        if (res.data.errcode === 0) {
          this.recordList = res.data.data.dataList
        }
      })
    },
    changeCheck (e, recordId) {
      var isChecked = e.target.checked
      if (isChecked) {
        this.recordIdList.push(recordId)
      } else {
        this.recordIdList.remove(recordId)
      }
    }
  }
}
</script>

<style lang="scss" scoped>
.candy-order-confirm {
  .title-area {
    width: 100%;
    height: 108px;
    background-color: #FFF;
    font-size: 24px;
    color: #4A4A4A;
    text-align: center;
    line-height: 108px;
    margin-bottom: 10px;
  }
  .content-area {
    background-color: #FFF;
    padding: 20px;
    .info-area {
      box-sizing: border-box;
      width: 100%;
      border-bottom: 1px solid #C2C2C2;
      padding: 30px 0 30px 80px;
      position: relative;
      .info-row {
        height: 46px;
        font-size: 16px;
        color: #4A4A4A;
        &:first-child {
          margin-bottom: 20px;
        }
        >.title {
          display: inline-block;
          width: 80px;
          text-align: right;
        }
        .info-item {
          margin-right: 40px;
          display: inline-block;
          .content {
            font-size: 16px;
            color: #FF6276;
          }
        }
        .content-box {
          display: inline-block;
          vertical-align: middle;
          font-size: 0;
          img {
            width: 40px;
            height: 40px;
            margin-right: 10px;
          }
          .info-box {
            display: inline-flex;
            vertical-align: top;
            flex-direction: column;
            justify-content: space-between;
            align-items: flex-start;
            height: 40px;
            font-size: 14px;
            color: #9B9B9B;
            .title {
              font-size: 18px;
              color: #4A4A4A;
              text-align: left;
            }
          }
        }
      }
    }
    .table-box {
      text-align: center;
      padding: 0 80px;
      .title {
        display: inline-block;
        font-size: 16px;
        color: #FF6276;
        margin: 30px auto;
      }
      table {
        width: 100%;
        font-size: 16px;
        color: #4A4A4A;
        margin-bottom: 40px;
        tr {
          border-bottom: 1px solid #979797;
          line-height: 60px;
          td {
            color: #000;
          }
        }
      }
      .btn {
        display: inline-block;
        width: 160px;
        height: 40px;
        border-radius: 23px;
        background-color: #FF6276;
        color: #FFF;
        line-height: 40px;
        cursor: pointer;
        margin-bottom: 10px;
      }
    }
  }
}
</style>
