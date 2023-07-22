<script setup lang="ts">
import { ref } from 'vue';
import NumericItem from './components/NumericItem.vue'
import HeaderItem from './components/HeaderItem.vue'
import ChartItem from './components/ChartItem.vue'
import SideItem from './components/SideItem.vue'
import LineItem from './components/LineItem.vue'
import BarItem from './components/BarItem.vue'
import TopItem from './components/TopItem.vue'

type ReqType = { date: Date; time: number; cached: boolean };
type ImpType = { date: Date; icons: Array<string> };
type AnalyticsType = { imports: Array<ImpType>, requests: Array<ReqType> }
type ObjType = { [name: string]: number }
type ArrType = Array<[string, number]>

const analytics = ref<AnalyticsType>({ imports: [], requests: [] });
const top = ref<{ icons: ArrType, categories: ArrType }>({ icons: [], categories: [] });
const icons: ObjType = {}
const categories: ObjType = {};

const sortObject = (obj: ObjType) => Object.entries(obj).sort((a, b) => b[1] - a[1])

const getData = async () => {
  const res = await fetch(`${import.meta.env.VITE_BASE_URL}analytics`);
  const data = await res.json() as AnalyticsType;
  analytics.value = data;
  data.imports.map(({ icons }) => icons).flat().forEach((name) => {
    if (!icons[name]) icons[name] = 0
    icons[name]++
    const category = name.split(` / `)[1]
    if (!categories[category]) categories[category] = 0
    categories[category]++
  })
  top.value = { icons: sortObject(icons), categories: sortObject(categories) }
}


getData()
</script>

<template>
  <HeaderItem />
  <main>
    <div>
      <article class="numeric">
        <NumericItem :value="analytics.imports.length">imported</NumericItem>
        <NumericItem :value="parseFloat((analytics.requests.map(({ time }) => time).reduce((a, b) => a + b, 0) /
          analytics.requests.length).toFixed(2)) || 0" unit="ms">response</NumericItem>
        <NumericItem :value="analytics.requests.filter(({ cached }) => cached).length">cache</NumericItem>
        <NumericItem :value="analytics.requests.filter(({ cached }) => !cached).length">without</NumericItem>
      </article>
      <ChartItem>
        <template #title>icons imported</template>
        <template #value>{{ analytics.imports.map(({ icons }) => icons.length).reduce((a, b) => a + b, 0) }}</template>
        <LineItem v-if="analytics.imports.length" :imports="analytics.imports" />
      </ChartItem>
    </div>
    <article>
      <SideItem>
        <template #title>Top icons</template>
        <div class="top">
          <TopItem v-for="index in 8" :key="index" :index="index - 1" :icons="top.icons" />
        </div>
      </SideItem>
      <SideItem>
        <template #title>Categories</template>
        <div class="bar">
          <BarItem v-for="index in 6" :key="index" :index="index - 1" :categories="top.categories" />
        </div>
      </SideItem>
    </article>
  </main>
</template>

<style scoped>
main {
  flex: 1;
  gap: var(--gap);
  display: grid;
  grid-template-columns: 1fr auto;
}

main>div {
  display: grid;
  grid-template-rows: auto 1fr;
  gap: var(--gap);
}

main>article {
  display: grid;
  grid-template-rows: auto 1fr;
  gap: var(--gap);
  min-height: 70vh;
}

.numeric {
  gap: var(--gap);
  display: grid;
  grid-template-columns: 1fr 1fr 1fr 1fr;
}

.bar {
  flex: 1;
  gap: 3px;
  display: flex;
  position: relative;
}

.bar:focus-within {
  padding-bottom: 58px;
}

.top {
  min-width: 270px;
  flex: 1;
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(62px, 1fr));
  gap: 10px;
}



@media (max-width: 1024px) {
  .numeric {
    grid-template-columns: 1fr 1fr;
  }

  main {
    grid-template-columns: 1fr;
    grid-template-rows: 1fr auto;
  }
}
</style>
