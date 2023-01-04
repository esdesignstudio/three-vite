<template>
    <div class="three">
        <canvas ref="threeWrap" class="three__wrapper" />
        <h1>THREE TEST</h1>
    </div>
</template>
<script setup>
    import { nextTick, onMounted, ref } from 'vue'
    import * as THREE from 'three';
    import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js'
    
    const threeWrap = ref()
    const objectLoadUrl = '/glbs/'
    const manager = new THREE.LoadingManager()
    const GLBLoader = new GLTFLoader(manager)

    // 設定參數
    const fov = 20 // 鏡頭光學倍率
    const near = 0.1
    const far = 1000
    const backgroundColor = new THREE.Color( 0x000000 );

    // 物件群組
    const heroGroup = new THREE.Group()

    // 材質設定 
    // 文件 https://threejs.org/docs/index.html?q=MeshPhysicalMaterial#api/en/materials/MeshPhysicalMaterial
    const material_1 = new THREE.MeshPhysicalMaterial({
        color: 0xffffff,
        metalness: 1,
        roughness: 0.6,
        transmission: 0.9,
        // flatShading: true,
        // transparent: true,
        opacity: 1
    })
    const material_2 = new THREE.MeshPhysicalMaterial({
        color: 0xffffff,
        metalness: 1,
        roughness: 0.6,
        transmission: 0.9,
        // flatShading: true,
        // transparent: true,
        opacity: 1
    })
    const material_3 = new THREE.MeshPhysicalMaterial({
        color: 0xffffff,
        metalness: 1,
        roughness: 0.6,
        transmission: 0.9,
        // flatShading: true,
        // transparent: true,
        opacity: 1
    })

    let canvas, camera, renderer, scene;


    const initThree = () => {
        scene = new THREE.Scene()
        window.addEventListener('resize', windowResize)


        canvas = threeWrap.value
        camera = new THREE.PerspectiveCamera(fov, window.innerWidth / window.innerHeight, near, far)
        camera.lookAt(0, 0, 0)
        camera.position.set(0,1,15)

        // 燈光 (color, intensity)
        const light = new THREE.DirectionalLight(0xCEC8C2, 0.5)
        const light2 = new THREE.DirectionalLight(0xffffff, 2)
        light.position.set(0, 5, 15)
        light2.position.set(5, 0, -5)
        scene.add(light)
        scene.add(light2)
        
        scene.background = backgroundColor
        scene.add(heroGroup)


        renderer = new THREE.WebGLRenderer({ canvas })
        renderer.setSize(window.innerWidth, window.innerHeight)
        renderer.render(scene, camera)
        renderer.outputEncoding = THREE.sRGBEncoding;

        animate()

        console.log(scene)
    }

    // 讀取物件
    const LoadObjects = () => {
        GLBLoader.load (
            '/capsule.glb',
            (gltf) => {
                const newAryMesh = []
                const innerGroup = new THREE.Group()
                gltf.scene.children.forEach((mesh) => {
                    console.log('已讀取到的物件', mesh)
                    mesh.material = material_1
                    newAryMesh.push(mesh)
                    // heroGroup.add(mesh)
                })
                newAryMesh.forEach((mesh) => {
                    heroGroup.add(mesh)
                })
            }
        )
    }

    const windowResize = () => {
        camera.aspect = window.innerWidth / window.innerHeight;
    }
    const animate = () => {
        // console.log('test')
        heroGroup.rotation.y += 0.01
        requestAnimationFrame( animate )
        renderer.render( scene, camera )
    }

    manager.onLoad = () => {
        initThree()
    }

    onMounted(() => {
        nextTick(() => {
            LoadObjects()
            // initThree()
        })
    })
</script>
<style lang="scss">
    $class-name: three;
    .#{$class-name} {
        &__wrapper {
            top: 0;
            left: 0;
            z-index: 0;
            width: 100%;
            height: 100%;
            position: fixed;
            background-color: #000;
        }
        h1 {
            z-index: 1;
            position: relative;
        }
    }
</style>