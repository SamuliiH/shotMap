<script>
  import { fade } from 'svelte/transition';
  import Gamesetup from './gamesetup/Gamesetup.svelte';
  import Game from './game/Game.svelte';
  import AfterGame from './aftergame/AfterGame.svelte';

  let readyToPlay = false;
  let gotoRes = false;
  let readyToPreviousGame = false;


  let rinks= [

    {src: "images/europeRink2.jpg",
    name: "IIHF",
    id: "1", select:false,
    length: 60, height: 30,
    goalX: 4, goalY: 15},

    {src:"images/northAmericaRink1.jpg",
    name: "NHL",
    id: "2", select:false,
    length: 60, height: 26,
    goalX: 3.35, goalY: 13},

  ];

  let selectedRink = rinks[0];
  let group = "rinks";

  let teams = [
    { select: true, name: 'Team One', shots: 0, goals: 0, teamId: 1, active: true},
    { select: true, name: 'Team Two', shots: 0 , goals: 0, teamId: 2, active: false},
  ];
  let selectedTeams;

  let minutes = 20;
  let periods = 3;
  let latestShot = {period: 1, time:0};

  function handleStartGame(e){
    readyToPlay = true;
    localStorage.removeItem("shots");
    localStorage.setItem("minutes", minutes);
    localStorage.setItem("periods", periods);
    localStorage.setItem("teams", JSON.stringify(teams));
    localStorage.setItem("rink", JSON.stringify(selectedRink));
    localStorage.setItem("latestShot", JSON.stringify(latestShot));
  }


  function gotoResult() {
    gotoRes = true;
    readyToPreviousGame = false;
    readyToPlay = false;
  }

  function continuePrevGame(){
    readyToPreviousGame = true;
    readyToPlay = true;
    minutes = localStorage.getItem("minutes");
    periods = localStorage.getItem("periods");
    selectedTeams = JSON.parse(localStorage.getItem("teams"));
    selectedRink = JSON.parse(localStorage.getItem("rink"));
  }


  function minuteHandler(e){
    minutes = e.detail.info;
  }

  function periodHandler(e){
    periods =  e.detail.info;
  }

  function handleTeamSelection(e){
    selectedTeams = e.detail.info;
    console.log(selectedTeams + " from app.svelte");
  }

  function handleRink(e) {
    selectedRink = e.detail.info;
  }


</script>

<style>

</style>


{#if !readyToPlay && !gotoRes}

<Gamesetup

on:minute={minuteHandler}
on:period={periodHandler}
minutes={minutes}
periods={periods}

on:team={handleTeamSelection}
teams={teams}

on:startGame={handleStartGame}
on:continuePrevGame={continuePrevGame}

on:changeRink={handleRink}
rinks={rinks}
group={group}
chosenRink={selectedRink}

/>

{/if}


{#if readyToPlay && !gotoRes}

  <Game
  minutes={minutes}
  periods={periods}
  teams={selectedTeams || teams}
  rink={selectedRink}
  on:gotoResults={gotoResult}
  readyToPreviousGame={readyToPreviousGame}
  />

{/if}

{#if gotoRes}

<div id="afterGame" in:fade="{{duration: 3000 }}">
  <AfterGame rink={selectedRink} teams={selectedTeams} periods={periods}/>
</div>

{/if}
