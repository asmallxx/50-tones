<template>
  <!-- 片假名 -->
  <div class="home">
    <header>
      <div class="header-left">
        <div class="item" @click="sort===1?shuffleItem():orderSort()">{{sort|sortMap}}</div>
        <div class="item" @click="type=type==='H'?'K':'H'">{{type|typeMap}}</div>
      </div>
      <div class="header-center">
        <span class="bread-img hidden">记</span>
        <span class="bread-img hidden">忆</span>
        <span class="bread-img">小</span>
        <span class="bread-img">面</span>
        <span class="bread-img">包</span>~
      </div>
      <div class="header-right">
        <i class="remove" @click="clearList()"></i>
        <i class="back-btn" @click="toBack()"></i>
      </div>
    </header>
    <main>
      <div v-show="breadList.length===0">
        <p class="tip">小面包全吃啦~</p>
        <!-- <p class="tip">去上一个页面</p>
        <p class="tip">卡片右上角添加吧！</p>-->
      </div>
      <!-- <div class="card-contain" v-show="breadList.length!==0">
        <div
          class="card"
          @click="changeShow(item)"
          :class="{'second-word':item.show}"
          v-for="item in breadList"
          :key="item.index"
        >
          <span v-if="type==='H'">{{item.show?item.K:item.H}}</span>
          <span v-if="type==='K'">{{item.show?item.H:item.K}}</span>
          <span class="eat-bread" @click.stop="removeFromBread(item)"></span>
        </div>
      </div>-->
      <transition-group v-if="breadList.length!==0" name="list" class="card-contain" tag="div">
        <div
          class="card"
          @click="changeShow(item)"
          :class="{'second-word':item.show}"
          v-for="item in breadList"
          :key="item.index"
        >
          <span v-if="type==='H'">{{item.show?item.K:item.H}}</span>
          <span v-if="type==='K'">{{item.show?item.H:item.K}}</span>
          <span class="eat-bread" @click.stop="removeFromBread(item)"></span>
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
      sort: 1,
      type: "H",
      orderList: [],
      breadList: []
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
      let { type, breadList } = JSON.parse(
        sessionStorage.getItem("homePageInfo")
      );
      this.type = type;
      this.breadList = breadList;
      this.orderList = JSON.parse(JSON.stringify(this.breadList));
    },

    shuffleItem: function() {
      this.sort = 2;
      this.breadList = _.shuffle(this.breadList);
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
      this.breadList = JSON.parse(JSON.stringify(this.orderList));
    },

    toBack() {
      let obj = JSON.parse(sessionStorage.getItem("homePageInfo"));
      obj.breadList = this.breadList;
      obj.type = this.type;
      sessionStorage.setItem("homePageInfo", JSON.stringify(obj));
      this.$router.push({
        path: "/"
      });
    },

    updateSessionStorageBreadList() {
      let obj = JSON.parse(sessionStorage.getItem("homePageInfo"));
      obj.breadList = this.breadList;
      sessionStorage.setItem("homePageInfo", JSON.stringify(obj));
    },

    clearList() {
      this.breadList = [];
      this.orderList = [];
      this.updateSessionStorageBreadList();
    },

    removeFromBread(item) {
      let index = this.breadList.indexOf(item);
      this.breadList.splice(index, 1);
      this.orderList = JSON.parse(JSON.stringify(this.breadList));
      this.updateSessionStorageBreadList();
    }
  },

  created() {
    this.initInfo();
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
    margin-left: 5%;
    display: flex;
    justify-content: flex-start;
    align-items: center;
  }

  .header-center {
    font-size: 16px;
    color: #fff;
    display: flex;
    justify-content: center;
    align-items: center;
    color: #82529d;

    .bread-img {
      display: inline-block;
      padding-right: 5px;
      margin: 1vw;
      width: 40px;
      height: 40px;
      line-height: 40px;
      text-align: center;
      background: url("../assets/image/bread-line-p.svg") center no-repeat;
      background-size: contain;
    }

    .hidden {
      display: block;
    }
  }

  .header-right {
    margin-right: 5%;
    display: flex;
    justify-content: flex-end;
    align-items: center;

    .remove {
      position: relative;
      display: inline-block;
      margin-right: 20px;
      width: 4vh;
      height: 4vh;
      background-color: #e5ebf4;
      z-index: 2;
      border-radius: 50%;
      cursor: pointer;

      &::after {
        content: "吃";
        position: absolute;
        left: 1vh;
        color: #7971c0;
        line-height: 4vh;
        width: 4vh;
        height: 4vh;
        z-index: 1;
      }
    }

    .back-btn {
      position: relative;
      display: inline-block;
      width: 6vh;
      height: 6vh;
      background-color: #e5ebf4;
      z-index: 2;
      border-radius: 50%;
      cursor: pointer;

      &::after {
        content: "back";
        position: absolute;
        top: 1vh;
        left: 50%;
        transform: translateX(-50%);
        color: #7971c0;
        text-align: center;
        line-height: 4vh;
        width: 4em;
        height: 4vh;
        z-index: 1;
      }
    }
  }

  .item {
    margin-right: 2vw;
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
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;

  .card-contain {
    margin: 20px 0;
    display: grid;
    grid-template-columns: repeat(10, 8vw);
    grid-row-gap: 10px;
    grid-column-gap: 1.5vw;
  }
  .card {
    position: relative;
    width: 100%;
    height: 12vw;
    line-height: 12vw;
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
}

.list-move {
  transition: transform 1s;
}

.list-enter,
.list-leave-to {
  opacity: 0;
  // transform: translateY(30px);
}

.eat-bread {
  position: absolute;
  top: 3px;
  right: 3px;
  color: #fff;
  width: 15px;
  height: 15px;
  border-radius: 50%;

  &::after {
    content: "";
    position: absolute;
    width: 15px;
    height: 15px;
    background: url("../assets/image/eat.svg") center no-repeat;
    background-size: contain;
    left: 50%;
    top: 42%;
    transform: translate(-50%, -50%);
  }
}

.tip {
  margin-top: 3vh;
  font-size: 20px;
  color: #c4c4e4;
}
@media screen and (max-width: 700px) {
  main {
    .card-contain {
      grid-template-columns: repeat(5, 15vw);
      grid-column-gap: 4vw;
    }

    .card {
      height: 22vw;
      line-height: 22vw;
    }
  }

  .hidden {
    display: none !important;
  }
}

@media screen and (max-width: 370px) {
  .header-center {
    display: none !important;
  }
}
</style>