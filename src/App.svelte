<script lang="ts">
  import "leaflet/dist/leaflet.css";

  import {
    CircleMarker,
    LeafletMap,
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

  let points = new Array<Point>();
  $: points = processInput(pointS);

  function processInput(data: string) {
    const result = new Array<Point>();

    for (const line of data.split("\n").map((s) => s.trim())) {
      try {
        if (line.startsWith("#")) {
          continue;
        }
        const [latS, lonS, title] = line.split(/\s*,\s*/);
        const lat = parseFloat(latS);
        const lon = parseFloat(lonS);
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

  function closestPoint(searchPoint: LatLng, points: Array<Point>) {
    let closest: Point = undefined;
    let distance = Infinity;
    for (const p of attractors) {
      const pl = new LatLng(p.lat, p.lon);
      const dist = searchPoint.distanceTo(pl);
      if (dist < distance) {
        distance = dist;
        closest = p;
      }
    }
    return closest;
  }

  function mousemove(ev) {
    const closest = closestPoint(ev.detail.latlng, attractors);
    for (let attractor of attractors) {
      attractor.selected = attractor === closest;
    }
    attractors = attractors;
  }
</script>

<div style="display: flex; flex-direction: row;">
  <div>
    <h2>Attractors</h2>
    <textarea
      style="height: 30vh; display: inline-block"
      cols="50"
      bind:value={attractorS}
    />
    <h2>Points</h2>
    <textarea
      style="height: 30vh; display: inline-block"
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
      {#each points as point}
        <CircleMarker
          latLng={[point.lat, point.lon]}
          color={point.selected ? "#ff0000" : "#0000ff"}
          fill={true}
          fillColor="#0000ff"
          fillOpacity={1.0}
          fillRule="nonzero"
          radius={5}
        >
          <Tooltip
            >{point.title}<br />
            closest to {closestPoint(
              new LatLng(point.lat, point.lon),
              attractors
            ).title}</Tooltip
          >
        </CircleMarker>
      {/each}
    </LeafletMap>
  </div>
</div>

<style>
</style>
