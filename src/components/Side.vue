<template>
    <article class="side">
        <p class="title">
            <slot name="title"></slot>
        </p>
        <slot></slot>
    </article>
</template>

<style scoped>
.side {
    border-radius: var(--radius);
    background: var(--color-background-soft);
    padding: 30px 25px;
    gap: 20px;
    display: flex;
    flex-direction: column;
}

.title {
    color: var(--color-heading);
    font-size: 30px;
    font-weight: 700;
}
</style>
