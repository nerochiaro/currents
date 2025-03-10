<script setup lang="ts">
import "cesium/Build/Cesium/Widgets/widgets.css";
import { setupCesium } from './setup-cesium';
import { onMounted } from 'vue';
import { Color, Cartesian2, Cartesian3, Math as CMath } from 'cesium';
import { latLngToCell, cellsToMultiPolygon } from 'h3-js';

declare global {
  interface Window { CESIUM_BASE_URL: string; }
}

window.CESIUM_BASE_URL = 'static/cesium';


onMounted(() => {
  const viewer = setupCesium('cesium');

  const wyoming = viewer.entities.add({
    polygon: {
      hierarchy: Cartesian3.fromDegreesArray([
        -109.080842, 45.002073, -105.91517, 45.002073, -104.058488, 44.996596,
        -104.053011, 43.002989, -104.053011, 41.003906, -105.728954, 40.998429,
        -107.919731, 41.003906, -109.04798, 40.998429, -111.047063, 40.998429,
        -111.047063, 42.000709, -111.047063, 44.476286, -111.05254, 45.002073,
      ]),
      height: 0,
      material: Color.RED.withAlpha(0.1),
      outline: true,
      outlineColor: Color.BLACK,
    },
  });


  viewer.canvas.addEventListener('click', function(e) {
    const mousePosition = new Cartesian2(e.clientX, e.clientY);
    const ellipsoid = viewer.scene.globe.ellipsoid;
    const cartesian = viewer.camera.pickEllipsoid(mousePosition, ellipsoid);
    if (cartesian) {
      const cartographic = ellipsoid.cartesianToCartographic(cartesian);
      const longitudeString = CMath.toDegrees(cartographic.longitude).toFixed(2);
      const latitudeString = CMath.toDegrees(cartographic.latitude).toFixed(2);
      const h3 = latLngToCell(Number(latitudeString), Number(longitudeString), 1);
      const hexes = cellsToMultiPolygon([h3], true);
      console.log(longitudeString + ', ' + latitudeString + ' -> ' + h3 + ' -> ', hexes);
      const points = hexes[0][0].reduce((a, p) => {
        a.push(p[0], p[1]);
        return a;
      }, [] as number[]);
      console.log(points);

      const hex = viewer.entities.add({
        polygon: {
          hierarchy: Cartesian3.fromDegreesArray(points),
          height: 0,
          material: Color.RED.withAlpha(0.1),
          outline: true,
          outlineColor: Color.BLACK,
        },
      });

      viewer.zoomTo(hex);
    } else {
        alert('Globe was not picked');
    }
  });
});
</script>

<template>
  <div id="cesium" />
</template>

<style scoped>
body {
  margin: 0px;
}
#cesium {
  margin: 0px;
  width: 100vw;
  height: 100vh;
}
</style>
