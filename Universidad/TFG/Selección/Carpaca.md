---
tags:
  - tfg
---
**Plataforma DevSecOps para seguridad de la cadena de suministro.** El producto sería una aplicación web más CLI que recibe un repositorio, construye su SBOM, identifica dependencias y vulnerabilidades, registra la procedencia de artefactos y genera un expediente técnico reproducible. El cliente objetivo serían pequeñas empresas de software, startups, departamentos de desarrollo y fabricantes de dispositivos conectados que no dispongan de grandes equipos de Product Security.

Su oportunidad es particularmente buena en 2026 porque las obligaciones de notificación del CRA comenzaron el 11 de septiembre; SBOM, SLSA, SPDX, CycloneDX y OSV proporcionan piezas abiertas con las que construir el producto.

El objetivo no sería afirmar “cumplimiento legal automático”, sino **automatizar evidencias técnicas y flujo de trabajo**. 

**Stack y requisitos:** Go o Python para el motor de análisis; FastAPI/Go HTTP para API; React/Vue para dashboard; PostgreSQL; Docker; GitHub Actions/GitLab CI; CycloneDX o SPDX; OSV; opcionalmente Sigstore y SLSA para attestations.

**Datos:** seleccionar 20–50 proyectos open source de Python, JavaScript o Java y combinar sus dependencias reales con repositorios preparados con vulnerabilidades conocidas; OSV permite consultar por paquete y versión.  

**Esfuerzo:** 380–460 h. 

**Riesgos:** alcance regulatorio excesivo y falsos positivos; se mitigan limitando el MVP a uno o dos ecosistemas y presentándolo como asistente técnico, no como certificador.

**Entregables:** CLI/scanner, backend, dashboard, SBOM, integración CI, informe de vulnerabilidades, exportación de evidencias y conjunto de pruebas reproducible.

**Métricas:** >95 % de dependencias del conjunto de prueba identificadas, recall medido sobre vulnerabilidades sembradas, tiempo de análisis, tasa de falsos positivos y porcentaje de evidencias generadas automáticamente.

**Coste:** aproximadamente **0–80 €**, salvo hosting opcional.

Si hablamos específicamente de [OWASP Dependency-Track](https://dependencytrack.org/?utm_source=chatgpt.com), **no intentaría competir haciendo un Dependency-Track mejor**. La diferenciación de SupplyShield debería estar una capa por encima.

### La diferencia clave

**Dependency-Track = análisis y gestión del riesgo de componentes.**  
**SupplyShield = orquestación del proceso de seguridad y generación de evidencias CRA.**

Podrías plantearlo así:

||Dependency-Track|**SupplyShield**|
|---|---|---|
|SBOM|✅ Gestiona/analiza|✅ Genera + gestiona|
|CVE|✅|✅|
|VEX|✅|✅|
|Políticas|✅|✅|
|Dependencias|✅|✅|
|Sigstore / provenance|Parcial|**✅ pieza central**|
|SLSA|Parcial|**✅**|
|Orquestar herramientas externas|Limitado|**✅ objetivo principal**|
|Evidencias CRA|❌ No es su foco|**✅**|
|Expediente técnico|❌|**✅**|
|Workflow CRA|❌|**✅**|
|Notificación vulnerabilidades CRA|❌ específica|**✅**|
|Auditoría reproducible|Parcial|**✅**|

### Ejemplo práctico

Una empresa conecta un repositorio de GitHub.

**Dependency-Track:**

```
SBOM
 ↓
Dependency-Track
 ↓
Componentes
Vulnerabilidades
Riesgos
Políticas
```

**SupplyShield:**

```
             GitHub
                │
                ▼
          SupplyShield
                │
 ┌──────────────┼──────────────┐
 ▼              ▼              ▼
Syft           OSV        OpenSSF Scorecard
 │              │              │
 └──────────────┼──────────────┘
                ▼
          Policy Engine
                │
       ┌────────┴────────┐
       ▼                 ▼
    Sigstore           SLSA
       │                 │
       └────────┬────────┘
                ▼
        CRA Evidence Store
                │
        ┌───────┼────────┐
        ▼       ▼        ▼
      SBOM     VEX    Evidencias
                        │
                        ▼
               Expediente CRA
```

SupplyShield **no sustituye necesariamente a Dependency-Track**. Incluso podrías integrarlo como otro motor:

```
SupplyShield
    │
    ├── Dependency-Track
    ├── Trivy
    ├── Syft
    ├── OSV
    ├── Sigstore
    └── OpenSSF Scorecard
```

Esto cambia bastante la propuesta: **SupplyShield sería el plano de control (_control plane_) de la seguridad de la cadena de suministro**.

### Donde veo la diferenciación más potente

No vendería:

> "Una herramienta para encontrar vulnerabilidades en dependencias."

Eso ya está extremadamente competido.

Vendería:

> **"Una plataforma que convierte automáticamente el pipeline de desarrollo de una PYME en evidencias técnicas trazables para el Cyber Resilience Act."**

El flujo podría ser:

**Repositorio → análisis → SBOM → CVEs → VEX → provenance → attestations → políticas → evidencias CRA → expediente técnico**

Y aquí aparece algo interesante para tu TFG: **Dependency-Track podría ser una dependencia de SupplyShield en lugar de un competidor**. Eso te permite construir un producto bastante más ambicioso sin tener que desarrollar scanners, bases de CVEs, analizadores SBOM, etc. desde cero.

Además, arquitectónicamente da muchísimo juego para un TFG de Ingeniería Informática: microservicios, colas, plugins/adapters, CI/CD, seguridad, APIs, PostgreSQL, Docker, firmas criptográficas, SBOM y generación documental.