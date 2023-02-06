<template>
  <div class="search_box">
    <div class="search_wrapper">
      <input type="text" class="search_bar" placeholder="Search...." v-model="search" />
      <div class="loading">
        <span v-if="isLoading">Loading...</span>
        <!-- v-if="isLoading" -->
      </div>
    </div>
    <div class="error_bar" v-if="error?.length > 0">
      <p>{{ error }}</p>
    </div>
    <div class="found_city_wraper" v-if="options?.length > 0">
      <button
        class="found_city"
        v-for="city in options"
        :key="city"
        @click="onSelectCity(city)"
      >{{ city.name }} {{ city.sys.country }}</button>
    </div>
  </div>
</template>

<script>
import weatherApi from "../../api";

export default {
  data() {
    return {
      search: "",
      error: "",
      options: [],
      date: null,
      isLoading: false,
    };
  },
  methods: {
    onSelectCity(city) {
      this.$emit("cityValue", city.name);
      this.options = [];
    },
  },
  watch: {
    search: function (value) {
      const a = this;
      const currentDate = Date.now();
      a.date = currentDate;
      a.isLoading = true;
      setTimeout(function () {
        // console.log("asdasdasd", a.date, currentDate);
        if (a.date != currentDate) {
          // знову натиснувся інпут
          return;
        }
        if (value.length < 4) {
          // ввели мельше 4 символів
          a.error = "add more symbols";
          a.isLoading = false;
          return;
        }
        a.error = null;
        weatherApi
          .findCity(a.search)
          .then((result) => {
            if (result.count < 1) {
              // отримали помилку що немає результатів
              a.options = null;
              a.error = "no such city";
              return;
            }
            //   отримали результат
            a.options = result.list;
          })
          .catch((error) => {
            console.error(error);
            a.error = "Api error";
          })
          .finally(() => {
            a.isLoading = false;
          });
      }, 1000);
    },
  },
};
</script>

<style scoped>
.search_box {
  width: 100%;
  margin-bottom: 30px;
  position: relative;
  background: none;
  display: flex;
  flex-direction: column;
}
.search_wrapper {
  display: flex;
  width: 100%;
  background: none;
}
.search_box .search_bar {
  width: 100%;
  border-radius: 5px;
  padding: 15px;
  
}
.loading {
  position: absolute;
  top: 20px;
  right: 50px;
}
.found_city {
  width: 100%;
  padding: 15px;
  text-align: left;
  box-shadow: 0 0 8px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255);
  border: none;
  transition: 0.4s;
  cursor: pointer;
}
.error_bar{
  padding: 15px;
  transition: 0.4s;
  position: absolute;
  width: 300px;
  top: 50px;
  left: 40px;
  background-color: rgba(255, 255, 255);
  box-shadow: 0 0 8px rgba(0, 0, 0, 0.25);
}

.search_box .search_bar,
.found_city_wraper {
  margin: 0 40px;
  color: #313131;
  font-size: 20px;
  border: none;
  outline: none;
  background: none;
  box-shadow: 0 0 8px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.5);
  border-radius: 5px;
  transition: 0.4s;
}
.found_city_wraper{
  position: absolute;
  top: 50px;
  width: 300px;
}
.error_bar,
.found_city {
  border-radius: 0 0 5px 5px;
}
.search_box .search_bar:hover
 {
  background-color: rgba(255, 255, 255, 0.7);
  box-shadow: 0 0 16px rgba(0, 0, 0, 0.25);
}
.error_bar:hover,
.found_city:hover{
  box-shadow: 0 0 16px rgba(0, 0, 0, 0.25);
}
</style>