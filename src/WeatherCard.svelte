<script>
  let promise = harborSpringsWeather();
  let response;
  let json = {};
  let daysOfWeek = [];
  let error;
  let eachDay;
  let day = {
    date: '',
    icon: '',
    summary: '',
    temperatureHighTime: '',
    temperatureHigh: '',
    temperatureLowTime: '',
    temperatureLow: '',
    precipProbability: '',
    precipType: '',
    precipAccumulation: '',
    precipIntensityMaxTime: '',
    sunriseTime: '',
    sunsetTime: ''
  }
  let harborSpringsCall = 'https://cors-anywhere.herokuapp.com/https://api.darksky.net/forecast/6391cb4b579b96ba00ae2f374f79d60b/42.5751,-83.4882';
  let months = [
    'January',
    'February',
    'March',
    'April',
    'May',
    'June',
    'July',
    'August',
    'September',
    'October',
    'November',
    'December'
  ]

    let getHours = (day) => {
      let temperatureLowTime = new Date(day.temperatureLowTime * 1000);
      let temperatureHighTime = new Date(day.temperatureHighTime * 1000);
      let lowHrs = temperatureLowTime.getHours();
      let lowMin = temperatureLowTime.getMinutes();
      if(lowMin == '0'){
        lowMin = lowMin + '0';
      }
      let highHrs = temperatureHighTime.getHours();
      let highAmPm = highHrs >= 12 ? 'pm' : 'am';
      highHrs = highHrs - 12;
      let highMin = temperatureHighTime.getMinutes();
      if(highMin == '0'){
        highMin = lowMin + '0';
      }
      let lowAmPm = lowHrs >= 12 ? 'pm' : 'am';
      day.temperatureLowTime = `${lowHrs}:${lowMin} ${lowAmPm}`;
      console.log(day.temperatureLowTime);
      day.temperatureHighTime = `${highHrs}:${highMin} ${highAmPm}`;
      console.log(day.temperatureHighTime);
      return day;
    };

    let weatherIcon = () => {
      switch (day.icon) {
        case 'sunny':
          day.icon = `assets/sun.svg`;
          break;
        case 'clear-day':
          day.icon = `assets/sun.svg`;
          break;
        case 'rain':
          day.icon = `assets/rain.svg`;
          break;
        case 'partly-cloudy-day':
          day.icon = `assets/cloudy.svg`;
          break;
        case 'cloudy':
          day.icon = `assets/cloudy.svg`;
          break;
      }
    }




  async function harborSpringsWeather() {
    response = await fetch(harborSpringsCall);
    json = await response.json();
    if(response.ok){
      console.log(json.daily.data )
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
        }
        let date = new Date(day.date * 1000);
        let month = date.getMonth();
        day.date = `${months[month]}, ${date.getDate()}, ${date.getFullYear()}`;
        getHours(day);
        weatherIcon();
        daysOfWeek.push(day);
      })

      return daysOfWeek;
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
  <ul>
    {#each daysOfWeek as day}
      <li>
        <p>Date: {day.date}</p>
        <img src={day.icon} alt="">
        <p>Daily Summary: {day.summary}</p>
        <p>Temp High: {day.temperatureHigh}</p>
        <p>Temp High Time: {day.temperatureHighTime}</p>
        <p>Temp Low: {day.temperatureLow}</p>
        <p>Temp Low Time: {day.temperatureLowTime}</p>

      </li>
    {/each}
  </ul>
{:catch error}
<p>Waiting To Start...</p>
{/await}