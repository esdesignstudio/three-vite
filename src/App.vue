<script setup>
import HelloWorld from './components/HelloWorld.vue'
import Cylinder from './components/Cylinder.vue'
import GlassTest from './components/GlassTest.vue'
import Humantest from './components/Humantest.vue'
</script>

<template>
    <div>
        <!-- <Cylinder /> -->
        <Humantest />
        <!-- <HelloWorld /> -->
        <h1>THREE.JS RENDER TEST<br><span>TESTING FOR CLIENT</span></h1>
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
    color: #005EBF;
    position: fixed;
    font-family: 'Roboto';
    font-weight: bolder;
    top: 50%;
    left: 50%;
    z-index: 100;
    text-align: center;
    transform: translate(-50%, -50%);
    line-height: 1;
    font-size: 5rem;
    
    span {
        font-size: 1rem;
    }
}
</style>
