<template>
    <header>
        <img alt="logo" src="../assets//logo.svg" width="72" height="72" />
        <h1>Dashboard</h1>
    </header>
</template>

<style scoped>
header {
    align-items: center;
    display: flex;
    padding: 10px 0;
}

header img {
    margin-right: 35px;
}

h1 {
    color: var(--color-heading);
    font-size: 70px;
    font-weight: 700;
    line-height: 100%;
}

@media (max-width: 1024px) {
    header img {
        height: 0;
        width: 0;
        margin: 0;
    }
}

@media (max-width: 720px) {
    header {
        padding: 20px 0;
    }

    h1 {
        font-size: 40px;
    }
}
</style>