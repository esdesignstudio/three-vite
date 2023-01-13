<template>
    <div class="three">
        <canvas ref="threeWrap" class="three__wrapper"/>
       <!-- <h1>THREE TEST</h1> -->
    </div>
</template>
<script setup>
    import { nextTick, onMounted, ref } from 'vue';
    import * as THREE from 'three';
    import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';
    import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js';
    import { RGBELoader } from 'three/examples/jsm/loaders/RGBELoader.js';
    
   
    const threeWrap = ref()
    const objectLoadUrl = '/glbs/'
    const manager = new THREE.LoadingManager()
    const GLBLoader = new GLTFLoader(manager)
    const RGBLoader = new RGBELoader()
   

// immediately use the texture for material creation
    // const textureMaterial = new THREE.MeshBasicMaterial( { map: texture } );


    // 設定參數
    const fov = 20 // 鏡頭光學倍率
    const near = 0.1
    const far = 1000
    const backgroundColor = new THREE.Color( '#CFE2F4' );
    const light1_color = 0xffffff
    const light2_color = 0xCFE2F4

    // 物件群組
    const heroGroup = new THREE.Group()

    // 材質設定 
    // 文件 https://threejs.org/docs/index.html?q=MeshPhysicalMaterial#api/en/materials/MeshPhysicalMaterial
    const material_1 = new THREE.MeshPhysicalMaterial({
        color:  '#5288D8',
        roughness: 0.5
       
    })
    const material_2 = new THREE.MeshPhysicalMaterial({
        color: 0xffffff,
        metalness: 0.1,
        roughness: 0.3,
        clearcoat:0.5,
        clearcoatRoughness:0.5,
        transmission: 1,
        // flatShading: true,
        transparent: true,
        opacity: 1,
        ior:1.3,
        thickness : 1.5,
        envMapIntensity:1,
        specularIntensity:1,
        specularColor:0xffffff
    })
    const material_3 = new THREE.MeshPhysicalMaterial({
        color: 0xffffff,
        metalness: 0.1,
        roughness: 0.5,
        clearcoat:0.5,
        clearcoatRoughness:0.5,
        transmission: 0.3,
        // flatShading: true,
        transparent: true,
        opacity: 1,
        ior:1.2,
        thickness : 0.7,
        envMapIntensity:1,
        specularIntensity:1,
        specularColor:0xffffff
        
    })

    let canvas, camera, renderer, scene, controls;


    const initThree = () => {
        scene = new THREE.Scene()
        window.addEventListener('resize', windowResize)


        canvas = threeWrap.value
        camera = new THREE.PerspectiveCamera(fov, window.innerWidth / window.innerHeight, near, far)
        camera.lookAt(0, 0, 0)
        camera.position.set(0,1,15)

        RGBLoader.load('/kloppenheim_06_puresky_4k.hdr', (texture) => {
            
        texture.mapping = THREE.EquirectangularReflectionMapping;
            // scene.background = texture
            scene.environment = texture
        })

        // 燈光 (color, intensity)
        const light = new THREE.DirectionalLight(light1_color, 0.5)
        const light2 = new THREE.DirectionalLight(light2_color, 0.7)
        light.position.set(0, 5, 15)
        light2.position.set(5, 0, 0)
        scene.add(light)
        scene.add(light2)
        
        scene.background = backgroundColor;
        scene.add(heroGroup)


        renderer = new THREE.WebGLRenderer({ canvas })
        renderer.setSize(window.innerWidth, window.innerHeight)
        renderer.render(scene, camera)
        renderer.physicallyCorrectLights = true;
        renderer.outputEncoding = THREE.sRGBEncoding;

        controls = new OrbitControls( camera, renderer.domElement );
        controls.enableDamping = true;
        controls.dampingFactor = 0.05;
        
        controls.update();
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
                gltf.scene.children.forEach((mesh, idx) => {
                    console.log('已讀取到的物件', idx, mesh)
                    switch (idx) {
                        case 0:
                            mesh.material = material_1
                            break;
                        case 1:
                            mesh.material = material_2
                            break;
                        case 2:
                            mesh.material = material_3
                            break;
                        default:
                            break;
                    }
                    newAryMesh.push(mesh)
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
        controls.update();
        // heroGroup.rotation.y += 0.01
        // heroGroup.children[0].rotation.y += 0.05
        requestAnimationFrame( animate )
        controls.update()
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