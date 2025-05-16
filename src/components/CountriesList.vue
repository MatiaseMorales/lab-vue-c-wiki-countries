<template>
  <div class="row" style="height: 100vh;">
    <div class="col-5" style="overflow-y: auto; height: 100vh;">
      <div class="list-group">
        <router-link
          v-for="country in countries"
          :key="country.alpha3Code"
          class="list-group-item list-group-item-action d-flex flex-column align-items-start"
          :to="`/list/${country.alpha3Code}`"
          style="padding: 10px 15px;"
        >
          <img
            :src="`https://flagpedia.net/data/flags/icon/72x54/${country.alpha2Code.toLowerCase()}.png`"
            alt="flag"
            style="width: 72px; height: 54px; object-fit: contain; margin-bottom: 5px;"
          />
          <span style="font-weight: 500;">{{ country.name.common }}</span>
        </router-link>
      </div>
    </div>

    <div class="col-7" style="overflow-y: auto; height: 100vh;">
      <router-view v-slot="{ Component }">
        <component :is="Component" :countries="countries" />
      </router-view>
    </div>
  </div>
</template>



<script setup>
import { ref, onMounted } from "vue";

const countries = ref([]);

onMounted(async () => {
  try {
    const res = await fetch("https://ih-countries-api.herokuapp.com/countries");
    if (!res.ok) throw new Error("Error fetching countries");
    const data = await res.json();

     data.sort((a, b) => {
      const nameA = a.name.common.toLowerCase();
      const nameB = b.name.common.toLowerCase();
      return nameA.localeCompare(nameB);
    });
    countries.value = data;
  } catch (error) {
    console.error(error);
  }
});
</script>
