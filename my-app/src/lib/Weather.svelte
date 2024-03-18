<script lang="ts">
  import { onMount } from "svelte";
  import drizzle from "../assets/drizzle.png";
  import humidity from "../assets/humidity.png";
  import rain from "../assets/rain.png";
  import search from "../assets/search.png";
  import snow from "../assets/snow.png";
  import wind from "../assets/wind.png";
  import clear from "../assets/clear.png";
  import cloud from "../assets/cloud.png";
  import Loader from "./Loader.svelte";
  import { writable } from "svelte/store";

  let wicon: string = "cloud";
  let isLoading: boolean = false;
  let datas: any = [];
  let items = writable([]);
  let isDataLoaded: boolean = false;
  let api_key = "a7cd2ad4816ed05ae76378735396feb6";
  let desc: string = "";
  console.log(api_key);
  const searchWeather = async () => {
    const element = document.querySelector(".city") as HTMLInputElement;
    if (!element || element.value === "") {
      return 0;
    }

    let url = `https://api.openweathermap.org/data/2.5/weather?q=${element.value}&units=metric&appid=${api_key}`;

    try {
      setIsLoading(true);
      let response = await fetch(url);

      if (!response.ok) {
        setIsDataLoaded(false);
        console.error("Error fetching weather data:", response.status);
        return;
      } else {
        setIsDataLoaded(false);
      }

      let data = await response.json();
      console.log(data);
      data.weather.map((item: any) => setDes(item.description));
      items.set(data);

      if (data.weather[0].icon === "01d" || data.weather[0].icon === "01n") {
        setWicon(clear);
      } else if (
        data.weather[0].icon === "02d" ||
        data.weather[0].icon === "02n"
      ) {
        setWicon(cloud);
      } else if (
        data.weather[0].icon === "03d" ||
        data.weather[0].icon === "03n"
      ) {
        setWicon(drizzle);
      } else if (
        data.weather[0].icon === "04d" ||
        data.weather[0].icon === "04n"
      ) {
        setWicon(drizzle);
      } else if (
        data.weather[0].icon === "09d" ||
        data.weather[0].icon === "09n"
      ) {
        setWicon(rain);
      } else if (
        data.weather[0].icon === "10d" ||
        data.weather[0].icon === "10n"
      ) {
        setWicon(rain);
      } else if (
        data.weather[0].icon === "13d" ||
        data.weather[0].icon === "13n"
      ) {
        setWicon(snow);
      } else {
        setWicon(clear);
      }
    } catch (error) {
      setIsDataLoaded(false);
      console.error("Error fetching weather data:", error);
      // Handle other errors (e.g., network issues)
    } finally {
      setIsLoading(false);
    }
  };

  items.subscribe((value) => {
    datas = value;
  });

  function setWicon(icon: string) {
    wicon = icon;
  }

  function setDes(des: string) {
    desc = des;
    console.log(desc);
  }

  function setIsLoading(value: boolean) {
    isLoading = value;
  }

  function setIsDataLoaded(value: boolean) {
    isDataLoaded = value;
  }

  onMount(() => {
    console.log("Component mounted");
  });

  console.log(desc);
</script>

<div>
  <div class="flex items-center justify-center">
    <div
      class="w-96 h-[80vh] rounded-lg bg-gradient-to-b from-indigo-900 to-purple-700 mt-8 flex flex-col items-center justify-center"
    >
      <h1 class="text-white mt-5 text-center text-3xl font-semibold mb-5">
        Weather Information
      </h1>
      <div class="flex items-center justify-center gap-4 mb-8">
        <input
          type="text"
          class="city w-64 h-12 rounded-full bg-white px-4 text-gray-800 focus:outline-none"
          placeholder="Search City (eg: Noida)"
        />
        <div
          class=" rounded-full bg-white w-12 h-12 flex items-center justify-center cursor-pointer"
          on:click={() => {
            searchWeather();
          }}
        >
          <img src={search} alt="" />
        </div>
      </div>
      {#if isLoading}
        <Loader />
      {:else}
        <div class="weather_image">
          <img src={wicon} alt="" class="cloud" />
        </div>
        <div class="text-white text-5xl font-semibold mb-2 mt-4">
          {datas?.main?.temp ? datas?.main?.temp : 12}°C
        </div>
        <div class="text-white text-2xl mb-4">
          {datas?.name ? datas?.name : "Faridabad"}
        </div>

        <div class="text-white text-2xl mb-4">
          {desc}
        </div>

        <div class="flex justify-center text-white gap-8 pe-4 ps-2 mb-5">
          <div class="mx-auto flex items-center gap-12">
            <img src={humidity} alt="" class="icon" />
            <div class="text-3xl font-bold">
              <div class="text-white text-xl">
                {datas?.main?.humidity ? datas?.main?.humidity : 53}%
              </div>
              <div class="text-xl font-bold">Humidity</div>
            </div>
          </div>
          <div class="mx-auto flex items-center gap-12 pe-3">
            <img src={wind} alt="" class="icon" />
            <div class="text-3xl font-bold">
              <div class="text-white text-xl">
                {datas?.wind?.speed ? datas?.wind?.speed : 12}km/h
              </div>
              <div class="text-xl font-bold">Wind Speed</div>
            </div>
          </div>
        </div>
      {/if}
    </div>
  </div>
</div>

<style>
  .weather_image {
    margin-top: -30px;
    display: flex;
    justify-content: center;
  }

  .weather_image .cloud {
    width: 80px;
  }
</style>
