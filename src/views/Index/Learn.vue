<template>
  <div ref="container"></div>
</template>

<script>
import dat from 'dat.gui'
import * as THREE from 'three'
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader"
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls"
let scene;
export default {
  data() {
    return {
      // scene: null,
      light: null,
      camera: null,
      renderer: null,
      cube: null,
      control: null,
    }
  },
  mounted() {
    this.draw()
    // this.render()
    // this.loadGltf()
  },
  methods: {
    draw() {
      this.initGui()
      this.initRenderer()
      this.initScene()
      this.initCamera()
      this.initLight()
      this.initModel()
      this.initControl()
      this.animate()
    },
    initRenderer() {
      this.renderer = new THREE.WebGLRenderer({
        antialias: true
      })
      // this.renderer.setClearColor(0xffffff)
      // this.renderer.setPixelRatio(window.devicePixelRatio)
      this.renderer.setSize( window.innerWidth, window.innerHeight)
      this.renderer.shadowMap.enabled = true
      this.renderer.shadowMap.type = THREE.PCFSoftShadowMap
      this.$refs.container.appendChild( this.renderer.domElement );
    },
    initScene() {
      scene = new THREE.Scene()
    },
    initCamera() {
      this.camera = new THREE.PerspectiveCamera(50, window.innerWidth / window.innerHeight, 0.1, 1000)
      this.camera.position.set(0, 100, 200)
      this.camera.lookAt(new THREE.Vector3(0, 0, 0))
    },
    initGui() {
       const control = {
        positionX: 0,
        positionY: 0,
        positionZ: 0
      }
      let gui = new dat.GUI()
      gui.add(control, 'positionX').onChange(updatePosition)
      gui.add(control, 'positionY', -1, 1).onChange(updatePosition)
      gui.add(control, 'positionZ', -1, 1).onChange(updatePosition)
      function updatePosition() {
        // this.cube.position.set(control.positionX, control.positionY, control.positionZ)
      }
    },
    initLight() {
      scene.add(new THREE.AmbientLight(0x404040 ))
      this.light = new THREE.DirectionalLight(0xffffff, 0.5)
      // light.color.set(0x000000)
      // light.intensity = 2.0
      this.light.position.set(40,60,10)
      // this.light.position.multiplyScalar(0.3)
      this.light.shadow.camera.near = 1; //???????????????????????????
      this.light.shadow.camera.far = 400; //???????????????????????????
      this.light.shadow.camera.left = -50; //??????????????????????????????????????????
      this.light.shadow.camera.right = 50; //?????????
      this.light.shadow.camera.top = 50; //?????????
      this.light.shadow.camera.bottom = -50; //?????????

        //???????????????????????????????????? ??????512
      this.light.shadow.mapSize.height = 1024;
      this.light.shadow.mapSize.width = 1024;
      this.light.castShadow = true;
      scene.add(this.light)

    },
    initModel() {
      const planeGeometry = new THREE.PlaneGeometry(100, 100)
      const planeMeterial = new THREE.MeshLambertMaterial({color: 0xaaaaaa, side: THREE.DoubleSide})
      const plane = new THREE.Mesh(planeGeometry, planeMeterial)
      plane.rotation.x = -0.5 * Math.PI
      plane.position.y = -0.1
      plane.receiveShadow = true
      scene.add(plane)

      let self = this
      let loader = new GLTFLoader()
      loader.load('static/model/scene.gltf', (gltf)=> {
        let mesh = gltf.scene
        mesh.scale.set(0.1, 0.1, 0.1)
        mesh.position.set(-70, 0, 0)
        self.scene.add(mesh)
        // mixer = new THREE.AnimationMixer(mesh)
        // mixer.clipAction(gltf.animations[0]).play()
        // self.render()
      }, () => {

      }, (err) => {
        console.log(err)
      })
    },
    initControl(){
      this.control = new OrbitControls(this.camera, this.renderer.domElement)
      // this.control.target.set(-70, 0, 0)
      // this.control.minDistance = 80
      // this.control.maxDistance = 400
      // this.control.maxPolarAngle = Math.PI / 3
      console.log(this.$refs.container)
      // const geometry = new THREE.BoxGeometry()
      // const material = new THREE.MeshPhongMaterial({color: 0xffff00})
      // const texture = new THREE.TextureLoader().load('./QRCode_258.png')
      // material.map = texture
      // this.cube = new THREE.Mesh(geometry, material)
      // this.cube.scale.set(1,1,1)
      // this.cube.castShadow = true
      // scene.add(this.cube)
      // this.control.update()
      // camera.position.z = 5

    },
    render() {
      this.control.update()
      this.renderer.render(scene, this.camera)
      // requestAnimationFrame(this.render)
      // let delta = clock.getDelta()
      // if(mixer) {
      //   mixer.update(delta)
      // }
      // this.renderer.render(scene, this.camera)
      // mixer.update(clock.getDelta())
    },
    animate() {
      this.render()
      requestAnimationFrame(this.animate)
    }
  }
}
</script>