<template>
  <div class="slide">
    <div class="showImg">
      <img src="" alt="">
    </div>
    <section class="container">
      <div class="row">
        <transition-group name="flip-list" tag="ul" class="slide-list">
          <li v-for="item in slideData" :key="item.id">
            <article class="slide-article">
              <img :src=" `${ welfare[item.ref].title }`" alt="">
            </article>
          </li>
        </transition-group>
      </div>
    </section>
     <div class="slide-ctrl">
      <div class="slide-prev" @click="slideCtrl(1)">Prev</div>
      <div class="slide-next" @click="slideCtrl(-1)">Next</div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      clickWait: false,
      timer: {},
      slideData: [],
      welfare: [
        {
          title: "../../static/img/gallery-image-1.jpg",
        },
        {
          title: "../../static/img/gallery-image-2.jpg",
        },
        {
          title: "../../static/img/gallery-image-3.jpg",
        }
      ],
    };
  },
  mounted() {
    for (let i = 0; i < this.welfare.length * 3; i++) {
      let obj = {};
      obj.id = i;
      obj.ref = i % this.welfare.length;
      // console.log('obj',obj)
      this.slideData.push(obj);
    }
  },
  methods: {
    copyData() {
      const arr = [];
      for (let i = 0; i < this.welfare.length * 2; i++) {
        let obj = {};
        obj.id = i;
        obj.ref = i % this.welfare.length;
        this.arr.push(obj);
      }
      return arr;
    },
    setTime() {
      this.timer = setTimeout(() => {
        this.clickWait = false;
      }, 500);
    },
    stopTime() {
      window.clearInterval(this.timer);
    },
    // 既然上面call slideCtrl帶1進去，為何這邊強制slidesToShow = 1
    slideCtrl(slidesToShow=1) {
    //  console.log('slidesToShow',slidesToShow)
      // console.log('this.clickWait',this.clickWait)
      if (this.clickWait) {
        return;
      }
      this.stopTime();
      this.clickWait = true;
      //  console.log('slideData',this.slideData)
      if (slidesToShow > 0) {
        // 回傳移除的第一個item
        const shiftItem = this.slideData.shift();
        console.log('shiftItem',shiftItem)
        // 把移除的加到最後面
        this.slideData.push(shiftItem);
        // 註解掉的話只能點一次
        this.setTime();
        return;
      }
      if (slidesToShow < 0) {
        // 移除最後一個
        const shiftItem = this.slideData.pop();
        this.slideData.unshift(shiftItem);
        this.setTime();
      }
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped >
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
a {
  color: #42b983;
}
/* slide ctrl */
.slide-prev,
.slide-next {
  user-select: none;
  width: 150px;
  display: inline-block;
  background-color: #000;
  color: #fff;
  margin: 0 10px;
  padding: 5px 15px;
  border-radius: 50px;
  cursor: pointer;
}

.slide-prev:hover,
.slide-next:hover {
  color: #ff0;
}
/* slide */
.slide {
  width: 100%;
  overflow: hidden;
}
.slide-list {
  display: flex;
  margin: 10px 10px;
  overflow: hidden;
}
.slide-list li {
  position: relative;
  flex: 2 1 0;
  left: calc(-100% / 2 * 2);
}

.slide-list li:nth-child(5) .slide-article {
      background-color: #ff0;
    }


.slide-list li:first-child,
.slide-list li:last-child{
   z-index: -1;
    opacity: 0;
}

.slide-article img{
  width: 100%;
}

.slide-article {
  background-color: #eee;
  padding-top: 10px;
  height: 100%;
  width: 300px;
  margin: 30px;
  border-radius: 10px;
  box-sizing: border-box;
  padding: 10px;
}
.flip-list-move {
  transition: transform 0.8s;
}
</style>
