<script>
  import { createEventDispatcher } from 'svelte';
  const dispatch = createEventDispatcher();

  export let teams;
  export let minutes;
  export let teamOffend;
  export let gameOn;
  export let idToEdit;
  export let allShots;
  export let period;
  let shotToEdit;


  $: shotToEdit = allShots.find(shot => shot.shotId === idToEdit);

  function handleShotChanges() {
    console.log('handleShotChanges');
    if(shotToEdit.meterX > shotToEdit.rinkLength ||
       shotToEdit.meterY > shotToEdit.rinkHeight ||
      (shotToEdit.time < 0 || shotToEdit.time > (minutes * 60)) ||
       isNaN(shotToEdit.meterX) || isNaN(shotToEdit.meterY) || isNaN(shotToEdit.time)){
      return;
    }
    shotToEdit.x = shotToEdit.meterX / shotToEdit.rinkLength;
    shotToEdit.y = shotToEdit.meterY / shotToEdit.rinkHeight;
    dispatch('shotEdited', {info: shotToEdit, id: idToEdit});
  }


  function handleShotDelete() {
    dispatch('shotDelete', {info: shotToEdit, id: idToEdit});
  }

</script>

<style>

input[type="text"], select{
  height: 33px;
  width: 100px;
  font-size: 15px;
  font-weight: bold;
  color: #0b0c10;
  background-color: #45a29e;
  border: 1px solid #c5c6c7;
  border-radius: 4px;
}
label, p{
  padding-top: 1px;
  margin-top: 1px;
  margin-left: 2%;
  color: #66fcf1;
  display: inline-block;
}
button, input[type=submit]{
  margin-left: 2%;
  width: 50%;
  background-color: #c5c6c7;
  border: 2px #45a29e solid;
}
span{
  margin-left: 15px;
  width: 30px;
  font-family:"wide font";
  color: #66fcf1;
}
</style>

<div>
  {#if shotToEdit}
    {#if shotToEdit.period == period}
  <button on:click={handleShotDelete}>DELETE</button>
  <form on:submit|preventDefault={handleShotChanges}>
    <label>Time</label>
    <input type="text" bind:value={shotToEdit.time}/><br>
    <label>Team</label>
    <select bind:value={shotToEdit.team}>
      {#each teams as team}
        <option value={team.name} selected={team.name === shotToEdit.team ? "selected" : ""}>
          {team.name}
        </option>
      {/each}
    </select>
    <br/>
    <label>Meters X</label>
    <input type="text" bind:value={shotToEdit.meterX}/><span>   &#x2194;   </span><br>
    <label>Meters Y</label>
    <input type="text" bind:value={shotToEdit.meterY}/><span>   &#x2195;   </span><br>
    <br/>
    <label>Goal ? </label>
    <select bind:value={shotToEdit.goal}>
        <option value={false}>
          No goal
        </option>
        <option value={true}>
          Goal
        </option>
    </select><br>
    <input type="submit" value="Update"/>
  </form>
    {/if}
  {/if}
</div>
