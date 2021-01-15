<script lang="ts">
  import {
    CircleMarker,
    LeafletMap,
    Marker,
    Polygon,
    Popup,
    TileLayer,
    Tooltip,
  } from "svelte-leafletjs";

  let input = "59.33258, 18.0649, Stockholm\n51.50853, -0.12574, London";

  type Point = {
    lat: number;
    lon: number;
    title: string;
    selected?: boolean;
  };
  let points = new Array<Point>();

  $: points = processInput(input);
  $: console.log(points);

  function processInput(data: string) {
    const result = new Array<Point>();

    for (const line of data.split("\n").map((s) => s.trim())) {
      try {
        console.log("line", line);
        const [latS, lonS, title] = line.split(/\s*,\s*/);
        const lat = parseFloat(latS);
        const lon = parseFloat(lonS);
        console.log(lat, lon);
        if (lat >= -90 && lat <= 90 && lon >= -180 && lon <= 180) {
          console.log("push");
          result.push({ lat, lon, title });
        }
      } catch (err) {}
    }
    console.log(result);
    return result;
  }
  const mapOptions = {
    center: [59.33258, 18.0649],
    zoom: 2,
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
    // for (const p of TestData.points) {
    //   p[3] = p[0] < ev.detail.latlng.lat;
    // }
    // TestData.points = TestData.points;
  }
</script>

<div style="display: flex; flex-direction: row;">
  <div>
    <textarea
      style="height: 95vh; display: inline-block"
      cols="50"
      bind:value={input}
    />
  </div>
  <div style="height: 95vh; width:100%">
    <LeafletMap
      bind:this={leafletMap}
      options={mapOptions}
      events={["mousemove"]}
      on:mousemove={mousemove}
    >
      <TileLayer url={tileUrl} options={tileLayerOptions} />
      {#each points as point}
        <CircleMarker
          latLng={[point.lat, point.lon]}
          color={point.selected ? "#ff0000" : "#0000ff"}
        >
          <Tooltip>{point.title}</Tooltip>
        </CircleMarker>
      {/each}
    </LeafletMap>
  </div>
</div>

<style>
  /* NOTE: Typically not imported from here, see documentation for more information. */
  @import "https://cdn.jsdelivr.net/npm/leaflet@1.7.1/dist/leaflet.css";

  .example {
    width: 100%;
    height: 100%;
  }
</style>
