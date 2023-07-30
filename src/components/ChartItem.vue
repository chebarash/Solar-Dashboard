<script setup lang="ts">
defineProps<{ value: number | string, title: string }>()
</script>

<template>
    <article>
        <div>
            <span>
                <p class="value">{{ value }}</p>
                <p class="title">{{ title }} </p>
            </span>
            <slot name="switch"></slot>
        </div>
        <slot></slot>
    </article>
</template>


<style scoped>
article {
    border-radius: var(--radius);
    background: var(--color-background-soft);
    padding: 45px 50px;
    gap: 30px;
    display: grid;
    grid-template-rows: auto 1fr;
    position: relative;
}

div {
    display: flex;
    align-items: center;
    justify-content: space-between;
}

span {
    display: flex;
    align-items: center;
    gap: 25px;
}

.title {
    color: var(--color-text);
    font-size: 20px;
    font-weight: 400;
    width: min-content;
}

.value {
    color: var(--color-heading);
    font-size: 70px;
    font-weight: 700;
}

@media (max-width: 1024px) {
    article {
        min-height: 500px;
    }
}

@media (max-width: 720px) {
    article {
        padding: 20px;
        gap: 20px;
        min-height: 400px;
    }

    span {
        flex-direction: column;
        align-items: flex-start;
        gap: 0px;
    }

    .title {
        font-size: 16px;
        width: max-content;
    }

    .value {
        font-size: 40px;
    }
}
</style>
