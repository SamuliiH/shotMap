<script>
	import { createEventDispatcher } from 'svelte';
	const dispatch = createEventDispatcher();

	export let teams;

	let errorMessages = [{
		message: "No team selected, choose at least one",
		status: false
	},{
		message: "Team must have name",
		status: false
	}];



	function chooseTeams(team) {

		let selectedTeams = teams.filter((team) => team.select === true);

		if(selectedTeams.length < 1){
			errorMessages[0].status = true;
		}else{
			errorMessages[0].status = false;
		}

		if(selectedTeams.some(team => team.name.length < 1)){
			errorMessages[1].status = true;
		}else{
			errorMessages[1].status = false;
		}

		if(errorMessages.every(errorMessage => errorMessage.status === false)){
			dispatch('team', {
				info: selectedTeams
			});
			dispatch('teamsOk', {
				info: true
			});
		}else{
			dispatch('teamsOk', {
				info: false
			});
		}
	}

	$: chooseTeams(teams);

</script>

<style>
p{
	font-weight: bold;
	font-size: 20px;
	color: #66fcf1;
}
input[type="checkbox"]{
	 color: #1f2833;
}
input[type="text"]{
	height: 70px;
	width: 210px;
	font-size: 35px;
	font-weight: bold;
	color: #0b0c10;
 	background-color: #1f2833;
	border: 1px solid #c5c6c7;
	border-radius: 4px;
}

</style>

<div>
<p>Choose team</p>
	{#each teams as team}
		<input type="checkbox" bind:checked={team.select}>
		<input type="text" bind:value={team.name}>
		<br/>
	{/each}


	{#each errorMessages as errorMessage}
		{#if errorMessage.status === true}
			<p>{errorMessage.message}</p>
		{/if}
	{/each}


</div>
