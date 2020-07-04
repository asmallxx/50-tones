<template>
  <!-- 片假名 -->
  <div class="home">
    <header>
      <div class="header-left">
        <div
          class="item"
          :class="{'unchoose-tone':!item.show}"
          @click="changeToneShow(item)"
          v-for="(item,index) in currentTones"
          :key="index"
        >{{item.name}}</div>
        <div class="last-tone"></div>
        <div class="item" @click="sort===1?shuffleItem():orderSort()">{{sort|sortMap}}</div>
        <div class="item" @click="type=type==='H'?'K':'H'">{{type|typeMap}}</div>
      </div>
      <div class="header-right"></div>
    </header>
    <main>
      <transition-group name="list" class="card-contain" tag="div">
        <div
          class="card"
          @click="changeShow(item)"
          :class="{'second-word':item.show}"
          v-for="(item) in wordList"
          :key="item.index"
        >
          <span v-if="type==='H'">{{item.show?item.K:item.H}}</span>
          <span v-if="type==='K'">{{item.show?item.H:item.K}}</span>
        </div>
      </transition-group>
    </main>
  </div>
</template>

<script>
import { allHWord, allKWord } from "../service/utils/word";
var _ = require("lodash");
export default {
  data() {
    return {
      currentTones: [
        { name: "50", show: true, type: 1 },
        { name: "浊", show: false, type: 2 },
        { name: "拗", show: false, type: 3 }
      ],
      currentTonesType: [],
      sort: 1,
      type: "H",
      orderList: [],
      wordList: [],
      textList: []
    };
  },

  filters: {
    sortMap(val) {
      return val === 1 ? "顺" : "乱";
    },
    typeMap(val) {
      return val === "H" ? "平" : "片";
    }
  },

  methods: {
    initInfo() {
      // 初始化type列表
      this.currentTonesType = this.currentTones
        .filter(e => e.show)
        .map(e => e.type);
      this.wordList = [];
      let Hword = allHWord
        .filter(e => this.currentTonesType.includes(e.type))
        .map(e => e.word);
      let Kword = allKWord
        .filter(e => this.currentTonesType.includes(e.type))
        .map(e => e.word);

      for (let i = 0; i < Hword.length; i++) {
        this.wordList.push({
          H: Hword[i],
          K: Kword[i],
          index: i,
          show: false
        });
      }
      this.orderList = JSON.parse(JSON.stringify(this.wordList));
    },

    shuffleItem: function() {
      this.sort = 2;
      this.wordList = _.shuffle(this.wordList);
    },

    changeShow(item) {
      if (!item.show) {
        item.show = true;
        setTimeout(() => {
          item.show = false;
        }, 2000);
      }
    },

    orderSort() {
      this.sort = 1;
      //   this.initInfo();
      this.wordList = JSON.parse(JSON.stringify(this.orderList));
    },

    changeToneShow(item) {
      item.show = !item.show;
      this.sort = 1;
      this.type = "H";
      this.initInfo();
    },

    creatinfo() {
      let arr = [];
      for (let i = 0; i < allKWord.length; i++) {
        arr.push({
          type: allKWord[i].type,
          word: allKWord[i].word,
          index: i + 1
        });
      }
    }
  },

  created() {
    this.initInfo();
    // this.creatinfo();
  }
};
</script>

<style lang="less" scoped>
@buleColor: #0000ff;
header {
  position: sticky;
  top: 0;
  display: flex;
  justify-content: space-between;
  align-items: center;
  height: 8vh;
  background-color: #b6b2dc;

  .header-left {
    flex: 6;
    margin-left: 5%;
    display: flex;
    justify-content: flex-start;
    align-items: center;
  }

  .header-right {
    flex: 1;
    margin-right: 5%;
    display: flex;
    justify-content: flex-end;
    align-items: center;
  }

  .item {
    margin-right: 5%;
    height: 4vh;
    line-height: 4vh;
    width: 4vh;
    text-align: center;
    border-radius: 2vh;
    color: #e3ebf5;
    background: rgb(121, 113, 192);
    cursor: pointer;
    transition: 0.3s;
  }

  .unchoose-tone {
    color: rgb(121, 113, 192);
    background: #e3ebf5;
  }

  .last-tone {
    margin-right: 5%;
    width: 1px;
    height: 4vh;
    background-color: #7971c0;
  }
}

main {
  .card-contain {
    display: flex;
    justify-content: flex-start;
    flex-wrap: wrap;
    //   align-items: center;
  }
  .card {
    margin: 0.2rem 0.2rem 0.2rem;
    width: 0.4rem;
    height: 0.5rem;
    line-height: 0.5rem;
    text-align: center;
    font-size: 0.2rem;
    color: rgb(113, 155, 192);
    background-color: rgb(219, 238, 255);
    border: 1px solid rgb(113, 155, 192);
    border-radius: 8px;
    cursor: pointer;
  }

  .second-word {
    color: rgb(121, 113, 192);
    background-color: fadeout(@buleColor, 90%);
    border: 1px solid rgb(121, 113, 192);
  }
}

.card {
  transition: all 1s;
  display: inline-block;
  margin-right: 10px;
}
.list-move {
  transition: transform 1s;
}
</style>