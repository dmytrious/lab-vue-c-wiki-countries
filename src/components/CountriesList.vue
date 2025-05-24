<template>
  <section>
    <div class="container">
      <div class="row">
        <div class="col-5" style="max-height: 90vh; overflow: scroll">
          <div class="list-group">
            <router-link
              v-for="country in countries"
              :key="country.alpha3Code"
              class="list-group-item list-group-item-action"
              :to="`/list/${country.alpha3Code}`"
            >
              <img
                :src="`https://flagpedia.net/data/flags/icon/72x54/${country.alpha2Code.toLowerCase()}.png`"
              />
              <p>{{ country.name.common }}</p>
            </router-link>
          </div>
        </div>

        <router-view v-slot="{ Component }">
          <component :is="Component" :countries="countries" />
        </router-view>
      </div>
    </div>
  </section>
</template>

<script setup>
import { ref, onMounted } from "vue";

const countries = ref([]);

const fetchCountries = async () => {
  try {
    const res = await fetch("https://ih-countries-api.herokuapp.com/countries");
    if (!res.ok) throw new Error("Error fetching countries");
    const data = await res.json();

    countries.value = data.sort((a, b) =>
      a.name.common.localeCompare(b.name.common, "en", { sensitivity: "base" })
    );
  } catch (error) {
    console.error("Failed to fetch countries:", error);
  }
};

onMounted(fetchCountries);
</script>

<style scoped>
.list-group-item {
  display: flex;
  flex-direction: column;
  flex-wrap: nowrap;
  align-items: center;
  padding: 20px 0px;
}

.list-group-item img {
  width: 50px;
}

.row {
  padding-top: 30px;
}
</style>
