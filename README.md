# Object Tracer

Browser-Tool, das STL-Dateien in saubere Linienzeichnungen verwandelt und als PNG, animiertes GIF oder Lottie exportiert. Läuft komplett clientseitig, die Modelle verlassen nie den Browser.

**Live: https://object-tracer.com**

## Lokal starten

```bash
docker build -t object-tracer .
docker run -p 8080:80 object-tracer
```

Dann `http://localhost:8080` öffnen. Ohne Docker reicht auch `npx serve public`.

## Deployment

Gehostet über Dokploy auf dem Server `vortex-imperialis` (SSH-Alias `homeserver`), ausgeliefert über Cloudflare Tunnel.

- Domain: `object-tracer.com`, Container-Port **80**
- Dokploy-App: `objecttracer-objecttracer-pmou7y`, Build Type **Dockerfile** (`./Dockerfile`), Branch `main`
- Traefik-Router: ``Host(`object-tracer.com`)`` → `http://objecttracer-objecttracer-pmou7y:80`
- Der `cloudflared`-Container leitet den Public Hostname auf `http://dokploy-traefik:80`

### Neu ausrollen

Normalerweise über die Dokploy-Oberfläche (`http://192.168.178.165:3000`) → App → Redeploy.

Geht das nicht, lässt sich das Image direkt aus diesem Repo bauen. Der Dokploy-Checkout unter
`/etc/dokploy/applications/objecttracer-objecttracer-pmou7y/code` gehört root und ist per SSH nicht
beschreibbar, der Build läuft deshalb gegen die GitHub-URL:

```bash
ssh homeserver
docker build --pull -t objecttracer-objecttracer-pmou7y:<sha> "https://github.com/vanderjanzen/object-tracer.git#main"
docker run --rm --entrypoint sh objecttracer-objecttracer-pmou7y:<sha> -c "md5sum /usr/share/nginx/html/index.html"
docker tag objecttracer-objecttracer-pmou7y:latest objecttracer-objecttracer-pmou7y:rollback-<alter-sha>
docker tag objecttracer-objecttracer-pmou7y:<sha> objecttracer-objecttracer-pmou7y:latest
docker service update --force --image objecttracer-objecttracer-pmou7y:latest objecttracer-objecttracer-pmou7y
```

Dabei bleibt der Dokploy-Checkout auf dem alten Commit, die Dokploy-UI zeigt dann einen veralteten
Stand an. Der nächste Deploy über Dokploy zieht den Code selbst neu.

### Live-Stand prüfen

```bash
curl -sI https://object-tracer.com | grep -i last-modified
```

## Struktur

```
public/index.html   Die komplette App (three.js, gifenc über jsDelivr-CDN)
public/favicon.svg  Tab-Icon
Dockerfile          nginx:alpine, liefert public/ aus
nginx.conf          gzip, Caching, SPA-Fallback
```
