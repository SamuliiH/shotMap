<script>
  export let minutes;
  export let periods;
  export let prevPeriod;
  export let readyToPreviousGame;
  export let time;

  let startTime;
  let inter;
  let delta = 0.0;
  let prevDelta = 0.0;

  let on = false;

  export let period;
  let readyToNextPeriod = false;

  import { createEventDispatcher } from 'svelte';
	const dispatch = createEventDispatcher();

  // if delta changes, inform parent component
  $: {
		dispatch('timechange', {
			info: delta
		});
  }

  $: {
    dispatch('gameon',{
      info: on
    })
  }

  let showPeriod = period;

  function nextPeriod() {
      if(showPeriod+1 <= periods){
        showPeriod++;
        readyToNextPeriod = false;
        delta = 0;
        prevDelta = 0;
        dispatch('periodChange', {info: showPeriod});
      }else{
        showPeriod ="game end";
        dispatch('gotoResults', {info: showPeriod});
      }
  }


  function handleKeydown(e) {
        handleFunctionCall(e.code);
  }

  function handleClickDown(event, todo) {
        handleFunctionCall(todo);
  }

  function handleFunctionCall(behavior) {
        if(behavior === "Space" && on){
          stopGame();
        }else if (behavior === "Space" && !on) {
          startGame();
        }else if (behavior === "ArrowRight") {
          plusOne()
        }else if (behavior === "ArrowLeft") {
          minusOne();
        }
  }



  function startGame() {
    if(on === true){
      return;
    }else{
      on = true;
      startTime = Date.now();
      inter = setInterval(start, 10);
    }
  }

  function stopGame() {
    on = false;
    clearInterval(inter);
    prevDelta = delta;
  }
  function start() {
    delta = Date.now() - startTime + prevDelta;
    if(delta >= minutes * 60000){
      stopGame();
      readyToNextPeriod = true;
    }
  }


// add 0.1 second to timer
  function plusOne() {
    if(on === false){
      prevDelta += 100;
      if(prevDelta > minutes * 60000){
        prevDelta = minutes * 60000;
      }

      if(prevDelta > delta){
        delta = prevDelta;
      }
    }else{
      return;
    }
  }
// reduce 0.1 second from timer
  function minusOne() {

    if(on === false){
      prevDelta -= 100;
      if(prevDelta < 0){
        prevDelta = 0;
      }
      if(prevDelta < delta){
        delta = prevDelta;
      }
    }else{
      return;
    }
  }


  let width = 0;
  $: {width = 100 * (delta /1000) / (Number(minutes) * 60)};

  $: totalTime = (delta/1000).toFixed(1);
  $: showMinutes = Math.floor((delta/1000).toFixed(1) / 60);
  $: showSeconds = ((delta/1000).toFixed(1) - Math.floor((delta/1000).toFixed(1) / 60) * 60).toFixed(1);

</script>

<style>

  #sliderContainer{
    width: 60%;
    margin: 0 40% 0 10%;
  }
  #slider{
    height: 20px;
    background-color: #66fcf1;
    opacity: 0.8;
    border-radius: 6%;
  }
  #buttons{
    margin: auto;
    justify-content: center;
    width: 50%;
  }
  button{
      margin-left: 2%;
      width: 30%;
      height: 10%;
      background-color: #c5c6c7;
      border: 2px #45a29e solid;
      font-size: 10px;
  }
  span{
    font-weight: bold;
    font-size: 20px;
  }
  .disabled{
    background-color: #3d4145;
  }

</style>

<svelte:window on:keydown={handleKeydown}/>
<div id="container">
  <div id="sliderContainer">
    <div id="slider" style="width: {width}%"></div>
  </div>
  <span>Period {showPeriod} / {periods}</span>
  {#if readyToNextPeriod}
    <div>
      <button on:click={nextPeriod}>{showPeriod < periods ? "Go To Next Period": "Go to Results"}</button>
    </div>
  {/if}
    <div id="buttons">
      <button on:click={()=> handleClickDown(event, "ArrowLeft")} class="{on ? "disabled" : ""}"><span><span>&#9194</span></button>
      {#if !on}
        <button on:click={()=> handleClickDown(event, "Space")}><span>&#x23ef;</span><br>
        <span>{showMinutes < 10 ? "0": ""}{showMinutes}</span><span>:{showSeconds < 10 ? "0": ""}{showSeconds}</span>
        </button>
      {/if}
      {#if on}
        <button on:click={()=> handleClickDown(event, "Space")}><span>&#9612; &#9612;</span><br>
        <span>{showMinutes < 10 ? "0": ""}{showMinutes}</span><span>:{showSeconds < 10 ? "0": ""}{showSeconds}</span>
        </button>
      {/if}
      <button on:click={()=> handleClickDown(event, "ArrowRight")} class="{on ? "disabled" : ""}"><span>&#9193;</span></button>
    </div>
</div>
