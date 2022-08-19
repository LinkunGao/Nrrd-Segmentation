<template>
  <div id="bg" ref="base_container"></div>
</template>
<script setup lang="ts">
import { GUI } from "dat.gui";
import * as Copper from "copper3d_visualisation";
import "copper3d_visualisation/dist/css/style.css";
import { getCurrentInstance, onMounted, ref } from "vue";

let refs = null;
let bg: HTMLDivElement = ref<any>(null);
let appRenderer: Copper.copperMSceneRenderer;

onMounted(() => {
  let { $refs } = (getCurrentInstance() as any).proxy;
  refs = $refs;
  bg = refs.base_container;

  appRenderer = new Copper.copperMSceneRenderer(bg, 1);

  appRenderer.sceneInfos[0].addSubView();

  loadNrrd(
    "/nrrd-segmentation/nrrd/breast-224.nrrd",
    "nrrd0",
    appRenderer.sceneInfos[0]
  );

  appRenderer.animate();
});

function loadNrrd(url: string, name: string, sceneIn: Copper.copperMScene) {
  const funa = (
    volume: any,
    nrrdMesh: Copper.nrrdMeshesType,
    nrrdSlices: Copper.nrrdSliceType
  ) => {
    /**
     * for test 1 view
     * */
    sceneIn.loadViewUrl("/nrrd-segmentation/nrrd_view.json");
    sceneIn.subScene.add(nrrdMesh.z);
    sceneIn.dragImage(nrrdSlices.z, {
      mode: "mode1",
      showNumber: true,
    });
    appRenderer.sceneInfos[0].drawImage(
      nrrdSlices.z,
      appRenderer.sceneInfos[0]
    );
  };
  if (sceneIn) {
    sceneIn?.loadNrrd(url, funa);
    sceneIn.loadViewUrl("/nrrd-segmentation/nrrd_view.json");
  }
  sceneIn.updateBackground("#18e5a7", "#ff00ff");
  Copper.setHDRFilePath("/nrrd-segmentation/venice_sunset_1k.hdr");
  appRenderer.updateEnvironment(sceneIn);
}
</script>

<style>
#bg {
  width: 100vw;
  height: 100vh;
  /* border: 1px solid palevioletred; */
}
.copper3d_sliceNumber {
  position: fixed !important;
  width: 300px;
  text-align: center;
  top: 5% !important;
  right: 1% !important;
  left: 0px !important;
  margin: 0 auto;
  border: 1px solid salmon;
  border-radius: 10px;
  padding: 5px;
  color: crimson;
}
.copper3D_scene_div {
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>
