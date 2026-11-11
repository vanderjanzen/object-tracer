# STL Konturzeichner

Browser-Tool, das STL-Dateien als technische Linienzeichnung darstellt und als PNG, GIF (Spin-Animation) oder Lottie-JSON exportiert. Alles läuft clientseitig, die STL verlässt den Browser nicht.

## Lokal starten

```bash
docker build -t stl-konturzeichner .
docker run -p 8080:80 stl-konturzeichner
```

Dann `http://localhost:8080` öffnen. Ohne Docker reicht auch `npx serve public`.

## Deployment mit Dokploy

1. Neue Application anlegen, dieses Repo verknüpfen, Branch `main`.
2. Build Type: **Dockerfile** (Pfad `./Dockerfile`).
3. Domain hinzufügen (z. B. `tools.fragment-market.de`), Container-Port **80**.
4. Im Cloudflare-Tunnel einen Public Hostname für die Domain auf den Dokploy/Traefik-Eingang anlegen.
5. Deploy. Bei jedem Push auf `main` neu deployen (Autodeploy in Dokploy aktivieren).

## Struktur

```
public/index.html   Die komplette App (three.js, gifenc über jsDelivr-CDN)
Dockerfile          nginx:alpine, liefert public/ aus
nginx.conf          gzip, Caching, SPA-Fallback
```
