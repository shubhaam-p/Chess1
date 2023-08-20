<script setup>
  import {onMounted, ref} from "vue"
  var place = ref(0);
  var countSteps = 0
  var currentPos = null;
  var currentEvent = null;
  var selectedPiece = null;
  var started = 0;
  var defaultPos = []
  var posInArr = []

  const pawnMove=(currentPos, pos, selectedPiece)=>{
    let returnValue = true;
    let isDefaultPos = false;

    let difference = Math.abs(parseInt(currentPos) - parseInt(pos));
    isDefaultPos = defaultPos[selectedPiece].includes(parseInt(currentPos))
    console.log(defaultPos[selectedPiece], isDefaultPos, currentPos)
    console.log(isDefaultPos)
    
    console.log(currentPos,pos,difference)
    if(!((difference == 2 && isDefaultPos) || difference == 1)){
      console.log("Illegal move")
      returnValue = false
    }
    return returnValue
  }

  const castleMove = (currentPos, pos, selectedPiece) =>{
    console.log("castle",currentPos)
    let row = parseInt(String(currentPos)[0])
    let col = parseInt(String(currentPos)[1])
    let colArr = []
    let rowArr = []
    console.log(currentPos , pos,currentPos > pos)
    if(currentPos < pos){
        for(let i=1;i<=8; i++){
        let colCords = parseInt(String(row)+String(i));
        let rowCords = parseInt(String(i)+String(col));
        // console.log(place.value[ parseInt( posInArr[colCords] ) ].classList[3], typeof(place.value[ parseInt( posInArr[colCords] ) ].classList[3]) === 'undefined', place.value[ parseInt( posInArr[rowCords] ) ].classList[3], !colPieceBetween)
        if(typeof(place.value[ parseInt( posInArr[colCords] ) ].classList[3]) === 'undefined'){
          colArr.push(String(row)+String(i))
          console.log(colCords)
        }

        if(typeof(place.value[ parseInt( posInArr[rowCords] ) ].classList[3]) === 'undefined'){
          console.log(rowCords)
          rowArr.push(rowCords)
        }

      }
    }else{
      for(let i=8;i>=1; i--){
        let colCords = parseInt(String(row)+String(i));
        let rowCords = parseInt(String(i)+String(col));
        // console.log(place.value[ parseInt( posInArr[colCords] ) ])
        if(typeof(place.value[ parseInt( posInArr[colCords] ) ].classList[3]) === 'undefined'){
          console.log(colCords)
          colArr.push(String(row)+String(i))
        }
        
        if(typeof(place.value[ parseInt( posInArr[rowCords] ) ].classList[3]) === 'undefined'){
          console.log(rowCords)
          rowArr.push(rowCords)
        }
        
      }
    }
    console.log(colArr,rowArr)
  }

  const movePiece = (e, pos)=>{
    let validMove = true;
    console.log(e.target.className,countSteps)
    let identifyPiece = e.target.classList[3]

    if( countSteps == 0 && identifyPiece == 'undefined')
      return 

    countSteps++
    if(countSteps ==1 ){
      currentEvent = e
      currentPos = pos
      selectedPiece = identifyPiece
      console.log("selected piece", pos)
    }

    if(countSteps == 2 && currentEvent !== null){
      if(currentPos == pos)
        validMove = false

      if(identifyPiece !== undefined && selectedPiece[0] === identifyPiece[0]) //cant remove own side pieces
        validMove = false

      // Inital moves of pawn
      if(['wp','bp'].includes(selectedPiece)){
        validMove = pawnMove(currentPos, pos, selectedPiece)
      }

      //Elephant
      if(['we','be'].includes(selectedPiece)){
        validMove = castleMove(currentPos, pos, selectedPiece)
      }

      if(validMove){
        console.log(e.target,identifyPiece,selectedPiece,currentEvent.target)
        if(identifyPiece !== undefined) // piece udavtoy tar
          e.target.classList.remove(identifyPiece)
        currentEvent.target.classList.remove(selectedPiece)
        e.target.classList.add(selectedPiece)
      }
      countSteps=0
    }
  }

  const posInRefArr=()=>{ 
    console.log("AP ",place.value)

    for(let i=1; i<9; i++){
      for(let j=1; j<9; j++){
        // console.log(i,j)
        if(i-1 !==0){
          // console.log(((i-1)*8)+j-1," ",i,j)
          posInArr[String(i)+String(j)] = String(((i-1)*8)+j-1)
        }else{
          posInArr[String(i)+String(j)] = String(j-1)
        }
      }
    }
  }

  const addPiece=()=>{
    
    if(started !==0)
      return

    defaultPos['wp'] = [17,27,37,47,57,67,77,87]
    defaultPos['we'] = [18, 88]
    defaultPos['wh'] = [28, 78]
    defaultPos['wb'] = [38, 68]
    defaultPos['wk'] = [48]
    defaultPos['wq'] = [58]
    defaultPos['bp'] = [12,22,32,42,52,62,72,82]
    defaultPos['be'] = [11,81]
    defaultPos['bh'] = [21,71]
    defaultPos['bb'] = [31,61]
    defaultPos['bq'] = [41]
    defaultPos['bk'] = [51]

    for(let key in defaultPos){
      for(let i=0; i<defaultPos[key].length; i++){
        place.value[posInArr[defaultPos[key][i]]].classList.add(key)
      }
    }
    started =1
  }

  onMounted(()=>{
    posInRefArr()
    addPiece()
  })
</script>

<template>
  <div class="container">
    Board Game 
    <div class="board">
      <div  v-for=" i in 8" :key="i">
        <div v-for="j in 8" class='squares' v-bind:key="j" draggable="true" ref ="place"
        :class="['square_'+String(i)+String(j)],[(i+j)%2 !==0? 'odd':'even']" v-on:click="movePiece($event, String(i)+String(j))">
           {{ i }} {{ j }}
        </div>
      </div>
    </div>

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
    background-image:url(./assets/bq.png);
    background-size: 100%;
  }

  .bk{
    background-image:url(./assets/bk.png);
    background-size: 100%;
  }

  .bp{
    background-image:url(./assets/bp.png);
    background-size: 100%;
  }

  .bb{
    background-image:url(./assets/bb.png);
    background-size: 100%;
  }

  .bh{
    background-image:url(./assets/bh.png);
    background-size: 100%;
  }

  .be{
    background-image:url(./assets/be.png);
    background-size: 100%;
  }
  /* white */
  .wq{
    background-image:url(./assets/wq.png);
    background-size: 100%;
  }

  .wk{
    background-image:url(./assets/wk.png);
    background-size: 100%;
  }

  .wp{
    background-image:url(./assets/wp.png);
    background-size: 100%;
  }

  .wb{
    background-image:url(./assets/wb.png);
    background-size: 100%;
  }

  .wh{
    background-image:url(./assets/wh.png);
    background-size: 100%;
  }

  .we{
    background-image:url(./assets/we.png);
    background-size: 100%;
  }
</style>