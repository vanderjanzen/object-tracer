# Object Tracer

Browser-Tool, das STL-Dateien in saubere Linienzeichnungen verwandelt und als PNG, animiertes GIF oder Lottie exportiert. Läuft komplett clientseitig, die Modelle verlassen nie den Browser.

## Lokal starten

```bash
docker build -t object-tracer .
docker run -p 8080:80 object-tracer
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
