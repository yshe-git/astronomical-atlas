# 🌌 Astronomical 3D Point Cloud Extension for Embedding Atlas

An elegant, component-based 3D extension for Apple's `embedding-atlas`. This module leverages the power of **Svelte 5** and **Three.js** to build an end-to-end 3D point cloud visualization, multi-dimensional interaction, and adaptive performance downsampling system tailor-made for large-scale astronomical datasets (e.g., LSDB / Gaia catalogs).

---

## ✨ Key Features

* 🪐 **Interactive 3D Workspace** 
  * Powered by Svelte 5 and Three.js for rendering.
  * Supports smooth navigation (orbit, pan, zoom) with a customized `OrbitControls` configuration featuring physical damping.
  * Implements responsive camera animations (Cubic Ease-Out interpolation) for smooth point-focusing and one-click camera resets.

* 🚀 **Frustum-Based 3D Downsampling**
  * Integrates an idle-scheduled 3D downsampling engine (`downsample_3d.ts`) designed for massive stellar point clouds.
  * Dynamically culls points outside the camera's viewing frustum, minimizing GPU memory footprint and maintaining high rendering frame rates (FPS).

* 🎯 **Dual Interaction Mode**
  * Employs 3D raycasting (`Raycaster`) for precise stellar point selection (Click) and hover tooltips (Hover).
  * Draws bright, enlarged point overlays for highlighted stars on top of the main point cloud dynamically.

* 🧩 **3D Tiling & Mosaic Support**
  * Extends the existing 2D tiling logic to a 3D spatial indexing pipeline, enabling chunked loading and unloading of extremely vast datasets.

---

## 🔧 File Changes & Architecture

Here is the structural mapping of the newly introduced 3D pipeline within the `embedding_view` module:

```text
packages/component/src/lib/embedding_view/
├── EmbeddingView3D.svelte        [🆕 Created] Core Svelte 5 + Three.js 3D point cloud component
├── StatusBar3D.svelte            [🆕 Created] 3D HUD control panel (Stats display / Camera reset)
├── downsample_3d.ts              [🆕 Created] 3D view-frustum downsampling math algorithms
├── EmbeddingView3DMosaic.svelte  [🆕 Created] 3D chunked/tiled mosaic renderer
├── embedding_view_3d_api.ts      [🆕 Created] API endpoints for 3D coordinate requests
├── embedding_view_3d_mosaic_api.ts [🆕 Created] API endpoints for 3D mosaic tiled requests
├── embedding_view_api.ts         [🛠️ Modified] Configured dimension endpoints to support Z-axis
└── api.ts                        [🛠️ Modified] Registered 3D endpoints to the main data gateway

🛸 System Workflow (Data Pipeline)
【LSDB Database / Parquet Catalog】
                 ↓ (FastAPI Filters & Queries)
【FastAPI Server】 (Serves ra, dec, parallax / distance coordinates)
                 ↓ (API requests: embedding_view_3d_api)
【3D Mosaic Component】 (EmbeddingView3DMosaic) Fetches tiled chunks on-demand
                 ↓
【3D Workspace Component】 (EmbeddingView3D) Converts raw coords to GPU Float32Arrays
                 ↓
【3D Downsampler】 (downsample_3d) Filters out points outside the camera frustum on idle ticks
                 ↓
【Three.js WebGL Engine】 Draws glowing stellar points on the canvas
                 ↓
【User Viewport】 Interacts with OrbitControls and views catalog stats in StatusBar3D
