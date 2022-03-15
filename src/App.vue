<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <h1>用户地址读取成功：{{account}}</h1>
    <h1>合约地址：{{ZombieFactoryAddress}}</h1>
    <h1>合约余额：{{ZombieFactoryBalance}}</h1>
    <input placeholder="输入查询余额的地址" v-model="addressBalance">
    <button @click="getBalance">查询余额</button>
    <br>
    <br>
    <br>
    <button @click="pay">转账给合约</button>
    <br>
    <br>
    <br>
    <input placeholder="输入地址" v-model="addressSendBalance">
    <button @click="sendBalance">合约转账给谁？</button>
    <br>
    <input placeholder="转账多少wei" v-model="addressSendBalanceVar">
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
      addressSendBalance: '',
      addressSendBalanceVar: '',
    }
  },
  created() {
    this.init()
  },
  methods: {
    init(){
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
        web3Provider = window.web3.currentProvider;
      } else {
        // web3Provider = new Web3.providers.HttpProvider('http://localhost:8545');
      }
      this.web3 = new Web3(web3Provider);//web3js就是你需要的web3实例

      this.web3.eth.getAccounts(function (error, result) {
        if (!error) {
          //授权成功后result能正常获取到账号了
          _this.account = result[0]
        }
      });

      var abi = [
        {
          "constant": true,
          "inputs": [],
          "name": "getBalance",
          "outputs": [
            {
              "name": "",
              "type": "uint256"
            }
          ],
          "payable": false,
          "stateMutability": "view",
          "type": "function"
        },
        {
          "constant": false,
          "inputs": [
            {
              "name": "account",
              "type": "address"
            }
          ],
          "name": "transfer",
          "outputs": [],
          "payable": true,
          "stateMutability": "payable",
          "type": "function"
        },
        {
          "constant": false,
          "inputs": [],
          "name": "pay",
          "outputs": [],
          "payable": true,
          "stateMutability": "payable",
          "type": "function"
        },
        {
          "constant": true,
          "inputs": [
            {
              "name": "account",
              "type": "address"
            }
          ],
          "name": "getrandomBalance",
          "outputs": [
            {
              "name": "",
              "type": "uint256"
            }
          ],
          "payable": false,
          "stateMutability": "view",
          "type": "function"
        },
        {
          "constant": true,
          "inputs": [],
          "name": "getThis",
          "outputs": [
            {
              "name": "",
              "type": "address"
            }
          ],
          "payable": false,
          "stateMutability": "view",
          "type": "function"
        }
      ]
      var contractAddress  = '0xA1036893411dC2f2267561F9a7BB7F0FE73C80F7'

      //3、根据abi获取合约
      this.ZombieFactory = new this.web3.eth.Contract(abi, contractAddress);
      console.log("合约实例==》{}",this.ZombieFactory)

      // 调用solidity合约的只读方法，并在EVM中直接执行方法，不需要发送任何交易。因此不会改变合约的状态。
      this.ZombieFactory.methods.getBalance().call().then((result) => {
        console.log('合约余额',result);
        _this.ZombieFactoryBalance = result
      });

      this.ZombieFactory.methods.getThis().call().then((result) => {
        console.log('合约地址',result);
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
      this.ZombieFactory.methods.getrandomBalance(this.addressBalance).call().then((result) => {
        alert(result)
      });
    },
    sendBalance(){
      this.ZombieFactory.methods.transfer(this.addressSendBalance).send({value:this.addressSendBalanceVar,from: this.account}).then((result) => {
        console.log(result);
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
