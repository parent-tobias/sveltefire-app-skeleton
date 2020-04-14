
<script>
  import { createEventDispatcher}  from 'svelte';
  import Card from './Card.svelte';
  
  let dispatcher = createEventDispatcher();
  
  export let user;
  export let privateStore;
  
  let weatherObject;
  const weatherAPIUrl = "https://api.openweathermap.org/data/2.5/weather?APPID=42b3e750d3710a0d50734387e48e3e1a&";
  
  // Convenience functions
  // returns true if a string is a valid zip
  const isValidZip = (str) => /^\d{5}$/.test(str);
  // trigger a flip event, used on the Card
  const flipEvent = () => dispatch("card.flip");
  // given an icon id, get the proper image.
  const getWeatherIcon = (id)=> `http://openweathermap.org/img/wn/${id}@2x.png`;
  // Take the location object and pull out its lat/long, return a URL
  const updateWeatherApi = ({lat, long})=>`${weatherAPIUrl}lon=${long}&lat=${lat}`;
  // Given a zip, get the location object (lat/long)
  const geocodingApi = (zip) => `https://api.zip-codes.com/ZipCodesAPI.svc/1.0/GetZipCodeDetails/${zip}?key=DKGO59K3TMAO35B6AUKI`
  // We'll get temps in Kelvin. Convenience function to convert them to F.
  const tempInF = (temp) => ((temp-273.15)*9/5+32).toFixed(2);
  const tempInC = (temp) => (temp-273.15).toFixed(2);

  // The workhorse. This gets the current location, then calls
  //   the API to get the weather.
  const getWeather = ()=>{
    // Call to get the current location.
    const location = privateStore.get('location');
    if(location && location.lat){
      // Convert our complex object to a simpler one, just for convenience sake.
      const locationObj = {
        lat: location.coords.latitude.toFixed(2), 
        long: location.coords.longitude.toFixed(2)
      };
      const tempUrl = updateWeatherApi(location);

      fetch(tempUrl)
        .then(response=>response.json())
        .then(jsonObj=>{
          weatherObject = JSON.parse(JSON.stringify(jsonObj));
          updateWeatherDisplay();
         });   
    }
  }

  const updateWeatherDisplay = ()=>{
    const weatherIcon = getWeatherIcon(weatherObject.weather[0].icon);
    let units = "English";
    if(privateStore.get("units")){
      units = JSON.parse(privateStore.get("units")).value;  
    }
    unitsEl.value = units;

    // the temps come in in Kelvin. Depending on the units, we convert them.
    const temps = units === "English" ? 
              {
                feels_like: tempInF(weatherObject.main.feels_like),
                temp: tempInF(weatherObject.main.temp),
              }
             :
              {
                feels_like: tempInC(weatherObject.main.feels_like),
                temp: tempInC(weatherObject.main.temp),
              }

    displayPane.innerHTML = `<p>In ${weatherObject.name}, the temp is ${temps.temp} but feels like ${temps.feels_like}</p><figure><img src='${weatherIcon}' alt='${weatherObject.weather[0].main}' ><figcaption>${weatherObject.weather[0].description}</figcaption></figure>`

  }

  const acceptClickHandler = (evt) => {
    // Let's save the position doohickey!
    const unitsEl = document.querySelector("[name='units']"),
          zipEl = document.querySelector("[name='zip']");
    const units = {
      selectedIndex: unitsEl.selectedIndex,
      value: unitsEl[unitsEl.selectedIndex].value
    };
    const location = {
      zip: zipEl.value
    };

    privateStore.set("units", units);

    if(isValidZip(location.zip)) {
      fetch(geocodingApi(location.zip))
        .then(response => response.json())
        .then(jsonObj => {
          location.lat = jsonObj.item.latitude;
          location.long = jsonObj.item.longitude;
          location.city = jsonObj.item.mixedCaseCity;
          location.state = jsonObj.item.state;
        
          privateStore.set("location", location)
          updateWeatherDisplay();
        })
    };
    
    flipEvent();
  };

  const cancelClickHandler = (evt) => {
    flipEvent();
  };

  getWeather();
</script>

  <svg style="position: absolute; width: 0; height: 0; overflow: hidden" version="1.1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
  <defs>
<symbol id="icon-cogs" viewBox="0 0 32 32">
<title>cogs</title>
<path d="M11.366 22.564l1.291-1.807-1.414-1.414-1.807 1.291c-0.335-0.187-0.694-0.337-1.071-0.444l-0.365-2.19h-2l-0.365 2.19c-0.377 0.107-0.736 0.256-1.071 0.444l-1.807-1.291-1.414 1.414 1.291 1.807c-0.187 0.335-0.337 0.694-0.443 1.071l-2.19 0.365v2l2.19 0.365c0.107 0.377 0.256 0.736 0.444 1.071l-1.291 1.807 1.414 1.414 1.807-1.291c0.335 0.187 0.694 0.337 1.071 0.444l0.365 2.19h2l0.365-2.19c0.377-0.107 0.736-0.256 1.071-0.444l1.807 1.291 1.414-1.414-1.291-1.807c0.187-0.335 0.337-0.694 0.444-1.071l2.19-0.365v-2l-2.19-0.365c-0.107-0.377-0.256-0.736-0.444-1.071zM7 27c-1.105 0-2-0.895-2-2s0.895-2 2-2 2 0.895 2 2-0.895 2-2 2zM32 12v-2l-2.106-0.383c-0.039-0.251-0.088-0.499-0.148-0.743l1.799-1.159-0.765-1.848-2.092 0.452c-0.132-0.216-0.273-0.426-0.422-0.629l1.219-1.761-1.414-1.414-1.761 1.219c-0.203-0.149-0.413-0.29-0.629-0.422l0.452-2.092-1.848-0.765-1.159 1.799c-0.244-0.059-0.492-0.109-0.743-0.148l-0.383-2.106h-2l-0.383 2.106c-0.251 0.039-0.499 0.088-0.743 0.148l-1.159-1.799-1.848 0.765 0.452 2.092c-0.216 0.132-0.426 0.273-0.629 0.422l-1.761-1.219-1.414 1.414 1.219 1.761c-0.149 0.203-0.29 0.413-0.422 0.629l-2.092-0.452-0.765 1.848 1.799 1.159c-0.059 0.244-0.109 0.492-0.148 0.743l-2.106 0.383v2l2.106 0.383c0.039 0.251 0.088 0.499 0.148 0.743l-1.799 1.159 0.765 1.848 2.092-0.452c0.132 0.216 0.273 0.426 0.422 0.629l-1.219 1.761 1.414 1.414 1.761-1.219c0.203 0.149 0.413 0.29 0.629 0.422l-0.452 2.092 1.848 0.765 1.159-1.799c0.244 0.059 0.492 0.109 0.743 0.148l0.383 2.106h2l0.383-2.106c0.251-0.039 0.499-0.088 0.743-0.148l1.159 1.799 1.848-0.765-0.452-2.092c0.216-0.132 0.426-0.273 0.629-0.422l1.761 1.219 1.414-1.414-1.219-1.761c0.149-0.203 0.29-0.413 0.422-0.629l2.092 0.452 0.765-1.848-1.799-1.159c0.059-0.244 0.109-0.492 0.148-0.743l2.106-0.383zM21 15.35c-2.402 0-4.35-1.948-4.35-4.35s1.948-4.35 4.35-4.35 4.35 1.948 4.35 4.35c0 2.402-1.948 4.35-4.35 4.35z"></path>
</symbol>
<symbol id="icon-location2" viewBox="0 0 32 32">
<title>location2</title>
<path d="M16 0c-5.523 0-10 4.477-10 10 0 10 10 22 10 22s10-12 10-22c0-5.523-4.477-10-10-10zM16 16.125c-3.383 0-6.125-2.742-6.125-6.125s2.742-6.125 6.125-6.125 6.125 2.742 6.125 6.125-2.742 6.125-6.125 6.125zM12.125 10c0-2.14 1.735-3.875 3.875-3.875s3.875 1.735 3.875 3.875c0 2.14-1.735 3.875-3.875 3.875s-3.875-1.735-3.875-3.875z"></path>
</symbol>
</defs>
</svg>
  <Card>
    <div class='front-content' slot='card-front'>
      <header>
        <h1>{user.displayName}Weather watcher!</h1>
        <span class='settings-icon'><svg class="icon icon-cogs" on:click={handleFlip}><use xlink:href="#icon-cogs"></use></svg></span>
      </header>
      <div class="weather-pane">
        
      </div>
    </div>
    <div class='back-content' slot='card-back'>
      <header><h3>Preferences</h3></header>
      <div class="prefs-pane">
        <label>Zip: <input type='text' name='zip' /></label>
        <label>Units: <select name='units'>
          <option value='English'>English(F)</option>
          <option value='Metric'>Metric(C)</option>
          </select>
      </div>
      <footer>
        <button name='accept'>Okay</button> <button name='cancel'>Cancel</button>
      </footer>
    </div>
  </Card>

<style>
  header {
    display: flex;
    border-bottom: 1px solid silver;
  }

  h1,
  h2,
  h3,
  h4,
  h5,
  h6 {
    font-family: "Lilita One", cursive;
  }
  h1 {
    font-size: 1.5em;
    text-align: center;
    width: 100%;
    margin: 15px 0 0 0;
  }

  h3,
  p {
    text-align: center;
  }

  h6 {
    text-transform: uppercase;
    line-height: 1em;
  }
  figure {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: space-around;
    background-color: hsl(135, 49%, 60%);
    border-radius: 5px;
    width: 180px;
    margin: 0 auto;
    padding: 0;
  }

  .icon {
    display: inline-block;
    width: 1em;
    height: 1em;
    stroke-width: 0;
    stroke: currentColor;
    fill: currentColor;
  }
</style>
