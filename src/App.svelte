<script>
  import {
    CircleMarker,
    LeafletMap,
    Marker,
    Polygon,
    Popup,
    TileLayer,
    Tooltip,
  } from "svelte-leafletjs";
  import TestData from "./TestData";

  const mapOptions = {
    center: [1.364917, 103.822872],
    zoom: 11,
  };
  const tileUrl = "https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png";
  const tileLayerOptions = {
    minZoom: 0,
    maxZoom: 20,
    maxNativeZoom: 19,
    attribution:
      '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors',
  };

  let leafletMap;

  function mousemove(ev) {
    for (const p of TestData.points) {
      p[3] = p[0] < ev.detail.latlng.lat;
    }
    TestData.points = TestData.points;
  }
</script>

<div class="example">
  <LeafletMap
    bind:this={leafletMap}
    options={mapOptions}
    events={["mousemove"]}
    on:mousemove={mousemove}
  >
    <TileLayer url={tileUrl} options={tileLayerOptions} />
    <Polygon
      latLngs={TestData.sentosaPolygon}
      color="#ff0000"
      fillColor="#ff0000"
    >
      <Popup>Sentosa</Popup>
      <Tooltip>Sentosa</Tooltip>
    </Polygon>
    {#each TestData.points as point}
      <CircleMarker
        latLng={[point[0], point[1]]}
        color={point[3] ? "#ff0000" : "#0000ff"}
      >
        <Tooltip>{point[2]}</Tooltip>
      </CircleMarker>
    {/each}
  </LeafletMap>
</div>

<style>
  /* NOTE: Typically not imported from here, see documentation for more information. */
  @import "https://cdn.jsdelivr.net/npm/leaflet@1.7.1/dist/leaflet.css";

  .example {
    width: 100%;
    height: 100%;
  }
</style>
