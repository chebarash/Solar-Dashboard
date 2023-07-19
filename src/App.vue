<script setup lang="ts">
import { ref } from 'vue';
import Numeric from './components/Numeric.vue'
import Header from './components/Header.vue'
import Chart from './components/Chart.vue'
import Side from './components/Side.vue'

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

async function getData() {
  const res = await fetch("https://solariconset.com/analytics");
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
          <template #title>в среднем</template>
          {{ parseFloat((analytics.imports.map(({ icons }) => icons.length).reduce((a, b) => a + b, 0) /
            analytics.imports.length).toFixed(2)) }}
          <template #unit>иконок</template>
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
        <template #value>{{ analytics.imports.map(({ icons }) => icons.length).reduce((a, b) => a + b, 0) }}</template>
      </Chart>
    </div>
    <article>
      <Side>
        <template #title>Топ иконок</template>
      </Side>
      <Side>
        <template #title>Категории</template>
        <div class="bar">
          <button v-for="[name, num] of top.categories.slice(0, 6)">
            <span :style="{ height: num / top.categories[0][1] * 100 + '%' }"></span>
            <svg width="18" height="8" viewBox="0 0 18 8" fill="none">
              <path d="M9 0C5.5 0 4 8 0 8H18C14 8 12.5 0 9 0Z" fill="var(--blue)" />
            </svg>
            <p>{{ `${name} - ${num}` }}</p>
          </button>
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
  grid-template-rows: 1fr 1fr;
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

.bar:hover,
.bar:focus-within {
  padding-bottom: 58px;
}

.bar button {
  display: flex;
  align-items: flex-end;
  flex: 1;
  min-width: 36px;
  justify-content: center;
}

.bar svg {
  opacity: 0;
  margin-top: 20px;
  position: absolute;
  top: calc(100% - 49px);
}

.bar p {
  line-height: 0px;
  opacity: 0;
  bottom: 5px;
  position: absolute;
  color: var(--text-dark-1);
  font-size: 16px;
  font-weight: 500;
  border-radius: 20px;
  background: var(--blue);
  padding: 8px 10px;
  width: max-content;
}

.bar button:hover p,
.bar button:focus p,
.bar button:hover svg,
.bar button:focus svg {
  opacity: 1;
  line-height: 20px;
  margin-top: 0;
}

.bar span {
  opacity: .4;
  background: var(--blue);
  flex: 1;
  border-radius: 10px;
}

.bar button:hover span,
.bar button:focus span {
  opacity: 1;
}

@media (max-width: 1024px) {
  .numeric {
    grid-template-columns: 1fr 1fr;
  }
}


@media (max-width: 720px) {
  main {
    grid-template-columns: 1fr;
    grid-template-rows: 1fr auto;
  }
}
</style>
