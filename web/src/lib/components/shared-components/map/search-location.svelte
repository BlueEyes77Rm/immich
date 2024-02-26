<script lang="ts">
  import Button from '$lib/components/elements/buttons/button.svelte';

  //import { countries } from './countriesData.js';
  import Location from './location.svelte';

  import { ConvertLocationOutput, LocationOutput } from '../../../models/location-output';
  import { createEventDispatcher } from 'svelte';

  interface Point {
    lng: number;
    lat: number;
  }

  const dispatch = createEventDispatcher<{
    locationSelected: Point;
  }>();

  let location: string;
  let noLocationFound: boolean | false;

  /* FILTERING countres DATA BASED ON INPUT */
  let locationsResult = [];
  // $: console.log(locationsResult)

  /* const filterCountries = () => {
    let storageArr = [];
    if (inputValue) {
      countries.forEach((country) => {
        if (country.toLowerCase().startsWith(inputValue.toLowerCase())) {
          storageArr = [...storageArr, makeMatchBold(country)];
        }
      });
    }
    locationsResult = storageArr;
  }; */

  const handleSearch = async () => {
    if (!location) {
      return;
    } else {
      noLocationFound = false;
      //&polygon_geojson=1&limit=1&
      const response = await fetch(
        encodeURI('https://nominatim.openstreetmap.org/search?q=' + location + '&format=jsonv2'),
      );
      const data = await response.text();
      //console.log(data);
      if (!data || data.length === 0) {
        noLocationFound = true;
      } else {
        try {
          const locRes = ConvertLocationOutput.toLocationOutput(data);
          //console.log(locRes);
          locationsResult = [
            ...new Set(
              locRes.map((l) => {
                return {
                  display_name: l.display_name,
                  lat: l.lat,
                  lon: l.lon,
                };
              }),
            ),
          ];
          document.querySelector('#autocomplete-items-list')?.focus();
          //console.log('locationsResult', locationsResult);
        } catch (e) {
          console.log('Handle error', e);
        }
      }
    }
  };

  /* HANDLING THE INPUT */
  //let searchInput; // use with bind:this to focus element
  let inputValue = '';

  $: if (!inputValue) {
    locationsResult = [];
    hiLiteIndex = null;
  }

  /* const clearInput = () => {
    inputValue = '';
    searchInput.focus();
  }; */

  const setInputVal = (country) => {
    if (country) {
      inputValue = country.display_name;
      locationsResult = [];
      hiLiteIndex = null;
      document.querySelector('#location-input')?.focus();
      const point = {
        lat: parseFloat(country.lat),
        lng: parseFloat(country.lon),
      };
      dispatch('locationSelected', point);
    } else {
      alert("You didn't type anything.");
    }
  };

  const submitValue = () => {};

  /* const makeMatchBold = (str) => {
    // replace part of (country name === inputValue) with strong tags
    let matched = str.substring(0, inputValue.length);
    let makeBold = `<strong>${matched}</strong>`;
    let boldedMatch = str.replace(matched, makeBold);
    return boldedMatch;
  }; */

  /*   const removeBold = (str) => {
    //replace < and > all characters between
    return str.replace(/<(.)*?>/g, '');
    // return str.replace(/<(strong)>/g, "").replace(/<\/(strong)>/g, "");
  }; */

  /* NAVIGATING OVER THE LIST OF COUNTRIES W HIGHLIGHTING */
  let hiLiteIndex = null;
  //$: console.log(hiLiteIndex);
  $: hiLitedCountry = locationsResult[hiLiteIndex];

  const navigateList = (e) => {
    if (e.key === 'ArrowDown' && hiLiteIndex <= locationsResult.length - 1) {
      hiLiteIndex === null ? (hiLiteIndex = 0) : (hiLiteIndex += 1);
    } else if (e.key === 'ArrowUp' && hiLiteIndex !== null) {
      hiLiteIndex === 0 ? (hiLiteIndex = locationsResult.length - 1) : (hiLiteIndex -= 1);
    } else if (e.key === 'Enter') {
      if (e.target.id === 'location-input') {
        handleSearch();
      } else {
        setInputVal(locationsResult[hiLiteIndex]);
      }
    } else {
      return;
    }
  };
</script>

<svelte:window on:keydown={navigateList} />

<form autocomplete="off" on:submit|preventDefault={submitValue}>
  <div class="autocomplete">
    <!-- <input
      id="country-input"
      type="text"
      placeholder="Search Country Names"
      bind:this={searchInput}
      bind:value={inputValue}
      on:input={filterCountries}
    /> -->
    <div class="w-full w-[300px]">
      <input
        id="location-input"
        type="text"
        class="w-full dark:bg-immich-dark-gray px-8 py-4 text-immich-fg/75 dark:text-immich-dark-fg rounded-3xl border border-transparent bg-gray-200"
        placeholder="Search location"
        bind:value={location}
      />
      <!-- FILTERED LIST OF COUNTRIES -->
      {#if locationsResult.length > 0}
        <ul id="autocomplete-items-list">
          {#each locationsResult as country, i}
            <Location item={country} highlighted={i === hiLiteIndex} on:click={() => setInputVal(country)} />
          {/each}
        </ul>
      {/if}
    </div>
    <div style="margin-left: 5px; margin-top: 10px;">
      <Button size="sm" on:click={handleSearch}>Search</Button>
    </div>
  </div>

  <!-- <input type="submit" /> -->
</form>

<style>
  div.autocomplete {
    /*the container must be positioned relative:*/
    position: relative;
    display: flex;
    width: 380px;
    margin: auto;
  }
  /* input {
    border: 1px solid transparent;
    background-color: #f1f1f1;
    padding: 10px;
    font-size: 16px;
    margin: 0;
  }
  input[type='text'] {
    background-color: #f1f1f1;
    width: 100%;
  }
  input[type='submit'] {
    background-color: DodgerBlue;
    color: #fff;
  } */

  #autocomplete-items-list {
    z-index: 100;
    position: relative;
    margin: 0;
    padding: 0;
    top: 0;
    width: 297px;
    border: 1px solid #ddd;
    background-color: #ddd;
    border-radius: 10px;
    max-height: 100px;
    overflow: auto;
  }
</style>
