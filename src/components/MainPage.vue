<template>
  <div class="wrapper">
    <autocompleteInput @cityValue="onInputSelectCity" />
    <div class="buttons">
      <actionButton @click="onPageChange(pages.pageHome)" text="HOME" :class="page == pages.pageHome ? 'active': ''" />
      <actionButton @click="onPageChange(pages.pageFavorite)" text="FAVORITE" :class="page == pages.pageFavorite ? 'active': ''" />
    </div>
    <div v-if="page == pages.pageHome">
      <cityWeather
        @onToggleFavorite="onToggleFavorite"
        :isFavorite="favoriteCities.includes(city)"
        v-for="city in cities"
        :city="city"
        :key="city"
      />
    </div>
    <div v-if="page == pages.pageFavorite">
      <cityWeather
        @onToggleFavorite="onToggleFavorite"
        :isFavorite="favoriteCities.includes(city)"
        v-for="city in favoriteCities"
        :city="city"
        :key="city"
      />
    </div>
  </div>
</template>

<script>
import actionButton from "./Shared/actionButton.vue";
import autocompleteInput from "./Shared/autocompleteInput.vue";
import cityWeather from "./WeatherResult/cityWeather.vue";
import weatherApi from "../api";
import config from '../config'

export default {
  components: {
    autocompleteInput,
    cityWeather,
    actionButton,
  },
  data() {
    return {
      
      cities: [],
      query: "",
      weather: {},
      weekweather: {},
      forecast: {},
      favoriteCities: [],
      src: "",
      page: config.pageHome,
      pages: {
        pageHome: config.pageHome,
        pageFavorite: config.pageFavorite,
      } 
    };
  },
  mounted() {
    // this.cities = ["Kyiv"];
    weatherApi.getUserInfo().then((result) => {
      this.cities = [result.city];
    });

    const array = localStorage.getItem(config.favoriteStoreKey);
    if (array) {
      this.favoriteCities = JSON.parse(array);
    }
  },
  methods: {
    // змінюємо активний компонент
    onPageChange(page) {
      this.page = page;
    },
    // додаємо або видаляємо місто з масива улюблені
    onToggleFavorite(city, onlyAdd) {
      const index = this.favoriteCities.indexOf(city);
      if (index > -1) {
        if (onlyAdd) {
          return;
        }
        if (!confirm('are you sure?')) {
          return
        } 
        this.favoriteCities.splice(index, 1);
      } else {
        
        if (this.favoriteCities.length >= config.maximumFavoriteCity) {
          alert("First delete one city");
          return;
        }
        this.favoriteCities.push(city);
      }
      localStorage.setItem(config.favoriteStoreKey, JSON.stringify(this.favoriteCities));

      // console.log("FAVORITE CITY", this.favoriteCities);
    },
    // в залежності від активного компонента додаємо місто в масив міст або масив улюблених
    onInputSelectCity(city) {
      if (this.page == config.pageHome) {
        this.addNewCityToArray(city)
        return;
      }
      this.onToggleFavorite(city, true);
    },
    // додаємо місто до масива міст
    addNewCityToArray(city) {
      if (this.cities.includes(city)) {
        alert("choose diferent city");
        return;
      }
      if (this.cities.length >= config.maximumCity) {
        alert("You can add only 5 cities");
        return;
      }
      this.cities.push(city);
    },
  },
};
</script>

<style  scoped>
.wrapper {
  max-width: 800px;
  margin: 135px auto;
  background: #fff;
  border-radius: 7px;
  padding: 20px;
}

.search_box {
  width: 100%;
  margin-bottom: 30px;
  display: flex;
}
.search_box .search_bar {
  display: block;
  width: 100%;
  width: 100%;
  margin: 0 40px;
  padding: 15px;
  color: #313131;
  font-size: 20px;
  border-radius: 5px;
  border: none;
  outline: none;
  box-shadow: 0 0 8px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.5);
  transition: 0.4s;
}
.active{
  background-color: rgb(169 169 169 / 50%) !important;
}
.search_box .search_bar:hover {
  background-color: rgba(255, 255, 255, 0.7);
  box-shadow: 0 0 16px rgba(0, 0, 0, 0.25);
}

.slide-enter-active,
.slide-leave-active {
  transition: opacity 1s, transform 0.7s;
}

.slide-enter-from,
.slide-leave-to {
  opacity: 0;
  transform: translateY(-30%);
}
</style>