<template>
    <article class="chart">
        <span>
            <p class="value">
                <slot name="value"></slot>
            </p>
            <p class="title">
                <slot name="title"></slot>
            </p>
        </span>
        <slot></slot>
    </article>
</template>

<style scoped>
.chart {
    border-radius: var(--radius);
    background: var(--color-background-soft);
    padding: 45px 50px;
    gap: 30px;
    display: flex;
    flex-direction: column;
}

.chart span {
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

@media (max-width: 720px) {
    .chart {
        padding: 20px;
        gap: 20px;
    }

    .chart span {
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
