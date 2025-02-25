<template>
    <h1>Image</h1>
    <div id="content"></div>
</template>

<script lang="ts" setup>
import { onMounted } from 'vue';
import {
  RenderingEngine,
  Enums,
  imageLoader,
  metaData,
  getRenderingEngine,
  setVolumesForViewports,
  volumeLoader,
  init,
} from '@cornerstonejs/core';
import hardcodedMetaDataProvider from './hardcodedMetaDataProvider';
import registerWebImageLoader from './registerWebImageLoader';

const { ViewportType, OrientationAxis } = Enums;
function createElements() {
    const content = document.getElementById('content');

    if (!content) return null;

    const element = document.createElement('div');
    element.id = 'cornerstone-element';
    element.style.width = '700px';
    element.style.height = '700px';

    content.appendChild(element);

    return { element };
}

// const imageIds = [
//   'web:https://cs3d-jpg-example.s3.us-east-2.amazonaws.com/a_vm1460.png'
// ]

    const imageIds = [
        'web:http://localhost:3000/frame_2533.jpg',
    ]


  async function renderImage() {
  console.log('Initializing Cornerstone3D...');
  await init();

  metaData.addProvider(
    (type, imageId) => hardcodedMetaDataProvider(type, imageId, imageIds),
    10000
  );

  const renderingEngineId = 'myRenderingEngine';
  const viewportId = 'COLOR_STACK';

  const elements = createElements();
  console.log('elements',elements);
  
  if (!elements) {
    console.error('Failed to create elements');
    return;
  }

  const { element } = elements;

  registerWebImageLoader(imageLoader);
  const renderingEngine = new RenderingEngine(renderingEngineId);

  const viewportInputArray = [
    { viewportId: viewportId, type: ViewportType.STACK, element: element },
    // { viewportId: 'COLOR_VOLUME_1', type: ViewportType.ORTHOGRAPHIC, element: element2 },
    // { viewportId: 'COLOR_VOLUME_2', type: ViewportType.ORTHOGRAPHIC, element: element3, defaultOptions: { orientation: OrientationAxis.CORONAL } },
    // { viewportId: 'COLOR_VOLUME_3', type: ViewportType.ORTHOGRAPHIC, element: element4, defaultOptions: { orientation: OrientationAxis.SAGITTAL } },
  ];

  const volumeId = 'cornerstoneStreamingImageVolume:COLOR_VOLUME';
  const volume = await volumeLoader.createAndCacheVolume(volumeId, { imageIds });

  renderingEngine.setViewports(viewportInputArray);
  renderingEngine.getStackViewports()[0].setStack(imageIds);
//   await setVolumesForViewports(renderingEngine, [{ volumeId }], ['COLOR_VOLUME']);
//   await volume.load();

  renderingEngine.render();
}

onMounted(() => {
  console.log('hello');
  renderImage();
});
</script>
