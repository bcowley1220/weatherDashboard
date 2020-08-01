<script>
  let promise = harborSpringsWeather();
  let response;
  let json = {};
  let daysOfWeek = [];
  let error;
  let eachDay;
  let userLocation = `Commerce Twp, MI`;
  let userLocationCoord = null;
  let day = {
    date: "",
    icon: "",
    summary: "",
    temperatureHighTime: "",
    temperatureHigh: "",
    temperatureLowTime: "",
    temperatureLow: "",
    precipProbability: "",
    precipType: "",
    precipAccumulation: "",
    precipIntensityMaxTime: "",
    sunriseTime: "",
    sunsetTime: ""
  };
  let harborSpringsCall = `https://cors-anywhere.herokuapp.com/https://api.darksky.net/forecast/6391cb4b579b96ba00ae2f374f79d60b/${userLocationCoord}`;
  let mapApiCall = `http://open.mapquestapi.com/geocoding/v1/address?key=QZSnSKeOgvAMPbWsHYlh7qVAnEeMGJBB&location=${userLocation}`;
  let months = [
    "January",
    "February",
    "March",
    "April",
    "May",
    "June",
    "July",
    "August",
    "September",
    "October",
    "November",
    "December"
  ];

  let getHours = day => {
    let temperatureLowTime = new Date(day.temperatureLowTime);
    let temperatureHighTime = new Date(day.temperatureHighTime);
    let sunriseTime = new Date(day.sunriseTime);
    let sunsetTime = new Date(day.sunsetTime);
    let lowHrs = temperatureLowTime.getHours();
    let lowMin = temperatureLowTime.getMinutes();
    if (lowMin == 0) {
      lowMin += "0";
    }
    let lowAmPm = lowHrs >= 12 ? "pm" : "am";
    if (lowHrs > 12) {
      lowHrs -= 12;
    }
    let highHrs = temperatureHighTime.getHours();

    let highAmPm = highHrs >= 12 ? "pm" : "am";
    if (highHrs > 12) {
      highHrs -= 12;
    }

    let highMin = temperatureHighTime.getMinutes();
    if (highMin == 0) {
      highMin += "0";
    } else if (highMin.toString().length == 1) {
      highMin = "0" + highMin;
    }

    let sunriseHr = sunriseTime.getHours();
    if (sunriseHr > 12) {
      sunriseHr -= 12;
    }
    let sunriseMin = sunriseTime.getMinutes();
    if (sunriseMin == 0) {
      sunriseMin += "0"
    } else if (sunriseMin.toString().length == 1) {
      sunriseMin = "0" + sunriseMin;
    }

    let sunsetHr = sunsetTime.getHours();
    if (sunsetHr > 12) {
      sunsetHr -= 12;
    }
    let sunsetMin = sunsetTime.getMinutes();
    if (sunsetMin == 0) {
      sunsetMin += "0";
    } else if (sunsetMin.toString().length == 1) {
      sunsetMin = "0" + sunsetMin;
    }

    day.temperatureLowTime = `${lowHrs}:${lowMin} ${lowAmPm}`;
    day.temperatureHighTime = `${highHrs}:${highMin} ${highAmPm}`;
    day.sunriseTime = `${sunriseHr}:${sunriseMin} am`;
    day.sunsetTime = `${sunsetHr}:${sunsetMin} pm`;
    return day;
  };

  let weatherIcon = () => {
    switch (day.icon) {
      case "sunny":
        day.icon = `assets/sun.svg`;
        break;
      case "clear-day":
        day.icon = `assets/sun.svg`;
        break;
      case "rain":
        day.icon = `assets/rain.svg`;
        break;
      case "partly-cloudy-day":
        day.icon = `assets/cloudy.svg`;
        break;
      case "cloudy":
        day.icon = `assets/cloudy.svg`;
        break;
    }
  };
  let getUserLocationCoord = async (userLocation, userLocationCoord) => {
    let locationReponse = await fetch(mapApiCall);
    let locationJSON = await locationReponse.json();
    if (locationReponse.ok) {
      return (userLocationCoord = `${locationJSON.results[0].locations[0].latLng.lat}, ${locationJSON.results[0].locations[0].latLng.lng}`);
    } else {
      throw new Error(locationJSON);
    }
  };

  async function harborSpringsWeather() {
    daysOfWeek = [];
    console.log(userLocation);
    mapApiCall = `http://open.mapquestapi.com/geocoding/v1/address?key=QZSnSKeOgvAMPbWsHYlh7qVAnEeMGJBB&location=${userLocation}`;
    let locationReponse = await fetch(mapApiCall);
    let locationJSON = await locationReponse.json();
    if (locationReponse.ok) {
      userLocationCoord = `${locationJSON.results[0].locations[0].latLng.lat}, ${locationJSON.results[0].locations[0].latLng.lng}`;
      harborSpringsCall = `https://cors-anywhere.herokuapp.com/https://api.darksky.net/forecast/6391cb4b579b96ba00ae2f374f79d60b/${userLocationCoord}`;

      response = await fetch(harborSpringsCall);
      json = await response.json();
      if (response.ok) {
        console.log(json);
        json.daily.data.forEach(info => {
          day = {
            date: info.time,
            icon: info.icon,
            summary: info.summary,
            temperatureHighTime: info.temperatureHighTime,
            temperatureHigh: `${info.temperatureHigh}F`,
            temperatureLowTime: info.temperatureLowTime,
            temperatureLow: `${info.temperatureLow}F`,
            precipProbability: info.precipProbability,
            precipType: info.precipType,
            precipAccumulation: info.precipAccumulation,
            precipIntensityMaxTime: info.precipIntensityMaxTime,
            sunriseTime: info.sunriseTime,
            sunsetTime: info.sunsetTime
          };
          let date = new Date(day.date * 1000);
          let month = date.getMonth();
          day.date = `${
            months[month]
          }, ${date.getDate()}, ${date.getFullYear()}`;
          getHours(day);
          weatherIcon();
          daysOfWeek.push(day);
        });

        return daysOfWeek;
      } else {
        throw new Error(json);
      }
    } else {
      throw new Error(locationJSON);
    }
  }

  let userLocationFormHandler = () => {
    // getUserLocationCoord(userLocation, userLocationCoord);
    promise = harborSpringsWeather();
  };
</script>

<style>
  .cardContainer {
    display: grid;
    grid-template-columns: repeat(
      auto-fill,
      minmax(min(calc(250px + 12vmin), 100%), 1fr)
    );
    list-style: none;
    grid-gap: 0.25em;
  }
  .weatherCard {
    border: solid 1px lightgray;
    border-radius: 5px;
    background: white;
  }
  .dateTitle {
    text-align: center;
    font-size: 1.25em;
    padding: 0.25em;
    border-bottom: solid 1px lightgray;
  }
  .cardMainInfo {
    padding: 1em;
  }
  strong {
    font-weight: 600;
  }
</style>

<div class="formWrapper">
  <div class="searchCont">
    <h1>
      Stay up to date with all things weather today and for the coming week.
    </h1>
    <form
      class="userLocationForm"
      on:submit|preventDefault={userLocationFormHandler}>
      <input type="text" name="userLocation" bind:value={userLocation} />
      <button type="submit">Get {userLocation} Weather Current</button>
    </form>
  </div>
</div>

{#await promise}
  <p>Fetching your weather report!</p>
{:then text}
  <ul class="cardContainer">
    {#each daysOfWeek as day}
      <li class="weatherCard">
        <p class="dateTitle">{day.date}</p>
        <div class="cardMainInfo">
          <img src={day.icon} alt="projectedWeaterImage" />
          <p>
            <strong>Daily Summary:</strong>
            {day.summary}
          </p>
          <p
            <strong>Sunrise:</strong>
            {day.sunriseTime}
          </p>
          <p>
            <strong>Sunset:</strong>
            {day.sunsetTime}
          </p>
          <p>
            <strong>Temp High:</strong>
            {day.temperatureHigh}
          </p>
          <p>
            <strong>Temp High Time:</strong>
            {day.temperatureHighTime}
          </p>
          <p>
            <strong>Temp Low:</strong>
            {day.temperatureLow}
          </p>
          <p>
            <strong>Temp Low Time:</strong>
            {day.temperatureLowTime}
          </p>
        </div>
      </li>
    {/each}
  </ul>
{:catch error}
  <h2>Ready For Your Search!</h2>
{/await}
