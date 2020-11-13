<script>
  import TeamStats from './TeamStats.svelte';

  export let allShots;
  export let teams;

</script>

<style>
p{
  margin-top: 5%;
  margin-left: 5%;
  font-size: 12px;
  font-weight: bold;
  line-height: 50%;
  color: #66fcf1;
}
</style>

<div>
  <p>Total shots: {allShots.length}</p>
  {#each teams as team}
    <TeamStats team={team} allShots={allShots}/>
  {/each}
</div>
