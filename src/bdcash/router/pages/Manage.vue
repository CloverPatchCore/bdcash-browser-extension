<template>
  <div id="managepage">
      <i v-on:click="showDashboard" class="fa fa-arrow-left back"></i>   
      <i v-on:click="showCreateIdentity" class="fas fa-plus new"></i>
      <div class="header">
        <strong>Manage IDs</strong>
      </div> 
      <div v-if="wallets">
        <div v-for="wallet in wallets" v-bind:key="wallet.pubkey">
          <b-card class="mb-2 mt-2">
            <b-card-text>
              <v-gravatar :email="wallet.pubkey" style="float:left; width:40px; height:40px; margin-right:10px; border-radius:4px;" />
              <strong>{{ wallet.label }}</strong>
              <p>{{ wallet.pubkey }}</p>
            </b-card-text>
            <a v-on:click="useIdentity(wallet.label)" class="fa fa-arrow-right arrow"></a>
          </b-card>
        </div>
      </div>
      <b-button variant="success" style="width:100%" v-on:click="openimport">IMPORT IDENTITY</b-button>
  </div>
</template>

<script>
export default {
  data () {
    return {
      wallets: [],
      bdcash: window.BDCashCore,
      localStorage: window.localStorage,
    }
  },
  methods: {
    showDashboard(){
      const app = this
      app.$router.push('dashboard')
    },
    showCreateIdentity(){
      const app = this
      app.$router.push('create')
    },
    openimport(){
      var isFirefox = typeof InstallTrigger !== 'undefined';
      var tablink
      if(!isFirefox){
        let id = chrome.runtime.id
        tablink = 'chrome-extension://' + id + '/bdcash/id.html#/import'
      }else{
        tablink = "./id.html#/import"
      }
      let createData = {
        type: "panel",
        url: tablink,
        width: 500,
        height: 400
      };
      let creating = browser.windows.create(createData);
    },
    useIdentity(label){
      const app = this
      app.localStorage.setItem('$BDCASH_lastid', label)
      app.$router.push('dashboard')
    }
  },
  mounted (){
    const app = this
    var wallets = localStorage.getItem('$BDCASH_ids');
    wallets = JSON.parse(wallets);
    if(wallets === null || wallets.length === 0){
      app.$router.push('index')
    }else{
      for(var key in wallets){
        var split = wallets[key].split(':')
        var wallet = {
          label: key,
          pubkey: split[0],
          wallet: split[1]
        }
        app.wallets.push(wallet)
      }
    }
  }
}
</script>

<style lang="scss" scoped>
  #managepage{
    width:400px;
  }
  p {
    font-size: 11px;
    margin-bottom:0px!important;
  }
  strong{
    font-size:14px;
  }
  a:hover{
    cursor: pointer!important;
  }
  .new{
    position:absolute; 
    top:20px; 
    color:#666; 
    right:20px;
  }
  .new:hover{
    color:#000;
    cursor:pointer;
  }
</style>
