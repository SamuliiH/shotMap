<script>
  import { createEventDispatcher } from 'svelte';

  const dispatch = createEventDispatcher();

  export let periods;
  export let minutes;

	function periodChange(e) {
    if(periods < 1){
      periods = 1;
    }
		dispatch('period', {
			info: periods
		});
	}

  function minuteChange(e) {
    if(minutes < 1){
      minutes = 1;
    }
		dispatch('minute', {
			info: minutes
		});
	}

</script>

<style>
p{
	font-weight: bold;
	font-size: 20px;
	color: #66fcf1;
}

input[type="number"]{
   height: 70px;
   width: 210px;
   font-size: 35px;
   font-weight: bold;
   color: #0b0c10;
	 background-color: #1f2833;
   border: 1px solid #c5c6c7;
   border-radius: 4px;
}
label{
  font-weight: bold;
  font-size: 20px;
  color: #66fcf1;
}
</style>

<div>
<label>Set number of periods</label>
<input on:change={periodChange} type="number" min={1} bind:value={periods}/>
<label>Set minutes per period</label>
<input on:change={minuteChange} type="number" min={1} bind:value={minutes}/>
</div>
