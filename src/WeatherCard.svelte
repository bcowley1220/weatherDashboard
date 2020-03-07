<script>
  let promise = harborSpringsWeather();
  let response;
  let json = {};
  let error;
  let harborSpringsCall = 'https://cors-anywhere.herokuapp.com/https://api.darksky.net/forecast/6391cb4b579b96ba00ae2f374f79d60b/45.4316759,-84.9919992';

  async function harborSpringsWeather() {
    response = await fetch(harborSpringsCall);
    json = await response.json();
    console.log(json.daily)

    if(response.ok){
      return json;
    } else {
      throw new Error(json);
    }
  }

  let getData = () => {
    promise = harborSpringsWeather();
  }


</script>

<button type="button" on:click={getData}>Get Weather</button>

{#await promise}
  <p>...going</p>
{:then text}
  <p>The data is:</p>
  <p>{json.daily.icon}</p>
{:catch error}
<p>Waiting To Start...</p>
{/await}