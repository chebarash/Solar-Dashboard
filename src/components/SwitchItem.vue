<script setup lang="ts">
defineProps<{ name: string, values: { [name: string]: any }, update: (index: { length: number, unit: number, options: Intl.DateTimeFormatOptions, name?: Intl.DateTimeFormatOptions, date: Intl.DateTimeFormatOptions }) => any }>()
</script>

<template>
    <ul>
        <li v-for="(value, key) in values" :key="value">
            <label>
                <input @change="update(value)" :checked="key === `1W`" type="radio" :name="name" :id="name">
                <p>{{ key }}</p>
            </label>
        </li>
    </ul>
</template>

<style scoped>
ul {
    width: max-content;
    padding: 4px;
    border-radius: 50px;
    display: grid;
    background: var(--color-background-mute);
    grid-template-columns: 1fr 1fr 1fr 1fr;
    gap: 4px;
}

li {
    display: flex;
    position: relative;
    cursor: pointer;
}

input {
    position: absolute;
    opacity: 0;
    cursor: pointer;
    height: 0;
    width: 0;
}

label {
    flex: 1;
    display: flex;
}

p {
    flex: 1;
    border-radius: 50px;
    padding: 12px 16px;
    color: var(--color-slider);
    font-size: 18px;
    font-weight: 400;
    text-align: center;
}

input:checked~p {
    color: var(--color-heading);
    background: var(--box-bg);
    box-shadow: var(--box-shadow);
}
</style>