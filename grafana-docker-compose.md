# Grafana con Plugin Zabbix — Docker Compose

## Estructura de carpetas

```
/opt/grafana/
└── docker-compose.yml
```

## docker-compose.yml

```yaml
version: "3.8"
services:
  grafana:
    image: grafana/grafana:latest
    container_name: grafana
    restart: unless-stopped
    ports:
      - "3000:3000"
    volumes:
      - grafana-storage:/var/lib/grafana
    environment:
      - GF_INSTALL_PLUGINS=alexanderzobnin-zabbix-app
      - GF_AUTH_ANONYMOUS_ENABLED=true
      - GF_AUTH_ANONYMOUS_ORG_ROLE=Admin
      - GF_AUTH_DISABLE_LOGIN_FORM=true

volumes:
  grafana-storage:
```

> ⚠️ Las variables `GF_AUTH_*` desactivan el login. Úsalas solo en entornos de prueba.

---

## Comandos

### Levantar
```bash
mkdir -p /opt/grafana && cd /opt/grafana
docker compose up -d
```

### Ver logs
```bash
docker compose logs -f grafana
```

### Parar
```bash
docker compose down
```

### Reiniciar
```bash
docker compose restart
```

### Actualizar imagen
```bash
docker compose pull
docker compose up -d
```

---

## Acceso

| Campo | Valor |
|---|---|
| URL | `http://<IP-del-servidor>:3000` |
| Usuario | `admin` (si tienes login activo) |
| Contraseña | `admin` (si tienes login activo) |

---

## Activar el plugin Zabbix

1. Ve a **Administration → Plugins**
2. Busca **Zabbix**
3. Clic en **Enable**

---

## Conectar con Zabbix

1. Ve a **Connections → Data Sources → Add data source**
2. Selecciona **Zabbix**
3. Rellena:
   - **URL:** `http://<ip-zabbix>/zabbix/api_jsonrpc.php`
   - **Username:** usuario de Zabbix
   - **Password:** contraseña de Zabbix
4. Clic en **Save & Test** ✅
