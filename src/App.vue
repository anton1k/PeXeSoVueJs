<template>
<div class="container">
  <div class="app row justify-content-around">
    <div class=" col-xl-3">
      <app-cartcount :count="count"
                     :formtime="formtime"
                     :timres="timres"></app-cartcount>
      <div v-show="btnleftshow">
        <button @click="restart"
                  class="btn-restart btn-left"> 
                  Сыграть еще </button>
      </div>
    </div>
    
    <div class="game col-xl-7 row  justify-content-around">
      <div class="block col-2" 
          v-for="(card, index) in cardclass" 
          :key="index">
           
            <div class="contblock">
               <div class="shirt"
                    @click="onClick($event, index)"
                    ></div>
               <transition :css="false"
                           @enter="enter"
                           @leave="leave"
                           @after-enter="afterEnter"
                      >
                  <div v-show="showlist[index]" 
                        class="cart" 
                        :class="cardclass[index]"
                        ></div>
               </transition>
            </div>
           
      </div>

    </div>
     
  </div>

  <div class="modal" v-show="modalshow">
    <transition enter-active-class="animated rubberBand">
      <div class="modal-window" v-show="modalshow">

        <h2>Ваши результаты!</h2>
        <hr>

        <p v-for="(time, index) in timres" :key="index">Время игры №{{index+1}}: {{time}}</p>
        <div class="close" 
            @click="btnleft">
          <span></span>
          <span></span>
        </div>
        <hr>

        <button @click="restart"
                class="btn-restart"> 
                Сыграть еще </button>
      </div>
    </transition>
  </div>

</div>
</template>

<script>
import AppCartcount from './components/Cartcount'


export default {
  name: 'cart',
  components: {
    AppCartcount
  },
  data() {
    return {
      cards: 10,
      count: 20,
      cardclass: [],
      cnt: 0,
      obj: [{
        first: '',
        last: ''
      }],
      cardlist: ['j','b2','b3','b4','b5','b6','b7','b8','b9','b10','bv','bd','bk','bt','c2','c3','c4','c5','c6','c7','c8','c9','c10','cv','cd','ck','ct','p2','p3','p4','p5','p6','p7','p8','p9','p10','pv','pd','pk','pt','t2','t3','t4','t5','t6','t7','t8','t9','t10','tv','td','tk','tt'],
      cardlistcopy: [],
      showlist: [],
      time: 0,
      timres: [],
      timestart: false,
      formtime: '00:00',
      stoptimer: '',
      modalshow: false,
      btnleftshow: false
    }
  },
  created(){
    for (let i = 0; i < this.cardlist.length; i++) {
      this.cardlistcopy.push(this.cardlist[i]);
    }
    for (let i = 0; i < this.cards; i++) {
      let j = random(0, this.cardlistcopy.length, false)
      this.cardclass.push(this.cardlistcopy[j]);
      this.cardclass.push(this.cardlistcopy[j]);
      this.cardlistcopy.splice(j, 1);
      this.showlist.push(false);
      this.showlist.push(false);
    }
    this.cardclass.sort(compareRandom);
  },
  methods: {
    btnleft() {
      this.modalshow = false;
      this.btnleftshow = true;
    },
    restart() {
    this.cardclass = [];
    this.cardlistcopy = [];

    setTimeout(() => {
      for (let i = 0; i < this.cardlist.length; i++) {
        this.cardlistcopy.push(this.cardlist[i]);
      }
      for (let i = 0; i < this.cards; i++) {
        let j = random(0, this.cardlistcopy.length, false)
        this.cardclass.push(this.cardlistcopy[j]);
        this.cardclass.push(this.cardlistcopy[j]);
        this.cardlistcopy.splice(j, 1);
        this.showlist.push(false);
        this.showlist.push(false);
      }

      this.cardclass.sort(compareRandom);
      this.modalshow = false;
      this.btnleftshow = false;
      this.count = 20;
      }, 0);

    },
    enter(el, done) {

      this.obj[0].first.firstChild.classList.add('shirtactiv');

      setTimeout(() => {
        this.obj[0].first.lastChild.classList.add('cartactiv');
      }, 50);
      
      if (this.obj[0].last != '') {      
        this.obj[0].last.firstChild.classList.add('shirtactiv');
        setTimeout(() => {
          this.obj[0].last.lastChild.classList.add('cartactiv');
        }, 50);
      }
      done();
    },
    afterEnter(el) {
      if (this.cnt < 2) {

        setTimeout(() => {

          if (this.obj[0].last != '')  {

            this.obj[0].first.firstChild.classList.remove('shirtactiv');
            this.obj[0].first.lastChild.classList.remove('cartactiv');
            this.obj[0].last.firstChild.classList.remove('shirtactiv');  
            this.obj[0].last.lastChild.classList.remove('cartactiv');

            if (this.obj[0].first.lastChild.className == this.obj[0].last.lastChild.className) {
              this.obj[0].first.remove();
              this.obj[0].last.remove();
              this.count-=2;

              if (this.count == 0) {
                this.timres.push(this.formtime);
                this.time = 0;
                this.formtime = '00:00',
                this.timestart =  false,
                this.modalshow = true;
                clearTimeout(this.stoptimer); 
              }
            }

            for (let i = 0; i < this.showlist.length; i++) {
              if (this.showlist[i]) {
                this.$set(this.showlist, i, false);
              }
            }  
        }
        this.cnt++;
        }, 1000);
      } 
    },
    leave(el, done) {
      this.cnt = 0;
      this.obj[0].first = '';
      this.obj[0].last = ''
    },
    onClick(e, i){
       
      if (!this.timestart) {
          this.stoptimer = setInterval(() => {
            this.time++;
            this.formtime = moment(this.time*1000).format('mm:ss');
          }, 1000);
        this.timestart = true;
      }

      if (this.cnt == 0 && this.obj[0].first == '' && this.obj[0].last == '') {
        this.$set(this.showlist, i, true);
        this.obj[0].first = e.target.parentNode;
      }

      if (this.cnt == 1 && this.obj[0].first != '' && this.obj[0].last == '') {
        this.$set(this.showlist, i, true);
        this.obj[0].last = e.target.parentNode;
      } 
    }
  },

}

  

function random(min, max, bool) {
    var rand = min + Math.random() * (max + 1 - min);
    bool ? rand = rand.toFixed(4) : rand = Math.floor(rand);
    return rand;
  }
function compareRandom() {
  return Math.random() - 0.5;
}
</script>

<style lang="scss" scopeds>
@import "./scss/bootstrap/scss/bootstrap-grid";
@import "./scss/cardpi";
@import "./scss/cardtr";
@import "./scss/cardch";
@import "./scss/cardbi";

html, body {
  margin: 0;
  padding: 0;
  background: rgb(247, 244, 244);
}
.app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  text-align: center;
  color: #2c3e50;
  margin-top: 10px;
  background: rgb(245, 243, 243);
}

.block {
  padding: 0;
  margin: 5px 10px;
  cursor: pointer;
  max-width: 112px;
  height: 170px;
  border-radius: 19px;
  background: white;
  box-shadow: inset -1px -2px 10px rgba(154, 147, 140, 0.5), 1px 1px 5px rgba(255, 255, 255, 1);
}
.contblock {
  position: relative;
  perspective: 650px;
  width: 114px;
  height: 172px;
}
.game {
 padding: 20px;
 background: rgb(238, 235, 235);
 margin-top: 19px;
 box-shadow: 2px 2px 10px rgba(154, 147, 140, 0.5), 1px 1px 5px rgba(255, 255, 255, 1);
}
.p {
  padding-top: 20px;
}
.cart {
  width: 100%;
  height: 100%;
  background-image: url('./assets/pack-of-cards-37194.svg');
  background-size: 1500%;
  transform: rotateY(180deg);
  transition: 1s;
  backface-visibility: hidden;
}

.shirt {
  position: absolute;
  background: linear-gradient(
    45deg,
    grey,
    rgb(37, 71, 37) 50%,
    rgb(37, 71, 37) 50%,
    #000
  );
  background-size: 7px 7px;
  width: 100%;
  height: 100%;
  border-radius: 19px;
  transition: 1s;
  backface-visibility: hidden;
}
.shirtactiv {
  transform: rotateY(180deg);
  }
.cartactiv {
  transform: rotateY(360deg);
}
.btn-restart {
     width: 150px;
     height: 40px;
     background: white;
     margin: 20px 0 15px auto;
     padding: 10px;
     border: 1px solid green;
     display: block;
     
     position: relative;
     cursor: pointer;
     &::after {
       content: "";
       width: 150px;
       height: 40px;
       position: absolute;
       border: 1px solid rgb(81, 153, 81);
       top: -1px;
       left: -1px;
       transition: all .3s
     }
     &::before {
       content: "";
       width: 150px;
       height: 40px;
       position: absolute;
       border: 1px solid rgb(7, 241, 7);
       top: -1px;
       left: -1px;
       transition: all .3s
     }
     &:hover::after {
       top: -5px;
       left: -5px;
     }
     &:hover::before {
       top: 3px;
       left: 3px
     }
 }
 .btn-left {
   margin: 20px auto;
 }
.modal {
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  background: rgba(126, 124, 124, 0.521);
  &-window {
    background: white;
    padding: 10px;
    width: 50%;
    margin: 200px auto;
    padding: 5px 20px;
    position: relative;
    & .close {
      cursor: pointer;
      position: absolute;
      top: 10px;
      right: 15px;
      width: 20px;
      height: 20px;
    }
    & .close span {
      position: absolute;
      height: 1px;
      width: 20px;
      background: rgb(119, 116, 116);
      display: block;
      top: 10px;
    }
    & .close span:last-child {
      transform: rotate(45deg);
    }
    & .close span:first-child {
      transform: rotate(-45deg);
    }
  }
}

</style>
