---
title: "Embed brand SVG icons in an Excalidraw diagram (image element + files dataURL; Simple Icons CDN)"
created: 2026-06-19
type: howto
status: seedling
source: "session 2026-06-19"
tags: [excalidraw, icons, svg, simple-icons, diagrams, branding]
---

# Embed brand SVG icons in an Excalidraw diagram (image element + files dataURL; Simple Icons CDN)

How to add real brand logos to an Excalidraw diagram (and have render_excalidraw.py draw them).

## Get the icons
Simple Icons CDN serves brand SVGs by slug, recolorable: `curl -s https://cdn.simpleicons.org/<slug>/<hexcolor> -o icon.svg`. Useful slugs: obsidian, claude, anthropic, googlecloud, googlegemini, github. (Monochrome — pass the brand hex you want.)

## Embed in the .excalidraw JSON
Excalidraw stores images in a top-level `files` map keyed by a fileId; an `image` element references it:
```js
const url='data:image/svg+xml;base64,'+fs.readFileSync('icon.svg').toString('base64');
const fid='ic_obsidian_1';
files[fid]={mimeType:'image/svg+xml',id:fid,dataURL:url,created:0,lastRetrieved:0};
elements.push({type:'image',id:'im1',x,y,width:size,height:size,angle:0,fileId:fid,scale:[1,1],
  strokeColor:'transparent',backgroundColor:'transparent',fillStyle:'solid',strokeWidth:1,strokeStyle:'solid',
  roughness:0,opacity:100,groupIds:[],frameId:null,roundness:null,seed,version:1,versionNonce,isDeleted:false,
  boundElements:null,updated:1,link:null,locked:false,status:'saved'});
```
Set `doc.files = files` (not `{}`). Confirmed: the `render_excalidraw.py` renderer (excalidraw-diagram skill) DOES rasterize embedded SVG image elements — tested with the Obsidian logo. Add icons AFTER their background rect in the elements array so they draw on top.

## Placement tip
Put icons on title-bar/title-row corners or as a hero inside a node — keep them off the body text. SVG scales crisply at any size.

Context: C:\Users\dvtliem\.claude\docs\obsidian-present\build\gen-ecosystem.js (Claude / Obsidian / GitHub / Google Cloud / Gemini icons).
