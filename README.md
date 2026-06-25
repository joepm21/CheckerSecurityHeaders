# PVGUARD - Security Headers Checker v1.0 💀

![Version](https://img.shields.io/badge/version-1.0-blue)
![Platform](https://img.shields.io/badge/platform-Linux%20x86__64-lightgrey)
![Python](https://img.shields.io/badge/python-3.13-green)
![Type](https://img.shields.io/badge/type-Security%20Headers%20Analyzer-red)

**PVGUARD Security Headers Checker** es una herramienta orientada a pentesters, auditores de seguridad y equipos Blue Team/DevSecOps para analizar cabeceras HTTP de seguridad en aplicaciones web. Permite validar controles defensivos comunes contra ataques como **clickjacking**, **MIME sniffing**, **downgrade/SSL stripping**, exposición de información, políticas CORS permisivas y configuraciones débiles de Content Security Policy.

La herramienta puede ejecutarse en modo consola, generar reportes profesionales en HTML, exportar resultados en JSON/CSV y trabajar con escenarios autenticados mediante cookies, tokens o cabeceras personalizadas.

---

## Tabla de contenido

- [Características principales](#características-principales)
- [Análisis técnico del ejecutable](#análisis-técnico-del-ejecutable)
- [Cabeceras evaluadas](#cabeceras-evaluadas)
- [Instalación y permisos](#instalación-y-permisos)
- [Uso rápido](#uso-rápido)
- [Opciones disponibles](#opciones-disponibles)
- [Ejemplos de uso para pentesting](#ejemplos-de-uso-para-pentesting)
- [Interpretación de resultados](#interpretación-de-resultados)
- [Reportes generados](#reportes-generados)
- [Flujo recomendado en auditorías](#flujo-recomendado-en-auditorías)
- [Limitaciones](#limitaciones)
- [Buenas prácticas de uso](#buenas-prácticas-de-uso)
- [Autor](#autor)
- [Disclaimer](#disclaimer)

---

## Características principales

- Análisis de cabeceras HTTP de seguridad modernas.
- Detección de cabeceras faltantes, débiles, obsoletas o informativas.
- Evaluación técnica de HSTS, CSP, X-Frame-Options, Referrer-Policy y Permissions-Policy.
- Validación de CORS, incluyendo configuraciones permisivas con credenciales.
- Identificación de cabeceras que pueden divulgar tecnología o versiones del servidor.
- Detección de cabeceras deprecated como `X-XSS-Protection`, `Expect-CT`, `Feature-Policy` y HPKP.
- Modo consola con salida progresiva estilo análisis técnico.
- Reporte HTML profesional para evidencias de auditoría.
- Exportación en JSON y CSV para integración con reportes o pipelines.
- Soporte bilingüe: inglés y español.
- Soporte para proxy HTTP/HTTPS, útil con Burp Suite, OWASP ZAP o mitmproxy.
- Soporte para cookies, Authorization header y cabeceras personalizadas.
- Validación opcional con certificados TLS inválidos mediante `-k` / `--insecure`.
- Modo gráfico con `--gui`.
- Compatible con ejecución como binario Linux x86_64.

---

## Análisis técnico del ejecutable

El ejecutable analizado corresponde a un binario Linux empaquetado desde Python.

| Propiedad | Valor |
|---|---|
| Nombre | `PVGUARD_SecurityHeaders` |
| Tipo | ELF 64-bit LSB PIE executable |
| Arquitectura | x86-64 |
| Sistema objetivo | GNU/Linux |
| Empaquetado | PyInstaller |
| Python embebido | Python 3.13 |
| Versión de la herramienta | 1.0 |
| Tamaño aproximado | 19 MB |
| SHA256 | `389af1f88127f8737f0502db4d46be31fedff6024268ccd5d1db52f78ecf617f` |
| Autor mostrado en banner | Gh0s7m4n |

El binario incluye dependencias necesarias para ejecutarse como herramienta portable en Linux. Durante el análisis se identificaron módulos asociados a solicitudes HTTP, manejo de TLS, parsing de URL, exportación de datos, generación de reportes y soporte de interfaz gráfica.

---

## Cabeceras evaluadas

### Cabeceras principales de seguridad

| Cabecera | Objetivo de seguridad |
|---|---|
| `Strict-Transport-Security` | Fuerza el uso de HTTPS y reduce riesgo de downgrade o SSL stripping. |
| `X-Content-Type-Options` | Mitiga MIME sniffing cuando usa `nosniff`. |
| `X-Frame-Options` | Reduce exposición a clickjacking con `DENY` o `SAMEORIGIN`. |
| `Content-Security-Policy` | Reduce impacto de XSS, clickjacking y carga de recursos no confiables. |
| `Referrer-Policy` | Controla exposición de información mediante el header Referer. |
| `Permissions-Policy` | Restringe APIs del navegador como cámara, micrófono o geolocalización. |

### Evaluaciones adicionales

| Categoría | Cabeceras / Controles |
|---|---|
| CORS | `Access-Control-Allow-Origin`, `Access-Control-Allow-Methods`, `Access-Control-Allow-Headers`, `Access-Control-Allow-Credentials`, `Access-Control-Expose-Headers`, `Access-Control-Max-Age` |
| Cross-Origin Isolation | `Cross-Origin-Opener-Policy`, `Cross-Origin-Embedder-Policy`, `Cross-Origin-Resource-Policy` |
| Divulgación de información | `Server`, `X-Powered-By`, `X-AspNet-Version`, `X-AspNetMvc-Version`, `X-Generator` |
| Caché | `Cache-Control`, `Pragma`, `Expires` |
| Manejo de contenido | `Content-Disposition`, `Upgrade-Insecure-Requests`, `X-Content-Duration`, `X-DNS-Prefetch-Control`, `X-Download-Options`, `X-Permitted-Cross-Domain-Policies` |
| Compatibilidad / legado | `X-Request-ID`, `X-UA-Compatible` |
| Deprecated | `X-XSS-Protection`, `Expect-CT`, `Feature-Policy`, `Public-Key-Pins`, `Public-Key-Pins-Report-Only`, `X-Content-Security-Policy`, `X-WebKit-CSP` |

---

## Instalación y permisos

### Usando el ejecutable

```bash
chmod +x PVGUARD_SecurityHeaders
./PVGUARD_SecurityHeaders --help
```
Dependencias recomendadas para ejecución desde código fuente:

```bash
pip install requests urllib3 colorama
```

`colorama` se usa para salida coloreada en consola. La herramienta puede ejecutarse sin colores usando `--no-color`.

---

## Uso rápido

Ejecutar análisis interactivo:

```bash
./PVGUARD_SecurityHeaders
```

Analizar un objetivo por consola:

```bash
./PVGUARD_SecurityHeaders https://example.com --console
```

Generar reporte HTML completo:

```bash
./PVGUARD_SecurityHeaders https://example.com --report
```

Generar reporte en español:

```bash
./PVGUARD_SecurityHeaders https://example.com --report --lang es
```

Guardar reportes en HTML, JSON y CSV:

```bash
./PVGUARD_SecurityHeaders https://example.com --report --html reporte.html --json reporte.json --csv hallazgos.csv --no-browser
```

---

## Opciones disponibles

```text
usage: pvguard-headers [-h] [--console | --report] [--lang {en,es}]
                       [--choose-lang] [--method {GET,POST,HEAD,TRACE,PUT}]
                       [--cookie COOKIE] [-a AUTHORIZATION] [-H HEADER]
                       [--proxy PROXY] [-k] [--timeout TIMEOUT]
                       [--no-redirect] [--live-delay LIVE_DELAY]
                       [--html HTML_PATH] [--no-browser] [--json JSON_PATH]
                       [--csv CSV_PATH] [--no-color] [--debug-errors] [--gui]
                       [-v]
                       [url]
```

| Opción | Descripción |
|---|---|
| `url` | URL o dominio objetivo. Si se omite, la herramienta lo solicita en consola. |
| `--console` | Ejecuta solo el análisis progresivo por consola. |
| `--report` | Ejecuta el análisis y genera reporte HTML completo. |
| `--lang {en,es}` | Define el idioma de salida. Por defecto: `en`. |
| `--choose-lang` | Solicita el idioma de forma interactiva. |
| `--method {GET,POST,HEAD,TRACE,PUT}` | Define el método HTTP usado en la solicitud. Por defecto: `GET`. |
| `--cookie` | Envía una cookie personalizada. Útil para pruebas autenticadas. |
| `-a`, `--authorization` | Envía un valor para el header `Authorization`. |
| `-H`, `--header` | Agrega cabeceras personalizadas. Se puede usar varias veces. |
| `--proxy` | Define proxy HTTP/HTTPS, por ejemplo Burp Suite o OWASP ZAP. |
| `-k`, `--insecure` | No valida el certificado TLS del objetivo. |
| `--timeout` | Timeout en segundos. Por defecto: `10`. |
| `--no-redirect` | No sigue redirecciones HTTP. |
| `--live-delay` | Delay de impresión progresiva en consola. Por defecto: `0.08`. |
| `--html` | Ruta personalizada para guardar reporte HTML. |
| `--no-browser` | Genera HTML sin abrir el navegador. |
| `--json` | Guarda resultados en JSON. |
| `--csv` | Guarda hallazgos en CSV. |
| `--no-color` | Desactiva colores ANSI. |
| `--debug-errors` | Muestra detalles técnicos ante errores de conexión o TLS. |
| `--gui` | Abre la interfaz gráfica con logs integrados. |
| `-v`, `--version` | Muestra la versión de la herramienta. |

---

## Ejemplos de uso para pentesting

### 1. Análisis rápido de cabeceras

```bash
./PVGUARD_SecurityHeaders https://target.com --console --lang es
```

### 2. Reporte profesional para evidencia

```bash
./PVGUARD_SecurityHeaders https://target.com --report --lang es --html pvguard_target.html
```

### 3. Prueba mediante Burp Suite

```bash
./PVGUARD_SecurityHeaders https://target.com --report --proxy http://127.0.0.1:8080 -k
```

### 4. Evaluación autenticada con cookie

```bash
./PVGUARD_SecurityHeaders https://target.com/dashboard --console --cookie "SESSIONID=abc123; csrftoken=xyz"
```

### 5. Evaluación con token Bearer

```bash
./PVGUARD_SecurityHeaders https://target.com/api/profile \
  --console \
  -a "Bearer eyJhbGciOi..."
```

### 6. Enviar cabeceras personalizadas

```bash
./PVGUARD_SecurityHeaders https://target.com \
  --report \
  -H "X-Forwarded-For: 127.0.0.1" \
  -H "X-Test: PVGUARD"
```

### 7. Revisar respuesta con método HEAD

```bash
./PVGUARD_SecurityHeaders https://target.com --method HEAD --console
```

### 8. Revisar exposición de TRACE

```bash
./PVGUARD_SecurityHeaders https://target.com --method TRACE --console
```

### 9. No seguir redirecciones

```bash
./PVGUARD_SecurityHeaders http://target.com --console --no-redirect
```

### 10. Exportar resultados para reporte o pipeline

```bash
./PVGUARD_SecurityHeaders https://target.com \
  --report \
  --json pvguard_result.json \
  --csv pvguard_findings.csv \
  --html pvguard_report.html \
  --no-browser
```

---

## Interpretación de resultados

PVGUARD clasifica los hallazgos usando estados técnicos para facilitar la priorización durante una auditoría.

| Estado | Significado |
|---|---|
| `OK` | La cabecera está presente y tiene una configuración aceptable. |
| `HIGH` | Falta una cabecera crítica o existe una configuración de alto riesgo. |
| `WARNING` | La cabecera existe, pero requiere endurecimiento o revisión. |
| `INFO` | Hallazgo informativo o dependiente del contexto de la aplicación. |
| `DEPRECATED` | Cabecera obsoleta o no recomendada para navegadores modernos. |
| `N/A` | No aplica en el contexto evaluado. Por ejemplo, HSTS sobre HTTP sin TLS. |

Ejemplos de hallazgos relevantes:

- `Strict-Transport-Security` ausente en HTTPS puede indicar riesgo de downgrade o SSL stripping.
- `Content-Security-Policy` con `unsafe-inline` o `unsafe-eval` requiere revisión por exposición a XSS.
- `X-Frame-Options` ausente puede aumentar riesgo de clickjacking si no existe `frame-ancestors` en CSP.
- `Access-Control-Allow-Origin: *` junto con credenciales representa configuración CORS insegura.
- `Server` o `X-Powered-By` pueden divulgar tecnología o versiones internas.
- `X-XSS-Protection` es una cabecera deprecated; debe preferirse CSP moderna.

---

## Reportes generados

### HTML

El reporte HTML contiene:

- Información del objetivo analizado.
- URL efectiva tras redirecciones.
- Código HTTP recibido.
- Método usado.
- Fecha del análisis.
- Tabla de hallazgos con estado, cabecera, valor, riesgo y recomendación.
- Cabeceras HTTP recibidas.
- Información de redirecciones HTTP/HTTPS cuando aplique.

### JSON

El JSON permite integrar resultados con otras herramientas, pipelines o reportes automatizados.

```bash
./PVGUARD_SecurityHeaders https://target.com --json result.json --no-browser --report
```

### CSV

El CSV es útil para matrices de hallazgos, anexos técnicos o reportes ejecutivos.

```bash
./PVGUARD_SecurityHeaders https://target.com --csv findings.csv --console
```

---

## Flujo recomendado en auditorías

1. Ejecutar un análisis inicial sin autenticación.
2. Validar redirecciones HTTP a HTTPS.
3. Revisar cabeceras principales de seguridad.
4. Repetir la prueba autenticada con cookies o token si existen zonas privadas.
5. Enviar el tráfico por Burp Suite u OWASP ZAP para correlacionar respuestas.
6. Exportar HTML para evidencia y CSV/JSON para trazabilidad.
7. Validar manualmente las recomendaciones antes de reportar severidad final.

Ejemplo de flujo completo:

```bash
./PVGUARD_SecurityHeaders https://target.com \
  --report \
  --lang es \
  --proxy http://127.0.0.1:8080 \
  -k \
  --json target_headers.json \
  --csv target_headers.csv \
  --html target_headers.html
```

---

## Limitaciones

- La herramienta evalúa cabeceras HTTP observadas en la respuesta del endpoint consultado.
- No reemplaza una revisión manual de arquitectura, configuración de reverse proxy, CDN o WAF.
- Algunos headers pueden ser válidos o no aplicables según el tipo de recurso, endpoint o necesidad funcional.
- La severidad final debe ajustarse según contexto, exposición, criticidad del activo y alcance del pentest.
- Para aplicaciones con autenticación, se recomienda usar `--cookie`, `--authorization` o `-H` para analizar respuestas internas reales.

---

## Buenas prácticas de uso

- Ejecutar únicamente sobre activos propios o autorizados.
- Usar `--proxy` para mantener evidencia en Burp Suite/ZAP.
- Usar `--no-browser` en servidores o entornos CI/CD.
- Usar `--debug-errors` solo cuando sea necesario, ya que puede exponer detalles técnicos en consola.
- Validar manualmente CSP, CORS y políticas cross-origin antes de cerrar un hallazgo.
- No reportar cabeceras informativas como vulnerabilidades críticas sin contexto técnico.

---

## Autor

**Created by:** Gh0s7m4n  
**Blog:** https://pvguard.blogspot.com/  
**GitHub:** https://github.com/joepm21

---

## Disclaimer

PVGUARD Security Headers Checker fue creado con fines educativos, defensivos y de auditoría autorizada. El uso de esta herramienta contra sistemas sin permiso explícito puede ser ilegal. El autor no se responsabiliza por usos indebidos.

