<template>
<div class="mx-auto pb-5 pt-4  px-10 bg-primary text-quaternary font-body font-normal">
<h1 class="text-blue-400 text-xl text-quaternary font-bold uppercase pb-4">Post ETH merge GPU profitability calculator (in fiat)</h1>
<p class="">Non ETH and ETC GPU minable coins (comma-separated):
    <br><textarea  class="bg-secondary rounded-lg w-64 lg:w-80 h-24 text-justify " v-model="CoinInput" placeholder="Example: RVN,ERG,FIRO"></textarea></p>
<p class="text-xs font-light">**default coins: "SERO,CTXC,ERG,RVN,CFX,LOG,FIRO,BTG,AE,BEAM,ZCL,QKC,FLUX,AION"</p>
<button class="mt-5 p-3 bg-secondary rounded-lg hover:bg-tertiary hover:text-secondary" @click="FetchReward()">Calculate</button>
</div>

<div class="mx-auto py-5 px-10 bg-secondary text-quaternary font-body font-normal">
<h1 class="text-xl font-bold uppercase pb-2" >Daily Reward For Miner</h1>
<p>Accumulated Fiat Reward: {{AccumulatedFiatReward.toFixed(2).replace(/\B(?=(\d{3})+(?!\d))/g, ",")}} USD</p>
<p>ETH Reward: {{EthReward.toFixed(2).toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",")}} USD</p>
<p>Accumulated Fiat Reward without ETH: {{AccumulatedFiatRewardNoEth.toFixed(2).replace(/\B(?=(\d{3})+(?!\d))/g, ",")}} USD 
({{(AccumulatedFiatRewardNoEth/AccumulatedFiatReward*100).toFixed(2).replace(/\B(?=(\d{3})+(?!\d))/g, ",")}}%)</p>
<p>ETC Reward: {{EtcReward.toFixed(2).replace(/\B(?=(\d{3})+(?!\d))/g, ",")}} USD ({{(EtcReward/AccumulatedFiatReward*100).toFixed(2).replace(/\B(?=(\d{3})+(?!\d))/g, ",")}}%)</p>
<p>Accumulated Fiat Reward without ETH and ETC: {{(AccumulatedFiatRewardNoEth - EtcReward).toFixed(2).replace(/\B(?=(\d{3})+(?!\d))/g, ",")}} USD 
({{((AccumulatedFiatRewardNoEth - EtcReward)/AccumulatedFiatReward*100).toFixed(2).replace(/\B(?=(\d{3})+(?!\d))/g, ",")}}%)</p>
</div>



<div class="mx-auto px-10 py-5 bg-primary text-quaternary font-body font-normal flex justify-between">
<p>Source: <a href="https://minerstat.com/">minerstat.com</a></p> 
<p>By BKminer</p>
</div>

</template>

<script>
import "./assets/css/tailwind.css"
export default {
    name: "App",
    data(){
       return{ DataCoin : 0,
                FiatReward : [],
                AccumulatedFiatReward : 0,
                AccumulatedFiatRewardNoEth : 0,
                EthReward : 0,
                EtcReward : 0,
                CoinInput : "SERO,CTXC,ERG,RVN,CFX,LOG,FIRO,BTG,AE,BEAM,ZCL,QKC,FLUX,AION",
                }
    },
    methods:{
        FetchReward(){

            this.DataCoin = 0
            this.AccumulatedFiatReward = 0
            this.AccumulatedFiatRewardNoEth = 0

            fetch("https://api.minerstat.com/v2/coins?list=ETH,ETC," + this.CoinInput)
            .then(response => response.json())
            .then(data => this.DataCoin = data)
            .then(this.CalculateReward)
        },
        CalculateReward(){
            this.EthReward = this.DataCoin[0].reward * this.DataCoin[0].price * this.DataCoin[0].network_hashrate * 24,
            this.EtcReward = this.DataCoin[1].reward * this.DataCoin[1].price * this.DataCoin[1].network_hashrate * 24,
            this.AccumulatedFiatRewardNoEth -= this.EthReward
            this.DataCoin.forEach(element => {
                console.log(element.name + " " + (element.reward * element.price * element.network_hashrate * 24) + "USD/Day")
                this.FiatReward.push(element.name + " " + (element.reward * element.price * element.network_hashrate * 24) + "USD/Day")
                let FiatRewardOfThisCoin = (element.reward * element.price * element.network_hashrate * 24)
                this.AccumulatedFiatReward += FiatRewardOfThisCoin
                this.AccumulatedFiatRewardNoEth += FiatRewardOfThisCoin
            },
            
            );
                
        
        }
    },

}
</script>

<style>

</style>