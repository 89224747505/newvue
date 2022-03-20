<template>
  <label>Text: </label>
  <input @keydown.enter="addTicker" v-model="ticker" type="text"/>
  <button @click="addTicker" class="btn">Добавить</button>
  <hr>
  <div class="wrapper">
    <div class="ticketMessage" v-for="item in tickers" :key="item.id">
      <div class="ticketMessage__name">{{ item.name }}</div>
      <div class="ticketMessage__price">
        <div class="tMessPrice__label">Цена валюты</div>
        <br>
        <div class="tMessPrice__value">{{ item.price }}</div>
      </div>
      <button @click="deleteItem(item)">Удалить</button>
    </div>
  </div>
</template>

<script>

export default {
  name: "App",
  data() {
    return {
      ticker: "",
      tickers: [],
      index:0,
    }
  },
  methods: {
    addTicker() {
      if (this.ticker.length > 4) this.ticker=this.ticker.slice(0,3);
      if (this.ticker == "") return
      const tickerObjForArray = {id:this.index, name: this.ticker.toUpperCase()+'-USD', price: '49560.12'}
      this.index++;
      this.tickers.push(tickerObjForArray);
      this.ticker = "";
    },
    deleteItem(ticketItem) {
      this.tickers = this.tickers.filter(value => value.id !== ticketItem.id);
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
</style>
