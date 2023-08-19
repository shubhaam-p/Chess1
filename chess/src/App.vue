<script setup>
  import {ref} from "vue"
  var place = ref(0);

  const Defineplace = (e)=>{
    console.log(e.target)
    e.target.classList.add('bq')
    // console.log(e.srcElement.classList[1].split('_')[1])
    // classOfSquare = e.srcElement.classList[1];

    // console.log(i,j)
    // place = i+" "+j
  }
</script>

<template>
  <div class="container">
    Board Game 
    <div class="board">
      <div  v-for=" i in 8" :key="i">
        <div v-for="j in 8" class='squares' v-bind:key="j" v-on:click="Defineplace($event)" :class="'square_'+String(i)+String(j),[(i+j)%2!==0? 'odd':'even']" >
           {{ i }} {{ j }}
        </div>
      </div>
    </div>

    <div class="queen"> image</div>
  </div>

</template>

<style scope>
  .board {
    display: flex;
    cursor: grab;
  }
  .squares{
    padding: 1rem;
  }
  .odd{
    background-color: beige;
  }
  .even{
    background-color: grey;
  }

  .bq{
    background-image:url(./assets/blackQueen.png);
    background-size: 100%;
  }
</style>