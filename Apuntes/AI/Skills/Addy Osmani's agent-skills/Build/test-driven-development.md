---
alias:
  - Skill test-driven-development
  - TDD Addy Osmani
  - Red Green Refactor
tags:
  - ai/skills
  - addy-osmani
  - ai/build
  - dev/tdd
  - dev/testing
source: https://github.com/addyosmani/agent-skills/blob/main/skills/test-driven-development/SKILL.md
type: skill-note
---
> [!abstract] **Propósito**
> Conduce el desarrollo con tests usando el bucle **Red-Green-Refactor**. Para correcciones de bugs, **reproduce el bug con un test antes de intentar arreglarlo**. Los tests son la prueba: *"parece correcto"* no está hecho. Un codebase con buenos tests es el **superpoder** de un agente de IA; uno sin tests, un pasivo.

> [!warning] ⚠️ Diferencia doctrinal con Matt Pocock
> Esta skill incluye **REFACTOR como tercer paso del ciclo**. La nota de Matt [[tdd]] afirma explícitamente que *"la refactorización no es parte del bucle"* y la traslada al code review. **No son contradictorias, pero no son intercambiables**: si adoptas una como router principal, decide cuál aplicas.

---

## 🎯 ¿Cuándo usarla?

- Implementando **cualquier lógica o comportamiento** nuevo.
- Arreglando **cualquier bug** (el *Prove-It Pattern*).
- Modificando funcionalidad existente.
- Añadiendo manejo de casos límite.
- Cualquier cambio que **pueda romper** comportamiento existente.

**Cuándo NO:** cambios de configuración puros, actualizaciones de documentación, o contenido estático sin impacto de comportamiento.

---

## 🧭 Descubre el stack primero

El ciclo TDD es universal; **los comandos no**. Antes de escribir el primer test, descubre cómo testea *este* repo:
- **Lenguaje y build** — `package.json`, `pom.xml`/`build.gradle`, `pyproject.toml`, `go.mod`, `Cargo.toml`, `Makefile`
- **Wrappers versionados** — prefiere `./gradlew`, `./mvnw`, `make test` sobre tools globales
- **Framework y config** — y cómo corre un test focalizado vs. la suite completa
- **Convenciones existentes** — dónde viven los tests, cómo se nombran, qué patrones siguen los vecinos
- **Comandos documentados** — README, CONTRIBUTING y los workflows de CI muestran los comandos que **realmente bloquean merges**

> [!important] Nunca asumas `npm test`
> Un proyecto Gradle, Cargo o pytest tiene su propio equivalente. Usa el comando focalizado del repo durante el bucle y el de suite completa antes de terminar.

---

## 🔄 El ciclo

```
    RED                GREEN              REFACTOR
 Write a test    Write minimal code    Clean up the
 that fails  ──→  to make it pass  ──→  implementation  ──→ (repetir)
      │                  │                    │
      ▼                  ▼                    ▼
   Test FAILS        Test PASSES         Tests still PASS
```

1. **RED.** El test **debe fallar**. Un test que pasa al instante no prueba nada.
2. **GREEN.** El **mínimo** código para hacerlo pasar. No sobre-ingenieries.
3. **REFACTOR.** Con los tests en verde, mejora el código **sin cambiar comportamiento**: extrae lógica compartida, mejora nombres, quita duplicación, optimiza. Corre los tests tras cada paso.

---

## 🔍 El Prove-It Pattern (bugs)

Cuando llega un reporte de bug, **no empieces intentando arreglarlo.** Empieza escribiendo un test que lo reproduzca:

```
Bug reportado → test de reproducción → FALLA (confirma el bug)
             → implementar el fix → PASA (prueba el fix)
             → suite completa (sin regresiones)
```

La separación garantiza que el test se escribe **sin conocimiento del fix**, lo que lo hace más robusto. Para bugs complejos, **spawn de un subagente para escribir el test de reproducción** y que el agente principal verifique que falla antes de implementar.

---

## 🔺 La pirámide de tests

```
          ╱╲
         ╱  ╲         E2E (~5%)
        ╱    ╲        Flujos completos de usuario, navegador real
       ╱──────╲
      ╱        ╲      Integración (~15%)
     ╱          ╲     Interacciones de componentes, fronteras de API
    ╱────────────╲
   ╱              ╲   Unitarios (~80%)
  ╱                ╲  Lógica pura, aislados, milisegundos
 ╱──────────────────╲
```

**The Beyonce Rule:** *si te gustó, deberías haberle puesto un test.* Los cambios de infraestructura, refactorizaciones y migraciones **no son responsables de atrapar tus bugs — tus tests lo son**.

### Tamaños de test (modelo de recursos)

| Tamaño | Restricciones | Velocidad | Ejemplo |
| :--- | :--- | :--- | :--- |
| **Small** | Un proceso, sin I/O, sin red, sin BD | Milisegundos | Funciones puras, transformaciones |
| **Medium** | Multi-proceso, solo localhost, sin servicios externos | Segundos | Tests de API con BD de test, componentes |
| **Large** | Multi-máquina, servicios externos permitidos | Minutos | E2E, benchmarks, staging |

### Guía de decisión
- ¿Lógica pura sin efectos? → **Unit (small)**
- ¿Cruza una frontera (API, BD, FS)? → **Integración (medium)**
- ¿Flujo crítico de usuario end-to-end? → **E2E (large)**, limitado a rutas críticas

---

## ✍️ Cómo escribir buenos tests

- **Testea estado, no interacciones.** Afirma sobre el *resultado*, no sobre qué métodos se llamaron. Los tests que verifican secuencias de llamadas se rompen al refactorizar aunque el comportamiento no cambie.
- **DAMP sobre DRY.** En código de producción DRY suele ser lo correcto; **en tests, DAMP** (*Descriptive And Meaningful Phrases*) es mejor. Cada test debe leerse como una especificación y contar una historia completa sin obligar a trazar helpers compartidos.
- **Prefiere implementaciones reales sobre mocks.** Orden de preferencia: `Real` → `Fake` (en memoria) → `Stub` (datos fijos) → `Mock` (interacciones, con prudencia). **MOCKea solo cuando** lo real es muy lento, no determinista, o tiene efectos que no puedes controlar.
- **Usa Arrange-Act-Assert.**
- **Una aserción por concepto.** Un test que valida "títulos correctamente" mezclando tres comportamientos es tres tests.
- **Nombra los tests descriptivamente**, como una especificación.

### Anti-patrones de testing

| Anti-patrón | Problema | Arreglo |
| :--- | :--- | :--- |
| Testear detalles de implementación | Se rompen al refactorizar | Testea entradas y salidas, no estructura interna |
| Tests flaky (timing, orden) | Erosionan la confianza | Aserciones deterministas, estado aislado |
| Testear código del framework | Desperdicia tiempo en comportamiento de terceros | Solo testea **tu** código |
| Abuso de snapshots | Snapshots grandes que nadie revisa | Úsalos con Sporádica y revisa cada cambio |
| Sin aislamiento | Pasan solos, fallan juntos | Cada test monta y desmonta su estado |
| Mockearlo todo | Pasan los tests, se rompe producción | Prefiere real > fake > stub > mock |

---

## ⚠️ Anti-racionalizaciones

| Excusa | Realidad |
| :--- | :--- |
| "Escribiré los tests cuando el código funcione" | No lo harás. Y los tests escritos a posteriori testean implementación, no comportamiento |
| "Esto es demasiado simple para testear" | El código simple se complica. El test documenta el comportamiento esperado |
| "Los tests me ralentizan" | Te ralentizan ahora. Te aceleran cada vez que toques el código después |
| "Ya lo probé a mano" | El testing manual no persiste. El cambio de mañana puede romperlo sin forma de saberlo |
| "El código se explica solo" | Los tests **son** la especificación |
| "Es solo un prototipo" | Los prototipos se vuelven código de producción. Empieza sin deuda de tests |

---

## 🔗 Relaciones

**En el flujo de Addy:** junto con [[incremental-implementation]] en la Fase BUILD. [[browser-testing-with-devtools]] lo complementa para lo que corre en navegador. La persona [[test-engineer]] es la que ejecuta esta disciplina como rol de QA (estrategia de test, escenarios cubiertos, análisis de prioridad).

**Equivalencia Matt Pocock:** la nota [[tdd]] es **más restrictiva en un punto clave**: Matt insiste en que los tests se escriban solo en las **costuras públicas (*seams*)** y prohíbe código de producción sin un test que haya fallado antes. Addy no usa el concepto de costura (usa **Boundaries** de validación en [[api-and-interface-design]] y la pirámide/tamaños).

**También solapa con:** [[constraint-driven-development]] (la cobertura es una de sus dimensiones medibles) · [[code-review-and-quality]] (revisar tests primero) · [[debugging-and-error-recovery]] (el Prove-It es su paso 1).

```bash
npx skills add addyosmani/agent-skills --skill test-driven-development
```
