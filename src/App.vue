<script setup lang="ts">
import { ref } from 'vue';
import Numeric from './components/Numeric.vue'
import Header from './components/Header.vue'
import Chart from './components/Chart.vue'
import Side from './components/Side.vue'
import Bar from './components/Bar.vue'
import Top from './components/Top.vue'

type ReqType = { date: Date; time: number; cached: boolean };
type ImpType = { date: Date; icons: Array<string> };
type AnalyticsType = { imports: Array<ImpType>, requests: Array<ReqType> }
type ObjType = { [name: string]: number }
type ArrType = Array<[string, number]>


const analytics = ref<AnalyticsType>({ imports: [], requests: [] });
const top = ref<{ icons: ArrType, categories: ArrType }>({ icons: [], categories: [] });
const icons: ObjType = {}
const categories: ObjType = {};

const sortObject = (obj: ObjType) => Object.entries(obj).sort(([_a, a], [_b, b]) => b - a)

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
  <Header />
  <main>
    <div>
      <article class="numeric">
        <Numeric>
          <template #title>импортировано</template>
          {{ analytics.imports.length }}
          <template #unit>раз</template>
        </Numeric>
        <Numeric>
          <template #title>время отклика</template>
          {{ parseFloat((analytics.requests.map(({ time }) => time).reduce((a, b) => a + b, 0) /
            analytics.requests.length).toFixed(2)) || 0 }}
          <template #unit>мс</template>
        </Numeric>
        <Numeric>
          <template #title>открытий с кешем</template>
          {{ analytics.requests.filter(({ cached }) => cached).length }}
          <template #unit>раз</template>
        </Numeric>
        <Numeric>
          <template #title>открытий без кеша</template>
          {{ analytics.requests.filter(({ cached }) => !cached).length }}
          <template #unit>раз</template>
        </Numeric>
      </article>
      <Chart>
        <template #title>Иконок импортировано</template>
        {{ analytics.imports.map(({ icons }) => icons.length).reduce((a, b) => a + b, 0) }}
      </Chart>
    </div>
    <article>
      <Side>
        <template #title>Топ иконок</template>
        <div class="top">
          <Top v-for="index in 8" :index="index - 1" :icons="top.icons" />
        </div>
      </Side>
      <Side>
        <template #title>Категории</template>
        <div class="bar">
          <Bar v-for="index in 6" :index="index - 1" :categories="top.categories" />
        </div>
      </Side>
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
