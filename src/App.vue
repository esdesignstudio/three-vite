<script setup>
import HelloWorld from './components/HelloWorld.vue'
import Cylinder from './components/Cylinder.vue'
</script>

<template>
    <div>
        <!-- <a href="https://vitejs.dev" target="_blank">
        <img src="/vite.svg" class="logo" alt="Vite logo" />
        </a>
        <a href="https://vuejs.org/" target="_blank">
        <img src="./assets/vue.svg" class="logo vue" alt="Vue logo" />
        </a> -->
        <Cylinder />
        <!-- <HelloWorld /> -->
        <h1>THREE.JS RENDER TEST<br><span>PLAY FUN CYLINDER GLASS</span></h1>
    </div>
</template>

<style lang="scss">
.logo {
height: 6em;
padding: 1.5em;
will-change: filter;
}
.logo:hover {
filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
filter: drop-shadow(0 0 2em #42b883aa);
}
h1 {
    position: fixed;
    font-family: 'NT SEAWAVE (standart)';
    top: 50%;
    left: 50%;
    z-index: 100;
    text-align: center;
    transform: translate(-50%, -50%);
    line-height: 1;
    
    span {
        font-size: 1rem;
    }
}
</style>
