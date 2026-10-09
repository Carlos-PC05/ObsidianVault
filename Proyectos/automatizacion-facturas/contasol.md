#facturas
En este documento se encontrarán todos los campos (el formato exacto) que deberá tener una factura en el software Contasol. Necesario para el desarrollo de este proyecto.

> Fuente: capturas de pantalla de los formularios reales de Contasol (`docs/NuevaFactura.jpeg`, `docs/formNuevoProv.jpeg`, `docs/ParamNuevoProv.jpeg`, `docs/Clave190.jpeg`, `docs/ClaveOpIntracom.jpeg`) para los §1 y §2 (entrada manual en la UI), y las plantillas oficiales de importación de Contasol (`src/plantillas/*.xlsx`) para el §3 (formato exacto de los ficheros de importación).

---

## 1. Formulario "Nuevo registro de factura recibida"

Ruta: `Libro facturas recibidas > Factura Recibida` (nuevo registro).

### 1.1 Cabecera / identificación

| Campo                    | Tipo                     | Notas                                                                             |
| ------------------------ | ------------------------ | --------------------------------------------------------------------------------- |
| Nº de registro           | Numérico                 | Autogenerado/correlativo (valor por defecto `0`)                                  |
| Tipo de gasto            | Desplegable              | Ej. "Consumos de explotación - [G01]" — catálogo de tipos de gasto de Contasol    |
| Actividad                | Desplegable              | Actividad económica del cliente/receptor. Ej. "SERV. NATUROPARIA, ACUPUNTURA, P…" |
| Número de factura        | Texto                    | Nº de factura del proveedor                                                       |
| Serie                    | Texto                    | Serie de la factura (opcional en muchos proveedores)                              |
| Fecha del registro       | Fecha                    | Fecha de asiento/registro contable                                                |
| Fecha expedición         | Fecha                    | Fecha de emisión de la factura                                                    |
| Fecha registro operación | Fecha                    | Fecha de la operación, si difiere de la de expedición                             |
| Proveedor                | Lookup + código numérico | Botón de búsqueda; código de tercero (por defecto `0`)                            |
| Nombre                   | Texto                    | Nombre fiscal del proveedor (se autocompleta al elegir proveedor)                 |
| Identificación fiscal    | Desplegable + texto      | Tipo de identificación (`N.I.F.`, etc.) + número                                  |
| Clave operación          | Desplegable              | Ej. "Operación habitual"                                                          |

### 1.2 Tipo de operación y deducibilidad

| Campo                 | Tipo        | Notas                                                      |
| --------------------- | ----------- | ---------------------------------------------------------- |
| Tipo de Operación     | Desplegable | Ej. "General"                                              |
| Deducción             | Desplegable | Ej. "Deducible"                                            |
| % deducible (IVA)     | Numérico    | Por defecto `100,00`                                       |
| Deducción en I.R.P.F. | Desplegable | Ej. "Deducible"                                            |
| % deducible (IRPF)    | Numérico    | Por defecto `100,00`                                       |
| Ayuda en el cálculo   | Desplegable | Ej. "Un tipo de IVA" — asistente para repartir el desglose |

### 1.3 Casillas de verificación

- Factura acogida a criterio de caja
- Operaciones no sujetas
- Incluir en volumen de operaciones (marcada por defecto)
- Bien de Inversión IRPF
- Bien de Inversión IVA

### 1.4 Desglose de importes (tabla, hasta 5 líneas + exenta)

Columnas: **Base Imponible | % IVA | Cuota | % RE | Cuota (RE) | % IRPF**

- Fila "Exenta": base imponible sin IVA (campo solo lectura salvo la base)
- Filas `1` a `5`: una fila por cada tipo impositivo de IVA presente en la factura (permite facturas multitipo: 21/10/4/0 %, con recargo de equivalencia)
- Junto a `% IRPF` hay un desplegable adicional (tipo de retención aplicable)

Totales calculados:

| Campo           | Notas                                     |
| --------------- | ----------------------------------------- |
| Total operación | Suma de bases + cuotas de IVA/RE          |
| Suplidos        | Importe sin IVA que no computa como gasto |
| Total Factura   | Total operación + suplidos − IRPF         |

### 1.5 Pagos

Tabla con columnas: **Fecha | Importe | E** (estado del pago), con botones para añadir/editar/eliminar líneas de pago.

### 1.6 Otros

| Campo           | Tipo                                                      | Notas                                        |
| --------------- | --------------------------------------------------------- | -------------------------------------------- |
| Observaciones   | Texto libre (multilínea)                                  |                                              |
| Imagen asociada | Ruta de fichero + botones "…", "Escanear", "Abrir imagen" | Adjunta el PDF/imagen original de la factura |

Botones: **Aceptar** / **Cancelar**.

### 1.7 Desplegable tipo de gasto

### 1.8 Desplegable de Actividad

Las actividades se crean manualmente y se le asignan a un cliente, los gastos se agrupan por actividades y dentro de las actividades por tipo de gasto

### 1.9 Clave operación

El 90% de las veces se usa Operación Habitual. Solo se cambia cuando es Inversión de Sujeto Pasivo y Adquisición intracomunitaria de bienes (cuando compras bienes en la unión europea).

### 1.10 Tipo de Operación

General o Intracomunitario

### 1.11 Deducción

Deducible, No deducible, prorrata especial y prorrata de la decucción.

### 1.12 Deducción en IRPF

Deducible o no deducible

---

## 2. Formulario "Nuevo proveedor"

### 2.1 Identificación

| Campo            | Tipo                       | Notas                                                               |
| ---------------- | -------------------------- | ------------------------------------------------------------------- |
| Imagen/logo      | Imagen                     | Opcional                                                            |
| Código           | Numérico                   | Código de tercero, por defecto `0`                                  |
| NIF              | Desplegable (tipo) + texto | Tipo de identificación (`N.I.F.`) + número, con enlace "Validación" |
| Nombre fiscal    | Texto                      | Con acceso rápido "F1 buscar en la biblioteca de terceros"          |
| Nombre comercial | Texto                      |                                                                     |

### 2.2 Pestaña "Contacto"

| Campo              | Tipo                            | Notas                                                                 |
| ------------------ | ------------------------------- | --------------------------------------------------------------------- |
| Domicilio          | Texto (tipo de vía + dirección) | Dos campos: tipo/código de vía y dirección                            |
| C.P. / Población   | Texto                           | Código postal + población                                             |
| Provincia          | Texto                           |                                                                       |
| País               | Desplegable + código numérico   | Por defecto "ESPAÑA" (código `724`)                                   |
| Teléfono           | Texto                           |                                                                       |
| Móvil              | Texto                           |                                                                       |
| Fax                | Texto                           |                                                                       |
| Banco              | Lookup                          | Botón de selección de entidad bancaria                                |
| Nº de cuenta       | 4 campos numéricos              | Formato CCC clásico (entidad / oficina / dígitos de control / cuenta) |
| IBAN               | Texto + botón "C"               | El botón "C" probablemente calcula el IBAN a partir del CCC           |
| BIC                | Texto                           |                                                                       |
| Pers. Contacto     | Texto                           | Persona de contacto en el proveedor                                   |
| Correo electrónico | Texto + icono QR                |                                                                       |
| Web                | Texto + icono QR                |                                                                       |
| Mensaje emergente  | Texto                           | Aviso que salta al seleccionar este proveedor                         |

### 2.3 Pestaña "Parámetros"

Define los valores por defecto que se aplican al proveedor al usarlo en una factura (evita rellenarlos cada vez en el formulario de factura recibida, ver §1).

| Campo                                            | Tipo                           | Notas                                                                                                                                                           |
| ------------------------------------------------ | ------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Actividad                                        | Desplegable                    | Por defecto "No seleccionada"                                                                                                                                   |
| Tipo de operaciones                              | Desplegable                    | Interior, Intracomunitario, Importación, Agricultura Ganadería y Pesca                                                                                          |
| Clave modelo 190                                 | Desplegable (código de letra)  | Solo relevante según el tipo de operación; catálogo en §2.5                                                                                                     |
| Subclave                                         | Desplegable                    | Subclave del modelo 190 asociada a la clave elegida                                                                                                             |
| Tipo de gasto                                    | Desplegable                    | Por defecto "[Sin predefinir]" — mismo catálogo que "Tipo de gasto" del formulario de factura (§1.1); catálogo en §2.7                                          |
| Tipo de IVA predefinido                          | Desplegable                    | Manual, Normal, Reducido, Superreducido, Normal + recargo de equivalencia, Reducido + recargo de equivalencia, Superreducido + recargo de equivalencia          |
| Acogido al Régimen especial del criterio de caja | Checkbox                       |                                                                                                                                                                 |
| Excluir del 347                                  | Checkbox                       |                                                                                                                                                                 |
| Tipo de deducción                                | Desplegable + % Deducción      | Ej. "Deducible" — por defecto `100,00`                                                                                                                          |
| Tipo de deducción de IRPF                        | Desplegable + % Deducción      | Ej. "Deducible" — por defecto `100,00`                                                                                                                          |
| Tipo de retenciones                              | Desplegable + % Retención      | Por defecto "[Sin predefinir]" — dineraria, en especie, dineraria agrícola, en especie agrícola, dinerario arrendamiento, en especie arrendamiento, empresarial |
| Clave de operación habitual                      | Desplegable                    | Ej. "Operación habitual" — mismo campo que "Clave operación" del formulario de factura (§1.1/§1.9)                                                              |
| Ayuda en el cálculo                              | Desplegable                    | Un tipo de IVA, Varios tipos de IVA, Exenta de IVA, 1 tipo IVA con IRPF, Varios tipos de IVA con IRPF                                                           |
| Clave op. intracomunitaria                       | Desplegable (código de letra)  | Solo aplica cuando "Tipo de operaciones" = Intracomunitario; catálogo en §2.6                                                                                   |
| Local arrendado                                  | Desplegable + tipo (Locales/…) |                                                                                                                                                                 |
| Descripción operación SII                        | Texto libre (multilínea)       |                                                                                                                                                                 |

### 2.4 Pestaña "Observaciones"

Pendiente de capturar.

Botones: **Aceptar** / **Cancelar**.

### 2.5 Catálogo "Clave modelo 190"

Clasifica el tipo de renta a efectos del modelo 190 (retenciones e ingresos a cuenta del IRPF). Las descripciones se muestran truncadas en la captura original (`docs/Clave190.jpeg`); se han recogido íntegras cuando eran legibles.

| Clave | Descripción                                                                                                            |
| ----- | ---------------------------------------------------------------------------------------------------------------------- |
| A     | Rendimientos del trabajo: Empleados por cuenta ajena en general.                                                       |
| B     | Rendimientos del trabajo: Pensionistas y perceptores de haberes pasivos y demás prestaciones previstas en el artículo… |
| C     | Rendimientos del trabajo: Prestaciones o subsidios por desempleo.                                                      |
| D     | Rendimientos del trabajo: Prestaciones por desempleo abonadas en la modalidad de pago único.                           |
| E     | Rendimientos del trabajo: Retribuciones a consejeros y administradores.                                                |
| F     | Rendimientos del trabajo: Cursos, conferencias, seminarios y similares y elaboración de obras literarias, artísticas…  |
| G     | Rendimientos de actividades económicas: actividades profesionales.                                                     |
| H     | Rendimientos de actividades económicas: actividades agrícolas, ganaderas y forestales y actividades económicas…        |
| I     | Rendimientos de actividades económicas: rendimientos a que se refiere el artículo 75.2, letra b), del Reglamento…      |
| J     | Imputación de rentas por cesión de derechos de imagen: contraprestaciones a que se refiere el artículo 92.8…           |
| K     | Premios y ganancias patrimoniales de los vecinos derivadas de los aprovechamientos forestales en montes públicos…      |
| L     | Rentas exentas y dietas exceptuadas de gravamen.                                                                       |

### 2.6 Catálogo "Clave op. intracomunitaria"

Solo aplica cuando "Tipo de operaciones" = Intracomunitario. Descripciones truncadas en la captura original (`docs/ClaveOpIntracom.jpeg`).

| Clave            | Descripción                                                                                                                                                                                  |
| ---------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Sin predefinir] | Valor por defecto                                                                                                                                                                            |
| A                | Adquisiciones intracomunitarias sujetas.                                                                                                                                                     |
| C                | Sustituciones del empresario o profesional destinatario de los bienes expedidos o transportados a otro Estado miembro en el marco de acuerdos de ventas de bienes en consigna.               |
| D                | Devoluciones de bienes desde otro Estado miembro al que previamente fueron enviados desde el TAI en el marco de acuerdos de ventas de bienes en consigna.                                    |
| E                | Entregas intracomunitarias exentas, excepto las entregas en otros Estados miembros subsiguientes a adquisiciones intracomunitarias exentas en el marco de operaciones triangulares.          |
| H                | Entregas intracomunitarias de bienes posteriores a una importación exenta, de acuerdo con el artículo 27.12° de la Ley del Impuesto sobre el Valor Añadido, efectuadas por el representante… |
| I                | Adquisiciones intracomunitarias de servicios localizadas en el TAI prestadas por empresarios o profesionales establecidos en otros EM cuyo destinatario es el declarante.                    |
| M                | Entregas intracomunitarias de bienes efectuadas en el marco de acuerdos de ventas de bienes en consigna, con arreglo al artículo 27.12° de la Ley del Impuesto sobre el Valor Añadido.       |
| R                | Transferencias de bienes efectuadas en el marco de acuerdos de ventas de bienes en consigna.                                                                                                 |
| S                | Prestaciones intracomunitarias de servicios realizadas por el declarante.                                                                                                                    |
| T                | Entregas en otros Estados miembros subsiguientes a adquisiciones intracomunitarias exentas en el marco de operaciones triangulares. Cuando se realice alguna entrega de bienes…              |

### 2.7 Catálogo "Tipo de gasto"

Mismo campo que "Tipo de gasto" del formulario de factura (§1.1) y de la pestaña "Parámetros" del proveedor (§2.3). Libro: Compras/Gastos.

| Código | Denominación                                        |
| ------ | --------------------------------------------------- |
| G01    | Consumos de explotación                             |
| G02    | Sueldos y salarios                                  |
| G03    | Seguridad Social a cargo de la empresa              |
| G04    | Otros gastos de personal                            |
| G05    | Arrendamientos y cánones                            |
| G06    | Reparaciones y conservación                         |
| G07    | Servicios de profesionales independientes           |
| G08    | Otros servicios exteriores                          |
| G09    | Tributos fiscalmente deducibles                     |
| G10    | Gastos financieros                                  |
| G11    | Amortizaciones                                      |
| G12    | Pérdidas por deterioro del valor de los elementos   |
| G13    | Incentivos al mecenazgo. Conv. Colab. Interés Gen.  |
| G14    | Incentivos al mecenazgo. Gastos Activ. Interés Gen. |
| G15    | Otros conceptos fiscalmente deducibles              |
| G16    | Variación de existencias                            |
| G17    | Suministros (agua, gas, electricidad, telefonía..)  |
| G18    | Gastos de manutención del contribuyente             |
| G19    | Otros consumos de explotación                       |
| G20    | Seg. Soc. o aport. a mut. alt. del tit. de la act.  |
| G21    | Indemnizaciones                                     |
| G22    | Dietas y asignaciones de viajes del pers. empleado  |
| G23    | Aport. a sist. de prev. soc. imput. a pers. emp.    |
| G24    | Otros suministros                                   |
| G25    | Primas de seguros                                   |
| G26    | IVA soportado (R.E. y/o compensación agrícola)      |
| G27    | Dotaciones del ejercicio para amort. Inm. Material  |
| G28    | Dotaciones del ejercicio para amort. Inm. Inmat.    |
| G29    | Pérdidas por insolvencias de deudores               |
| G30    | Provisiones fiscalmente deducibles                  |
| G45    | Seguridad Social del titular de la actividad        |
| G46    | Aportaciones mutualidades altern. titular activid.  |
| G98    | INVERSIONES                                         |
| G99    | SEGURO AUTONOMO                                     |

---

## 3. Formatos de importación

Podemos importar 4 tipos de ficheros distintos. Son plantillas `.xlsx` oficiales de Contasol, ya incorporadas al repo:

| Fichero                   | Contenido                             | Nº de columnas |
| ------------------------- | ------------------------------------- | -------------- |
| `src/plantillas/PRO.xlsx` | Fichero de proveedores                | 39             |
| `src/plantillas/CLI.xlsx` | Fichero de clientes                   | 40             |
| `src/plantillas/FRE.xlsx` | Libro de facturas recibidas (gastos)  | 77             |
| `src/plantillas/FEM.xlsx` | Libro de facturas emitidas (ingresos) | 67             |

> El fichero de proveedores se llama `PRO.xlsx`, no `PRV.xlsx` (nombre usado por error en una versión anterior de este documento).

Cada plantilla tiene una única hoja (`Hoja1`): la fila 1 es la cabecera con los nombres de columna y la fila 2 en adelante son los registros, uno por fila. Las plantillas tal cual están en `src/plantillas/` traen además una fila de ejemplo con datos ficticios (proveedor/cliente/factura inventados) a modo de referencia de tipos — no debe copiarse a los ficheros reales que genere el exportador.

Referencia oficial de Contasol/TeamSystem sobre estas plantillas: [Plantillas de importación — Contabilidad en Estimación Directa y Objetiva](https://ayuda.teamsystem.es/hc/es/articles/32074535585809-Plantillas-de-importaci%C3%B3n-Contabilidad-en-Estimaci%C3%B3n-Directa-y-Objetiva) (es un índice de artículos; no detalla el formato celda a celda — para eso, la fuente fiable son las propias plantillas `.xlsx`, inspeccionadas más abajo).

### 3.1 Reglas de formato comunes a las 4 plantillas

- **Fechas** (`Fecha`, `Fecha de expedición de la factura`, `Fecha de operación de la factura`...): celda de tipo fecha nativa de Excel (nº de serie), no texto. Formato de visualización `mm-dd-yy`. Al generar el fichero hay que escribir un `datetime.date`, nunca un string con la fecha formateada a mano.
- **Importes** (bases, cuotas de IVA, recargo, retención, totales): celda numérica, formato de visualización `#,##0.00 "€"`. El valor subyacente es un número decimal normal (p. ej. `2000`, `420`, `2420`) — el separador de miles y el símbolo `€` son solo de visualización de Excel. Hay que escribir siempre un número (nunca texto con coma decimal); en Python, convertir el `Decimal` del contrato (`src/modelos.py`) a `float`/número al volcarlo a la celda.
- **Porcentajes** (`% de IVA N`, `% de recargo N`, `% de retención`, `% de deducción`...): número entero/decimal literal (`21`, `10`, `4`), **no** una fracción (`0.21`). Coincide con el criterio ya documentado en §1.4/§2.3.
- **Campos "código"** que apuntan a una entidad o a una entrada de catálogo (`Proveedor`, `Cliente`, `Actividad`, `Tipo de operación`, `Clave de operación`, `Tipo de retención`, `Ayuda en el cálculo`, `Clave modelo 190`...): número entero, el identificador interno de Contasol para esa fila/opción — no el texto legible. Esto implica que el proveedor/cliente debe existir ya en Contasol (o importarse antes vía `PRO.xlsx`/`CLI.xlsx`) para que el código usado en la fila de factura apunte a la entidad correcta; no basta con el NIF/nombre.
- **Texto libre** (nombre, NIF, descripción, observaciones): celda de texto normal.
- No hay hojas auxiliares con catálogos dentro del propio fichero de importación; los catálogos (tipo de gasto, clave 190, etc.) son internos de Contasol y ya están documentados en §1 y §2.

### 3.2 `PRO.xlsx` — Fichero de proveedores

Columnas, en orden: `Código, NIF, Nombre, Nombre comercial, Sigla del domicilio, Domicilio, Número del domicilio, Código postal, Población, Provincia, Código del país, Teléfono, Fax, Móvil, E-mail, Web, Persona de contacto, Mensaje emergente, Banco, Entidad, Oficina, Dígito de control, Cuenta del banco, Tipo de operaciones, Tipo de retención, % de IVA, % de recargo, Porcentaje de retención, Cuenta banco, Código IBAN, Identificación fiscal, Clave de operación habitual, Ayuda en el cálculo, Tipo de IVA predefinido, Clave modelo 190, Subclave modelo 190, Código BIC, Actividad, Descripción operación SII`.

Corresponde a los campos ya documentados en §2.1–§2.3 (identificación + pestaña "Parámetros"); `Clave modelo 190`/`Subclave modelo 190` usan el catálogo de §2.5, y `Clave de operación habitual`/`Tipo de operaciones` los de §1.9/§2.3.

### 3.3 `CLI.xlsx` — Fichero de clientes

Mismo criterio que `PRO.xlsx` pero para clientes (facturas emitidas). Columnas: `Código, Nombre, NIF, Nombre comercial, Sigla del domicilio, Domicilio, Número del domicilio, Código postal, Población, Provincia, Código del país, Teléfono, Fax, Móvil, E-mail, Web, Persona de contacto, Mensaje emergente, Banco, Entidad, Oficina, Dígito de control, Cuenta del banco, Tipo de operaciones, Tipo de retención, Porcentaje de IVA, Porcentaje de recargo, Porcentaje de retención, Cuenta banco, Código IBAN, Identificación fiscal, Clave de operación habitual, Ayuda en el cálculo, Tipo de IVA predefinido, Código BIC, Actividad, Descripción SII, Causa de exención, Operación No Sujeta, Aplicar el Recargo de Equivalencia`.

Diferencias con `PRO.xlsx`: no tiene `Clave modelo 190`/`Subclave` (es propio de retenciones IRPF de proveedores); a cambio añade `Causa de exención`, `Operación No Sujeta` y `Aplicar el Recargo de Equivalencia`.

### 3.4 `FRE.xlsx` — Libro de facturas recibidas (gastos)

Es la plantilla más relevante para el pipeline: es el formato de salida de la fase `exportar` para las facturas de gasto (el foco actual del MVP). Columnas: `Tipo de movimiento, Código, Actividad, Tipo de compra o gasto, Fecha, Fecha de expedición de la factura, Proveedor, Nombre, Identificación fiscal, N.I.F., Clave de operación, Número de factura, Número de facturas, Primer número, Último número, Número de factura rectificada, Situación del inmueble arrendado, Referencia catastral del inmueble arrendado, Base imponible a coste, Tipo de operación, Incluir importaciones en la liquidación, Tipo de operación intracomunitaria, Días de plazo, Estado miembro origen o destino del bien, Descripción de los bienes, Otras facturas o documentación, Descripción del gasto, Base exenta factura, Base 1, Base 2, Base 3, Base 4, Base 5, % de IVA 1, % de IVA 2, % de IVA 3, % de IVA 4, % de IVA 5, Importe de IVA 1, Importe de IVA 2, Importe de IVA 3, Importe de IVA 4, Importe de IVA 5, % de recargo 1, % de recargo 2, % de recargo 3, % de recargo 4, % de recargo 5, Importe de recargo 1, Importe de recargo 2, Importe de recargo 3, Importe de recargo 4, Importe de recargo 5, Tipo de retención, Porcentaje de retención, Importe de retención, Total de la operación, Importe de suplidos, Total, Tipo de deducción, % de deducción, Incluir en el modelo 347, Es bien de inversión, Código del bien de inversión, Tipo de deducción en IRPF, % de deducción en IRPF, Imagen asociada, Trabajador, Base IRPF, Importe Seguridad Social, Importe IRPF, Base IRPF, Tipo de rectificativa, Fecha de operación de la factura, Serie, Operación no sujeta de IVA, Bien de inversión de IVA`.

Esto confirma el modelo de `desglose_iva` de `src/modelos.py`: hasta 5 bloques `Base N` / `% de IVA N` / `Importe de IVA N` (+ recargo N) en columnas paralelas, más una `Base exenta factura` aparte — el mismo diseño que la tabla de §1.4. `Tipo de movimiento` distingue una factura propiamente dicha (`F`) de otros apuntes del libro sin factura, como una nómina (`G`, visto en la fila de ejemplo de la plantilla).

### 3.5 `FEM.xlsx` — Libro de facturas emitidas (ingresos)

Misma estructura que `FRE.xlsx` pero para ingresos: usa `Cliente`/`Tipo de venta o ingreso` en vez de `Proveedor`/`Tipo de compra o gasto`, y no tiene las columnas propias de gasto (`% de deducción`, bien de inversión, retenciones de trabajador/Seguridad Social). Fuera del alcance actual del MVP (centrado en facturas recibidas); se documenta aquí por completitud, ya que la plantilla forma parte del repo.

---

## 4. Pendiente

- Descripciones íntegras de las claves `Clave modelo 190` y `Clave op. intracomunitaria` marcadas con "…" (aparecen cortadas por el borde de la captura original) -> no es crítico.
- ~~**Próximo paso:** crear el test dorado ("golden test") de los formatos de importación~~ — hecho para `FRE.xlsx` (facturas recibidas, el foco actual del MVP) y `FEM.xlsx` (facturas emitidas): `src/exportador_contasol.py` genera la fila (`fila_fre`/`fila_fem`) y `tests/test_exportador_contasol.py` la compara celda a celda contra `tests/fixtures/contasol_prueba/fre_dorado.xlsx`/`fem_dorado.xlsx`. `fila_fre` recibe una `Factura` del contrato; `fila_fem` recibe los datos del cliente como parámetros sueltos porque el contrato no modela facturas emitidas (no hay `nombre_receptor`). En ambos casos los códigos de catálogo de Contasol (proveedor/cliente, actividad) se pasan a mano, porque el mapeo proveedor→cuenta aún no existe, y el resto de columnas de reglas de negocio (criterio de caja, modelo 347, bienes de inversión...) siguen en blanco. Falta el mismo test dorado para `PRO.xlsx` y `CLI.xlsx` si se necesitan antes de exportar esos libros.
