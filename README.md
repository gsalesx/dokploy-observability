# Observabilidade — Dokploy Pessoal

Loki + Grafana + Promtail no mesmo host do Dokploy. Coleta logs de **todos** os containers Docker (Galeed, Planilha Pro, Hermes, paperclip, Univer, Traefik, etc.).

## Deploy

Projeto Dokploy **Observability**, compose `dokploy-logs`, source git:

- Repo: `https://github.com/gsalesx/dokploy-observability.git`
- Branch: `main`
- Compose path: `./docker-compose.yml`
- `isolatedDeployment: false` (Promtail precisa do docker.sock do host)

Variáveis no ambiente do compose:

```
GF_SECURITY_ADMIN_USER=admin
GF_SECURITY_ADMIN_PASSWORD=<senha forte>
GF_SERVER_ROOT_URL=https://<dominio-sslip-do-grafana>
```

Grafana é exposto só pelo Traefik (HTTPS sslip.io). Loki **não** publica a porta 3100.

## Como filtrar erros

Dashboard **Erros globais — todas as apps** (pasta Observability):

- Filtros: `compose`, `container`, `service`
- Galeed: `compose` ≈ `compose-index-multi-byte-matrix-sygim7` (ou o project name do compose)
- Planilha Pro: container `planilha-pro-*`

Explore (LogQL):

```
{job="docker"} |~ "(?i)(error|exception|fatal|panic|traceback| 5[0-9]{2})"
{job="docker", compose=~".*galeed.*|.*sygim7.*"}
{job="docker", container=~".*planilha.*"}
```

## Retenção

90 dias (`2160h`), igual ao checkout.

## Segurança

Troque a senha do Grafana no primeiro acesso. Não exponha Loki na internet.
