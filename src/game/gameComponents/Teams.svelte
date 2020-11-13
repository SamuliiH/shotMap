<script>
  import { createEventDispatcher } from 'svelte';
  const dispatch = createEventDispatcher();

  export let teams;
  export let period;

  function offend(team) {
    for(let i = 0; i < teams.length; i++){
      teams[i].active = false;
    }
    team.active = true;

    dispatch('teamOffends', {
      info: team
    })
  }

  function keydown(e) {
    if(e.key === 'a' && teams.length > 1){
      offend(teams[0]);
    }else if (e.key === 'd' && teams.length > 1) {
      offend(teams[1]);
    }
  }

</script>

<style>
  button{
    margin-left: 3%;
    width: 30%;
    background-color: #45a29e;
    border: #0b0c10 solid 1px;
  }
  .active{
    background-color: #66fcf1;
    border: #45a29e solid 1px;
  }
</style>

<svelte:window on:keydown={keydown}/>
<div>
  {#each teams as team, index}
    {#if team.select}
    <button class="{team.active === true ? "active" : ""}"
     on:click={() => offend(team)}>
     {team.name}
     {#if team.teamId % 2 === 1 && period % 2 === 1}
        <i>&#x21D2;</i>
     {/if}
     {#if team.teamId % 2 === 1 && period % 2 === 0}
        <i>&#x21D0;</i>
     {/if}
     {#if team.teamId % 2 === 0 && period % 2 === 1}
        <i>&#x21D0;</i>
     {/if}
     {#if team.teamId % 2 === 0 && period % 2 === 0}
        <i>&#x21D2;</i>
     {/if}
    </button>
    {/if}
  {/each}
</div>
