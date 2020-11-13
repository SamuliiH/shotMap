<script>
  import Stats from './Stats.svelte';
  import ShotEditor from './ShotEditor.svelte';
  import { fade, fly } from 'svelte/transition';
  import { createEventDispatcher } from 'svelte';
  const dispatch = createEventDispatcher();


  export let period;
  export let readyToPreviousGame;

  export let minutes;
  export let teams;
  export let teamOffend;
  export let rink;
  export let allShots = [];
  export let delta;
  export let gameOn = false;

  if(localStorage.getItem("shots") !== null){
    allShots = JSON.parse(localStorage.getItem("shots"));
  }


  let cursorEffect;

  function handleShot(e) {
    if(!gameOn){
      return;
    }

    cursorEffect = "expand";
      setTimeout(function(){
        cursorEffect = "";
      }, 500);

    let x = e.target.clientWidth;
    let y = e.target.clientHeight;
    let xLocation = e.offsetX/x;
    let yLocation = e.offsetY/y;

    function getDistanceToGoal() {
      let target = getTargetGoal();
      let goalX;
      if(target ===1){
        goalX = rink.length - rink.goalX  //esim 56m
      }else{
        goalX = rink.goalX
      }
      let goalY = rink.goalY;
      let katetX = Math.abs(goalX - xLocation * rink.length)
      let katetY = Math.abs(goalY - yLocation * rink.height);
      let hypotenus = Math.hypot(katetX, katetY);
      return hypotenus;
    }

    function getTargetGoal(){
      if(teamOffend.teamId % 2 === 1 && period % 2 === 1){
        return 1;
      }else if(teamOffend.teamId % 2 === 1 && period % 2 === 0){
        return 2;
      }else if(teamOffend.teamId % 2 === 0 && period % 2 === 1){
        return 2;
      }else{
        return 1;
      }
    }

    let newShot = {
      period: getPeriod(),
      team: teamOffend.name,
      meterX: Number((xLocation * rink.length).toFixed(1)),
      meterY: Number((yLocation * rink.height).toFixed(1)),
      x: xLocation,
      y: yLocation,
      rinkLength: rink.length,
      rinkHeight: rink.height,
      distanceToGoal: getDistanceToGoal(),
      targetGoal: getTargetGoal(),
      time: Number((delta/1000).toFixed(1)),
      goal: false,
      toggleShotDetails: true,
      shotId: Math.floor(Math.random() * 10000) + 1,
    }
    updateShots(newShot);
  }

  function updateShots(newShot){
    allShots =[...allShots, newShot];
    localStorage.setItem("shots", JSON.stringify(allShots));

    let latestShot = JSON.parse(localStorage.getItem("latestShot"));
    if(newShot.period > latestShot.period){
      latestShot = newShot;
      localStorage.setItem("latestShot", JSON.stringify(latestShot));
    }else if(newShot.period === latestShot.period && newShot.time >= latestShot.time){
      latestShot = newShot;
      localStorage.setItem("latestShot", JSON.stringify(latestShot));
    }

  }

  function getAllShots() {
    let shots = localStorage.getItem("shots");
    return JSON.parse(shots);
  }

  let cursorX;
  let cursorY;
  function handleCursor(e) {
    cursorX = e.offsetX -10;
    cursorY = e.offsetY;
  }

  let idToEdit = 0;
  function editShot(shotId){
    idToEdit = shotId;
  }

  function getPeriod() {
    return period;
  }

  function handleShotChanges(e) {
    let shotToRemove = allShots.find(shot=>shot.shotId == e.detail.id);
    let indexOfshotToRemove = allShots.indexOf(shotToRemove);
    allShots.splice(indexOfshotToRemove, 1);
    updateShots(e.detail.info);
    idToEdit = 0;
  }

  function handleDelete(e) {
    let shotToRemove = allShots.find(shot=>shot.shotId == e.detail.id);
    let indexOfshotToRemove = allShots.indexOf(shotToRemove);
    allShots.splice(indexOfshotToRemove, 1);
    allShots = allShots;
    localStorage.setItem("shots", JSON.stringify(allShots));
  }

  let shotsInMap;
  $: shotsInMap = allShots;

</script>

<style>
  img.IIHF{
    width: 100%;
    border: 10px #66fcf1 solid;
    border-bottom-left-radius: 14% 24%;
    border-bottom-right-radius: 14% 24%;
    border-top-left-radius: 14% 24%;
    border-top-right-radius: 14% 24%;
  }

  img.NHL{
    width: 100%;
    border: 10px #66fcf1 solid;
    border-bottom-left-radius: 15% 28%;
    border-bottom-right-radius: 15% 28%;
    border-top-left-radius: 15% 28%;
    border-top-right-radius: 15% 28%;
  }

  #flexContainer{
    display: flex;
    flex-wrap: nowrap;
    padding-bottom: 0;
    min-width: 1150px;
  }

  #rinkContainer{
    position: relative;
    width: 70%;
    margin: 1% 1% 1% 2%;
  }

  #statsAndEditor{
    border: 1px #66fcf1 solid;
    border-radius: 1%;
    width: 15%;
    background-color: #1f2833;
    height:auto;
    margin-left: 2%;
    margin-right: 2%;
  }


  #stats{
    margin-top: 0;
    margin-bottom: 0;
    border-bottom: grey solid 2px;
  }
  #editor{
    padding-top: 20px;
    margin-top: 10px;
    margin-bottom: 0;

  }


  .cursorOff{
    cursor: none;
  }

  .shot{
    position: absolute;
    width:2%;
    height:2%;

  }

  #cursor {
    width: 18px;
    height: 18px;
    border: 6px solid black;
    border-radius: 50%;
    position: absolute;
    transition-timing-function: ease-out;
    animation: cursorAnim .9s infinite alternate;
    pointer-events: none;
}

@keyframes cursorAnim {
    from {
        transform: scale(1);
        opacity: 0.6;
    }
    to {
        transform: scale(.6);
        opacity: 1;
    }
}


@keyframes cursorAnim3 {
    0% {
        transform: scale(1);
    }
    50% {
        transform: scale(1.5);
    }
    100% {
        transform: scale(1);
        opacity: 0;
    }
}

.expand {
    animation: cursorAnim3 .5s forwards !important;
    border: 3px solid #66fcf1 !important;
}



</style>


<div id="flexContainer">
  <div class={gameOn ? "cursorOff" : ""} id="rinkContainer" on:mousemove={handleCursor}>
    <div hidden={!gameOn} class={cursorEffect} id="cursor" style="top:{cursorY}px; left:{cursorX}px"></div>
    <img src={rink.src} class={rink.name} alt="Hockeymap" on:click={handleShot}>
    {#if !gameOn && allShots.length}
      {#each shotsInMap as shot, i (shot.shotId)}
        {#if shot.period === period}
        <div on:click={()=>editShot(shot.shotId)}
             in:fade="{{duration: 2500 }}"
             out:fade="{{duration: 1500 }}"
             class="shot"
             style="left:{shot.x*100}%; top:{shot.y*100}%;
             content:{shot.goal?'url("images/puckGoal.png")':'url("images/puck2.png")'}">
        </div>
        {/if}
      {/each}
    {/if}
  </div>

  <div id="statsAndEditor">
    <div id="stats">
    <Stats
    allShots={shotsInMap}
    teams={teams}
    />
    </div>
    <div id="editor">
    <ShotEditor
    teams={teams}
    teamOffend={teamOffend}
    idToEdit={idToEdit}
    gameOn={gameOn}
    allShots={shotsInMap}
    on:shotEdited={handleShotChanges}
    on:shotDelete={handleDelete}
    minutes={minutes}
    period={period}
    />
    </div>
  </div>
</div>
