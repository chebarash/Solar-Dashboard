<script setup lang="ts">
defineProps<{ def?: string, name: string, values: { [name: string]: any }, update: (index: any) => any }>()
</script>

<template>
    <ul>
        <li v-for="(_, key) in  values " :key="key">
            <label>
                <input @change="update(key)" :checked="key === def" type="radio" :name="name" :id="name">
                <p>{{ key }}</p>
            </label>
        </li>
    </ul>
</template>

<style scoped>
ul {
    padding: 4px;
    border-radius: 50px;
    display: flex;
    background: var(--color-background-mute);
    border: 2px solid transparent;
}

ul:focus-within {
    border: 2px solid var(--blue);
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
        padding: 1px;
        flex-direction: column;
        border-radius: 24px;
        background: var(--color-background-soft);
        border: 1px solid var(--color-border);
    }

    p {
        display: none;
        padding: 10px 16px;
        font-size: 14px;
    }

    label {
        pointer-events: none;
    }

    ul:hover {
        box-shadow: var(--box-shadow);
    }

    ul:hover p {
        display: flex;
    }

    ul:hover label {
        pointer-events: all;
    }

    input:checked~p {
        display: flex;
        color: var(--color-heading);
        box-shadow: none;
        background: transparent;
    }
}
</style>