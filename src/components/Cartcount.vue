<template>
<div>
  <div class="namegame">
        <h2>Пексесо</h2>
      </div>
  <div class="cardinfo">
    <p class="p">Осталось карт: {{ count }}</p>
    <p>Время: {{ formtime }}</p> 

    <p v-for="(time, index) in timres" :key="index">Время игры №{{index+1}}: {{time}}</p>
  </div>
</div>
</template>

<script>
export default {
  name: 'Cartcount',
  props: ['count', 'formtime', 'timres'],
  data(){
    return{
       tim: 0,
    }
  },
  computed: {
    
  }
}

</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style  lang="scss" scoped>
.cardinfo,
.namegame {
  background: rgb(238, 235, 235);
  padding-bottom: 10px;
  box-shadow: 2px 2px 10px rgba(154, 147, 140, 0.5), 1px 1px 5px rgba(255, 255, 255, 1);
}
.namegame h2 {
   line-height: 30px;
   padding-top: 20px;
 }


</style>
