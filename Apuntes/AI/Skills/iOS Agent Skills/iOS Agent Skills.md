---
tags:
  - ai/skills
  - ai/ios-agent-skills
  - ios
source: https://github.com/SH-OH/ios-agent-skills
---
# iOS Agent Skills

> [!abstract] Resumen
> Conjunto de diez skills para agentes de programación centradas en Swift, iOS, arquitectura, revisión y herramientas de desarrollo. Incluye una guía de backend general y dos especializaciones para iOS 26.

## 📦 Skills del conjunto

| Skill | Nombre | Función |
| --- | --- | --- |
| [[Swift Coding Standards]] | `swift-coding-standards` | Aplicar convenciones de Swift que priorizan claridad y mantenibilidad. |
| [[SwiftUI Patterns]] | `swiftui-patterns` | Construir vistas SwiftUI componibles y gestionar su estado correctamente. |
| [[iOS Architecture]] | `ios-architecture` | Diseñar la estructura de aplicaciones Swift y las responsabilidades de sus módulos. |
| [[iOS Code Review]] | `ios-code-review` | Revisar cambios Swift/iOS en seguridad, memoria, concurrencia y calidad. |
| [[iOS Security Review]] | `ios-security-review` | Revisar vulnerabilidades de apps iOS con referencia a OWASP Mobile Top 10. |
| [[Swift TDD Workflow]] | `swift-tdd-workflow` | Guiar el desarrollo test-first mediante el ciclo rojo, verde y refactorización. |
| [[Xcode Build Resolver]] | `xcode-build-resolver` | Diagnosticar y corregir errores de compilación de Swift y Xcode con cambios acotados. |
| [[iOS Backend Patterns]] | `ios-backend-patterns` | Orientar el diseño de servicios backend que pueden dar soporte a una app. |
| [[Liquid Glass]] | `liquid-glass` | Aplicar el lenguaje visual Liquid Glass introducido en iOS 26. |
| [[Foundation Models]] | `foundation-models` | Integrar capacidades de lenguaje ejecutadas en el dispositivo con FoundationModels. |

## 🧭 Cómo elegir

- Para implementar: [[Swift Coding Standards]], [[SwiftUI Patterns]] e [[iOS Architecture]].
- Para revisar cambios y riesgos: [[iOS Code Review]] e [[iOS Security Review]].
- Para pruebas y compilación: [[Swift TDD Workflow]] y [[Xcode Build Resolver]].
- Para servicios: [[iOS Backend Patterns]], orientada a Node.js, Express y Next.js.
- Para funciones de iOS 26: [[Liquid Glass]] y [[Foundation Models]].

## ⚙️ Instalación y uso

Según el README del repositorio:

```bash
# Instalar todas
npx skills add SH-OH/ios-agent-skills -y

# Instalar una skill concreta
npx skills add SH-OH/ios-agent-skills --skill swift-coding-standards
```

El repositorio indica que las skills se activan en tareas relevantes. También se pueden solicitar describiendo el trabajo, por ejemplo: «Revisa la seguridad de este código Swift» o «Ayúdame a resolver este error de Xcode».

> [!note] Fuente y vigencia
> Notas basadas en el repositorio, commit `d146bef`. Las APIs y requisitos de Apple evolucionan; consulta la documentación oficial vigente antes de implementarlos.

## 🔗 Relacionado

* [[Android Review]]
* [Repositorio iOS Agent Skills](https://github.com/SH-OH/ios-agent-skills)
