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
}

input {
    position: absolute;
    opacity: 0;
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
    cursor: pointer;
}

input:checked~p {
    color: var(--color-heading);
    background: var(--box-bg);
    box-shadow: var(--box-shadow);
}

@media (max-width: 720px) {
    ul {
        position: absolute;
        top: 25px;
        right: 20px;
        grid-template-columns: 1fr;
        border-radius: 24px;
        z-index: 9;
        gap: 0;
    }

    p {
        display: none;
    }

    ul:hover p {
        display: flex;
    }

    input:checked~p {
        display: flex;
        padding: 12px 16px;
        color: var(--color-heading);
        box-shadow: var(--box-shadow);
        background: transparent;
    }
}
</style>