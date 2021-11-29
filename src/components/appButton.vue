<template>
    <div>
        <button @click="$emit('action')">{{text}}</button>
    </div>
</template>

<script>
    export default {
        name: "appButton",
        props:{text:String},
        emits:['action']
    }

</script>

<style scoped>

</style>