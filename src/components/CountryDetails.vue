<script setup>
import { useRoute } from "vue-router";
import { computed, watch, ref } from "vue";

const props = defineProps({
  countries: {
    type: Array,
    default: () => []
  }
});

const route = useRoute();
const alpha3Code = computed(() => route.params.alpha3Code);

const country = ref(null);
const borderCountries = ref([]);

watch(
  [alpha3Code, () => props.countries],
  () => {
    country.value = props.countries.find(c => c.alpha3Code === alpha3Code.value) || null;

        console.log("Country:", country.value);
    console.log("Borders codes:", country.value?.borders);


    if (country.value && country.value.borders && country.value.borders.length) {
      borderCountries.value = country.value.borders
        .map(code => props.countries.find(c => c.alpha3Code === code))
        .filter(Boolean);
    } else {
      borderCountries.value = [];
    }
     console.log("Border Countries:", borderCountries.value);
  },
  { immediate: true }
);
</script>

<template>
  <div v-if="country" style="padding: 20px;">
    <img
      :src="`https://flagpedia.net/data/flags/icon/72x54/${country.alpha2Code.toLowerCase()}.png`"
      alt="flag"
      style="width: 100px; height: 90px; object-fit: contain; margin-bottom: 15px;"
    />
    <h1 style="border-bottom: 1px solid #ccc; padding-bottom: 8px; margin-bottom: 16px;">
      {{ country.name.common }}
    </h1>
    <p style="border-bottom: 1px solid #ccc; padding-bottom: 8px; margin-bottom: 16px;">
      <strong>Capital:</strong> {{ country.capital ? country.capital[0] : "No disponible" }}
    </p>
    <p style="border-bottom: 1px solid #ccc; padding-bottom: 8px; margin-bottom: 16px;">
      <strong>Área:</strong> {{ country.area || "No disponible" }} km²
    </p>

  <div v-if="borderCountries.length">
  <h4>Fronteras:</h4>
  <ul>
    <li v-for="borderCountry in borderCountries" :key="borderCountry.alpha3Code">
      <router-link :to="`/list/${borderCountry.alpha3Code}`">
        <img
          :src="`https://flagpedia.net/data/flags/icon/72x54/${borderCountry.alpha2Code.toLowerCase()}.png`"
          alt="flag"
          style="width: 20px; margin-right: 8px;"
        />
        {{ borderCountry.name.common }}
      </router-link>
    </li>
  </ul>
</div>

  </div>
  <div v-else>
    <p>Cargando detalles...</p>
  </div>
</template>
