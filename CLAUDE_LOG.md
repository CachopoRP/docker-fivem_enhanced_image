# CLAUDE_LOG — docker-fivem_enhanced

## 2026-08-31 — Auto-actualizacion apuntando a nuestro propio ghcr.io

El servidor avisaba de una build nueva de FiveM Enhanced disponible
(actual: b129, ultima: b139). El repo (fork de xBytez/docker-fivem_enhanced)
ya traia un workflow (`.github/workflows/update.yml`) que revisa
docs.fivem.net cada dia (cron 4:17 UTC), detecta builds nuevas, actualiza
el `Dockerfile` y publica la imagen -- pero nunca se habia ejecutado en
este fork (0 runs, 0 secrets) y publicaba a `ghcr.io/xbytez/...` y
`docker.io/xbytez/...` -- el registro del autor original, no el nuestro.

**Cambios:**
- `update.yml`: imagen ahora `ghcr.io/cachoporp/fivem-enhanced` (antes
  `xbytez`). Quitado el login/push a Docker Hub por completo (no
  teniamos `DOCKERHUB_USERNAME`/`DOCKERHUB_TOKEN` configurados y no
  hacia falta, solo usamos GHCR). El login a GHCR usa `secrets.GITHUB_TOKEN`
  (automatico, sin configuracion extra).
- `Dockerfile`: bump manual inmediato a build 139 (ejecutando
  `scripts/update-enhanced-build.sh` en local) para no esperar al cron
  de mañana -- de aqui en adelante el propio workflow se encarga solo.

**Pendiente / a verificar por Josema:**
- Comprobar en la pestaña Actions del repo que los workflows esten
  habilitados (los forks nuevos a veces piden activarlos a mano la
  primera vez).
- Comprobar en Settings > Actions > General > Workflow permissions que
  este en "Read and write permissions" (el workflow hace `git push`
  directo a `main` y publica paquetes -- con permisos de solo lectura
  fallaria).
- Una vez publique la primera imagen, el paquete en GHCR puede salir
  como privado por defecto -- si el host del servidor hace `docker pull`
  sin estar autenticado a ghcr.io, hay que o bien hacer el paquete
  publico (Settings del paquete en GitHub) o `docker login ghcr.io` en
  el host con un token con `read:packages`.
- Cuando la imagen este publicada y confirmada, cambiar
  `CachopoRP/FiveM-Enhanced`'s `docker-compose.yaml` de
  `image: xbytez/fivem-enhanced:latest` a
  `image: ghcr.io/cachoporp/fivem-enhanced:latest`.
