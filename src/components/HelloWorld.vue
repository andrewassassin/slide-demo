<template>
  <div class="slide">
    <div class="showImg col-md-4">
      <img width="400px" height="400px" :src="`../../static/img/${itemObj[focusIndex]}`" alt="">
    </div>
    <section class="col-md-6"> 
        <div class="slide-item">
          <transition-group name="flip-list" tag="ul" class="slide-list">
            <li v-for="(item,index) in slideData" :key="item.id">
            
                <img :src="`../../static/img/${itemObj[item.ref]}`" :name="`${itemObj[item.ref]}`" :id="`${index}`"  @click="clickImg($event,index)" alt="">
             
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
import axios from 'axios'
export default {
  data() {
    return {
      clickWait: false,
      timer: {},
      slideData: [],
      itemObj:[],
      focusIndex:1
    };
  },
  async mounted() {
     await axios.get("https://x-home.pcpogo.com/homex/product.php?RDEBUG=andrewc")
          .then(response => {
              const item = response.data.find(item=>{
                return item.id == 167
              })
              this.itemObj  = JSON.parse(item.image)         
          })
          .catch(error => {
            console.log('err',error);
          })
    for (let i = 0; i < this.itemObj.length * 3; i++) {
      let obj = {};
      obj.id = i;
      // 2%10  10除以2的餘數是什麼，在此例 ref = i
      obj.ref = i % this.itemObj.length;
      this.slideData.push(obj);
    }
  },
  methods: {
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
        // 把移除的加到最後面
        this.slideData.push(shiftItem);
        // 註解掉的話只能點一次
        this.setTime();
        // return;
      }
    },
    clickImg(event,index) {
      // 直接靠map回傳的title屬性轉成陣列，做indexOf找出點選圖片的ref
      const ref = this.itemObj.map(item => item).indexOf(event.currentTarget.name)
      // 9/2=4.5   取四捨五入為5，但陣列從0開始，故-1
      const middleImg =  Math.round(this.slideData.length/2)-1
      // 如果我點的圖片在我的(中間為4)右邊
        if(index>middleImg){
          const needToSlice = index-middleImg
                this.focusIndex = ref 
                const shiftItem = this.slideData.splice(0,needToSlice);
                this.slideData = this.slideData.concat(shiftItem);
                this.setTime();
      // 如果我點的圖片在我的左邊
          }else{
              const needToSlice = -(middleImg-index)
                this.focusIndex = ref 
                const shiftItem = this.slideData.splice(needToSlice);
                this.slideData =[...shiftItem,...this.slideData]
                this.setTime();
          }
    }
  },
};
</script>
<style scoped>
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
  bottom: 80px;
}

.slide-next{
  position: relative;
  right:420px;
  bottom: 80px;
}

.slide-prev:hover,
.slide-next:hover {
  color: #ff0;
}
/* slide */
.slide-list {
  display: flex;
  margin: 10px 0px;
  padding: 5px 0px;
  width:800px;
  height: 100px;
  /* overflow: hidden; */
}
.slide-list li {
  position: relative;
  flex: 1 0 0;
  left:calc(-100% / 40 * 5);
  opacity: 0.4;
  margin: 15px;
}

.slide-list li:nth-child(5) {
      opacity: 1;
      transform: scale(1.3);
      z-index: 2;
    }


.slide-list li:nth-child(1),
.slide-list li:nth-child(2),
.slide-list li:nth-child(8),
.slide-list li:nth-child(9)
{
   z-index: 0;
    opacity: 0;
} 

.slide-list img{
  width: 100%;
}

.flip-list-move {
  transition: transform 0.8s;
}

.slide-item{
  width: 60%;
  margin-top: 20px;
  /* margin-right: 40px; */
  background-color: #eee;
  overflow: hidden;
}
</style>
