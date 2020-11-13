<script>

  export let rink;
  export let teams;
  export let periods;

  let periodArray = new Array(Number(periods));
  console.log(periodArray);

  let shotsInMap = getAllShots();

  function getAllShots() {
    let shots = localStorage.getItem("shots");
    return JSON.parse(shots);
  }

  function printPage() {
    // save to db

    // clear localStorage
    window.print();
    localStorage.removeItem("shots");
  }

</script>


<style>


.rinkContainer{
  position:relative;
  width: 30%;
}
.shot{
  position: absolute;
  width:2%;
  height:2%;
}

img {
  border: 10px #66fcf1 solid;
  width: 100%;
  border-bottom-left-radius: 14% 24%;
  border-bottom-right-radius: 14% 24%;
  border-top-left-radius: 14% 24%;
  border-top-right-radius: 14% 24%;
  margin-top: 5%;
}
</style>


<div id="afterGame">
  <button on:click={printPage}>Print or save</button>
  {#each periodArray as period, i}
    <div class="rinkContainer">
      <img src="{rink.src}" alt="rink"/>
      {#each shotsInMap as shot}
        {#if shot.period === (i+1)}
          <div
               class="shot"
               style="left:{shot.x*100}%; top:{shot.y*100}%;
               content:{shot.goal?'url("images/puckGoal.png")':'url("images/puck2.png")'}">
          </div>
        {/if}
      {/each}
    </div>
  {/each}
</div>
