<script>
  import Rinksetups from './gameSetupComponents/Rinksetups.svelte';
  import Timesetups from './gameSetupComponents/Timesetups.svelte';
  import Teamsetups from './gameSetupComponents/Teamsetups.svelte';

  import { fade } from 'svelte/transition';

  import { createEventDispatcher } from 'svelte';
	const dispatch = createEventDispatcher();

  //timesetup
  export let periods;
  export let minutes;

  //teamsetup
  export let teams;
  let teamsOk = true;

 // rink setup
  export let rinks;
  export let group;
  export let chosenRink;

  let shotStorage;

  if(localStorage.getItem("shots") !== null){
    shotStorage = true;
  }

  function continuePrevGame(e){
    dispatch('continuePrevGame', {
      info: true
    });
  }

  function handleTeamsOk(e) {
    teamsOk = e.detail.info;
    console.log('teamsOk' + teamsOk);
  }

  function startGame(){
    dispatch('startGame', {
      info: true
    });
  }

</script>

<style>
#container{
  display: flex;
  justify-content: space-between;
  background-color: #0b0c10;
  min-width: 1150px;
}
#rinks{
  width: 65%;
  box-shadow: 5px 10px #0b0c10;
}
#teamAndTime{
  margin-left: 0;
  height: 90%;
  width: 30%;
  border-radius: 10%;
  box-shadow: 5px 10px #0b0c10;
}
</style>

<div id="container" out:fade="{{duration: 2000 }}">

    <div id="rinks">
      <Rinksetups rinks={rinks} group={group} chosenRink={chosenRink} on:changeRink/>
    </div>
    <div id="teamAndTime">
      <Timesetups on:minute on:period minutes={minutes} periods={periods} />
      <Teamsetups on:team teams={teams} on:teamsOk={handleTeamsOk}/>

      <button disabled={!teamsOk} on:click={startGame}>Start Game</button>

      {#if shotStorage}
        <button on:click={continuePrevGame}>Continue previous game</button>
      {/if}
    </div>

</div>
