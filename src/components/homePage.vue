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

      <div class="header-right">
        <i class="memory" @click="toReview()"></i>
      </div>
    </header>
    <main>
      <div class="tip" v-show="wordList.length===0">
        <p>左上角选择类型哦~</p>
      </div>
      <transition-group v-show="wordList.length!==0" name="list" class="card-contain" tag="div">
        <div
          class="card"
          @click="changeShow(item)"
          :class="{'second-word':item.show}"
          v-for="(item) in wordList"
          :key="item.index"
        >
          <span v-if="type==='H'">{{item.show?item.K:item.H}}</span>
          <span v-if="type==='K'">{{item.show?item.H:item.K}}</span>
          <span v-show="!item.bread" class="add-to-bread" @click.stop="addToBread(item)"></span>
          <span v-show="item.bread" class="alread-add" @click.stop="removeFromBread(item)"></span>
        </div>
      </transition-group>
    </main>
  </div>
</template>

<script>
import { allWord } from "../service/utils/word";
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
    //检查小面包页面是否吃掉
    checkBreadInfo() {
      let { type, breadList, currentTones } = JSON.parse(
        sessionStorage.getItem("homePageInfo")
      );
      console.log(JSON.parse(sessionStorage.getItem("homePageInfo")));
      this.type = type;
      this.breadList = breadList;
      this.currentTones = currentTones;
    },

    // 初始化列表信息
    initInfo() {
      this.currentTonesType = this.currentTones
        .filter(e => e.show)
        .map(e => e.type);
      this.wordList = allWord.filter(e =>
        this.currentTonesType.includes(e.type)
      );
      this.initListBread();
      this.orderList = JSON.parse(JSON.stringify(this.wordList));
    },

    initListBread() {
      this.wordList.forEach(e => {
        if (this.breadList.map(i => i.index).includes(e.index)) {
          e.bread = true;
        } else {
          e.bread = false;
        }
      });
    },

    // 列表乱序
    shuffleItem: function() {
      this.sort = 2;
      this.wordList = _.shuffle(this.wordList);
    },

    // 列表顺序
    orderSort() {
      this.sort = 1;
      this.wordList = JSON.parse(JSON.stringify(this.orderList));
    },

    // 查看对应平假或者片假
    changeShow(item) {
      if (!item.show) {
        item.show = true;
        setTimeout(() => {
          item.show = false;
        }, 2000);
      }
    },

    // 选择音的对应类型显示
    changeToneShow(item) {
      item.show = !item.show;
      this.sort = 1;
      this.type = "H";
      if (!item.show) {
        this.breadList = this.breadList.filter(e => e.type !== item.type);
      }
      this.updateSessionStorageBreadList();
      this.initInfo();
    },

    updateSessionStorageBreadList() {
      if (sessionStorage.getItem("homePageInfo")) {
        let obj = JSON.parse(sessionStorage.getItem("homePageInfo"));
        obj.breadList = this.breadList;
        sessionStorage.setItem("homePageInfo", JSON.stringify(obj));
      } else {
        this.breadList.forEach(e => (e.show = false));
        let obj = {
          breadList: this.breadList,
          type: this.type,
          currentTones: this.currentTones
        };
        sessionStorage.setItem("homePageInfo", JSON.stringify(obj));
      }
    },

    // 去记忆小面包页面
    toReview() {
      this.breadList.forEach(e => (e.show = false));
      let obj = {
        breadList: this.breadList,
        type: this.type,
        currentTones: this.currentTones
      };
      sessionStorage.setItem("homePageInfo", JSON.stringify(obj));
      this.$router.push({
        path: "/breadPage"
      });
    },

    // 添加小面包
    addToBread(item) {
      item.bread = true;
      this.breadList.push(item);
      this.updateSessionStorageBreadList();
    },

    //取消添加小面包
    removeFromBread(item) {
      item.bread = false;
      let index = this.breadList.indexOf(item);
      this.breadList.splice(index, 1);
      this.updateSessionStorageBreadList();
    }
  },

  created() {
    if (sessionStorage.getItem("homePageInfo")) {
      console.log(JSON.parse(sessionStorage.getItem("homePageInfo")));
      this.checkBreadInfo();
    }
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
  z-index: 3;

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

    .memory {
      position: relative;
      display: inline-block;
      width: 6vh;
      height: 6vh;
      background-color: #e5ebf4;
      border-radius: 50%;
      cursor: pointer;

      &::after {
        content: "";
        position: absolute;
        top: 1vh;
        left: 1vh;
        background: url("../assets/image/bread-whole.svg") center no-repeat;
        background-size: contain;
        width: 4vh;
        height: 4vh;
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
    margin-right: 2vw;
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

  .add-to-bread {
    position: absolute;
    top: 0;
    right: 3px;
    color: #fff;
    width: 15px;
    height: 15px;
    border-radius: 50%;

    &::after {
      content: "+";
      position: absolute;
      right: 0;
      top: 3px;
      line-height: 15px;
      width: 0.15rem;
      height: 0.15rem;
    }
  }

  .alread-add {
    position: absolute;
    top: 2px;
    right: 5px;
    color: #fff;
    width: 0.16rem;
    height: 0.16rem;
    border-radius: 50%;

    &::after {
      content: "";
      position: absolute;
      width: 0.16rem;
      height: 0.16rem;
      left: 50%;
      top: 50%;
      transform: translate(-50%, -50%);
      background: url("../assets/image/bread-slice.svg") center no-repeat;
      background-size: contain;
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

.tip {
  p {
    margin-top: 3vh;
    font-size: 20px;
    color: #c4c4e4;
  }
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
}
</style>