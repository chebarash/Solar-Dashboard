<script setup lang="ts">
defineProps<{ index: number, icons: Array<[string, number]> }>()

const getIconUrl = (fullName: string) => {
    const [style, category, name] = fullName.split(` / `).map(s => encodeURIComponent(s))
    return `${import.meta.env.VITE_BASE_URL}icon?category=${category}&name=${name}&style=${style}`
}
</script>

<template>
    <button :title="icons[index] ? icons[index][0] : ``">
        <div :style="{ opacity: icons[index] ? 0 : 0.5 }"></div>
        <span v-if="icons[index]" :style="{ maskImage: `url('${getIconUrl(icons[index][0])}')`, maskSize: `cover` }"></span>
        <p v-if="icons[index]">{{ icons[index][1] }}</p>
    </button>
</template>

<style scoped>
button {
    aspect-ratio: 1;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    border-radius: 12px;
    border: 1px solid var(--color-border);
}

button:hover {
    scale: 1.05;
}

button:focus {
    background: var(--box-bg);
    border-color: transparent;
    box-shadow: var(--box-shadow);
}

span {
    width: 32px;
    height: 32px;
    background: var(--color-icon);
}

button:focus span {
    background: var(--color-icon-active);
}

p {
    color: var(--color-icon-active);
    font-size: 0;
    font-weight: 500;
}

button:focus p {
    margin-top: 2px;
    font-size: 12px;
}

div {
    width: 32px;
    height: 32px;
    border-radius: 100%;
    background: var(--color-icon);
    position: absolute;
}
</style>