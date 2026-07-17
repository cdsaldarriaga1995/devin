# 🛡️ kali-burp-mcp-bridge

<div align="center">

### *El servidor MCP definitivo para pruebas de penetración impulsadas por IA*

[![Python 3.10+](https://img.shields.io/badge/Python-3.10%2B-blue?style=for-the-badge&logo=python&logoColor=white)](https://python.org)
[![MCP Protocol](https://img.shields.io/badge/MCP-v1.2%2B-purple?style=for-the-badge&logo=anthropic&logoColor=white)](https://modelcontextprotocol.io)
[![Burp Suite](https://img.shields.io/badge/Burp%20Suite-Compatible-orange?style=for-the-badge&logo=portswigger&logoColor=white)](https://portswigger.net)
[![Kali Linux](https://img.shields.io/badge/Kali%20Linux-Ready-red?style=for-the-badge&logo=kalilinux&logoColor=white)](https://kali.org)
[![License: MIT](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)](LICENSE)
[![Stars](https://img.shields.io/github/stars/hyperps/kali-burp-mcp-bridge?style=for-the-badge&color=yellow)](https://github.com/hyperps/kali-burp-mcp-bridge/stargazers)

**Conecta Claude, ChatGPT, Cursor y cualquier IA compatible con MCP a todo tu conjunto de herramientas de seguridad de Kali Linux + Burp Suite.**

Ejecuta `nmap`, `sqlmap`, `metasploit`, `hydra`, `nuclei` y más de 60 herramientas, todo desde un único mensaje de chat.

[🚀 Inicio rápido](#-inicio-rápido) · [🔧 Todas las herramientas](#-herramientas-disponibles) · [🤖 Configuración de IA](#-conecta-tu-ia) · [📖 Ejemplos](#-ejemplos-de-uso) · [🙏 Créditos](#-créditos)

</div>

---

> ⚠️ **Aviso legal:** Esta herramienta está destinada **únicamente a pruebas de penetración autorizadas y a la investigación de seguridad**. Obtén siempre permiso explícito y por escrito antes de probar cualquier sistema que no sea de tu propiedad. El uso indebido de esta herramienta es ilegal y poco ético. Los autores no asumen ninguna responsabilidad por el uso no autorizado.
#### Este proyecto no está diseñado para exponerse públicamente sin autenticación. El servidor kali-burp-mcp-bridge está pensado para ejecutarse localmente o detrás de un límite de confianza (por ejemplo, un entorno de laboratorio interno, una VPN o un proxy inverso protegido), y debe estar protegido por un token de autenticación o un mecanismo equivalente de control de acceso durante el despliegue.
#### Las funciones destacadas (integración con Metasploit, wrappers de CLI, herramientas basadas en archivos, etc.) están diseñadas intencionadamente para entornos controlados de seguridad ofensiva en los que el operador ya es de confianza y está autorizado. Este bridge actúa como una capa fina de ejecución entre MCP y las herramientas de seguridad locales, no como un servicio API multiinquilino reforzado.
---

## 📌 ¿Qué es esto?

`kali-burp-mcp-bridge` (punto de entrada: **`server.py`**) es un **servidor del Model Context Protocol (MCP)** que proporciona a cualquier asistente de IA —Claude, ChatGPT, Cursor y otros— acceso directo y en tiempo real a tu conjunto de herramientas de seguridad de Kali Linux y a la API REST de Burp Suite.

Integra tres capas potentes en una única interfaz conversacional:

```
You ──► AI Chat ──► server.py (MCP Server) ──► 🔴 Burp Suite REST API
                                           ├──► 🟡 Kali Linux CLI Tools
                                           └──► 🟢 Pure Python Security Modules
```

En lugar de alternar entre terminales y herramientas con interfaz gráfica, solo tienes que describir lo que quieres:

> *«Escanea 10.10.10.5 en busca de puertos abiertos, identifica los servicios y comprueba los CVE conocidos»*

Y tu asistente de IA llamará automáticamente a `nmap_scan`, `whatweb_scan` y `search_cve_by_keyword` por ti.

---

## 🏗️ Arquitectura

```
╔══════════════════════════════════════════════════════════════╗
║           kali-burp-mcp-bridge  v3.0  (server.py)           ║
╠══════════════════════════════════════════════════════════════╣
║  🔴 Layer 1 — Burp Suite REST API  (port 9876)              ║
║     Proxy history · Active/passive scanner · Repeater        ║
║     Intruder · Collaborator OOB · Encoding · Config          ║
╠══════════════════════════════════════════════════════════════╣
║  🟡 Layer 2 — Kali Linux CLI Tools                          ║
║     nmap · masscan · nikto · gobuster · ffuf · dirb          ║
║     sqlmap · hydra · john · hashcat · metasploit             ║
║     whatweb · wpscan · nuclei · enum4linux · smbclient       ║
║     subfinder · amass · fierce · dnsx · sslscan              ║
║     curl · wget · netcat · openssl · whois · dig             ║
╠══════════════════════════════════════════════════════════════╣
║  🟢 Layer 3 — Pure Python Security Modules                  ║
║     JWT decode/forge/brute · CORS · HTTP smuggling           ║
║     SSRF probe · Cache poisoning · OAuth analysis            ║
║     GraphQL introspect · Session entropy · Param mining      ║
║     CVE lookup · GitHub secret scan · Exploit templates      ║
╚══════════════════════════════════════════════════════════════╝

  MCP Transport:  stdio (default)  |  SSE port 8082 (--transport sse)
  Burp REST API:  http://127.0.0.1:9876  (via BurpAI extension)
```

---

## 🚀 Inicio rápido

### Paso 1 — Clona el repositorio

```bash
git clone https://github.com/yourusername/kali-burp-mcp-bridge.git
cd kali-burp-mcp-bridge
```

### Paso 2 — Instala las dependencias de Python

El script usa metadatos de dependencias PEP 723 insertados, por lo que puedes utilizar `uv` para configurarlo sin configuración adicional:

```bash
# Option A — using uv (recommended, auto-installs all deps)
pip install uv
uv run server.py

# Option B — using pip manually
pip install "requests>=2,<3" "mcp>=1.2.0,<2" "flask>=3,<4" "websocket-client>=1.6"
python3 server.py
```

### Paso 3 — Instala las herramientas de seguridad de Kali

```bash
sudo apt-get update && sudo apt-get install -y \
  nmap masscan nikto gobuster dirb ffuf sqlmap \
  hydra john hashcat whatweb wpscan \
  enum4linux smbclient netcat-openbsd \
  curl wget whois dnsutils sslscan \
  amass subfinder fierce \
  metasploit-framework

# Wordlists
sudo apt-get install -y wordlists seclists
sudo gunzip /usr/share/wordlists/rockyou.txt.gz 2>/dev/null || true

# Go-based tools (nuclei, dnsx, ffuf, gobuster latest)
go install github.com/projectdiscovery/nuclei/v3/cmd/nuclei@latest
go install github.com/projectdiscovery/dnsx/cmd/dnsx@latest
go install github.com/ffuf/ffuf/v2@latest
```

> También puedes instalar cualquier herramienta que falte en cualquier momento pidiéndoselo a tu IA: *«Instala nuclei»*; la función `install_tool` gestiona apt, pip y go automáticamente.

### Paso 4 — Conecta tu IA

Consulta la guía completa de conexión más abajo.

---

## 🤖 Conecta tu IA

### 🟣 Claude (Anthropic) — Aplicación Claude Desktop

Claude admite MCP de forma nativa mediante la aplicación Desktop. Esta es la integración más sencilla y potente.

**Localiza tu archivo de configuración:**

| Sistema operativo | Ruta |
|----|------|
| macOS | `~/Library/Application Support/Claude/claude_desktop_config.json` |
| Windows | `%APPDATA%\Claude\claude_desktop_config.json` |
| Linux | `~/.config/Claude/claude_desktop_config.json` |

**Añade esto a tu configuración:**

```json
{
  "mcpServers": {
    "kali-burp": {
      "command": "python3",
      "args": ["/full/path/to/kali-burp-mcp-bridge/server.py"],
      "env": {
        "BURP_URL": "http://127.0.0.1:9876",
        "BURP_API_KEY": "",
        "TOOL_TIMEOUT": "120"
      }
    }
  }
}
```

Reinicia Claude Desktop. Verás el icono de herramientas 🔧 en la interfaz de chat, lo que confirma que el servidor MCP está activo.

**Para una máquina Kali remota, utiliza el modo SSE:**

```bash
# On your Kali machine
python3 server.py --transport sse --mcp-host 0.0.0.0 --mcp-port 8082
```

```json
{
  "mcpServers": {
    "kali-burp-remote": {
      "url": "http://YOUR_KALI_IP:8082/sse"
    }
  }
}
```

---

### 🟢 Claude.ai (Interfaz web)

Claude.ai admite MCP mediante conexiones SSE remotas.

```bash
# Start SSE server on your machine
python3 server.py --transport sse --mcp-host 0.0.0.0 --mcp-port 8082
```

En la configuración de Claude.ai, ve a **Integraciones → Añadir servidor MCP** e introduce:

```
http://YOUR_IP:8082/sse
```

> Para que claude.ai pueda acceder a tu servidor a través de Internet, expón el puerto o utiliza un túnel:
> ```bash
> ngrok http 8082
> # Then use the ngrok HTTPS URL in claude.ai
> ```

---

### 🟡 ChatGPT / OpenAI — Agents SDK

ChatGPT se conecta mediante el transporte SSE utilizando el OpenAI Agents SDK.

```bash
# Start SSE server first
python3 server.py --transport sse --mcp-port 8082
```

```python
# pip install openai-agents
from agents import Agent, MCPServerSse
import asyncio

async def main():
    async with MCPServerSse(
        name="kali-burp",
        params={"url": "http://127.0.0.1:8082/sse"},
    ) as mcp_server:
        agent = Agent(
            name="PenTest Agent",
            instructions="You are an expert penetration tester. Use the available tools to help test targets.",
            mcp_servers=[mcp_server],
        )
        result = await agent.run("Scan 10.10.10.5 for open ports and identify services")
        print(result.final_output)

asyncio.run(main())
```

---

### 🔵 Cursor IDE

Añade lo siguiente a `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "kali-burp": {
      "command": "python3",
      "args": ["/full/path/to/server.py"],
      "env": {
        "BURP_URL": "http://127.0.0.1:9876",
        "TOOL_TIMEOUT": "120"
      }
    }
  }
}
```

Reinicia Cursor. Las herramientas aparecerán automáticamente en el panel de chat de IA.

---

### 🟠 Windsurf (Codeium)

Añade lo siguiente a tu archivo de configuración MCP de Windsurf:

```json
{
  "mcp": {
    "servers": {
      "kali-burp": {
        "command": "python3",
        "args": ["/full/path/to/server.py"],
        "env": {
          "BURP_URL": "http://127.0.0.1:9876"
        }
      }
    }
  }
}
```

---

### ⚪ Cualquier cliente compatible con MCP (SSE genérico)

```bash
# Start in SSE mode
python3 server.py --transport sse --mcp-host 0.0.0.0 --mcp-port 8082

# Connect any MCP client to:
# http://YOUR_HOST:8082/sse
```

Esto funciona con adaptadores MCP de LangChain, CrewAI, AutoGen, LlamaIndex y cualquier framework compatible con el protocolo de transporte SSE de MCP.

---

## 🔧 Herramientas disponibles

### 🌐 Escaneo de red

| Herramienta | Descripción |
|------|-------------|
| `nmap_scan` | Escaneo de puertos y servicios con flags personalizados (por ejemplo, `-sV -T4 --open`) |
| `nmap_vuln_scan` | Ejecuta todos los scripts NSE de vulnerabilidades de Nmap contra un objetivo |
| `nmap_os_detect` | Identificación del sistema operativo con `-O -sV -A` |
| `nmap_full_port_scan` | Escanea los 65535 puertos (lento, pero exhaustivo) |
| `nmap_udp_scan` | Escaneo UDP de los N puertos principales (requiere root) |
| `nmap_script` | Ejecuta cualquier script NSE específico (por ejemplo, `smb-vuln-ms17-010`) |
| `masscan_fast` | Barrido de puertos a nivel de paquetes ultrarrápido y con tasa configurable |

### 🕸️ Escaneo web y fuzzing

| Herramienta | Descripción |
|------|-------------|
| `nikto_scan` | Escaneo de vulnerabilidades del servidor web por host y puerto |
| `nikto_scan_url` | Escaneo con Nikto utilizando una URL completa (detecta HTTPS automáticamente) |
| `gobuster_dir` | Fuerza bruta de directorios y archivos con compatibilidad para extensiones |
| `gobuster_dns` | Fuerza bruta de subdominios mediante resolución DNS |
| `gobuster_vhost` | Enumeración de hosts virtuales |
| `dirb_scan` | Escaneo clásico de directorios (alternativa cuando gobuster no está disponible) |
| `ffuf_dir` | Fuzzing rápido de directorios web mediante el marcador FUZZ |
| `ffuf_param` | Fuzzing de parámetros con método HTTP y datos POST personalizados |
| `wpscan` | Scanner de WordPress para usuarios, plugins, temas y timthumbs |
| `nuclei_scan` | Escaneo de vulnerabilidades basado en plantillas (plantillas de la comunidad) |
| `nuclei_scan_list` | Ejecuta Nuclei contra un archivo que contiene varias URL objetivo |
| `whatweb_scan` | Identificación de tecnologías web con agresividad configurable |

### 💉 Inyección SQL

| Herramienta | Descripción |
|------|-------------|
| `sqlmap_scan` | Prueba completa de inyección SQL en una URL |
| `sqlmap_dbs` | Enumera las bases de datos disponibles |
| `sqlmap_tables` | Enumera las tablas de una base de datos específica |
| `sqlmap_dump` | Extrae datos de una base de datos o tabla |
| `sqlmap_os_shell` | Intenta ejecutar comandos del sistema operativo mediante SQLi |
| `sqlmap_request_file` | Prueba a partir de un archivo de solicitud de Burp Suite guardado |

### 🔐 Fuerza bruta y cracking de hashes

| Herramienta | Descripción |
|------|-------------|
| `hydra_ssh` | Fuerza bruta de credenciales SSH |
| `hydra_ftp` | Fuerza bruta de credenciales FTP |
| `hydra_smb` | Fuerza bruta de credenciales SMB |
| `hydra_http_post` | Ataque al formulario de inicio de sesión HTTP POST con cadena de fallo |
| `hydra_service` | Ataque contra servicios genéricos (rdp, smtp, mysql, mssql, telnet, etc.) |
| `john_crack` | John the Ripper con wordlist y reglas opcionales |
| `john_show` | Muestra las contraseñas descifradas anteriormente |
| `hashcat_crack` | Cracking acelerado por GPU (MD5, NTLM, SHA1, bcrypt, etc.) |

### 💣 Explotación — Metasploit

| Herramienta | Descripción |
|------|-------------|
| `msf_exploit` | Ejecuta un exploit con las opciones RHOSTS, LHOST, LPORT y PAYLOAD |
| `msf_generate_payload` | Generación de payloads con msfvenom (exe, elf, apk, ps1, dll, raw) |
| `msf_search` | Busca en la base de datos de módulos de Metasploit por palabra clave |
| `msf_module_info` | Obtiene información detallada sobre un módulo específico |
| `msf_run_resource` | Ejecuta un script de recursos .rc de Metasploit completo y multilínea |

### 🔍 Reconocimiento y OSINT

| Herramienta | Descripción |
|------|-------------|
| `subfinder_enum` | Enumeración pasiva de subdominios mediante fuentes OSINT |
| `amass_enum` | Mapeo activo y pasivo de subdominios |
| `enumerate_subdomains` | Registros de transparencia de certificados de crt.sh más resolución DNS |
| `fierce_dns` | Reconocimiento DNS e intentos de transferencia de zona |
| `dnsx_resolve` | Resolución DNS rápida con registros A, CNAME, MX y NS |
| `whois_lookup` | Datos WHOIS de un dominio o dirección IP |
| `dig_query` | Consulta DNS de cualquier tipo de registro mediante cualquier servidor de nombres |
| `enum4linux` | Enumeración SMB y LDAP de recursos compartidos, usuarios y grupos |
| `smbclient_list_shares` | Enumera los recursos compartidos SMB de un objetivo |
| `smbclient_connect` | Conecta a un recurso compartido SMB y ejecuta comandos |
| `github_repo_secret_scan` | Escanea los commits de un repositorio de GitHub en busca de claves API y secretos expuestos |

### 🔑 JWT y pruebas de autenticación

| Herramienta | Descripción |
|------|-------------|
| `jwt_decode` | Decodifica la cabecera y el payload JWT, y detecta problemas de algoritmo y expiración |
| `jwt_forge_none_alg` | Genera tokens de bypass `alg:none` (4 variantes de mayúsculas/minúsculas) |
| `jwt_brute_secret` | Fuerza bruta del secreto HS256 contra contraseñas comunes o una wordlist |
| `analyze_oauth_flow` | Detecta errores de configuración de OAuth: flujo implícito, ausencia de state y ausencia de PKCE |

### 🕵️ Pruebas avanzadas de vulnerabilidades web

| Herramienta | Descripción |
|------|-------------|
| `test_cors_misconfiguration` | Prueba la política CORS para detectar reflexión del origen y filtración de credenciales |
| `detect_request_smuggling` | Detección de HTTP request smuggling mediante sockets sin procesar (técnicas CL.TE y TE.CL) |
| `test_host_header_injection` | Inyección de la cabecera Host mediante X-Forwarded-Host y Collaborator OOB |
| `detect_cache_poisoning` | Detección de envenenamiento de caché mediante cabeceras no incluidas en la clave |
| `probe_ssrf` | SSRF mediante endpoints de metadatos de AWS/GCP/Azure y Collaborator OOB de Burp |
| `scan_open_redirects` | Escaneo de redirecciones abiertas en parámetros de URL habituales |
| `mine_hidden_parameters` | Descubre parámetros ocultos comparando la longitud y el estado de las respuestas |
| `graphql_introspect` | Extrae el esquema GraphQL mediante una consulta de introspección |
| `discover_api_endpoints_from_js` | Extrae rutas de API, secretos y URL externas de archivos JavaScript |
| `analyze_session_entropy` | Análisis de entropía de tokens y detección de patrones secuenciales |

### 🎯 Payloads y plantillas de exploits

| Herramienta | Descripción |
|------|-------------|
| `get_payloads` | Devuelve payloads para: sqli, xss, ssti, ssrf, xxe, lfi, cmd_injection, open_redirect |
| `get_all_payload_types` | Enumera todas las categorías de payload disponibles |
| `generate_exploit_template` | Genera un script de exploit Python listo para ejecutar para un tipo de vulnerabilidad |
| `auto_exploit_from_scan` | Obtiene los hallazgos del scanner de Burp y genera automáticamente scripts de exploit para cada incidencia |
| `build_intruder_attack` | Configura Burp Intruder con payloads generados para un tipo de ataque determinado |

### 🔬 Inteligencia sobre CVE

| Herramienta | Descripción |
|------|-------------|
| `lookup_cve` | Detalles completos del CVE desde NVD, incluida la puntuación CVSS v3 y las referencias |
| `search_cve_by_keyword` | Búsqueda de CVE por palabras clave en la base de datos NVD |
| `tech_to_cves` | Relaciona una tecnología y versión detectadas con los CVE conocidos |
| `github_advisory_search` | Busca avisos de seguridad de GitHub por ecosistema, severidad o palabra clave |

### 🔒 Pruebas SSL y TLS

| Herramienta | Descripción |
|------|-------------|
| `sslscan` | Prueba cifrados, protocolos y vulnerabilidades conocidas de SSL/TLS |
| `testssl` | Pruebas exhaustivas de TLS con testssl.sh |
| `openssl_check_cert` | Muestra la cadena completa del certificado y sus detalles |

### 🌊 API REST de Burp Suite

| Categoría | Herramientas |
|----------|-------|
| **Proxy** | `get_proxy_http_history`, `get_proxy_http_history_regex`, `get_proxy_websocket_history`, `get_proxy_websocket_history_regex`, `set_proxy_intercept_state` |
| **Scanner** | `burp_active_scan`, `burp_passive_scan`, `burp_get_scan_status`, `burp_cancel_scan`, `get_scanner_issues` |
| **Repeater** | `send_http1_request`, `send_http2_request`, `create_repeater_tab`, `replay_and_diff` |
| **Intruder** | `send_to_intruder`, `build_intruder_attack` |
| **Collaborator** | `generate_collaborator_payload`, `get_collaborator_interactions` |
| **Encoding** | `base64_encode`, `base64_decode`, `url_encode`, `url_decode`, `generate_random_string` |
| **Config** | `output_project_options`, `output_user_options`, `set_project_options`, `set_user_options`, `set_task_execution_engine_state`, `get_active_editor_contents`, `set_active_editor_contents` |

### ⚙️ Herramientas del sistema y del flujo de trabajo

| Herramienta | Descripción |
|------|-------------|
| `recon_target` | Pipeline completo de reconocimiento: nmap + whatweb + whois + subdominios + indicios de CVE |
| `full_web_audit` | Auditoría web completa: nikto + gobuster + CORS + extracción de parámetros + análisis de JS |
| `chain_vulnerabilities_into_narrative` | Construye narrativas estructuradas de cadenas de ataque a partir de hallazgos del scanner de Burp |
| `list_all_tools` | Enumera todas las herramientas disponibles organizadas por categoría |
| `list_installed_tools` | Muestra qué herramientas de Kali están instaladas y cuáles faltan |
| `install_tool` | Instala automáticamente cualquier herramienta que falte mediante apt-get, pip o go install |
| `install_wordlists` | Instala las wordlists rockyou, dirb y SecLists |
| `run_command` | Ejecuta directamente cualquier comando bash arbitrario |
| `curl_request` | Solicitud HTTP con control total sobre método, cabeceras, datos y proxy |
| `wget_download` | Descarga archivos desde una URL |
| `nc_banner_grab` | Obtiene banners de servicios mediante netcat |
| `burp_health_check` | Verifica la conectividad con la API REST de Burp |

### 🤝 Flujo de Pentest Asistido (Validación con Humano en el Bucle)

Una capa de validación que mantiene al pentester como autoridad final: la IA sugiere/reproduce pruebas, BurpIA hace un segundo análisis con LLM y **tú validas manualmente en Repeater antes de reportar nada**. Los hallazgos se centralizan en un registro de sesión y avanzan por los estados `suggested → llm_reviewed → manually_validated → reported`.

| Herramienta | Descripción |
|------|-------------|
| `finding_add` | Registra un hallazgo en el registro central (inicia como `suggested`) |
| `finding_list` | Lista los hallazgos, filtrados por estado o severidad |
| `finding_update_status` | Avanza un hallazgo por el pipeline de validación |
| `finding_report` | Genera un reporte tipo PoC — **solo tras `manually_validated`** |
| `burp_get_filtered_issues` | Filtra los hallazgos del scanner de Burp (severidad/confianza/url) y opcionalmente los importa como findings |
| `send_for_second_analysis` | Fuerza un segundo análisis LLM de BurpIA vía el header `X-BurpIA-AutoAnalyze`, incluso cuando no coincide con los filtros estándar |
| `validation_workflow` | Orquesta el flujo de 3 pasos (sugerir → análisis LLM → checkpoint manual obligatorio); nunca reporta solo |
| `checklist_show` / `checklist_check` / `checklist_reset` | Mantiene el checklist del pentester (basado en OWASP WSTG) durante todo el engagement |
| `workflow_cost_report` | Muestra los contadores de llamadas a Burp/LLM y guía para reducir costos |

> Esta capa integra lo mejor de un flujo de validación Burp + BurpIA + MCP: hallazgos centralizados, filtrado de hallazgos del scanner, un segundo análisis LLM forzado y un checkpoint de validación humana obligatorio — sobre todo el toolset de Kali + Burp.

---

## 📖 Ejemplos de uso

Una vez conectado `server.py` a tu asistente de IA, solo tienes que hablar con naturalidad:

### Reconocimiento

```
"Run a full port scan on 10.10.10.5 and identify all running services"
"Enumerate subdomains for example.com and resolve their IP addresses"
"Do a WHOIS lookup and a service scan on 192.168.1.0/24"
"Find subdomains of target.com using both subfinder and amass"
"Check what web technologies are running on https://target.com"
```

### Pruebas de aplicaciones web

```
"Test https://target.com for SQL injection vulnerabilities"
"Run a full web audit on https://target.com"
"Check for CORS misconfigurations on https://api.target.com"
"Fuzz for hidden directories and files on https://target.com"
"Scan https://target.com/graphql for introspection and exposed types"
"Find hidden parameters on https://target.com/search"
"Extract API endpoints and secrets from https://target.com/app.bundle.js"
```

### JWT y autenticación

```
"Decode this JWT and check for vulnerabilities: eyJhbGciOiJIUzI1NiJ9..."
"Try an alg:none signature bypass on this token"
"Brute-force the HS256 secret on this JWT using rockyou.txt"
"Analyze this OAuth authorization URL for security issues"
```

### Explotación

```
"Generate a Windows x64 reverse shell payload for 192.168.1.10 port 4444"
"Run the EternalBlue exploit against 10.10.10.4"
"Use sqlmap to dump all databases from https://target.com/vuln?id=1"
"Brute-force SSH on 10.10.10.5 using rockyou.txt"
"Generate a Python cookie stealer exploit for the XSS at https://target.com/search?q="
"Search Metasploit for Apache Struts exploit modules"
```

### Inteligencia

```
"What CVEs affect Apache 2.4.49?"
"Look up CVE-2021-44228 Log4Shell and explain the risk"
"Scan the GitHub repo owner/repo for exposed API keys in recent commits"
"Map all known CVEs for nginx 1.14.0"
```

### Automatización de Burp Suite

```
"Get the last 100 requests from Burp proxy history"
"Launch an active scan against https://target.com"
"Show me all high and critical issues from the Burp scanner"
"Generate a Burp Collaborator payload for out-of-band testing"
"Send this HTTP request to Burp Repeater and show me the response"
"Build an Intruder attack with XSS payloads against this request template"
```

---

## ⚙️ Referencia de configuración

### Variables de entorno

| Variable | Valor por defecto | Descripción |
|----------|---------|-------------|
| `BURP_URL` | `http://127.0.0.1:9876` | Endpoint de la API REST de Burp Suite |
| `BURP_API_KEY` | *(vacío)* | Clave API para la autenticación de Burp, si es necesaria |
| `TOOL_TIMEOUT` | `120` | Tiempo máximo de ejecución, en segundos, para las herramientas CLI |
| `TIMEOUT` | `30` | Tiempo de espera de las solicitudes HTTP, en segundos |

### Opciones de línea de comandos

```
python3 server.py [OPTIONS]

  --burp-url TEXT        Burp REST API URL       (default: http://127.0.0.1:9876)
  --api-key TEXT         Burp API key            (default: empty)
  --transport TEXT       stdio or sse            (default: stdio)
  --mcp-host TEXT        SSE server bind host    (default: 127.0.0.1)
  --mcp-port INT         SSE server port         (default: 8082)
  --timeout INT          HTTP request timeout    (default: 30)
  --tool-timeout INT     CLI tool timeout        (default: 120)
  --debug                Enable debug logging
```

### Comandos habituales de ejecución

```bash
# Default stdio mode — for Claude Desktop, Cursor, local AI tools
python3 server.py

# SSE mode — for claude.ai web, ChatGPT Agents SDK, remote clients
python3 server.py --transport sse

# SSE mode, publicly accessible, custom port
python3 server.py --transport sse --mcp-host 0.0.0.0 --mcp-port 8082

# With Burp API key and debug output
python3 server.py --api-key YOUR_KEY --debug

# Full custom configuration
python3 server.py \
  --burp-url http://127.0.0.1:9876 \
  --api-key YOUR_KEY \
  --transport sse \
  --mcp-host 0.0.0.0 \
  --mcp-port 8082 \
  --tool-timeout 300 \
  --debug
```

---

## 🔌 Configuración de Burp Suite

Para activar la capa de API REST de Burp Suite:

1. Instala **Burp Suite Professional** o **Community Edition** desde [portswigger.net/burp](https://portswigger.net/burp)
2. Abre Burp Suite y ve a **BApp Store**
3. Instala la extensión **BurpAI** (busca "BurpAI" o "Burp MCP")
4. La extensión inicia automáticamente un servidor REST en `http://127.0.0.1:9876`
5. Ejecuta `server.py`; se conectará automáticamente a Burp

> Sin Burp Suite, todas las herramientas CLI de Kali y los módulos de seguridad de Python puro funcionan perfectamente. La integración con Burp es opcional, pero habilita los resultados del scanner, el historial del proxy, las interacciones OOB de Collaborator y la automatización de Intruder.

---


---

## 🔐 Consideraciones de seguridad

- Ejecuta esta herramienta únicamente contra sistemas que **sean de tu propiedad o para los que tengas autorización explícita y por escrito**
- La herramienta `run_command` proporciona acceso completo al shell; ejecútala en una VM o contenedor para aislarla
- Vincula el servidor SSE a `127.0.0.1` salvo que dispongas de controles de acceso adecuados a nivel de red
- Mantén `BURP_API_KEY` en secreto y rótala periódicamente
- Los archivos de salida y los logs de las herramientas pueden contener datos sensibles; gestiónalos y almacénalos de forma segura
- Establece valores prudentes para `TOOL_TIMEOUT` para evitar procesos de larga duración o descontrolados

---

## 🙏 Créditos

Este proyecto se apoya en el trabajo de grandes referentes de la comunidad de seguridad.

### 👤 Daniel S. — PortSwigger

Por iniciar y publicar la investigación fundamental sobre seguridad de aplicaciones web en la que se basa este bridge. Los módulos de pruebas de CORS, detección de HTTP request smuggling, envenenamiento de caché web, inyección de la cabecera Host y errores de configuración de OAuth están inspirados directamente en la investigación de primer nivel de PortSwigger. La [Web Security Academy](https://portswigger.net/web-security) es el mejor recurso gratuito de formación en seguridad del mundo, y Burp Suite Pro es la herramienta estándar del sector que hace posible la capa de integración con la API REST.

### 👤 Daniel Allen

Por sus contribuciones a los flujos de trabajo de pruebas de penetración del mundo real (PortSwigger MCP - Extension)

### 🛠️ Herramientas de código abierto integradas

| Herramienta | Autor / Proyecto |
|------|-----------------|
| [Nmap](https://nmap.org) | Gordon Lyon (Fyodor) |
| [Metasploit Framework](https://metasploit.com) | Rapid7 |
| [SQLMap](https://sqlmap.org) | Bernardo Damele & Miroslav Stampar |
| [Nuclei](https://github.com/projectdiscovery/nuclei) | ProjectDiscovery |
| [Subfinder](https://github.com/projectdiscovery/subfinder) | ProjectDiscovery |
| [dnsx](https://github.com/projectdiscovery/dnsx) | ProjectDiscovery |
| [Gobuster](https://github.com/OJ/gobuster) | OJ Reeves |
| [ffuf](https://github.com/ffuf/ffuf) | Joona Hoikkala |
| [Amass](https://github.com/owasp-amass/amass) | OWASP |
| [Hydra](https://github.com/vanhauser-thc/thc-hydra) | Van Hauser / THC |
| [Nikto](https://github.com/sullo/nikto) | Chris Sullo |
| [Hashcat](https://hashcat.net) | Jens Steube |
| [John the Ripper](https://www.openwall.com/john/) | Openwall |
| [WhatWeb](https://github.com/urbanadventurer/WhatWeb) | Andrew Horton |
| [WPScan](https://wpscan.com) | WPScan Team |
| [Fierce](https://github.com/mschwager/fierce) | Mark Schwager |
| [SSLScan](https://github.com/rbsec/sslscan) | rbsec |
| [FastMCP](https://github.com/jlowin/fastmcp) | MCP Community |
| [Burp Suite](https://portswigger.net/burp) | PortSwigger |

---

## 📄 Licencia

Licencia MIT — consulta [LICENSE](LICENSE) para conocer los términos completos.

---

<div align="center">

**Creado con ❤️ para la comunidad de investigación de seguridad**

*Solo para pruebas autorizadas. Practica el hacking de forma responsable.*

⭐ **Da una estrella a este repositorio** si te ha ayudado.

</div>
