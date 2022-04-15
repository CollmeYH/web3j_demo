<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <h1>用户地址读取成功：{{account}}</h1>
    <h1>合约名称：{{ZombieFactoryAddress}}</h1>
    <h1>合约余额：{{ZombieFactoryBalance}}</h1>
    <input placeholder="输入查询余额的地址" v-model="addressBalance">
    <button @click="getBalance">查询余额</button>
    <br>
    <br>
    <br>
<!--    <button @click="pay">转账给合约</button>-->
    <br>
    <br>
    <br>
    <input placeholder="输入地址" v-model="addressSendBalance">
    <br>
    <input placeholder="转账多少wei" v-model="addressSendBalanceVar">
    <button @click="sendBalance">合约转账给谁？</button>
  </div>
</template>

<script>
import Web3 from 'web3'

export default {
  name: 'App',
  data() {
    return {
      account: '',
      addressBalance: '',
      web3: {},
      ZombieFactory:{},
      ZombieFactoryBalance: '',
      ZombieFactoryAddress: '',
      addressSendBalance: null,
      addressSendBalanceVar: '',
      itemList:null
    }
  },
  created() {
    this.init()
  },
  methods: {
    async init(){
      /* 新版的方式 */
      let _this = this
      var web3Provider;
      if (window.ethereum) {
        web3Provider = window.ethereum;
        try {
          // 请求用户授权
          window.ethereum.enable();
        } catch (error) {
          // 用户不授权时
          console.error("User denied account access")
        }
      } else if (window.web3) {   // 老版 MetaMask Legacy dapp browsers...
        web3Provider = new Web3.providers.HttpProvider('https://data-seed-prebsc-1-s1.binance.org:8545');
      } else {
        web3Provider = new Web3.providers.HttpProvider('http://localhost:8545');
      }
      this.web3 = new Web3(web3Provider);//web3js就是你需要的web3实例

      this.web3.eth.getAccounts(function (error, result) {
        if (!error) {
          //授权成功后result能正常获取到账号了
          _this.account = result[0]
        }
      });

      var abi = [{"anonymous":false,"inputs":[{"indexed":true,"internalType":"address","name":"owner","type":"address"},{"indexed":true,"internalType":"address","name":"spender","type":"address"},{"indexed":false,"internalType":"uint256","name":"value","type":"uint256"}],"name":"Approval","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"internalType":"address","name":"delegator","type":"address"},{"indexed":true,"internalType":"address","name":"fromDelegate","type":"address"},{"indexed":true,"internalType":"address","name":"toDelegate","type":"address"}],"name":"DelegateChanged","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"internalType":"address","name":"delegate","type":"address"},{"indexed":false,"internalType":"uint256","name":"previousBalance","type":"uint256"},{"indexed":false,"internalType":"uint256","name":"newBalance","type":"uint256"}],"name":"DelegateVotesChanged","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"internalType":"address","name":"previousOwner","type":"address"},{"indexed":true,"internalType":"address","name":"newOwner","type":"address"}],"name":"OwnershipTransferred","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"internalType":"address","name":"from","type":"address"},{"indexed":true,"internalType":"address","name":"to","type":"address"},{"indexed":false,"internalType":"uint256","name":"value","type":"uint256"}],"name":"Transfer","type":"event"},{"inputs":[],"name":"DELEGATION_TYPEHASH","outputs":[{"internalType":"bytes32","name":"","type":"bytes32"}],"stateMutability":"view","type":"function"},{"inputs":[],"name":"DOMAIN_TYPEHASH","outputs":[{"internalType":"bytes32","name":"","type":"bytes32"}],"stateMutability":"view","type":"function"},{"inputs":[{"internalType":"address","name":"owner","type":"address"},{"internalType":"address","name":"spender","type":"address"}],"name":"allowance","outputs":[{"internalType":"uint256","name":"","type":"uint256"}],"stateMutability":"view","type":"function"},{"inputs":[{"internalType":"address","name":"spender","type":"address"},{"internalType":"uint256","name":"amount","type":"uint256"}],"name":"approve","outputs":[{"internalType":"bool","name":"","type":"bool"}],"stateMutability":"nonpayable","type":"function"},{"inputs":[{"internalType":"address","name":"account","type":"address"}],"name":"balanceOf","outputs":[{"internalType":"uint256","name":"","type":"uint256"}],"stateMutability":"view","type":"function"},{"inputs":[{"internalType":"address","name":"","type":"address"},{"internalType":"uint32","name":"","type":"uint32"}],"name":"checkpoints","outputs":[{"internalType":"uint32","name":"fromBlock","type":"uint32"},{"internalType":"uint256","name":"votes","type":"uint256"}],"stateMutability":"view","type":"function"},{"inputs":[],"name":"decimals","outputs":[{"internalType":"uint8","name":"","type":"uint8"}],"stateMutability":"view","type":"function"},{"inputs":[{"internalType":"address","name":"spender","type":"address"},{"internalType":"uint256","name":"subtractedValue","type":"uint256"}],"name":"decreaseAllowance","outputs":[{"internalType":"bool","name":"","type":"bool"}],"stateMutability":"nonpayable","type":"function"},{"inputs":[{"internalType":"address","name":"delegatee","type":"address"}],"name":"delegate","outputs":[],"stateMutability":"nonpayable","type":"function"},{"inputs":[{"internalType":"address","name":"delegatee","type":"address"},{"internalType":"uint256","name":"nonce","type":"uint256"},{"internalType":"uint256","name":"expiry","type":"uint256"},{"internalType":"uint8","name":"v","type":"uint8"},{"internalType":"bytes32","name":"r","type":"bytes32"},{"internalType":"bytes32","name":"s","type":"bytes32"}],"name":"delegateBySig","outputs":[],"stateMutability":"nonpayable","type":"function"},{"inputs":[{"internalType":"address","name":"delegator","type":"address"}],"name":"delegates","outputs":[{"internalType":"address","name":"","type":"address"}],"stateMutability":"view","type":"function"},{"inputs":[{"internalType":"address","name":"account","type":"address"}],"name":"getCurrentVotes","outputs":[{"internalType":"uint256","name":"","type":"uint256"}],"stateMutability":"view","type":"function"},{"inputs":[],"name":"getOwner","outputs":[{"internalType":"address","name":"","type":"address"}],"stateMutability":"view","type":"function"},{"inputs":[{"internalType":"address","name":"account","type":"address"},{"internalType":"uint256","name":"blockNumber","type":"uint256"}],"name":"getPriorVotes","outputs":[{"internalType":"uint256","name":"","type":"uint256"}],"stateMutability":"view","type":"function"},{"inputs":[{"internalType":"address","name":"spender","type":"address"},{"internalType":"uint256","name":"addedValue","type":"uint256"}],"name":"increaseAllowance","outputs":[{"internalType":"bool","name":"","type":"bool"}],"stateMutability":"nonpayable","type":"function"},{"inputs":[{"internalType":"address","name":"_to","type":"address"},{"internalType":"uint256","name":"_amount","type":"uint256"}],"name":"mint","outputs":[],"stateMutability":"nonpayable","type":"function"},{"inputs":[{"internalType":"uint256","name":"amount","type":"uint256"}],"name":"mint","outputs":[{"internalType":"bool","name":"","type":"bool"}],"stateMutability":"nonpayable","type":"function"},{"inputs":[],"name":"name","outputs":[{"internalType":"string","name":"","type":"string"}],"stateMutability":"view","type":"function"},{"inputs":[{"internalType":"address","name":"","type":"address"}],"name":"nonces","outputs":[{"internalType":"uint256","name":"","type":"uint256"}],"stateMutability":"view","type":"function"},{"inputs":[{"internalType":"address","name":"","type":"address"}],"name":"numCheckpoints","outputs":[{"internalType":"uint32","name":"","type":"uint32"}],"stateMutability":"view","type":"function"},{"inputs":[],"name":"owner","outputs":[{"internalType":"address","name":"","type":"address"}],"stateMutability":"view","type":"function"},{"inputs":[],"name":"renounceOwnership","outputs":[],"stateMutability":"nonpayable","type":"function"},{"inputs":[],"name":"symbol","outputs":[{"internalType":"string","name":"","type":"string"}],"stateMutability":"view","type":"function"},{"inputs":[],"name":"totalSupply","outputs":[{"internalType":"uint256","name":"","type":"uint256"}],"stateMutability":"view","type":"function"},{"inputs":[{"internalType":"address","name":"recipient","type":"address"},{"internalType":"uint256","name":"amount","type":"uint256"}],"name":"transfer","outputs":[{"internalType":"bool","name":"","type":"bool"}],"stateMutability":"nonpayable","type":"function"},{"inputs":[{"internalType":"address","name":"sender","type":"address"},{"internalType":"address","name":"recipient","type":"address"},{"internalType":"uint256","name":"amount","type":"uint256"}],"name":"transferFrom","outputs":[{"internalType":"bool","name":"","type":"bool"}],"stateMutability":"nonpayable","type":"function"},{"inputs":[{"internalType":"address","name":"newOwner","type":"address"}],"name":"transferOwnership","outputs":[],"stateMutability":"nonpayable","type":"function"}]
      var contractAddress  = '0xe38a71627E3289D8154da871F9ecF25Cd41EB483'

      //3、根据abi获取合约
      this.ZombieFactory = new this.web3.eth.Contract(abi, contractAddress);
      console.log("合约实例==》{}",this.ZombieFactory)

      // 调用solidity合约的只读方法，并在EVM中直接执行方法，不需要发送任何交易。因此不会改变合约的状态。
      await this.ZombieFactory.methods.balanceOf("0xE4f221684EA25d65B3f85821E4086e018eDa0211").call().then((result) => {
        console.log('合约余额',result);
        _this.ZombieFactoryBalance = result
      });

      await this.ZombieFactory.methods.name().call().then((result) => {
        console.log('合约名称',result);
        _this.ZombieFactoryAddress = result
      });
    },
    pay(){
      let _this = this
      // 向solidity合约发送交易来执行指定方法，将改变合约的状态(必须输入发送交易的账户地址,需要消耗gas)
      this.ZombieFactory.methods.pay().send({value:this.addressSendBalanceVar,from: this.account}).then((result) => {
        console.log(result);
      });
      this.ZombieFactory.methods.getBalance().call().then((result) => {
        console.log('合约余额',result);
        _this.ZombieFactoryBalance = result
      });
    },
    getBalance(){
      this.ZombieFactory.methods.balanceOf(this.addressBalance).call().then((result) => {
        alert(result)
      });
    },
    sendBalance(){

      this.ZombieFactory.methods.transfer(this.addressSendBalance,this.web3.utils.toWei(this.addressSendBalanceVar,'ether')).send({
        gas: 500000,
        from: this.account,
        chainId: this.web3.utils.toHex('97'),
      }).then((result) => {
        console.log("转账成功==》"+result.transactionHash);
      });
    }
  }
}
</script>

<style>
#app{
  text-align: center;
}
</style>
