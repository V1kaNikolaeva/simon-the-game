<template>
    <div class="defeatMessage">
        <p class="message">
            <slot></slot>
        </p>
    </div>
</template>

<script>
export default {
    name: "UiMessage",
}
</script>

<style scoped>
.message {
    color: #902f2f;
}

.defeatMessage {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 100%;
    height: 60px;
}
</style>