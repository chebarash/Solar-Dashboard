<script setup lang="ts">
defineProps<{ index: number, categories: Array<[string, number]> }>()

const getPosition = (index: number, string: string) => {
    const num = string.length > 16 ? 2 : 1
    return index < num ? `left` : index > (5 - num) ? `right` : ``
}
</script>

<template>
    <button>
        <span :style="{ height: categories.length ? categories[index][1] / categories[0][1] * 100 + '%' : `0%` }"></span>
        <svg width="18" height="8" viewBox="0 0 18 8" fill="none">
            <path d="M9 0C5.5 0 4 8 0 8H18C14 8 12.5 0 9 0Z" fill="var(--blue)" />
        </svg>
        <p v-if="categories.length"
            :style="{ [getPosition(index, `${categories[index][0]} - ${categories[index][1]}`)]: `-6px` }">{{
                `${categories[index][0]} - ${categories[index][1]}` }}</p>
    </button>
</template>

<style scoped>
button {
    display: flex;
    align-items: flex-end;
    flex: 1;
    min-width: 36px;
    justify-content: center;
}

svg {
    opacity: 0;
    margin-top: 20px;
    position: absolute;
    top: calc(100% - 49px);
}

p {
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


button:focus p,
button:focus svg {
    opacity: 1;
    line-height: 20px;
    margin-top: 0;
}

span {
    opacity: .4;
    background: var(--blue);
    flex: 1;
    border-radius: 10px;
}

button:hover span {
    scale: 1.05;
    opacity: .6;
}

button:focus span {
    opacity: 1;
}
</style>