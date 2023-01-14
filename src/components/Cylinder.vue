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
    import { EffectComposer } from 'three/examples/jsm/postprocessing/EffectComposer'
    import { RenderPass } from 'three/examples/jsm/postprocessing/RenderPass'
    import { BokehPass } from 'three/examples/jsm/postprocessing/BokehPass'
    
   
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
    const backgroundColor = new THREE.Color( '#000000' );
    const light1_color = 0xe41749
    const light2_color = 0x00a03e

    // 物件群組
    const heroGroup = new THREE.Group()

    // 材質設定 
    // 文件 https://threejs.org/docs/index.html?q=MeshPhysicalMaterial#api/en/materials/MeshPhysicalMaterial
    const material_1 = new THREE.MeshPhysicalMaterial({
        color:  '#000000',
        roughness: 0.1
       
    })
    const material_2 = new THREE.MeshPhysicalMaterial({
        color: 0xffffff,
        metalness: 0.1,
        roughness: 0.1,
        clearcoat:0.5,
        clearcoatRoughness:0.5,
        transmission: 1,
        transparent: true,
        opacity: 1,
        ior:1.3,
        thickness : 1.5,
        envMapIntensity:1,
        specularIntensity:1,
        specularColor:0xffffff
    })
    const material_3 = new THREE.MeshPhysicalMaterial({
        reflectivity: 1.0,
        transmission: 1.0,
        roughness: 0,
        metalness: 0,
        clearcoat: 0.3,
        clearcoatRoughness: 0.25,
        color: new THREE.Color(0xffffff),
        ior: 1.2,
        thickness: 1,
    })

    let canvas, camera, renderer, scene, controls, composer, renderPass, bokehPass;

    let cubeRenderTarget, cubeCamera;

    const initThree = () => {
        scene = new THREE.Scene()
        window.addEventListener('resize', windowResize)


        canvas = threeWrap.value
        camera = new THREE.PerspectiveCamera(fov, window.innerWidth / window.innerHeight, near, far)
        camera.lookAt(0, 0, 0)
        camera.position.set(0,1,15)

        RGBLoader.load('/Env_NoTell_Meredith.hdr', (texture) => {
            
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
        renderer.shadowMap.enabled = true
        // renderer.physicallyCorrectLights = true;
        renderer.outputEncoding = THREE.sRGBEncoding;


        renderPass = new RenderPass(scene, camera)
        bokehPass = new BokehPass(scene, camera, {
            focus: 0.1,
            aperture: 0.0001,
            maxblur: 0.1,
            width: window.innerWidth,
            height: window.innerHeight,
        })

        composer = new EffectComposer(renderer);
        composer.addPass(renderPass)
        composer.addPass(bokehPass)


        cubeRenderTarget = new THREE.WebGLCubeRenderTarget(2048)
        cubeCamera = new THREE.CubeCamera(0.1, 100, cubeRenderTarget)
        scene.add(cubeCamera)

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
            '/cylinder_with_ball.glb',
            (gltf) => {
                const newAryMesh = []
                const innerGroup = new THREE.Group()
                gltf.scene.children.forEach((mesh, idx) => {
                    console.log('已讀取到的物件', idx, mesh)
                    switch (idx) {
                        case 0:
                            mesh.material = material_2
                            break;
                        case 1:
                            mesh.material = material_1
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
        composer.setSize(window.innerWidth, window.innerHeight)
    }
    const animate = () => {
        controls.update();
        // heroGroup.rotation.x += 0.01
        heroGroup.children[0].rotation.z += 0.02
        heroGroup.children[1].rotation.z += 0.01
        requestAnimationFrame( animate )
        controls.update()
        render()
        renderer.render( scene, camera )
    }

    function render() {
        cubeCamera.position.copy(camera.position)
        cubeCamera.update(renderer, scene)
        //renderer.render(scene, camera)
        composer.render(0.1)
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