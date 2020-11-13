<script>
import { fade, slide, scale } from 'svelte/transition';
import { createEventDispatcher } from 'svelte';

export let rinks;
export let group;
export let chosenRink;

const dispatch = createEventDispatcher();

function changeRink(e) {
  dispatch('changeRink', {
    info: chosenRink
  });
}

</script>

<style>

  .rink{
    width: 80%;
    margin-left: 3%;
    margin-right: 3%;
    border-bottom-left-radius: 15% 28%;
    border-bottom-right-radius: 15% 28%;
    border-top-left-radius: 15% 28%;
    border-top-right-radius: 15% 28%;
    border: 10px #66fcf1 solid;
  }
  .rinkContainer {
    border: 1px #c5c6c7 solid;
    border-radius: 1%;
    width: 70%;
    margin-left: 5%;
    margin-top: 1%;
    box-shadow: 5px 10px #0b0c10;

  }
  p{
    font-weight: bold;
    font-size: 20px;
    color: #66fcf1;
  }
</style>


{#each rinks as rink}
  <div class="rinkContainer" >
    <input type=radio name={group} bind:group={chosenRink} value={rink} on:change={changeRink}/>
    <p>{rink.name}</p>
    <img src={rink.src} alt={rink.name} class="rink">
  </div>
{/each}
