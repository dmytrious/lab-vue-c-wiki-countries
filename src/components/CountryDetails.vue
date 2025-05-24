<script setup>
import { useRoute } from "vue-router";
import { computed, watch, ref } from "vue";

const props = defineProps({
  countries: {
    type: Array,
    default: () => [],
  },
});

const route = useRoute();
const alpha3Code = computed(() => route.params.alpha3Code);

const country = ref(null);
const borderCountries = ref([]);

watch(
  [alpha3Code, () => props.countries],
  () => {
    country.value =
      props.countries.find((c) => c.alpha3Code === alpha3Code.value) || null;

    if (
      country.value &&
      country.value.borders &&
      country.value.borders.length
    ) {
      borderCountries.value = country.value.borders
        .map((code) => props.countries.find((c) => c.alpha3Code === code))
        .filter(Boolean);
    } else {
      borderCountries.value = [];
    }
  },
  { immediate: true }
);
</script>

<template>
  <div class="col-7" v-if="country">
    <img
      :src="`https://flagpedia.net/data/flags/icon/72x54/${country.alpha2Code.toLowerCase()}.png`"
      alt="country flag"
      style="width: 300px"
    />
    <h1 style="text-align: center">{{ country.name.common }}</h1>
    <table class="table">
      <thead></thead>
      <tbody>
        <tr>
          <td style="width: 30%">Capital</td>
          <td>{{ country.capital[0] }}</td>
        </tr>
        <tr>
          <td>Area</td>
          <td>{{ country.area }} km <sup>2</sup></td>
        </tr>
        <tr v-if="borderCountries.length">
          <td>Borders</td>
          <td>
            <ul>
              <li
                v-for="borderCountry in borderCountries"
                :key="borderCountry.alpha3Code"
              >
                <router-link :to="`/list/${borderCountry.alpha3Code}`">
                  {{ borderCountry.name.common }}
                </router-link>
              </li>
            </ul>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<style scoped>
.col-7 {
  text-align: center;
}

.col-7 li {
  list-style: none;
  text-decoration: underline;
}
</style>
