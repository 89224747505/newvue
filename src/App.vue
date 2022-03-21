<template>
  <label>Text: </label>
  <input @keydown.enter="addTicker" v-model="ticker" type="text"/>
  <button @click="addTicker" class="btn">Добавить</button>
  <hr>
  <div class="wrapper">
    <div class="ticketMessage" v-for="item in tickers" :key="item.id" @click="graphState(1, item.id)">
      <div class="ticketMessage__name">{{ item.name }}</div>
      <div class="ticketMessage__price">
        <div class="tMessPrice__label">Цена валюты</div>
        <br>
        <div class="tMessPrice__value">{{ item.price }}</div>
      </div>
      <button @click.stop="deleteItem(item)">Удалить</button>
    </div>
  </div>
  <template v-if="graphVisible">
    <button class="graph_close" @click="graphState(0)">x</button>
    <div class="graph">
      <div
          class="graph__item"
          v-for="line in graph"
          :key="line.id"
          :style="{height:`${modificatedDataGraph(line)}%`}"
      ></div>
    </div>
  </template>
</template>

<script>

export default {
  name: "App",
  data() {
    return {
      ticker: "",
      tickers: [],
      index:0,
      graph:[],
      graphVisible: false,
      changedTicket:0,
    }
  },
  methods: {
    addTicker() {
      if (this.ticker.length > 4) this.ticker=this.ticker.slice(0,3);
      if (this.ticker === "") return
      const tickerObjForArray = {id:this.index, name: this.ticker.toUpperCase()+'-USD', price: '-'}
      this.index++;
      this.tickers.push(tickerObjForArray);
      const newTicker = this.ticker;
      this.ticker = "";
      setInterval(async()=>{
          const statusFromCryptoExchange = await fetch(`https://min-api.cryptocompare.com/data/price?fsym=${newTicker.toUpperCase()}&tsyms=USD&api_key=406466565df21086870b450e984fce583801468b61e16dd559eaaa0530ceb006`)
          const dataFromResponse = await statusFromCryptoExchange.json();
          this.tickers.find(t => t.name === tickerObjForArray.name).price = dataFromResponse.USD;

          if (this.graphVisible && (this.changedTicket === tickerObjForArray.id)) {
            if (dataFromResponse.USD >= 1) {
              const graphElement = Math.floor(dataFromResponse.USD);
              this.graph.push(graphElement);
            }
            if (dataFromResponse.USD < 1) {
              const graphElement = Math.floor(dataFromResponse.USD*10000);
              this.graph.push(graphElement);
            }

          }

          },10000);
    },

    modificatedDataGraph(item) {
        const min = Math.min.apply(null, this.graph);
        const max = Math.max.apply(null, this.graph);
        let result = (item - min)*100 / (max - min);
        if (min === max) result = 100;
        return result;
    },

    deleteItem(ticketItem) {
      this.tickers = this.tickers.filter(value => value.id !== ticketItem.id);
    },
    graphState(state, id) {
      if (state) {
        this.graphVisible = true;
        this.changedTicket = id;
        this.graph = [];
      }
      else {
        this.graphVisible = false;
        this.graph = [];
        this.changedTicket = 0;
      }
    }
  }
}
</script>

<style>
.btn {
  margin-left: 50px;
}

.wrapper {
  display: flex;
  flex-wrap: wrap;
}

.ticketMessage {
  border: 1px solid #000;
  width: 200px;
  height: 200px;
  display: flex;
  flex-direction: column;
  align-items: center;
}
.ticketMessage:hover {
  background: antiquewhite;
  cursor: pointer;
}
.ticketMessage__name {
  color: brown;
  font-size: 30px;
  font-weight: 700;
  padding-bottom: 30px;
}
.ticketMessage__price {
  display: flex;
  flex-direction: column;
  align-items: center;
}
.tMessPrice__label {
  color: black;
  font-size: 20px;
  font-weight: 900;
}
.tMessPrice__value {
  padding-bottom: 20px;
  font-size: 36px;
  font-weight: 900;
}

.graph_close {
  margin: 10px 0;
  color: black;
  font-weight: 700;
}

.graph {
  height: 300px;
  width: 100%;
  border-bottom: 1px solid cadetblue;
  border-left: 1px solid cadetblue;
  display: flex;
  align-items: end;
}
.graph__item {
  background: cornflowerblue;
  width: 5px;
}
</style>
