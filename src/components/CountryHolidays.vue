<template>
  <div class="wrapper">
    <h1>Country holidays</h1>
    Select Country:
    <select v-model="country">
      <option v-for="item in countryCodes" :key="item" :value="item">{{ item }}</option>
    </select>
    <button @click="handleSearch">Search Holidays</button>
    <template v-if="coutryAdminDivisions.length > 0">
      Select adminDivisions:
      <select v-model="adminDivisions">
        <option v-for="item in coutryAdminDivisions" :key="item.region">{{item.region}}</option>
      </select>
      <button @click="handleSearchWeatherAndHotel">Search Weather And Hotel</button>
    </template>
    <HolidayList :country="country" :holidays="holidays" @chooseHoliday="handleChooseHoliday"/>
    <Weather :weather="weather"/>
    <Hotels :hotels="hotels"/>
  </div>
</template>

<script>
import countryCodes from '../CountryCode.json';

const requestCityWeather = async (city) => {
  const URL = `https://api.weatherapi.com/v1/forecast.json?key=e1134f4f25e3479b87322624202904&q=${city}`;
  return fetch(URL).then(res => res.json());
}

const requestHotel = async (city) => {
  const URL = `https://hotels4.p.rapidapi.com/locations/search?query=${city}&locale=en_US`;
  return fetch(URL, {
     headers: {
      'x-rapidapi-key': 'b0f32c0012msh24cf1286fbc01b8p14c070jsn48150cf35a4b',
      'x-rapidapi-host': 'hotels4.p.rapidapi.com',
      'useQueryString': true
    }
  }).then(res => res.json());
}

const requestHolidaysOfCountry = async (country) => {
  const URL = `https://holidays.abstractapi.com/v1/?api_key=5f3369df331646a582d86fbc748ac54d&country=${country}&year=2020`;
  return fetch(URL).then(res => res.json());
}

const requestCountryAdminDivisions = async (country) => {
  const URL = `https://wft-geo-db.p.rapidapi.com/v1/geo/adminDivisions?countryIds=${country}`;
  return fetch(URL, {
    headers: {
      'x-rapidapi-key': 'b0f32c0012msh24cf1286fbc01b8p14c070jsn48150cf35a4b',
      'x-rapidapi-host': 'wft-geo-db.p.rapidapi.com',
      'useQueryString': true
    }
  }).then(res => res.json());
}

import HolidayList from './HolidayList';
import Weather from './Weather';
import Hotels from './Hotels';
export default {
  name: 'CountryHolidays',
  data() {
    return {
      weather: null,
      country: '',
      adminDivisions: '',
      selectedHoliday: null,
      holidays: [],
      hotels: [],
      countryCodes,
      coutryAdminDivisions: []
    }
  },
  components: {
    HolidayList,
    Weather,
    Hotels
  },
  mounted() {
  },
  methods: {
    async handleChooseHoliday(holiday) {
      this.selectedHoliday = holiday;
    },
    async handleSearchWeatherAndHotel() {
      const adminDivisions = this.adminDivisions;
      const holiday = this.selectedHoliday;
      if (!adminDivisions || !holiday) {
        alert("You must choose a adminDivision and a holiday");
        return;
      }
      const suggestedHotels = [];
      const fetches = [requestCityWeather(adminDivisions), requestHotel(adminDivisions)];
      const fetchesResult = await Promise.all(fetches);
      const [cityWeathers, hotels] = fetchesResult;
      const holidayDate = `${holiday.date_year}-${holiday.date_month}-${holiday.date_day}`;
      const holidayWeather = cityWeathers.forecast.forecastday.find(item => item.date === holidayDate);
      const weather = holidayWeather && holidayWeather.day.condition || null;
      this.weather = weather;
      if (Array.isArray(hotels.suggestions)) {
        hotels.suggestions.forEach(item => {
          suggestedHotels.push(...item.entities);
        });
      }
      this.hotels = suggestedHotels;
    },
    async handleAfterSearchHolidays() {
      const res = await requestCountryAdminDivisions(this.country);
      if (res && res.data) {
        this.coutryAdminDivisions = res.data;
      }
    },
    async handleSearch() {
      const { country } = this;
      const res = await requestHolidaysOfCountry(country);
      this.holidays = res.reduce((prev, next) => {
        const holiday = prev.find(item => item.name === next.name);
        if (!holiday) {
          prev.push(next);
        }
        return prev;
      }, []);
      this.handleAfterSearchHolidays();
    }
  }
}
</script>

<style scoped>
.wrapper {
  box-sizing: border-box;
  padding: 10px;
  width: 80vw;
  border: 4px solid darkcyan;
}
</style>
