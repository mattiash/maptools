# Maptools

This tool allows you to visualize the behaviour of an algorithm that classifies each point on a map
based on which "attractor" it is closest to. An attractor is a point on the map.

You define a set of attractors by their geographical coordinates.
As you move around on the map with the mouse, the closest attractor lights up.
You can also place points on the map and they will be annotated by which attractor they are closest to.

- To temporarily disable an attractor or point, comment it out with a #-sign
- Add a new point by clicking on the map.
- Turn a point into an attractor by copy/pasting it between the two textareas.

The tool can be accessed at https://maptools.holmlund.se

## Linking to Maptools

All data entered in the text-boxes is encoded with encodeURIComponent() and placed in the fragment part of the url.
This allows you to link to maptools with the text-boxes prefilled.

## Data handling

The data entered in the text-boxes is only processed locally in your browser.
The only requests made from your browser are requests for the static files that make up the application
and for the map-tiles from Open Streetmap.

# Development

Install the dependencies...

```bash
npm install
```

...then start [Rollup](https://rollupjs.org):

```bash
npm run dev
```

Navigate to [localhost:5000](http://localhost:5000). You should see your app running. Edit a component file in `src`, save it, and reload the page to see your changes.

If you're using [Visual Studio Code](https://code.visualstudio.com/) we recommend installing the official extension [Svelte for VS Code](https://marketplace.visualstudio.com/items?itemName=svelte.svelte-vscode). If you are using other editors you may need to install a plugin in order to get syntax highlighting and intellisense.

## Building and running in production mode

To create an optimised version of the app:

```bash
npm run build
```

# License

MIT License

Copyright (c) 2021, Mattias Holmlund, <mattias@holmlund.se>

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
