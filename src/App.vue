<script setup lang="ts">
import { ref } from 'vue';
import NumericItem from './components/NumericItem.vue'
import HeaderItem from './components/HeaderItem.vue'
import SwitchItem from './components/SwitchItem.vue'
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
type PerType = { length: number, unit: number, options: Intl.DateTimeFormatOptions, name?: Intl.DateTimeFormatOptions, date: Intl.DateTimeFormatOptions }
type CurType = '1H' | '1D' | '1W' | '1M'
type ValType = 'imports' | 'requests'

const minute = 1000 * 60
const hour = minute * 60
const day = hour * 24

const analytics = ref<AnalyticsType>({ imports: [], requests: [] });
const top = ref<{ icons: ArrType, categories: ArrType }>({ icons: [], categories: [] });
const icons: ObjType = {}
const categories: ObjType = {};

const base: Intl.DateTimeFormatOptions = { year: `numeric`, month: `2-digit`, day: `2-digit` }

const periods = ref<{ [name: string]: PerType }>({
  '1H': { length: 60, unit: minute, options: { ...base, hour: `2-digit`, minute: `2-digit` }, date: { hour: `2-digit`, minute: `2-digit` } },
  '1D': { length: 24, unit: hour, options: { ...base, hour: `2-digit` }, date: { hour: `2-digit`, minute: `2-digit` } },
  '1W': { length: 7, unit: day, options: base, name: { weekday: `short` }, date: { day: `2-digit` } },
  '1M': { length: 31, unit: day, options: base, name: { month: `short` }, date: { day: `2-digit` } },
});
const current = ref<CurType>(`1W`);

const values: { [name: string]: ValType } = {
  'imports': `imports`, 'requests': `requests`
}
const value = ref<ValType>(`imports`);

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
      <ChartItem title="icons imported"
        :value="analytics.imports.map(({ icons }) => icons.length).reduce((a, b) => a + b, 0)">
        <template #switch>
          <div class="switch">
            <SwitchItem def="1W" name="period" :values="periods" :update="(index: CurType) => {
              current = index
            }" />
            <SwitchItem def="imports" name="values" :values="values" :update="(index: 'imports' | 'requests') => {
              value = index
            }" />
          </div>
        </template>
        <LineItem v-if="analytics.imports.length" :data="analytics[value]" :period="periods[current]" />
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

.switch {
  align-items: center;
  display: flex;
  gap: 20px;
}

@media (max-width: 1024px) {
  .numeric {
    grid-template-columns: 1fr 1fr;
  }

  main {
    grid-template-columns: 1fr;
    grid-template-rows: 1fr auto;
  }

  .switch {
    position: absolute;
    gap: 10px;
    right: 20px;
    z-index: 9;
  }
}
</style>
