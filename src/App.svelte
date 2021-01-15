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
  import { LatLng } from "leaflet";

  let attractorS = "59.33258, 18.0649, Stockholm\n51.50853, -0.12574, London";
  let pointS = "52.520008, 13.404954, Berlin\n48.864716, 2.349014, Paris";

  type Point = {
    lat: number;
    lon: number;
    title: string;
    selected?: boolean;
  };
  let attractors = new Array<Point>();

  $: attractors = processInput(attractorS);
  $: console.log(attractors);

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
    let closest: Point = undefined;
    let distance = Infinity;

    for (const p of attractors) {
      const pl = new LatLng(p.lat, p.lon);
      const dist = ev.detail.latlng.distanceTo(pl);
      if (dist < distance) {
        distance = dist;
        if (closest) {
          closest.selected = false;
        }
        p.selected = true;
        closest = p;
      } else {
        p.selected = false;
      }
    }
    attractors = attractors;
  }
</script>

<div style="display: flex; flex-direction: row;">
  <div>
    <h2>Attractors</h2>
    <textarea
      style="height: 40vh; display: inline-block"
      cols="50"
      bind:value={attractorS}
    />
    <h2>Points</h2>
    <textarea
      style="height: 40vh; display: inline-block"
      cols="50"
      bind:value={pointS}
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
      {#each attractors as point}
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
</style>
