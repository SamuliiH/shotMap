<script>
  import HockeyMap from './gameComponents/HockeyMap.svelte';
  import Timer from './gameComponents/Timer.svelte';
  import Teams from './gameComponents/Teams.svelte';

  import { fade } from 'svelte/transition';

  export let minutes;
  export let periods;
  export let readyToPreviousGame;
  export let teams;
  export let rink;

  let delta;
  let on;
  let teamOffend = teams[0];

  let lastShot = JSON.parse(localStorage.getItem("latestShot"));
  let period = lastShot.period;
  let time = lastShot.time;

  function changetime(e) {
    delta = e.detail.info;
  }

  function changeGameOn(e) {
    on = e.detail.info;
  }

  function changeteamOffend(e) {
    teamOffend = e.detail.info;
  }

  function changePeriod(e) {
    period = e.detail.info;
  }




</script>

<style>
#game{
  background-color: #0b0c10;
  margin: 0;
  padding: 0;
  position: relative;
  min-width: 1150px;
}
#timer{
  margin-left: 12.5%;
  justify-content: center;
  width: 50%;
}
#teams{
  margin-left: 20%;
  margin-top: 0;
  width: 50%;
  padding: 10px;
}
</style>


<div id="game" in:fade="{{duration: 4000 }}" out:fade="{{duration: 1000 }}">
  <div id="teams">
      <Teams
        teams={teams}
        on:teamOffends={changeteamOffend}
        period={period}/>
    </div>
  <HockeyMap
    readyToPreviousGame={readyToPreviousGame}
    rink={rink}
    delta={delta}
    gameOn={on}
    teamOffend={teamOffend}
    teams={teams}
    period={period}
    minutes={minutes}
  />


  <div id="timer">
    <Timer
      minutes={minutes}
      periods={periods}
      on:timechange={changetime}
      readyToPreviousGame={readyToPreviousGame}
      on:gameon={changeGameOn}
      on:periodChange={changePeriod}
      on:gotoResults
      period={period}
      time={time}
      />

  </div>
</div>
