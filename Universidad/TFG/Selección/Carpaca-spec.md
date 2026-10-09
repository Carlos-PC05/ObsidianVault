# SupplyShield CRA

## Plataforma DevSecOps para seguridad de la cadena de suministro y automatización de evidencias técnicas del Cyber Resilience Act

---

# 1. Resumen de la propuesta

El proyecto propone el diseño e implementación de **SupplyShield**, una plataforma DevSecOps orientada a pequeñas y medianas organizaciones que necesitan mejorar la seguridad de su cadena de suministro software y generar evidencias técnicas trazables de forma automatizada.

La plataforma recibiría como entrada un repositorio Git o un proyecto local y coordinaría herramientas especializadas para producir una visión reproducible del estado de seguridad del software:

```text
Repositorio / commit
        │
        ▼
   SupplyShield
        │
        ├── inventario de componentes
        ├── SBOM
        ├── análisis de vulnerabilidades
        ├── evaluación del repositorio
        ├── VEX / triaje
        ├── provenance
        ├── attestations
        ├── políticas
        └── evidencias
        │
        ▼
 Expediente técnico reproducible
```

El objetivo **no será certificar automáticamente el cumplimiento del Cyber Resilience Act (CRA)** ni sustituir una evaluación jurídica o de conformidad. La propuesta se centrará en algo más concreto y técnicamente defendible:

> **Automatizar la recopilación, normalización, trazabilidad y exportación de evidencias técnicas relacionadas con seguridad de la cadena de suministro, gestión de vulnerabilidades y documentación del producto.**

La idea se diferencia deliberadamente de un escáner de dependencias tradicional. SupplyShield no intentará desarrollar desde cero motores de SCA, bases de datos de vulnerabilidades o sistemas criptográficos completos. En su lugar, actuará como un **plano de control (_control plane_)** que integra herramientas existentes y convierte sus resultados en un flujo coherente, versionado, reproducible y auditable.

El TFG combinaría así:

- Ingeniería de software.
- DevSecOps.
- Seguridad de la cadena de suministro.
- SBOM.
- Gestión de vulnerabilidades.
- CI/CD.
- Firmas y attestations.
- Arquitectura de sistemas.
- APIs.
- Bases de datos.
- Automatización documental.
- Evaluación experimental.

El resultado sería un producto funcional y demostrable, acompañado de una evaluación cuantitativa sobre repositorios reales y escenarios controlados con vulnerabilidades conocidas.

---

# 2. El problema

Actualmente una empresa puede disponer de numerosas herramientas independientes:

```text
Syft                  → SBOM
OSV / Trivy           → vulnerabilidades
Dependency-Track      → gestión de componentes
OpenSSF Scorecard     → postura del repositorio
Sigstore / Cosign     → firmas y attestations
GitHub Actions        → CI/CD
SLSA                  → requisitos de provenance
```

El problema no es únicamente que falten herramientas. El problema es que **la información aparece fragmentada entre ellas**.

Una organización pequeña puede llegar a tener:

```text
sbom.json
scan.json
scorecard.json
attestation.json
workflow.yml
informe.pdf
hoja_manual.xlsx
capturas/
notas/
```

pero seguir sin poder responder fácilmente a preguntas como:

- ¿Qué versión exacta del producto fue analizada?
- ¿Con qué versiones de las herramientas?
- ¿Qué dependencias tenía ese commit?
- ¿Qué vulnerabilidades se detectaron?
- ¿Cuáles fueron evaluadas como no explotables?
- ¿Quién tomó esa decisión?
- ¿Qué artefacto salió del proceso de build?
- ¿Existe provenance verificable para dicho artefacto?
- ¿Qué evidencias técnicas están disponibles?
- ¿Qué cambió entre dos releases?
- ¿Puede repetirse hoy el análisis realizado hace tres meses?
- ¿Qué información sigue faltando y requiere intervención humana?

El problema puede representarse como:

```text
                  Herramientas independientes

      Syft       OSV       Scorecard      Cosign
       │          │            │             │
       ▼          ▼            ▼             ▼
     JSON        JSON         JSON          JSON
       │          │            │             │
       └──────────┴────────────┴─────────────┘
                          │
                          ▼
                 Información fragmentada
                          │
                          ▼
                Trabajo manual adicional
                          │
                          ▼
                  Evidencia difícil de
                 reproducir y mantener
```

SupplyShield intentaría resolver precisamente esta capa de coordinación.

---

# 3. Por qué el proyecto resulta especialmente oportuno en 2026

El contexto de 2026 hace especialmente relevante este tipo de plataforma.

El Reglamento (UE) 2024/2847, **Cyber Resilience Act**, establece requisitos horizontales de ciberseguridad para productos con elementos digitales comercializados en la Unión Europea.

Desde el **11 de septiembre de 2026** han comenzado a aplicarse sus obligaciones de notificación de vulnerabilidades activamente explotadas e incidentes graves de seguridad.

La aplicación general del CRA está prevista para el **11 de diciembre de 2027**.

Además, el Reglamento incluye requisitos directamente relacionados con varios de los artefactos que SupplyShield pretende gestionar. Entre ellos se encuentra la obligación de identificar y documentar vulnerabilidades y componentes y elaborar una **Software Bill of Materials en un formato común y legible por máquina**, cubriendo al menos las dependencias de primer nivel.

La documentación técnica definida por el CRA contempla igualmente información sobre:

- diseño y desarrollo del producto;
- arquitectura y componentes;
- procesos de gestión de vulnerabilidades;
- SBOM;
- evaluación de riesgos de ciberseguridad;
- procesos de producción y monitorización;
- resultados de pruebas;
- soluciones adoptadas para satisfacer los requisitos de ciberseguridad.

Por tanto, existe una oportunidad clara para construir herramientas que reduzcan el coste operativo de mantener esas evidencias.

Sin embargo, esta relación con el CRA debe manejarse con precisión.

SupplyShield **no debería afirmar**:

> «Este producto cumple el CRA.»

Debería afirmar algo similar a:

> «SupplyShield automatiza la generación y conservación de determinadas evidencias técnicas que pueden apoyar los procesos internos asociados al CRA.»

Esta distinción será esencial tanto desde el punto de vista académico como desde el diseño del producto.

---

# 4. Delimitación regulatoria

Una de las mayores amenazas para el TFG sería intentar implementar “el CRA completo”.

Eso convertiría el proyecto en una mezcla demasiado amplia de:

- derecho;
- compliance;
- ciberseguridad;
- gestión documental;
- evaluación de conformidad;
- gestión de incidentes;
- seguridad de producto;
- seguridad de desarrollo.

El proyecto debe limitarse a la **evidencia técnica automatizable**.

SupplyShield podría trabajar con una matriz como la siguiente:

| Área | Evidencia técnica posible | Automatización |
|---|---|---:|
| Inventario de componentes | SBOM | Alta |
| Vulnerabilidades conocidas | Resultados OSV/SCA | Alta |
| Estado de vulnerabilidades | VEX / triaje | Media-Alta |
| Procedencia del artefacto | SLSA provenance | Alta |
| Integridad | firma / attestation | Alta |
| Controles del repositorio | OpenSSF Scorecard | Alta |
| Evidencias de build | workflow + hashes + metadatos | Alta |
| Pruebas ejecutadas | informes CI | Alta |
| Historial de análisis | audit log | Alta |
| Riesgo de ciberseguridad global | evaluación humana | Baja |
| Clasificación jurídica CRA | asesoramiento jurídico | Fuera de alcance |
| Declaración UE de conformidad | responsabilidad fabricante | Fuera de alcance |
| Certificación del producto | organismo/proceso correspondiente | Fuera de alcance |

De esta forma, SupplyShield se presenta como un **asistente técnico de ingeniería**, no como certificador.

---

# 5. Pregunta principal del TFG

Una formulación académica adecuada sería:

> **¿Hasta qué punto puede una plataforma DevSecOps basada en la orquestación de herramientas abiertas automatizar de forma reproducible la generación de evidencias técnicas de seguridad de la cadena de suministro útiles para procesos asociados al Cyber Resilience Act?**

De ella podrían derivar varias preguntas secundarias.

### RQ1 — Cobertura de componentes

¿Con qué precisión puede identificarse el conjunto de dependencias de un proyecto y representarse mediante una SBOM normalizada?

### RQ2 — Detección de vulnerabilidades

¿Qué porcentaje de vulnerabilidades conocidas presentes en un conjunto de prueba puede identificar el pipeline?

### RQ3 — Reproducibilidad

¿Produce el mismo commit resultados equivalentes cuando se ejecuta bajo una configuración y versiones de herramientas fijadas?

### RQ4 — Evidencias automatizables

¿Qué porcentaje del conjunto de evidencias técnicas definido por el proyecto puede generarse sin intervención manual?

### RQ5 — Coste operativo

¿Qué latencia y consumo de recursos introduce la plataforma respecto a ejecutar individualmente las herramientas?

### RQ6 — Integración

¿Puede una arquitectura de orquestación desacoplar los motores concretos de análisis y sustituirlos sin alterar el modelo de evidencia de SupplyShield?

---

# 6. Hipótesis

## H1 — Orquestación

Una plataforma que normalice la salida de diferentes herramientas permitirá generar un expediente técnico más completo y reproducible que la ejecución independiente de dichas herramientas.

## H2 — Cobertura

Para los ecosistemas incluidos en el MVP será posible identificar más del **95 % de las dependencias del conjunto de referencia** cuando exista información suficiente en manifests y lockfiles.

## H3 — Detección

El uso de versiones explícitas y consultas normalizadas a OSV debería permitir alcanzar un recall elevado sobre vulnerabilidades conocidas sembradas en el conjunto experimental.

## H4 — Reproducibilidad

La fijación de:

```text
commit
+
versión de scanner
+
versión de política
+
configuración
```

permitirá reproducir de forma determinista la mayor parte de las evidencias generadas.

## H5 — Automatización documental

Una parte relevante del expediente técnico definido por el proyecto podrá generarse automáticamente a partir de datos ya disponibles en el repositorio y el pipeline CI/CD.

---

# 7. Visión del producto

El producto puede entenderse como una capa situada entre el repositorio y las herramientas especializadas.

```text
                   ┌───────────────────┐
                   │ Repositorio Git   │
                   └─────────┬─────────┘
                             │
                             ▼
                   ┌───────────────────┐
                   │   SupplyShield    │
                   │   Control Plane   │
                   └─────────┬─────────┘
                             │
          ┌──────────────────┼──────────────────┐
          │                  │                  │
          ▼                  ▼                  ▼
      SBOM Engine       Vuln Engine        Repo Security
        Syft               OSV             Scorecard
          │                  │                  │
          └──────────────────┼──────────────────┘
                             │
                             ▼
                     Policy Engine
                             │
               ┌─────────────┴─────────────┐
               ▼                           ▼
          Provenance                  Evidence Model
          Sigstore / SLSA                  │
               │                           │
               └─────────────┬─────────────┘
                             ▼
                     Evidence Store
                             │
              ┌──────────────┼──────────────┐
              ▼              ▼              ▼
             CLI         Dashboard     Exportación
                                           │
                                           ▼
                                 Expediente técnico
```

La propiedad intelectual o aportación principal del TFG no estaría en Syft, OSV o Sigstore.

Estaría en:

1. el modelo unificado de evidencias;
2. la arquitectura de orquestación;
3. la trazabilidad entre repositorio, análisis, artefacto y evidencia;
4. la evaluación reproducible;
5. la generación del expediente técnico.

---

# 8. Usuario objetivo

El producto no debería diseñarse inicialmente para grandes organizaciones con equipos completos de AppSec o Product Security.

El usuario objetivo podría ser:

### Perfil A — Startup de software

Tiene GitHub Actions, Docker y varios proyectos, pero carece de un equipo específico de Product Security.

### Perfil B — PYME tecnológica

Desarrolla software comercial o productos conectados y necesita profesionalizar sus procesos de seguridad.

### Perfil C — Equipo de desarrollo interno

Tiene que proporcionar evidencias de seguridad a otro departamento, cliente o auditor.

### Perfil D — Fabricante de producto conectado

Integra software propio y componentes de terceros y necesita conocer qué componentes termina distribuyendo.

El problema común es el mismo:

> **Tienen las herramientas o podrían instalarlas, pero carecen de una capa que convierta sus resultados en un proceso coherente, trazable y mantenible.**

---

# 9. Flujo principal de SupplyShield

El flujo ideal podría ser:

```text
1. Conectar repositorio
        │
        ▼
2. Identificar commit / release
        │
        ▼
3. Detectar ecosistema
        │
        ▼
4. Generar SBOM
        │
        ▼
5. Normalizar componentes
        │
        ▼
6. Consultar vulnerabilidades
        │
        ▼
7. Ejecutar controles de repositorio
        │
        ▼
8. Aplicar políticas
        │
        ▼
9. Registrar decisiones VEX
        │
        ▼
10. Vincular build y artefacto
        │
        ▼
11. Obtener / verificar provenance
        │
        ▼
12. Crear evidencias versionadas
        │
        ▼
13. Generar expediente
```

Cada ejecución debería estar asociada de forma inequívoca a:

```text
project_id
repository
commit_sha
branch/tag
scan_id
timestamp
tool_versions
policy_version
```

Esto convierte cada análisis en una unidad reproducible.

---

# 10. Diferenciación frente a OWASP Dependency-Track

Este apartado es crítico para justificar el producto.

**No tendría sentido plantear SupplyShield como un Dependency-Track alternativo.**

Dependency-Track es una plataforma madura, centrada en inventario y análisis de componentes y riesgo de la cadena de suministro. Dispone de capacidades relacionadas con SBOM, vulnerabilidades, VEX/VDR, políticas, auditoría, integridad y gestión de proyectos.

Intentar competir directamente implicaría desarrollar una versión inferior de una herramienta existente.

La diferenciación debe situarse una capa por encima.

| Capacidad | Dependency-Track | SupplyShield |
|---|---:|---:|
| Inventario de componentes | ✅ | ✅ mediante adapters |
| Ingesta SBOM | ✅ | ✅ |
| Generación SBOM | Integrable | ✅ pipeline propio/orquestado |
| Vulnerabilidades | ✅ | ✅ mediante motores externos |
| VEX / VDR | ✅ | ✅ integrado en expediente |
| Políticas de componentes | ✅ | ✅ políticas de workflow/evidencia |
| Scorecard del repositorio | No es su núcleo | ✅ |
| Provenance de builds | No es su foco principal | **✅ pieza central** |
| Verificación de attestations | No es su foco principal | **✅** |
| SLSA | Relacionable | **✅ modelo explícito** |
| Orquestación de herramientas | Parcial | **✅ objetivo principal** |
| Evidencias CRA | No es su foco de producto | **✅** |
| Matriz evidencia ↔ requisito | No es su foco | **✅** |
| Expediente técnico | No | **✅** |
| Reproducibilidad del expediente | Parcial | **✅ objetivo central** |
| Preparación de datos para reporting | No específica CRA | **✅ alcance limitado** |

Por tanto:

> **Dependency-Track administra riesgo de componentes. SupplyShield administra el proceso de generación de evidencias de seguridad.**

---

# 11. Dependency-Track como integración

SupplyShield podría incluso ofrecer un adapter para Dependency-Track.

```text
SupplyShield
    │
    ├── Syft
    ├── OSV
    ├── OpenSSF Scorecard
    ├── Sigstore
    ├── GitHub Attestations
    └── Dependency-Track
```

Esto tiene varias ventajas.

### Ventaja 1

No obliga a SupplyShield a reconstruir toda la lógica de análisis de componentes.

### Ventaja 2

Demuestra una arquitectura desacoplada.

### Ventaja 3

Permite comparar dos modos:

```text
Modo ligero
SupplyShield + Syft + OSV
```

frente a:

```text
Modo avanzado
SupplyShield + Dependency-Track
```

### Ventaja 4

Refuerza la idea de que el proyecto es un **control plane**.

---

# 12. Módulo 1 — Ingesta de repositorios

El primer módulo debe transformar distintas fuentes en una unidad de análisis normalizada.

Entradas posibles:

```text
supplyshield scan .

supplyshield scan https://github.com/org/repo

supplyshield scan --repo org/repo --ref v1.4.0
```

La ingesta debería registrar:

- URL del repositorio;
- commit SHA;
- branch;
- tag si existe;
- fecha;
- gestor de dependencias detectado;
- manifests encontrados;
- lockfiles encontrados;
- configuración de análisis;
- hash de la configuración.

El sistema no debería analizar simplemente “el proyecto actual”.

Debería analizar:

> **un estado concreto y direccionable del proyecto.**

---

# 13. Módulo 2 — SBOM Engine

La SBOM constituirá una de las evidencias fundamentales.

Para el MVP utilizaría **Syft** como generador principal.

Syft permite analizar imágenes y sistemas de archivos y exportar SBOM en formatos como:

- CycloneDX JSON;
- SPDX JSON;
- SPDX Tag-Value;
- formato JSON propio.

SupplyShield no debería limitarse a almacenar el fichero generado.

Debería realizar tres pasos:

```text
Scanner
   │
   ▼
SBOM nativa
   │
   ▼
Normalización SupplyShield
   │
   ▼
Component Inventory
```

Modelo interno simplificado:

```text
Component
─────────
id
name
version
ecosystem
purl
licenses
scope
source
parent_component
sbom_ref
```

Esto permite desacoplar el resto de la plataforma del formato concreto de SBOM.

---

# 14. CycloneDX o SPDX

No intentaría soportar ambos completamente desde el primer día.

Para el MVP elegiría **CycloneDX JSON como formato principal**, manteniendo exportación adicional a SPDX cuando la herramienta utilizada la proporcione.

La razón no debería formularse como “CycloneDX es mejor”, sino como una decisión de alcance.

CycloneDX resulta especialmente atractivo porque integra en una misma familia de formatos conceptos como:

- SBOM;
- VEX;
- VDR;
- attestations;
- formulación y relaciones entre componentes.

El diseño interno de SupplyShield debería evitar, no obstante, quedar acoplado permanentemente al estándar.

```text
        CycloneDX Adapter
               │
               ▼
       Internal Evidence Model
               ▲
               │
          SPDX Adapter
```

---

# 15. Módulo 3 — Vulnerability Intelligence

El MVP utilizaría **OSV** como fuente principal de vulnerabilidades.

OSV permite consultar vulnerabilidades por:

- paquete;
- ecosistema;
- versión;
- Package URL;
- commit.

Flujo:

```text
SBOM
 │
 ▼
Componentes normalizados
 │
 ▼
PURL / ecosystem / version
 │
 ▼
OSV API
 │
 ▼
Findings normalizados
```

Un finding podría almacenar:

```text
VulnerabilityFinding
────────────────────
id
scan_id
component_id
vulnerability_id
source
severity
aliases
introduced_version
fixed_version
status
justification
reviewed_by
reviewed_at
```

El sistema debería conservar también la respuesta original del proveedor para garantizar trazabilidad.

---

# 16. Vulnerabilidades: detectar no significa decidir explotabilidad

SupplyShield debe distinguir cuidadosamente entre:

```text
Vulnerabilidad conocida
```

y:

```text
Vulnerabilidad explotable en este producto
```

Una coincidencia de versión con OSV no demuestra necesariamente que el producto sea explotable.

Por ello, el sistema debería permitir un flujo de triaje:

```text
DETECTED
   │
   ├── UNDER_REVIEW
   │
   ├── AFFECTED
   │
   ├── NOT_AFFECTED
   │
   ├── FIXED
   │
   └── ACCEPTED_RISK
```

Cada transición debería registrar:

- responsable;
- fecha;
- justificación;
- evidencia adjunta;
- versión del producto.

Esto resulta mucho más defendible que intentar decidir automáticamente la explotabilidad.

---

# 17. Módulo 4 — VEX

VEX permitirá expresar de forma estructurada decisiones sobre la explotabilidad de vulnerabilidades.

Ejemplo conceptual:

```text
CVE detectado
    │
    ▼
¿Afecta realmente al producto?
    │
    ├── Sí  → affected
    │
    └── No  → not_affected
                │
                ▼
           justificación
```

SupplyShield debería permitir:

1. crear una decisión desde el dashboard;
2. conservar su historial;
3. vincularla a un componente y release;
4. exportarla a un formato VEX compatible;
5. incluirla en el expediente técnico.

El valor del producto no estaría en inventar VEX, sino en integrarlo en el proceso completo.

---

# 18. Módulo 5 — OpenSSF Scorecard

El análisis de componentes solamente cubre una parte de la cadena de suministro.

También importa cómo se mantiene el propio repositorio.

OpenSSF Scorecard puede aportar señales relacionadas con aspectos como:

- protección de ramas;
- code review;
- permisos de tokens;
- dependencias;
- actualización automática;
- workflows peligrosos;
- presencia de binarios;
- prácticas de release.

SupplyShield podría almacenar:

```text
RepositoryAssessment
────────────────────
scan_id
provider
score
check_name
check_score
risk_level
details
```

No convertiría el Scorecard en un “porcentaje CRA”.

Se utilizaría como una **fuente de evidencia de prácticas de desarrollo**.

---

# 19. Módulo 6 — Policy Engine

La plataforma necesita políticas explícitas.

Ejemplo:

```yaml
policies:
  - id: no-critical-vulnerabilities
    severity: error
    condition:
      vulnerability.severity: CRITICAL
      vulnerability.status: AFFECTED

  - id: sbom-required
    severity: error
    condition:
      evidence.sbom.exists: false

  - id: provenance-required
    severity: warning
    condition:
      artifact.provenance.verified: false
```

El motor de políticas permitiría responder a:

```text
¿Qué condiciones debe cumplir una release
antes de considerarla preparada?
```

No debería responder a:

```text
¿Cumple jurídicamente el CRA?
```

Salida posible:

```text
PASS     SBOM generated
PASS     no unresolved critical vulnerabilities
WARN     provenance not verified
PASS     repository score >= 7
FAIL     2 affected high vulnerabilities
```

---

# 20. Módulo 7 — Provenance

Uno de los elementos más diferenciadores de SupplyShield sería tratar la procedencia de artefactos como una pieza de primer nivel.

La pregunta es:

> **¿Podemos demostrar qué proceso, repositorio y commit produjeron un artefacto concreto?**

SLSA define provenance como información verificable que permite seguir un artefacto hasta su proceso y fuente de construcción.

SupplyShield podría registrar:

```text
Artifact
────────
name
version
digest
build_id
repository
commit_sha
workflow
builder
created_at
provenance_uri
verification_status
```

La cadena deseada sería:

```text
Source commit
     │
     ▼
Build workflow
     │
     ▼
Artifact digest
     │
     ▼
Provenance
     │
     ▼
Verification
```

---

# 21. SLSA como marco, no como certificación automática

SLSA dispone de varios niveles que incrementan las garantías de seguridad de la cadena de build.

SupplyShield podría comprobar determinadas propiedades observables, por ejemplo:

```text
¿Existe provenance?
¿Está firmada?
¿La produjo la plataforma de build?
¿Puede verificarse?
¿Coincide el digest del artefacto?
```

Pero debería evitar afirmar automáticamente:

> «Este repositorio es SLSA Build L3.»

salvo que se haya implementado de forma rigurosa la evaluación de todos los requisitos correspondientes.

Para el TFG, sería suficiente que SupplyShield:

- ingiriese provenance;
- verificase su firma;
- comprobase identidad y digest;
- registrase las propiedades verificadas;
- generase una evaluación técnica parcial y explícita.

---

# 22. Módulo 8 — Sigstore y attestations

Sigstore/Cosign podría utilizarse para:

- firmar artefactos;
- verificar firmas;
- crear attestations;
- verificar attestations;
- asociar metadatos de build a un artefacto.

Ejemplo conceptual:

```text
artifact.tar.gz
      │
      ├── sha256
      │
      ├── signature
      │
      └── attestation
             │
             ▼
        SupplyShield
             │
             ▼
       verification
```

SupplyShield almacenaría **el resultado de la verificación**, no únicamente el fichero.

```text
verified = true
subject_digest = sha256:...
issuer = ...
identity = ...
timestamp = ...
predicate_type = ...
```

---

# 23. GitHub Artifact Attestations

Para repositorios alojados en GitHub existe además una oportunidad especialmente interesante.

GitHub Actions permite generar attestations asociadas a los artefactos construidos, incluyendo información como:

- repositorio;
- organización;
- commit SHA;
- workflow;
- evento de ejecución;
- identidad derivada mediante OIDC.

SupplyShield podría implementar un adapter:

```text
GitHub Actions
      │
      ▼
Artifact Attestation
      │
      ▼
SupplyShield Adapter
      │
      ▼
Normalized Provenance
```

Esto permitiría demostrar una integración real con un pipeline moderno sin implementar una infraestructura completa de firma propia.

---

# 24. Módulo 9 — CRA Evidence Store

Esta sería probablemente la parte más original del producto.

En lugar de guardar resultados sueltos, SupplyShield tendría un **Evidence Store**.

Una evidencia podría modelarse así:

```text
Evidence
────────
id
type
project_id
release_id
scan_id
source_tool
source_version
created_at
content_hash
status
artifact_path
metadata
```

Tipos posibles:

```text
SBOM
VULNERABILITY_REPORT
VEX
REPOSITORY_ASSESSMENT
TEST_REPORT
BUILD_PROVENANCE
SIGNATURE_VERIFICATION
POLICY_EVALUATION
ARCHITECTURE_METADATA
RISK_NOTE
```

Cada evidencia tendría:

```text
quién la produjo
+
cuándo
+
para qué commit
+
con qué herramienta
+
con qué configuración
+
hash del contenido
```

Esto permite hablar de **evidencia trazable** en lugar de simples resultados.

---

# 25. Matriz evidencia ↔ requisito técnico

SupplyShield podría incluir una matriz interna que indique qué evidencias pueden ayudar a documentar cada área.

Ejemplo simplificado:

| Área técnica | Evidencia SupplyShield | Estado |
|---|---|---|
| Componentes del producto | SBOM | automática |
| Vulnerabilidades detectadas | OSV report | automática |
| Gestión de vulnerabilidades | historial de triaje | semiautomática |
| Explotabilidad | VEX | semiautomática |
| Build | metadatos CI | automática |
| Procedencia del artefacto | SLSA/in-toto provenance | automática si existe |
| Integridad | firma verificada | automática |
| Pruebas | JUnit/pytest report | automática |
| Prácticas repositorio | Scorecard | automática |
| Evaluación de riesgo | documento / decisión | manual |
| Arquitectura | documento | manual |

El dashboard podría mostrar:

```text
Technical Evidence Coverage
───────────────────────────
SBOM                    READY
Vulnerability report    READY
VEX                     PARTIAL
Build provenance        READY
Repository assessment   READY
Risk assessment         MISSING
Architecture document   MISSING
```

Este porcentaje **no sería un porcentaje de cumplimiento del CRA**.

Sería exclusivamente:

> **porcentaje de evidencias previstas por el modelo SupplyShield que están disponibles.**

---

# 26. Módulo 10 — Generación de expediente técnico

La plataforma podría generar un paquete reproducible similar a:

```text
supplyshield-evidence-v1.4.0/
│
├── manifest.json
│
├── project/
│   ├── repository.json
│   ├── release.json
│   └── toolchain.json
│
├── sbom/
│   └── bom.cdx.json
│
├── vulnerabilities/
│   ├── findings.json
│   └── summary.pdf
│
├── vex/
│   └── vex.json
│
├── repository-security/
│   └── scorecard.json
│
├── provenance/
│   ├── provenance.json
│   └── verification.json
│
├── policies/
│   └── evaluation.json
│
├── tests/
│   └── test-results.xml
│
└── report/
    └── technical-evidence-report.pdf
```

El `manifest.json` sería especialmente importante.

Podría incluir:

```json
{
  "project": "example-api",
  "release": "1.4.0",
  "commit": "abc123...",
  "generated_at": "...",
  "policy_version": "2026.1",
  "evidence": [
    {
      "type": "SBOM",
      "path": "sbom/bom.cdx.json",
      "sha256": "..."
    }
  ]
}
```

De esta forma, el expediente en sí mismo puede validarse.

---

# 27. Integridad del expediente

Una ampliación interesante consiste en calcular hashes de todas las evidencias.

```text
Evidence files
     │
     ▼
 SHA-256
     │
     ▼
manifest.json
     │
     ▼
 firma opcional
```

Así SupplyShield podría verificar posteriormente:

```text
supplyshield evidence verify ./evidence-v1.4.0
```

Resultado:

```text
Manifest signature     VALID
SBOM hash              VALID
VEX hash               VALID
Provenance hash        VALID
Policy report hash     VALID

Evidence package integrity: VERIFIED
```

Esto aporta valor técnico sin intentar convertir el proyecto en un sistema jurídico de certificación.

---

# 28. Preparación de información para notificación CRA

Desde septiembre de 2026 existen obligaciones de notificación de vulnerabilidades activamente explotadas e incidentes graves para los fabricantes afectados.

SupplyShield no debería enviar automáticamente notificaciones oficiales en el MVP.

Sí podría ofrecer un módulo de **Incident Evidence Pack** que recopile la información ya disponible.

Ejemplo:

```text
Vulnerability
CVE-XXXX-YYYY

Affected products
- product-api 2.1.0
- device-firmware 4.3.2

Affected component
pkg:pypi/example@1.2.3

First detected
2026-10-02T10:12Z

Status
AFFECTED

Fixed version
1.2.4

Related releases
...
```

El usuario seguiría siendo responsable de:

- determinar si existe obligación de notificar;
- validar jurídicamente la información;
- completar campos no técnicos;
- realizar la comunicación oficial.

---

# 29. CLI

La CLI constituye uno de los entregables principales.

Podría tener una interfaz similar a:

```text
supplyshield init

supplyshield scan .

supplyshield scan . --profile release

supplyshield sbom generate .

supplyshield vuln list

supplyshield vuln review OSV-2026-XXXX

supplyshield policy check

supplyshield provenance verify artifact.tar.gz

supplyshield evidence build --release v1.4.0

supplyshield evidence verify ./evidence-v1.4.0
```

La CLI permitiría integrar SupplyShield en CI sin depender del dashboard.

---

# 30. Integración CI/CD

La plataforma debería poder utilizarse como paso de pipeline.

Ejemplo conceptual en GitHub Actions:

```text
checkout
   │
   ▼
tests
   │
   ▼
build
   │
   ▼
supplyshield scan
   │
   ▼
policy check
   │
   ├── PASS → release
   │
   └── FAIL → stop
```

SupplyShield podría producir outputs como:

```text
sbom_path=...
critical_findings=0
high_findings=2
policy_status=PASS
evidence_bundle=...
```

De esta forma puede convertirse en una pieza del pipeline y no únicamente en una aplicación web de consulta.

---

# 31. Dashboard web

El dashboard no debería intentar replicar Dependency-Track.

Su pantalla principal debería estar orientada a **releases y evidencias**.

Ejemplo:

```text
SupplyShield — project-api / v1.4.0

Release status                  READY WITH WARNINGS
Commit                          8fd412a
SBOM                            ✓
Known vulnerabilities           7
Affected vulnerabilities        1
Critical affected               0
VEX coverage                    86 %
Repository assessment           8.4 / 10
Provenance                      ✓ VERIFIED
Policy                          ✓ PASS
Evidence coverage               81 %
```

Secciones:

```text
Overview
Components
Vulnerabilities
VEX
Repository
Build & Provenance
Policies
Evidence
Audit Log
```

---

# 32. Arquitectura técnica recomendada

Para un TFG no utilizaría microservicios desde el principio.

Recomendaría un **monolito modular con workers de análisis**.

```text
                    ┌────────────────┐
                    │ React / Vue    │
                    └───────┬────────┘
                            │
                            ▼
                    ┌────────────────┐
                    │ FastAPI        │
                    │ API + Domain   │
                    └───────┬────────┘
                            │
               ┌────────────┼────────────┐
               │            │            │
               ▼            ▼            ▼
          PostgreSQL      Queue       Object Store
                           │
                           ▼
                       Workers
                           │
             ┌─────────────┼──────────────┐
             ▼             ▼              ▼
            Syft          OSV          Scorecard
                           │
                           ▼
                        Cosign
```

Componentes:

| Componente | Tecnología propuesta |
|---|---|
| Backend | Python + FastAPI |
| CLI | Python + Typer |
| Frontend | React + TypeScript |
| Base de datos | PostgreSQL |
| Jobs | Redis + RQ/Celery o worker propio |
| SBOM | Syft |
| Vulnerabilidades | OSV |
| Repo security | OpenSSF Scorecard |
| Firma/provenance | Cosign/Sigstore |
| Formato principal | CycloneDX |
| CI | GitHub Actions |
| Contenedores | Docker Compose |
| Tests | pytest |
| E2E | Playwright opcional |
| Informes | HTML → PDF opcional |

Para el TFG, un worker propio basado en una tabla de jobs o Redis puede ser suficiente. No es necesario introducir Kafka o Kubernetes.

---

# 33. Modelo de adapters

Cada herramienta externa debería estar encapsulada.

```text
ScannerAdapter
├── SyftAdapter
└── FutureAdapter

VulnerabilityProvider
├── OSVProvider
└── DependencyTrackProvider

ProvenanceProvider
├── GitHubAttestationProvider
└── CosignProvider
```

Interfaz conceptual:

```python
class SBOMProvider:
    def generate(self, source: Source) -> SBOMResult:
        ...
```

El resto del sistema solamente consume:

```text
SBOMResult
VulnerabilityResult
RepositoryAssessment
ProvenanceResult
```

Esto hace que la aportación arquitectónica sea mucho más clara.

---

# 34. Modelo de datos

Entidades principales:

```text
Organization
    │
    └── Project
          │
          ├── Repository
          │
          └── Release
                │
                ├── Scan
                │    ├── Component
                │    ├── VulnerabilityFinding
                │    └── RepositoryAssessment
                │
                ├── Artifact
                │    └── Provenance
                │
                ├── PolicyEvaluation
                │
                └── Evidence
```

Tablas mínimas:

```text
projects
repositories
releases
scans
components
vulnerabilities
findings
vex_statements
artifacts
provenance_records
policy_definitions
policy_evaluations
evidence_records
audit_events
```

---

# 35. Audit log

Todas las decisiones relevantes deberían registrarse.

Ejemplo:

```text
2026-10-06 14:22 scan.started
2026-10-06 14:23 sbom.generated
2026-10-06 14:23 vulnerability.detected OSV-...
2026-10-06 15:10 vulnerability.reviewed NOT_AFFECTED
2026-10-06 15:11 vex.generated
2026-10-06 16:02 evidence.bundle.generated
```

El audit log aporta:

- trazabilidad;
- reproducibilidad;
- responsabilidad;
- comparación entre releases;
- demostración académica de diseño orientado a evidencias.

---

# 36. Estructura posible del repositorio

```text
supplyshield/
│
├── apps/
│   ├── api/
│   ├── cli/
│   └── web/
│
├── supplyshield/
│   ├── domain/
│   │   ├── projects/
│   │   ├── releases/
│   │   ├── components/
│   │   ├── vulnerabilities/
│   │   ├── policies/
│   │   └── evidence/
│   │
│   ├── services/
│   │   ├── scan_service.py
│   │   ├── evidence_service.py
│   │   ├── policy_service.py
│   │   └── provenance_service.py
│   │
│   ├── adapters/
│   │   ├── syft/
│   │   ├── osv/
│   │   ├── scorecard/
│   │   ├── cosign/
│   │   ├── github/
│   │   └── dependency_track/
│   │
│   ├── persistence/
│   └── workers/
│
├── policies/
│
├── tests/
│   ├── unit/
│   ├── integration/
│   └── e2e/
│
├── benchmark/
│   ├── datasets/
│   ├── fixtures/
│   ├── runner/
│   └── results/
│
├── docker/
└── docs/
```

No introduciría una arquitectura distribuida más compleja salvo que el desarrollo demuestre que realmente es necesaria.

---

# 37. Ecosistemas soportados

El mayor riesgo de alcance es intentar soportar demasiados gestores de dependencias.

Para el MVP recomendaría:

```text
Python
  ├── requirements.txt
  ├── pyproject.toml
  └── poetry.lock / uv.lock

JavaScript / TypeScript
  ├── package.json
  └── package-lock.json
```

Esto permite trabajar con dos ecosistemas populares sin dispersar el TFG.

Java/Maven podría quedar como ampliación.

El motor SBOM ya soportará más ecosistemas, pero **las garantías experimentales del TFG solamente se realizarían sobre los ecosistemas explícitamente estudiados**.

---

# 38. Dataset experimental

El proyecto necesita un conjunto de prueba reproducible.

Propongo dos grupos.

## A. Repositorios reales

Seleccionar entre **20 y 50 proyectos open source**.

Por ejemplo:

```text
10–25 Python
10–25 JavaScript/TypeScript
```

Se fijaría para cada uno:

- repositorio;
- commit SHA;
- lockfile;
- número esperado de dependencias;
- SBOM de referencia;
- resultado OSV obtenido en una fecha congelada o snapshot local.

## B. Repositorios controlados

Crear un conjunto de pequeños proyectos preparados deliberadamente con:

- una versión vulnerable conocida;
- una versión corregida;
- dependencia directa vulnerable;
- dependencia transitiva vulnerable;
- componente no vulnerable con nombre similar;
- lockfile ausente;
- versión ambigua;
- múltiples gestores de paquetes.

Estos repositorios permitirán construir un **ground truth**.

---

# 39. Sembrado de vulnerabilidades

En lugar de inventar vulnerabilidades, se utilizarán versiones históricas de paquetes que OSV reconoce como afectadas.

Ejemplo conceptual:

```text
fixture-01
requirements.txt
└── package-A==vulnerable-version

expected:
OSV-XXXX-YYYY = detected
```

Y:

```text
fixture-02
requirements.txt
└── package-A==fixed-version

expected:
OSV-XXXX-YYYY = not detected
```

Esto permite medir:

- true positives;
- false negatives;
- false positives;
- precisión;
- recall.

---

# 40. Diseño experimental

Se podrían realizar cuatro experimentos principales.

## Experimento 1 — Cobertura SBOM

Comparar las dependencias esperadas con las detectadas.

```text
Ground truth dependencies
           │
           ▼
      SupplyShield
           │
           ▼
Detected dependencies
```

## Experimento 2 — Vulnerabilidades

Ejecutar fixtures con vulnerabilidades sembradas y comprobar detección.

## Experimento 3 — Reproducibilidad

Ejecutar el mismo análisis varias veces con:

```text
mismo commit
mismas tool versions
misma policy
```

comparando los hashes de las evidencias normalizadas.

## Experimento 4 — Coste

Medir:

- tiempo;
- CPU;
- memoria;
- tamaño de artefactos;
- tiempo añadido al pipeline CI.

---

# 41. Métricas

## Dependency Coverage

```text
Dependency Coverage =

dependencias correctamente identificadas
─────────────────────────────────────────
dependencias presentes en ground truth
```

Objetivo orientativo:

```text
> 95 %
```

para los escenarios compatibles con el MVP.

## Vulnerability Recall

```text
Recall = TP / (TP + FN)
```

## Precision

```text
Precision = TP / (TP + FP)
```

## False Positive Rate

Casos reportados como vulnerables sin pertenecer al ground truth.

## Evidence Automation Rate

```text
Evidence Automation Rate =

evidencias generadas automáticamente
────────────────────────────────────
evidencias definidas en el perfil
```

## Reproducibility Rate

Porcentaje de evidencias normalizadas equivalentes entre ejecuciones idénticas.

## Analysis Time

Tiempo total:

```text
clone
+
SBOM
+
OSV
+
Scorecard
+
policies
+
evidence bundle
```

## CI Overhead

Tiempo añadido al pipeline respecto al pipeline base.

---

# 42. Métrica especialmente interesante: Evidence Completeness

Una métrica propia del proyecto podría ser:

```text
Evidence Completeness =

número de evidencias disponibles
───────────────────────────────
número de evidencias esperadas
```

Por ejemplo:

```text
Perfil RELEASE_BASELINE

SBOM                     1
Vulnerability report     1
VEX                      1
Test report              1
Repository assessment    1
Build provenance         1
Policy report            1
──────────────────────────
Total esperado           7
```

Si existen 6:

```text
Evidence Completeness = 85.7 %
```

Debe indicarse explícitamente:

> **No representa un porcentaje de cumplimiento del CRA.**

Representa únicamente el grado de completitud del perfil técnico definido por SupplyShield.

---

# 43. Reproducibilidad

La reproducibilidad debería ser una propiedad de diseño.

Cada análisis tendría un fichero similar a:

```yaml
scan:
  project: api
  commit: 8fd412a

runtime:
  supplyshield: 0.4.0

adapters:
  syft: 1.x
  scorecard: x.y

providers:
  osv_snapshot: 2026-10-01

policy:
  profile: cra-baseline
  version: 2026.1
```

Esto permite explicar por qué dos análisis pueden diferir.

Especialmente importante: las bases de vulnerabilidades evolucionan con el tiempo.

Por ello, un análisis reproducible debe diferenciar:

```text
reproducibilidad de extracción
```

de:

```text
re-evaluación con inteligencia actualizada
```

SupplyShield podría ofrecer ambas operaciones.

---

# 44. Dos modos de reanálisis

## Historical Replay

Utiliza el snapshot original de datos y herramientas para reproducir el resultado histórico.

## Current Re-evaluation

Mantiene el mismo commit pero consulta inteligencia de vulnerabilidades actual.

Ejemplo:

```text
Commit 8fd412a
   │
   ├── Scan 2026-05-10 → 2 CVEs
   │
   └── Re-evaluate 2026-10-06 → 4 CVEs
```

Esta funcionalidad refleja muy bien una propiedad real de la seguridad de componentes:

> un artefacto puede no cambiar y, sin embargo, su conocimiento de riesgo sí.

---

# 45. Perfil de políticas

SupplyShield podría ofrecer perfiles.

```text
baseline
release
strict
cra-evidence-baseline
```

Ejemplo:

```yaml
profile: release

requirements:
  sbom: required
  vulnerability_report: required
  provenance: required

thresholds:
  affected_critical: 0
  affected_high: 3
```

Los perfiles se versionarían.

Así se evita que un análisis cambie de significado porque una política fue modificada posteriormente.

---

# 46. Qué no construiría

Para mantener un alcance realista, excluiría deliberadamente:

- un nuevo motor SCA;
- una nueva base de datos CVE;
- un sustituto completo de Dependency-Track;
- un scanner de código SAST propio;
- un scanner DAST propio;
- un SIEM;
- un gestor completo de incidentes;
- un sistema jurídico de determinación de cumplimiento;
- presentación automática de notificaciones oficiales CRA;
- clasificación automática definitiva de explotabilidad;
- certificación SLSA automática completa;
- una plataforma Kubernetes empresarial;
- soporte completo para todos los gestores de paquetes;
- generación automática de toda la documentación legal del CRA.

Estas exclusiones son importantes para proteger el TFG frente al crecimiento incontrolado de alcance.

---

# 47. MVP

El MVP debería demostrar un recorrido completo.

## Entrada

```text
Repositorio GitHub Python o npm
```

## Pipeline

```text
clone
  ↓
commit fingerprint
  ↓
Syft
  ↓
CycloneDX SBOM
  ↓
OSV
  ↓
vulnerability report
  ↓
Scorecard
  ↓
policy engine
  ↓
GitHub/Cosign provenance
  ↓
evidence store
  ↓
report
```

## Interfaces

- CLI funcional.
- API REST.
- dashboard mínimo.

## Salida

- SBOM;
- findings;
- triaje/VEX básico;
- evaluación de políticas;
- provenance si existe;
- paquete de evidencias;
- informe técnico.

Con este alcance ya existe un TFG completo.

---

# 48. Ampliaciones

Solamente si el MVP está terminado:

### A. Dependency-Track Adapter

Sincronizar proyectos/SBOM/findings.

### B. GitLab CI

Segundo proveedor CI.

### C. SPDX

Importación/exportación completa adicional.

### D. Java/Maven

Tercer ecosistema.

### E. Firma del expediente

Firmar el manifest final mediante Sigstore.

### F. Reporting Pack

Preparar información técnica para incidentes o vulnerabilidades que puedan requerir notificación.

### G. Diff entre releases

```text
v1.3.0 → v1.4.0

+ 4 dependencies
- 1 dependency
+ 2 vulnerabilities
- 5 vulnerabilities
provenance changed
policy PASS → WARN
```

Esta última ampliación tendría bastante valor de producto.

---

# 49. Riesgos del proyecto

## Riesgo 1 — Alcance regulatorio excesivo

**Mitigación:** definir SupplyShield como herramienta técnica de evidencias, nunca como certificador.

## Riesgo 2 — Reinventar herramientas existentes

**Mitigación:** arquitectura basada en adapters.

## Riesgo 3 — Competir directamente con Dependency-Track

**Mitigación:** mantener el foco en orquestación, provenance, evidencias y expediente.

## Riesgo 4 — Demasiados ecosistemas

**Mitigación:** Python + npm para las garantías experimentales.

## Riesgo 5 — Ground truth difícil

**Mitigación:** combinar repositorios reales con fixtures controlados.

## Riesgo 6 — Falsos positivos

**Mitigación:** separar detección de vulnerabilidad y decisión VEX/explotabilidad.

## Riesgo 7 — Dependencia de servicios externos

**Mitigación:** cachear respuestas, almacenar resultados originales y soportar snapshots experimentales.

## Riesgo 8 — Frontend consume demasiado tiempo

**Mitigación:** priorizar CLI, API y flujo completo; dashboard sencillo.

---

# 50. Seguridad de SupplyShield

La propia plataforma trabaja con información potencialmente sensible.

Debería aplicar:

- tokens GitHub de mínimo privilegio;
- secretos fuera de la base de datos cuando sea posible;
- cifrado de credenciales;
- separación por organización/proyecto;
- audit log;
- validación estricta de repositorios y paths;
- ejecución de scanners en procesos aislados;
- límites de recursos;
- no ejecutar scripts arbitrarios del repositorio durante el análisis SBOM salvo necesidad explícita.

Este último punto es especialmente importante.

Analizar un repositorio no debería implicar automáticamente:

```text
npm install
pip install
./gradlew build
```

porque implicaría ejecutar código potencialmente no confiable.

Siempre que sea posible, SupplyShield debería realizar análisis estático de manifests, lockfiles, filesystem o imágenes ya construidas.

---

# 51. Fases del proyecto

## Fase 1 — Estado del arte

Estudiar:

- seguridad de cadena de suministro;
- CRA;
- SBOM;
- CycloneDX;
- SPDX;
- VEX;
- OSV;
- SLSA;
- Sigstore;
- OpenSSF Scorecard;
- Dependency-Track;
- attestations de GitHub.

## Fase 2 — Especificación

Definir:

- alcance CRA;
- perfil de evidencias;
- entidades;
- métricas;
- dataset.

## Fase 3 — Arquitectura

Diseñar:

- domain model;
- adapters;
- pipeline;
- persistence;
- evidence store.

## Fase 4 — CLI + SBOM

Primer recorrido:

```text
repo → Syft → SBOM → PostgreSQL
```

## Fase 5 — Vulnerabilidades

```text
SBOM → OSV → findings
```

## Fase 6 — Triaje + VEX

Estado y justificación de findings.

## Fase 7 — Scorecard + políticas

Evaluación de repositorio y policy engine.

## Fase 8 — Provenance

GitHub Attestations / Cosign.

## Fase 9 — Evidence Store

Crear modelo de evidencias, hashes y manifest.

## Fase 10 — Dashboard

Interfaz orientada a releases.

## Fase 11 — CI

Integración GitHub Actions.

## Fase 12 — Benchmark

Ejecutar dataset completo.

## Fase 13 — Evaluación

Calcular métricas y analizar resultados.

## Fase 14 — Memoria y reproducibilidad

Documentación final, scripts y resultados.

---

# 52. Estimación de esfuerzo

Una distribución realista para **380–460 horas** podría ser:

| Trabajo | Horas aproximadas |
|---|---:|
| Estado del arte y CRA | 40–50 |
| Especificación y diseño | 25–35 |
| Backend y modelo de datos | 45–55 |
| CLI | 20–25 |
| SBOM + Syft | 25–30 |
| OSV + vulnerabilidades | 25–30 |
| VEX + triaje | 20–25 |
| Policy engine | 20–25 |
| Scorecard | 10–15 |
| Provenance / Sigstore | 30–40 |
| Evidence Store | 30–40 |
| Dashboard | 25–35 |
| CI/CD | 15–20 |
| Benchmark y dataset | 25–35 |
| Experimentación y análisis | 30–40 |
| Memoria final | 35–45 |

No todas las líneas son estrictamente aditivas, porque parte de la documentación se desarrollará en paralelo.

---

# 53. Coste

El proyecto puede desarrollarse prácticamente sin coste.

```text
GitHub                     gratuito
Syft                       open source
OSV                        gratuito
OpenSSF Scorecard          open source
Sigstore                   open source
PostgreSQL                 open source
Docker                     open source
FastAPI                    open source
React                      open source
```

Posibles costes:

- dominio opcional;
- VPS opcional;
- almacenamiento de artefactos;
- servicios privados de CI si se supera cuota.

Estimación razonable:

> **0–80 € durante el desarrollo**, dependiendo del hosting elegido.

---

# 54. Entregables

El TFG debería terminar con:

## Producto

- CLI SupplyShield.
- API backend.
- dashboard web.
- PostgreSQL.
- pipeline de jobs.

## Integraciones

- Syft.
- OSV.
- OpenSSF Scorecard.
- Sigstore/Cosign o GitHub Attestations.
- GitHub Actions.

## Evidencias

- SBOM.
- vulnerability report.
- VEX básico.
- policy report.
- provenance report.
- evidence manifest.
- technical evidence bundle.

## Investigación

- dataset reproducible.
- fixtures con vulnerabilidades conocidas.
- scripts de benchmark.
- resultados.
- métricas.
- análisis.

---

# 55. Aportación académica

La contribución no sería desarrollar un nuevo scanner.

Una formulación fuerte sería:

> **Diseño y evaluación de una arquitectura DevSecOps de orquestación para generar, normalizar y conservar evidencias reproducibles de seguridad de la cadena de suministro aplicables a procesos técnicos asociados al Cyber Resilience Act.**

La aportación tendría cuatro partes.

### 1. Arquitectura

Modelo desacoplado basado en adapters y un Evidence Store central.

### 2. Implementación

Prototipo completo desde repositorio hasta expediente.

### 3. Modelo de evidencias

Representación versionada y verificable de SBOM, vulnerabilidades, VEX, provenance, políticas y resultados.

### 4. Evaluación

Benchmark reproducible sobre repositorios reales y fixtures controlados.

Esto resulta académicamente mucho más sólido que limitarse a:

> «He conectado Syft con OSV.»

---

# 56. Qué tendría de producto real

SupplyShield podría presentarse como una aplicación utilizable por un equipo pequeño.

Flujo real:

```text
1. Instalar GitHub App
2. Seleccionar repositorio
3. SupplyShield detecta release
4. Ejecuta análisis
5. Genera SBOM
6. Detecta vulnerabilidades
7. Solicita revisión de findings pendientes
8. Verifica provenance
9. Evalúa políticas
10. Genera expediente
```

El usuario no necesita conocer en profundidad cada herramienta subyacente.

Ese es precisamente el valor de una capa de orquestación.

---

# 57. Posicionamiento de producto

Evitaría mensajes como:

> «Encuentra vulnerabilidades en tus dependencias.»

porque ese mercado está extremadamente cubierto.

La propuesta debería formularse más cerca de:

> **«Convierte el pipeline de desarrollo en evidencias técnicas trazables y reproducibles de seguridad de la cadena de suministro.»**

Y, en el contexto europeo:

> **«Automatiza la recopilación de evidencias técnicas que tu equipo necesita mantener para procesos de seguridad de producto y preparación CRA.»**

Esto diferencia mucho mejor el producto.

---

# 58. Resultado esperado

Al finalizar el TFG debería ser posible tomar un repositorio y ejecutar:

```text
supplyshield scan ./project
```

para obtener:

```text
Project: example-api
Commit: 8fd412a

Components detected      214
Known vulnerabilities     12
Affected                   4
Critical affected          0
VEX reviewed              10/12
Repository score           8.2
Provenance              VERIFIED
Policy                    PASS
Evidence completeness     85.7 %

Bundle:
./evidence/example-api-v1.4.0/
```

Y posteriormente:

```text
supplyshield evidence verify ./evidence/example-api-v1.4.0
```

para comprobar que las evidencias no han sido modificadas.

---

# 59. Posibles resultados experimentales

Una conclusión final podría tener una forma similar a:

> La plataforma identificó más del 95 % de las dependencias del conjunto de referencia en los escenarios soportados y detectó la mayoría de las vulnerabilidades sembradas cuando existía una versión resoluble del componente. La normalización de SBOM, vulnerabilidades, provenance y políticas permitió generar automáticamente una parte sustancial del perfil de evidencias definido, con una sobrecarga asumible para pipelines de integración continua.

También podrían aparecer resultados negativos útiles.

Por ejemplo:

> Los proyectos sin lockfile redujeron significativamente la capacidad de determinar versiones exactas y, por tanto, la fiabilidad del análisis de vulnerabilidades.

O:

> La reproducibilidad absoluta de findings históricos no es posible sin conservar una referencia temporal o snapshot de la inteligencia de vulnerabilidades utilizada.

Estas conclusiones serían incluso más interesantes académicamente porque revelarían límites reales del enfoque.

---

# 60. Por qué este TFG es interesante desde Ingeniería Informática

El proyecto conecta varias áreas sin depender de hardware especializado.

```text
                SupplyShield
                     │
       ┌─────────────┼─────────────┐
       │             │             │
       ▼             ▼             ▼
Ciberseguridad   Software       Sistemas
       │             │             │
       ├─────────────┼─────────────┤
       │             │             │
       ▼             ▼             ▼
     SBOM          APIs          Docker
     VEX         arquitectura    CI/CD
     SLSA        PostgreSQL      workers
   Sigstore       testing       tooling
```

Permite trabajar con:

- backend;
- frontend;
- CLI;
- integración de herramientas;
- arquitectura limpia;
- seguridad;
- bases de datos;
- CI/CD;
- formatos estándar;
- criptografía aplicada;
- experimentación;
- métricas;
- documentación técnica.

Y lo hace sin requerir entrenamiento de modelos, GPUs o infraestructuras costosas.

---

# 61. Principal fortaleza del proyecto

La principal fortaleza de SupplyShield no sería ninguna herramienta concreta.

Sería el pipeline completo:

```text
Repositorio
    │
    ▼
Inventario
    │
    ▼
SBOM
    │
    ▼
Vulnerabilidades
    │
    ▼
Triaje / VEX
    │
    ▼
Seguridad del repositorio
    │
    ▼
Build provenance
    │
    ▼
Attestations
    │
    ▼
Políticas
    │
    ▼
Evidence Store
    │
    ▼
Expediente reproducible
```

Ahí existe una historia de producto clara y una aportación técnica defendible.

---

# 62. Principal debilidad potencial

El peligro es terminar creando solamente un wrapper sobre CLIs existentes.

Para evitarlo, el TFG debe aportar explícitamente:

1. un **modelo de dominio propio**;
2. un **modelo unificado de evidencia**;
3. persistencia e historial entre releases;
4. políticas versionadas;
5. triaje y VEX;
6. relación entre artefacto y provenance;
7. generación reproducible del expediente;
8. benchmark cuantitativo.

Si se implementan estos elementos, SupplyShield deja de ser un script que ejecuta Syft y pasa a ser una plataforma.

---

# 63. Decisiones que recomendaría cerrar desde el comienzo

## Lenguaje

**Python**.

Razón: FastAPI, Typer, integración rápida con CLIs, tratamiento JSON/YAML y menor coste de desarrollo para un TFG.

Go seguiría siendo una excelente elección para una CLI comercial, pero introducir backend Go + frontend + integraciones aumentaría el coste de implementación sin aportar necesariamente mejores resultados académicos.

## Backend

**FastAPI**.

## Frontend

**React + TypeScript**.

## Base de datos

**PostgreSQL**.

## SBOM

**Syft + CycloneDX JSON**.

## Vulnerabilidades

**OSV** como fuente base.

## Provenance

**GitHub Artifact Attestations + Cosign/Sigstore**.

## Repo security

**OpenSSF Scorecard**.

## CI

**GitHub Actions** primero.

## Arquitectura

**Monolito modular con workers**.

---

# 64. Título recomendado

Mantendría como opción principal:

> **Diseño de una plataforma DevSecOps para automatizar evidencias de seguridad de la cadena de suministro en el contexto del Cyber Resilience Act**

Opción más técnica:

> **Diseño y evaluación de una arquitectura de orquestación para SBOM, vulnerabilidades, provenance y evidencias reproducibles de seguridad software**

Opción con el nombre del producto:

> **SupplyShield: plataforma DevSecOps para generación reproducible de evidencias de seguridad de la cadena de suministro y soporte técnico al Cyber Resilience Act**

De los tres, el tercero comunica mejor que existe un producto real, mientras que el segundo tiene un tono más académico y evita que el CRA parezca abarcar la totalidad del trabajo.

Una formulación equilibrada podría ser:

> **SupplyShield: diseño y evaluación de una plataforma DevSecOps para evidencias reproducibles de seguridad de la cadena de suministro en el contexto del Cyber Resilience Act**

---

# 65. Idea central del proyecto

El proyecto debería mantener una idea sencilla durante todo el desarrollo:

> **No construir otro scanner, sino construir la capa que convierte los resultados de múltiples herramientas de seguridad en un proceso trazable, versionado y reproducible.**

La cadena conceptual sería:

```text
Herramientas
     ↓
Datos
     ↓
Normalización
     ↓
Decisiones
     ↓
Evidencias
     ↓
Trazabilidad
     ↓
Expediente reproducible
```

Y el posicionamiento final:

> **SupplyShield es el control plane que conecta el repositorio, la SBOM, las vulnerabilidades, las decisiones VEX, la procedencia de los artefactos y las políticas de seguridad para producir evidencias técnicas reproducibles.**

Esta formulación diferencia el proyecto de Dependency-Track, evita la promesa excesiva de “cumplimiento automático” y mantiene un alcance suficientemente ambicioso para un TFG de Ingeniería Informática.

---

# 66. Fuentes principales utilizadas para la profundización

La propuesta anterior se apoya principalmente en documentación técnica y normativa oficial o primaria consultada en octubre de 2026:

1. **Reglamento (UE) 2024/2847 — Cyber Resilience Act**, especialmente el Anexo I, Parte II, sobre gestión de vulnerabilidades y SBOM, y el Anexo VII sobre documentación técnica.
2. **Comisión Europea — Cyber Resilience Act: Reporting obligations**, para la entrada en aplicación de las obligaciones de notificación el 11 de septiembre de 2026 y el funcionamiento de la Single Reporting Platform.
3. **Comisión Europea — CRA implementation / summary / FAQ**, para calendario, alcance general y tratamiento de software open source.
4. **OWASP Dependency-Track**, documentación oficial sobre inventario, análisis, VEX/VDR, políticas, integridad y gestión de riesgo de componentes.
5. **OSV**, documentación oficial de la API para consultas de vulnerabilidades por paquete, versión, PURL y commit.
6. **Anchore Syft**, documentación oficial para generación de SBOM en CycloneDX y SPDX.
7. **SLSA Specification v1.2**, especialmente Build Track y provenance.
8. **Sigstore / Cosign**, documentación oficial sobre firma, verificación y attestations in-toto.
9. **GitHub Artifact Attestations**, documentación oficial sobre provenance de builds y verificación de attestations.
10. **OpenSSF Scorecard**, documentación oficial de los controles de seguridad del repositorio.

---

# 67. Nota de alcance jurídico

SupplyShield debe presentarse en la memoria como una herramienta de ingeniería y automatización de evidencias.

El proyecto:

- **no proporciona asesoramiento jurídico**;
- **no determina por sí mismo si una organización o producto está sujeto al CRA**;
- **no certifica conformidad**;
- **no sustituye una evaluación de riesgos del fabricante**;
- **no sustituye los procedimientos de evaluación de conformidad exigibles**;
- **no convierte automáticamente una colección de evidencias en cumplimiento legal**.

Su objetivo es reducir trabajo técnico repetitivo y mejorar la trazabilidad de información que ya forma parte de los procesos de seguridad software.

