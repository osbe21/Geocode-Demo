<script setup>
import { ref, onMounted } from 'vue';
import * as THREE from 'three';
import { GLTFLoader } from 'three/addons/loaders/GLTFLoader.js'

const props = defineProps(['latitude', 'longitude']);

const sceneParent = ref(null);

const scene = new THREE.Scene();
const camera = new THREE.PerspectiveCamera(60, 1, 0.5, 1000);
camera.position.z = 60;

const renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
renderer.setSize( 800, 800 );

const light = new THREE.DirectionalLight(0xffffff, 6);
light.position.set(1, 1, 1);

onMounted(() => sceneParent.value.appendChild(renderer.domElement));


let earth;
let atmosphere;

const loader = new GLTFLoader();
loader.load('/earth.glb', (gltf) => {
    earth = gltf.scene;
    earth.rotation.y = -0.5;

    earth.traverse((obj) => {
        if (!obj.material) return;

        obj.material = new THREE.MeshPhongMaterial({ map: obj.material.map, alphaToCoverage: true });
    });

    const atmosphereGeometry = new THREE.SphereGeometry(26, 40, 40);

    const vertexShader = [
        'varying vec3 vNormal;',
        'varying vec3 vPosition;',
        'void main() {',
            'vNormal = normalize( normalMatrix * normal );',

            'gl_Position = projectionMatrix * modelViewMatrix * vec4( position, 1.0 );',
            'vPosition = gl_Position.xyz;',
        '}'
    ].join('\n')

    const fragmentShader = [
        'varying vec3 vNormal;',
        'varying vec3 vPosition;',
        'uniform vec3 lightDir;',

        'void main() {',
            'vec3 lightDirection = normalize(vec3(lightDir));',
            'float dotNL = clamp(dot(-lightDirection, vNormal) + 0.2, 0.0, 1.0);',
            'float intensity = pow( 0.8 - dot( vNormal, vec3( 0, 0, 1.0 ) ), 0.5 );',
            'gl_FragColor = vec4( 0.6, 0.64, 1.0, 1.0 ) * dotNL * intensity;',
        '}'
    ].join('\n')

    const atmosphereMaterial = new THREE.ShaderMaterial({
        uniforms: { lightDir: { value: new THREE.Vector3(0, 0, 0) } },
        vertexShader: vertexShader, 
        fragmentShader: fragmentShader, 
        transparent: true 
    });

    atmosphere = new THREE.Mesh(atmosphereGeometry, atmosphereMaterial);

    earth.add(atmosphere);
    earth.add(light);

    scene.add(earth);
});


function animate() {
	requestAnimationFrame(animate);

    const lerp = (a, b, t) => (1 - t) * a + t * b;

    if (earth) {
    
        //earth.rotation.y -= 0.001;

        const lightPos = new THREE.Vector3(); 
        light.getWorldPosition(lightPos);
        const lightDir = earth.position.clone();
        lightDir.sub(lightPos);
            
        atmosphere.material.uniforms.lightDir = { value: lightDir };

        earth.rotation.y = lerp(earth.rotation.y, -props.longitude * Math.PI / 180, 0.03);
        earth.rotation.x = lerp(earth.rotation.x, props.latitude * Math.PI / 180, 0.03);
    }

	renderer.render( scene, camera );
}

animate();

</script>


<template>

<div ref="sceneParent" id="scene">

</div>

</template>


<style scoped>

</style>