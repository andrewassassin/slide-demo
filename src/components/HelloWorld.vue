<template>
  <div class="slide">
    <div class="showImg col-md-6 mb-2">
      <img width="400px" height="400px"   :src="`${product[focusIndex].title}`" alt="">
    </div>
    <section class="col-md-6">
      <div class="container">
        <div class="row slide-item">
          <transition-group name="flip-list" tag="ul" class="slide-list">
            <li v-for="(item,index) in slideData" :key="item.id">
              <article class="slide-article">
                <img :src=" `${ product[item.ref].title }`" :name="`${product[item.ref].title}`" :id="`${index}`"  @click="clickImg($event,index)" alt="">
              </article>
            </li>
          </transition-group>
        </div>
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
      product: [
        {
          // 綠色 =0
          title: "../../static/img/gallery-image-1.jpg",
        },
        {
          // 黃色 = 1
          title: "../../static/img/gallery-image-2.jpg",
        },
        {
          // 白色 = 2
          title: "../../static/img/gallery-image-3.jpg",
        }
      ],
      focusIndex:1
    };
  },
  mounted() {
    for (let i = 0; i < this.product.length * 3; i++) {
      let obj = {};
      obj.id = i;
      // 2%10  10除以2的餘數是什麼，在此例 ref = i
      obj.ref = i % this.product.length;
      this.slideData.push(obj);
    }
  },
  methods: {
    copyData() {
      const arr = [];
      for (let i = 0; i < this.product.length * 2; i++) {
        let obj = {};
        obj.id = i;
        obj.ref = i % this.product.length;
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

      if (this.clickWait) {
        return;
      }
      this.stopTime();
      this.clickWait = true;
      //  console.log('slideData',this.slideData)
      if (slidesToShow > 0) {
          if(this.focusIndex>0 ){
        this.focusIndex=this.focusIndex-1
          }else if(this.focusIndex==0){
            this.focusIndex += 2
          }
    
          // 移除最後一個
          const shiftItem = this.slideData.pop();
          this.slideData.unshift(shiftItem);
          this.setTime();
          return;
      }
      if (slidesToShow < 0) {
         if(this.focusIndex<2){
          this.focusIndex++
        }else if(this.focusIndex == 2){
          this.focusIndex=this.focusIndex-2
          console.log('this.focusIndex 內',this.focusIndex)
        }

        const shiftItem = this.slideData.shift();
        console.log('shiftItem',shiftItem)

        // 把移除的加到最後面
        this.slideData.push(shiftItem);
        // 註解掉的話只能點一次
        this.setTime();
        // return;
      }
    },
    clickImg(event,index) {
      // click 哪張圖就會show哪張
      console.log('index',event.currentTarget.id)
      console.log('slideData',this.slideData)
      // 直接靠map回傳的title屬性轉成陣列，做indexOf
      const ref = this.product.map(item => item.title).indexOf(event.currentTarget.name)
      // 如果我點的圖片ref大於現在置中的圖片，以及我點的圖片在我的右邊
        if(ref > this.focusIndex && index>4 ){
              this.focusIndex = ref 
              if(index==6){
                const shiftItem = this.slideData.splice(0,2);
                  this.slideData.push(shiftItem[0],shiftItem[1]);
                   this.setTime();
                   return;
              }
              const shiftItem = this.slideData.shift();
              console.log('shiftItem',shiftItem)

              // 把移除的加到最後面
              this.slideData.push(shiftItem);
              // 註解掉的話只能點一次
              this.setTime();
        // 如果我點的圖片ref大於現在置中的圖片，但是我點的圖片在我的左邊
          }else if(ref > this.focusIndex && index<4){
             this.focusIndex = ref 
              const shiftItem = this.slideData.pop();
              this.slideData.unshift(shiftItem);
              this.setTime();
              return;
          }else if(ref < this.focusIndex && index<4){
              this.focusIndex = ref 
              const shiftItem = this.slideData.pop();
              this.slideData.unshift(shiftItem);
              this.setTime();
              return;
          }else if(ref ==  this.focusIndex){
              return;
          }else{
               this.focusIndex = ref 
              const shiftItem = this.slideData.shift();
              console.log('shiftItem',shiftItem)

              // 把移除的加到最後面
              this.slideData.push(shiftItem);
              // 註解掉的話只能點一次
              this.setTime();
          }
   
    
    }
  },
};
</script>
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
  width: 50px;
  display: inline-block;
  background-color: #000;
  color: #fff;
  margin: 0 10px;
  padding: 5px 15px;
  border-radius: 50px;
  cursor: pointer;
}

.slide-prev{
  position: relative;
  left: -880px;
  bottom: 100px;
}

.slide-next{
  position: relative;
  right: 50px;
  bottom: 100px;
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
  margin: 10px 5px;
  /* width:60%; */
  overflow: hidden;
}
.slide-list li {
  position: relative;
  flex: 1 0 0;
  left: calc(-100% / 8.5 * 4);
  opacity: 0.4;
  
}

.slide-list li:nth-child(5) {
      opacity: 1;
      transform: scale(1.3);
    }


.slide-list li:first-child,
.slide-list li:last-child{
   z-index: -1;
    opacity: 0;
}

.slide-article img{
  width: 60%;
}

.slide-article {
  /* position: relative; */
  background-color: #eee;
  /* left: 50%; */
  padding-top: 10px;
  height: 80%;
  width: 200px;
  margin: 20px 0px;
  border-radius: 10px;
  box-sizing: border-box;
  padding: 10px;
}
.flip-list-move {
  transition: transform 0.8s;
}

.slide-item{
  margin-top: 20px;
  background-color: #eee;
  /* height: 300px; */
}
</style>
