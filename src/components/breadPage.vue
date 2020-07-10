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
        <i class="remove" @click="breadList=[]"></i>
        <i class="back-btn" @click="toBack()"></i>
      </div>
    </header>
    <main>
      <transition-group name="list" class="card-contain" tag="div">
        <div
          class="card"
          @click="changeShow(item)"
          :class="{'second-word':item.show}"
          v-for="item in breadList"
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
      //   currentTones: [
      //     { name: "50", show: true, type: 1 },
      //     { name: "浊", show: false, type: 2 },
      //     { name: "拗", show: false, type: 3 }
      //   ],
      //   currentTonesType: [],
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
      this.type = this.$route.query.type;
      this.breadList = JSON.parse(this.$route.query.list);
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
      this.$router.push({
        path: "/",
        query: {
          breadList: JSON.stringify(this.breadList),
          type: this.$route.query.type
        }
      });
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
        content: "丢";
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

  .add-to-bread {
    position: absolute;
    top: 0;
    right: 3px;
    color: #fff;
    width: 15px;
    height: 15px;
    border-radius: 50%;
    // background-color: #dd9ebd;

    &::after {
      content: "+";
      position: absolute;
      width: 15px;
      height: 15px;
      line-height: 15px;
      left: 50%;
      top: 42%;
      transform: translate(-50%, -50%);
    }
  }

  .alread-add {
    position: absolute;
    top: 0;
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
      background: url("../assets/image/bread.svg") center no-repeat;
      background-size: contain;
      left: 50%;
      top: 42%;
      transform: translate(-50%, -50%);
    }
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