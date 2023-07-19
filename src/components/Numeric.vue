<template>
    <article class="card">
        <p class="title">
            <slot name="title"></slot>
        </p>
        <span>
            <p class="value">
                <slot></slot>
            </p>
            <p class="unit">
                <slot name="unit"></slot>
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
}

.card span {
    gap: 9px;
    display: flex;
    align-items: center;
}

.title {
    color: var(--color-text);
    font-size: 18px;
    font-weight: 400;
}

.value {
    color: var(--color-heading);
    font-size: 40px;
    font-weight: 700;
}

.unit {
    color: var(--color-text);
    font-size: 25px;
    font-weight: 400;
    padding-top: 6px;
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
