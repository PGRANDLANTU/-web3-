<template>
  <div class='bodyContent' style='display: block;'>
    <div class='Home'>
      <header-my></header-my>
      <div id='HomeContributeArea' class='ContributeArea'>
        <div class='child-main'>

          <div>
            <span class='el-icon-s-flag'></span>
            钱包查询过程均在本地电脑完成，我们无法获取到您的助记词及私钥！
          </div>
          <div>
            <el-select v-model='bscurl' placeholder='请选择' style='width: 500px'>
              <el-option
                v-for='item in options'
                :key='item.value'
                :label='item.label'
                :value='item.value'>
              </el-option>
            </el-select>
          </div>
          <el-row :gutter='20'>
            <el-col :span='18'>
              <div class='grid-content bg-purple'>
                <el-input
                  v-model="walletlist"
                  type="textarea"
                  :autosize="{ minRows: 4, maxRows: 8}"
                  placeholder="请输入钱包地址，并以英文逗号隔开">
                </el-input>
              </div>
            </el-col>
            <el-col v-if='wallet_arr.length ===0' :span='6'>
              <div class='grid-content bg-purple' style='text-align: left;'>
                <el-button @click='searchWallet()'>查询</el-button>
              </div>
            </el-col>
            <el-col v-if='wallet_arr.length !==0' :span='3'>
              <div class='grid-content bg-purple' style='text-align: left;'>
                <el-button @click='searchWallet()'>查询</el-button>
              </div>
            </el-col>
            <el-col v-if='wallet_arr.length !==0' :span='3'>
              <div class='grid-content bg-purple' style='text-align: left;'>
                <exportExcel :id="'exportTab'" :name="'导出Table'"></exportExcel>
              </div>
            </el-col>
          </el-row>
          <el-table
            v-if='wallet_arr.length !==0'
            id='exportTab'
            :data='wallet_arr'
            height='800'
            border
            style='width: 100%;margin-top: 15px;'>
            <el-table-column
              prop='address'
              label='钱包地址'
              width='375'>
            </el-table-column>
            <el-table-column
              prop='balance'
              label='账号余额'
            >
            </el-table-column>
          </el-table>
        </div>
      </div>

      <footer-my></footer-my>
    </div>
  </div>

</template>

<script>
import Web3 from 'web3'
import ExportExcel from '@/components/ExportExcel.vue'

export default {
  name: 'WalletBalance',
  components: { ExportExcel },

  data() {
    return {
      walletnum: '',
      bscurl: 'https://bsc-dataseed3.binance.org',
      web3: '',
      wallet_arr: [],
      options: [{
        value: 'https://bsc-dataseed3.binance.org',
        label: 'https://bsc-dataseed3.binance.org'
      }],
      value: '',
      walletlist: '',
    }
  },
  // eslint-disable-next-line require-await
  async mounted() {
    this.checkWeb3()
    const web3provider = this.bscurl
    window.web3 = new Web3(web3provider)
    this.web3 = new Web3(web3provider)
  },
  methods: {
    async searchWallet() {
      // eslint-disable-next-line no-empty
      if (this.walletlist === '') {
        this.$message.error('钱包地址不能为空');
      }
      // eslint-disable-next-line camelcase
      this.walletlist = this.walletlist.replace(/[\uFF0C]/g,',')
      // eslint-disable-next-line camelcase
      const add_list = this.walletlist.split(',')
      // eslint-disable-next-line camelcase
      for (const swallet of add_list) {
        const balance = await this.web3.eth.getBalance(swallet)
        const humanread = await this.web3.utils.fromWei(balance)
        this.wallet_arr.push({
          address:swallet,
          balance:humanread
        })

      }

    },
    checkWeb3() {
      const web3 = window.web3
      if (typeof web3 === 'undefined') {
        this.web3 = null
        this.Log(this.MetamaskMsg.METAMASK_NOT_INSTALL, 'NO_INSTALL_METAMASK')
      }
    }

  }


}
</script>
<style>

</style>
