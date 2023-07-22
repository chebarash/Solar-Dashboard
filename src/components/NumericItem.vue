<script setup lang="ts">
defineProps<{ value: number, unit?: string }>()
const getUnit = (n: number) => `time${n !== 1 ? `s` : ``}`
</script>

<template>
    <article class="card">
        <p class="title">
            <slot></slot>
        </p>
        <span>
            <p class="value">
                {{ value }}
            </p>
            <p class="unit">
                {{ unit || getUnit(value) }}
            </p>
        </span>
    </article>
</template>

<style scoped>
.card {
    border-radius: var(--radius);
    background: var(--color-background-soft);
    padding: 20px 18px;
    gap: 5px;
    display: flex;
    flex-direction: column;
    overflow: hidden;
}

.card span {
    gap: 6px;
    display: flex;
    align-items: center;
    overflow: hidden;
}

.title {
    color: var(--color-text);
    font-size: 18px;
    font-weight: 400;
    overflow: hidden;
    text-overflow: ellipsis;
}

.value {
    color: var(--color-heading);
    font-size: 40px;
    font-weight: 700;
}

.unit {
    color: var(--color-text);
    font-size: 20px;
    font-weight: 400;
    padding-top: 14px;
    overflow: hidden;
    text-overflow: ellipsis;
}

@media (max-width: 720px) {
    .title {
        font-size: 16px;
    }

    .value {
        font-size: 32px;
    }
}
</style>
