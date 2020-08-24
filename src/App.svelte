<script>
  import { onMount } from "svelte";
  import {
    Canvas,
    Scene,
    PerspectiveCamera,
    DirectionalLight,
    //BasicShadowMap,
    //PCFShadowMap,
    PCFSoftShadowMap,
    //VSMShadowMap,
    AmbientLight,
    BoxBufferGeometry,
    PlaneBufferGeometry,
    Mesh,
    MeshStandardMaterial,
    WebGLRenderer,
    // OrbitControls,
    DoubleSide,
    MathUtils,
  } from "svelthree";

  let cubeGeometry = new BoxBufferGeometry(1, 1, 1);
  let cubeMaterial = new MeshStandardMaterial();

  let fruitMaterial = new MeshStandardMaterial();

  let floorGeometry = new BoxBufferGeometry(10.5, 0.5, 10.5);
  let floorMaterial = new MeshStandardMaterial();

  let xspeed = 1;
  let yspeed = 0;
  let zspeed = 0;
  let x = 1;
  let y = 1;
  let z = 1;
  let fruitx = 5;
  let fruity = 1;
  let fruitz = 1;
  let length = 1;
  let tail = [{ x: 1, y: 1, z: 1 }];
  setInterval(() => {
    console.log("test");
    // set snake direction
    x = x + xspeed;
    y = y + yspeed;
    z = z + zspeed;
    // eat fruit
    if (x === fruitx && y === fruity && z === fruitz) {
      length = length + 1;
      fruitx = Math.floor(Math.random() * 10) + 1;
      fruity = Math.floor(Math.random() * 10) + 1;
      fruitz = Math.floor(Math.random() * 10) + 1;
    }
    // wrap snake if hits bounding box
    if (x > 10) {
      x = 1;
    }
    if (x < 1) {
      x = 10;
    }

    if (y > 10) {
      y = 1;
    }
    if (y < 1) {
      y = 10;
    }

    if (z > 9) {
      z = 1;
    }
    if (z < 1) {
      z = 10;
    }
    //add voxel to end of tail
    tail.push({ x, y, z });
    var i = 1;
    //delete end of tail
    while (tail.length > length) {
      tail.shift();
      i = i + 1;
    }
    tail = tail;
  }, 10);

  document.addEventListener("keydown", function (event) {
    if (event.shiftKey) {
      xspeed = 0;
      yspeed = -1;
      zspeed = 0;
    }
    if (event.code == "Space") {
      xspeed = 0;
      yspeed = 1;
      zspeed = 0;
    }
    if (event.code == "KeyD") {
      xspeed = 1;
      yspeed = 0;
      zspeed = 0;
    }
    if (event.code == "KeyA") {
      xspeed = -1;
      yspeed = 0;
      zspeed = 0;
    }
    if (event.code == "KeyW") {
      xspeed = 0;
      yspeed = 0;
      zspeed = -1;
    }
    if (event.code == "KeyS") {
      xspeed = 0;
      yspeed = 0;
      zspeed = 1;
    }
  });
</script>

<style>
  div {
    overflow: hidden;
  }
</style>

<div>
  <Canvas let:sti w={window.innerWidth} h={window.innerHeight} id="canvas">

    <Scene {sti} let:scene id="scene1" props={{ background: 0xedf2f7 }}>

      <PerspectiveCamera
        {scene}
        id="cam1"
        pos={[20, 10, 20]}
        lookAt={[0, 0, 0]} />
      <AmbientLight {scene} intensity={1.25} />
      <DirectionalLight
        {scene}
        pos={[15, 15, 10]}
        intensity={0.8}
        shadowMapSize={512 * 8}
        castShadow />

      {#each tail as item}
        <Mesh
          {scene}
          geometry={cubeGeometry}
          material={cubeMaterial}
          mat={{ roughness: 0.5, metalness: 0.5, color: 0x00ff00 }}
          pos={[item.x, item.y, item.z]}
          rot={[0, 0, 0]}
          castShadow
          receiveShadow />
      {/each}

      <Mesh
        {scene}
        geometry={cubeGeometry}
        material={fruitMaterial}
        mat={{ roughness: 0.5, metalness: 0.5, color: 0xff3e00 }}
        pos={[fruitx, fruity, fruitz]}
        rot={[0, 0, 0]}
        castShadow
        receiveShadow />

      <Mesh
        {scene}
        geometry={floorGeometry}
        material={floorMaterial}
        mat={{ roughness: 0.5, metalness: 0.5, side: DoubleSide, color: 0xf7fafc }}
        pos={[5.25, 0.25, 5.25]}
        rot={[MathUtils.degToRad(0), 0, 0]}
        scale={[1, 1, 1]}
        receiveShadow />
      <Mesh
        {scene}
        geometry={floorGeometry}
        material={floorMaterial}
        mat={{ roughness: 0.5, metalness: 0.5, side: DoubleSide, color: 0xf7fafc }}
        pos={[5.25, 5.25, 0.25]}
        rot={[MathUtils.degToRad(-90), 0, 0]}
        scale={[1, 1, 1]}
        receiveShadow />
      <Mesh
        {scene}
        geometry={floorGeometry}
        material={floorMaterial}
        mat={{ roughness: 0.5, metalness: 0.5, side: DoubleSide, color: 0xf7fafc }}
        pos={[0.25, 5.25, 5.25]}
        rot={[-1.57, 0, -1.57]}
        scale={[1, 1, 1]}
        receiveShadow />

      <!-- <OrbitControls {scene} autoRotate enableDamping /> -->
      <!-- <OrbitControls {scene} /> -->

    </Scene>

    <WebGLRenderer
      {sti}
      sceneId="scene1"
      camId="cam1"
      config={{ antialias: true, alpha: true }}
      enableShadowMap
      shadowMapType={PCFSoftShadowMap} />

  </Canvas>
</div>
