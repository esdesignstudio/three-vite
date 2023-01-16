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

    
    import { EffectComposer } from 'three/addons/postprocessing/EffectComposer.js';
    import { RenderPass } from 'three/addons/postprocessing/RenderPass.js';
    import { GlitchPass } from 'three/addons/postprocessing/GlitchPass.js';
    const composer = new EffectComposer( renderer );
   
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
    const backgroundColor = new THREE.Color( '#B2D9EA' );
    const light1_color = 0xffffff
    const light2_color = 0x2DC4ED

    // 物件群組
    const heroGroup = new THREE.Group()

    // 材質設定 
    // 文件 https://threejs.org/docs/index.html?q=MeshPhysicalMaterial#api/en/materials/MeshPhysicalMaterial
    // const material_1 = new THREE.MeshPhysicalMaterial({
    //     color:"#C9F6FF",
    //     metalness: 0,
    //     roughness: 0.6,
    //     clearcoat:0.2,
    //     clearcoatRoughness:0.2,
    //     transmission: 1,
    //     // flatShading: true,
    //     transparent: true,
    //     opacity: 1,
    //     ior:1.2,
    //     thickness : 2,
    //     envMapIntensity:2,
    //     specularIntensity:1,
    //     specularColor:0xffffff,
    //     exposure:1,
    //     attenuationTint:"#4589FF",
    //     attenuationDistance:0.2
     

       
    // })
    // const material_2 = new THREE.MeshPhysicalMaterial({
    //     color: '#FFFFFF',
    //     roughness: 0.6,
        
    // })
    const material_1 = new THREE.MeshPhysicalMaterial({
        color:"#FFFFFF",
        metalness: 0,
        roughness: 0.4,
        clearcoat:0.2,
        clearcoatRoughness:0.2,
        transmission: 1,
        // flatShading: true,
        transparent: true,
        opacity: 1,
        ior:1.2,
        thickness : 2,
        envMapIntensity:4,
        specularIntensity:1,
        specularColor:0xffffff,
        exposure:1,
        attenuationTint:"#4589FF",
        attenuationDistance:0.2
     

       
    })
    const material_2 = new THREE.MeshPhysicalMaterial({
        color: '#4F90FF',
        roughness: 0.2,
        
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
        const light = new THREE.DirectionalLight(light1_color, 0.01)
        const light2 = new THREE.DirectionalLight(light2_color, 0.02)
        light.position.set(0, 10, 20)
        light2.position.set(5, 25, 30)
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
        
        controls.update();
        animate()

        console.log(scene)
    }

    // 讀取物件
    const LoadObjects = () => {
        GLBLoader.load (
            '/body.glb',
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

//     const glitchPass = new GlitchPass();
// renderer.addPass( glitchPass );

    const windowResize = () => {
        camera.aspect = window.innerWidth / window.innerHeight;
    }
    const animate = () => {
        controls.update();

        heroGroup.children[0].rotation.y += 0
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