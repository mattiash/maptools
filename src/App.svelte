<script lang="ts">
  import "leaflet/dist/leaflet.css";

  import {
    CircleMarker,
    LeafletMap,
    TileLayer,
    Tooltip,
  } from "svelte-leafletjs";
  import { LatLng } from "leaflet";

  const orgData = decodeURIComponent(window.location.hash.substr(1)).split(
    "**"
  );
  let attractorS =
    orgData[0] || "59.33258, 18.0649, Stockholm\n51.50853, -0.12574, London";
  let pointS =
    orgData[1] || "52.520008, 13.404954, Berlin\n48.864716, 2.349014, Paris";

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

  $: window.location.hash = encodeURIComponent(attractorS + "**" + pointS);

  let currLat = 0;
  let currLon = 0;

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
          result.push({ lat, lon, title });
        }
      } catch (err) {}
    }
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
    currLat = ev.detail.latlng.lat.toFixed(3);
    currLon = ev.detail.latlng.lng.toFixed(3);

    const closest = closestPoint(ev.detail.latlng, attractors);
    for (let attractor of attractors) {
      attractor.selected = attractor === closest;
    }
    attractors = attractors;
  }

  let clickCount = 0;
  let prevLat = 0;
  let prevLon = 0;

  function click(ev) {
    const lat = ev.detail.latlng.lat;
    const lon = ev.detail.latlng.lng;

    // The click handler is fired twice. Why?
    if (lat !== prevLat && lon !== prevLon) {
      prevLat = lat;
      prevLon = lon;
      clickCount++;
      const title = `click${clickCount}`;
      pointS += `\n${lat}, ${lon}, ${title}`;
    }
  }

  let isOver = false;
  function mouseover() {
    isOver = true;
  }

  function mouseout() {
    isOver = false;
  }
</script>

<div class="outer">
  <div>
    <h2>Attractors</h2>
    <textarea cols="50" bind:value={attractorS} />
    <h2>Points</h2>
    <textarea cols="50" bind:value={pointS} />
    {#if isOver}
      <div>Lat: {currLat}</div>
      <div>Lon: {currLon}</div>
    {/if}
  </div>
  <div class="map">
    <LeafletMap
      bind:this={leafletMap}
      options={mapOptions}
      events={["mousemove", "click", "mouseover", "mouseout"]}
      on:mousemove={mousemove}
      on:mouseover={mouseover}
      on:mouseout={mouseout}
      on:click={click}
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
  .outer {
    display: flex;
    flex-direction: row;
  }

  textarea {
    height: 30vh;
    display: inline-block;
    margin-right: 0.5em;
  }

  .map {
    height: 95vh;
    width: 100%;
  }
</style>
