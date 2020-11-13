<script>

  export let team;
  export let allShots;
  let teamShots;
  let teamShotsLength;
  let teamGoals;
  let teamGoalsLength;

  $: allShots = allShots;
  $: teamShots = allShots.filter(shot => team.name === shot.team);
  $: teamShotsLength = teamShots.length;
  $: teamGoals = teamShots.filter(shot => shot.goal === true);
  $: teamGoalsLength = teamGoals.length;

</script>

<style>
  div{
    border-left: solid 1px black;
    border-right: solid 1px black;
    border-top: solid 1px black;
  }
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
  <p>Team: {team.name}</p>
  <p>Shots: {teamShotsLength}</p>
  <p>Goals: {teamGoalsLength}</p>
</div>
