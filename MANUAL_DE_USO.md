# Manual de Uso: OCA Account Financial Reporting (Rama 18.0)

Este repositorio contiene un conjunto de módulos avanzados desarrollados por la **Odoo Community Association (OCA)** para la versión **18.0** de Odoo. Su principal objetivo es extender y mejorar las capacidades de informes financieros y contables de Odoo, ofreciendo mayor flexibilidad, soporte multidivisa avanzado, exportaciones a Excel personalizadas y herramientas de proyección de flujo de caja.

---

## Tabla de Contenidos
1. [Resumen de Módulos](#1-resumen-de-módulos)
2. [Dependencias del Sistema](#2-dependencias-del-sistema)
3. [Guía de Configuración y Uso por Módulo](#3-guía-de-configuración-y-uso-por-módulo)
   - [3.1. Account Financial Reports (`account_financial_report`)](#31-account-financial-reports-account_financial_report)
   - [3.2. Partner Statement (`partner_statement`)](#32-partner-statement-partner_statement)
   - [3.3. Account Move Line XLSX Export (`account_move_line_report_xls`)](#33-account-move-line-xlsx-export-account_move_line_report_xls)
   - [3.4. Tax Balance (`account_tax_balance`)](#34-tax-balance-account_tax_balance)
   - [3.5. Account Financial Reports Sale (`account_financial_report_sale`)](#35-account-financial-reports-sale-account_financial_report_sale)
   - [3.6. MIS Builder Cash Flow (`mis_builder_cash_flow`)](#36-mis-builder-cash-flow-mis_builder_cash_flow)
   - [3.7. P&L y Balance Sheet MIS Templates (`mis_template_financial_report`)](#37-pl-y-balance-sheet-mis-templates-mis_template_financial_report)
4. [Instalación en el Servidor](#4-instalación-en-el-servidor)

---

## 1. Resumen de Módulos

El repositorio está compuesto por los siguientes módulos:

| Módulo | Versión | Mantenedor | Descripción Corta |
| :--- | :--- | :--- | :--- |
| **`account_financial_report`** | 18.0.1.4.14 | OCA | Reportes contables clásicos (Mayor, Sumas y Saldos, IVA). |
| **`partner_statement`** | 18.0.1.2.0 | ForgeFlow | Estados de cuenta interactivos para clientes/proveedores con antigüedad de saldos. |
| **`account_move_line_report_xls`** | 18.0.1.0.0 | Noviat | Exportación directa a Excel (`.xlsx`) de apuntes contables seleccionados. |
| **`account_tax_balance`** | 18.0.1.0.3 | Tecnativa / Agile BG | Cálculo de balances de impuestos basados en un rango de fechas. |
| **`account_financial_report_sale`** | 18.0.1.0.0 | Tecnativa | Agrupación por dirección de entrega en el informe de Partidas Abiertas. |
| **`mis_builder_cash_flow`** | 18.0.1.0.1 | ADHOC SA | Pronóstico y visualización del flujo de caja integrado en MIS Builder. |
| **`mis_template_financial_report`** | 18.0.2.0.0 | Hunki Enterprises | Plantillas KPI genéricas de Pérdidas y Ganancias y Balance de Situación. |

---

## 2. Dependencias del Sistema

Para que estos módulos funcionen correctamente, es necesario contar con otros repositorios o módulos de la comunidad (módulos OCA). A continuación, se detallan las dependencias externas principales:

*   **`report_xlsx`** e **`report_xlsx_helper`**: Requeridos para todas las exportaciones a Excel (provenientes del repositorio [OCA/reporting-engine](https://github.com/OCA/reporting-engine)).
*   **`date_range`**: Necesario para los módulos que manejan periodos de fechas personalizados (disponible en [OCA/server-ux](https://github.com/OCA/server-ux)).
*   **`mis_builder`**: Motor de reportes financieros interactivos, obligatorio para los módulos de Cash Flow y plantillas de P&L (disponible en [OCA/mis-builder](https://github.com/OCA/mis-builder)).

---

## 3. Guía de Configuración y Uso por Módulo

### 3.1. Account Financial Reports (`account_financial_report`)

Este es el módulo central del repositorio. Sustituye y mejora los reportes financieros clásicos de Odoo con una interfaz asistida (wizards) y exportación limpia a PDF y Excel.

#### Reportes Disponibles:
1.  **Libro Mayor (General Ledger):** Detalle de transacciones por cuenta contable con filtros analíticos y por diario.
2.  **Balance de Sumas y Saldos (Trial Balance):** Vista jerárquica de cuentas con opciones para ocultar niveles superiores o filtrar por niveles específicos.
3.  **Partidas Abiertas (Open Items):** Detalle de apuntes pendientes de conciliación (deudas y créditos activos).
4.  **Antigüedad de Saldos (Aged Partner Balance):** Análisis de saldos pendientes distribuidos en intervalos temporales de vencimiento.
5.  **Libro Diario (Journal Ledger):** Registro cronológico de transacciones por diario contable.
6.  **Informe de IVA (VAT Report):** Reporte resumido de transacciones con etiquetas de impuestos.

> [!NOTE]
> Los informes de **Libro Mayor**, **Sumas y Saldos** y **Partidas Abiertas** son completamente compatibles con configuraciones multidivisa. Si una cuenta tiene una segunda divisa configurada, los reportes mostrarán automáticamente los saldos tanto en la divisa de la compañía como en la divisa extranjera.

#### Configuración de Intervalos de Antigüedad:
Puedes personalizar los rangos de días del reporte *Aged Partner Balance* (por ejemplo: de 0 a 15 días, de 16 a 30, etc.):
1.  Ve a **Ajustes ➔ Facturación ➔ OCA Aged Report Configuration**.
2.  Crea un nuevo registro de configuración de intervalos.
3.  Establece los límites inferiores de forma incremental (ejemplo: `15 ➔ 30 ➔ 60`). Odoo calculará los rangos automáticamente (0-15, 16-30, 31-60, y 61+).
4.  Puedes asignar una configuración predeterminada por compañía en este mismo menú de ajustes para que se cargue de forma automática al abrir el asistente del informe.

---

### 3.2. Partner Statement (`partner_statement`)

Permite generar y enviar estados de cuenta claros y profesionales a tus clientes o proveedores.

#### Tipos de Estados de Cuenta:
*   **Activity Statement (Estado de Actividad):** Muestra todos los movimientos (facturas, pagos, reembolsos) entre dos fechas seleccionadas. Los saldos anteriores al periodo se agrupan en una línea de "Saldo Inicial" (Forward Balance). Se divide por divisas.
*   **Detailed Activity Statement (Detalle de Actividad Completo):** Extensión del anterior que añade una estructura formal con desglose explícito de Saldo Inicial y Saldo Final de forma analítica.
*   **Outstanding Statement (Partidas Pendientes):** Lista cronológica de todas las transacciones que siguen abiertas (sin conciliar) hasta una fecha específica (facturas sin pagar, pagos sin asignar).

#### Configuración y Acceso:
1.  **Permisos de Usuario:** El usuario que requiera emitir estos estados debe tener al menos el permiso de **Facturación (Invoicing)** o **Administrador** en *Ajustes / Usuarios y Compañías / Usuarios*.
2.  **Activar Módulo:**
    *   Ve a **Facturación ➔ Configuración ➔ Ajustes**.
    *   En la sección **Partner Statements**, marca los checks correspondientes a *OCA Activity Statement* y/o *OCA Outstanding Statement*.
    *   Define los valores y comportamientos por defecto del reporte y haz clic en **Guardar**.

#### Cómo Emitirlo:
1.  Ve a la lista de contactos (Clientes, Proveedores o Contactos generales).
2.  Selecciona uno o varios registros utilizando las casillas de verificación de la vista lista.
3.  Haz clic en el botón **Acción** en la barra superior y selecciona **Partner Activity Statement** o **Partner Outstanding Statement**.
4.  Completa los parámetros del asistente:
    *   **Tipo de cuentas:** Cuentas por cobrar (Clientes) o Cuentas por pagar (Proveedores).
    *   **Buckets de Antigüedad:** Permite elegir si deseas mostrar u ocultar la tabla de antigüedad de saldos y si se medirá en días o meses.
    *   **Filtros avanzados:** Opción para excluir partners con saldo negativo o saldo cero, o filtrar partidas que aún no han vencido.

---

### 3.3. Account Move Line XLSX Export (`account_move_line_report_xls`)

Añade un botón de exportación rápida a Excel en la vista lista de los apuntes contables (`account.move.line`).

#### Instrucciones de Uso:
1.  Ve a **Contabilidad ➔ Asientos contables ➔ Apuntes contables** (Journal Items).
2.  Filtra y selecciona los apuntes que deseas exportar.
3.  Haz clic en el botón de exportación a Excel que añade el módulo en la barra superior.

#### Para Desarrolladores (Personalización):
El formato de la exportación está estructurado y puede heredarse o modificarse mediante código Python sobreescribiendo dos métodos del modelo `account.move.line`:
*   `_report_xlsx_fields`: Permite añadir, reordenar o eliminar columnas del Excel. Las columnas por defecto incluyen información como asiento, fecha, diario, partner, cuenta contable, debe, haber, saldo, conciliación, cuenta analítica, importes en divisa extranjera, producto, entre otros.
*   `_report_xlsx_template`: Permite formatear visualmente la plantilla de Excel resultante.

---

### 3.4. Tax Balance (`account_tax_balance`)

Calcula de forma dinámica los balances y bases imponibles de los impuestos dentro de un rango de fechas determinado. Este módulo es ampliamente utilizado por localizaciones específicas para generar reportes tributarios.

#### Instrucciones de Uso:
1.  Ve a **Contabilidad / Facturación ➔ Informes ➔ Taxes Balance**.
2.  Selecciona la compañía, el rango de fechas (usando el modelo `date_range`) y el tipo de movimientos (todos los asientos o solo asientos asentados).
3.  Haz clic en **Calcular/Abrir impuestos** para ver el desglose financiero de cada impuesto configurado en el sistema para ese periodo.

---

### 3.5. Account Financial Reports Sale (`account_financial_report_sale`)

Es un módulo de extensión que vincula los informes financieros con el flujo de ventas.

#### Funcionalidad Principal:
Añade una opción en el asistente del informe **Partidas Abiertas (Open Items)** para agrupar los resultados por la **Dirección de entrega (Delivery Address)** del partner. Esto es útil para empresas que realizan envíos a múltiples sucursales de un mismo cliente corporativo y necesitan analizar la deuda segmentada por dirección física.

---

### 3.6. MIS Builder Cash Flow (`mis_builder_cash_flow`)

Integra proyecciones de flujo de caja financiero dentro del motor de informes de MIS Builder.

#### Funcionamiento de la Proyección:
El flujo de caja proyectado se calcula en función de dos variables:
1.  **Apuntes Contables Existentes:** Utiliza la **Fecha de vencimiento** de las facturas de clientes y proveedores (en lugar de la fecha de emisión del asiento).
2.  **Líneas de Pronóstico Manuales (Forecast Lines):** Permite registrar de forma manual cobros o pagos estimados no facturados (por ejemplo, nóminas futuras, alquileres, impuestos previstos o inversiones futuras).

#### Configuración y Generación:
1.  **Registrar Pronósticos Manuales:** Ve a **Contabilidad ➔ Informes ➔ MIS Reporting ➔ Cash Flow Forecast Line** y añade las previsiones de ingresos y egresos de caja.
2.  **Visualizar el Reporte:**
    *   Ve a **Contabilidad ➔ Informes ➔ MIS Reporting ➔ MIS Reports**.
    *   Selecciona el reporte preconfigurado **Cash Flow**.
    *   En el campo **Target Moves** (Asientos objetivo), selecciona:
        *   *All Posted Entries (Solo Asentados):* Muestra el flujo de caja real basado en facturas validadas y líneas de pronóstico.
        *   *All Entries (Todos los Asientos):* Incluye también facturas en borrador para un análisis más temprano.
    *   El sistema omitirá automáticamente cualquier factura o asiento cancelado.

---

### 3.7. P&L y Balance Sheet MIS Templates (`mis_template_financial_report`)

Este módulo proporciona plantillas de configuración KPI estándar para que comiences a usar MIS Builder de inmediato sin tener que configurar las fórmulas de cuentas desde cero.

#### Plantillas Incluidas:
*   **Pérdidas y Ganancias (Profit & Loss / P&L)** genérico.
*   **Balance de Situación (Balance Sheet)** genérico.

#### Configuración y Diseño:
Al crear un nuevo informe a partir de estas plantillas, puedes activar la opción **Horizontal** en la pestaña **Layout** del informe. Esto dividirá la página PDF resultante en dos columnas (ej. Balance Activo a la izquierda, Pasivo y Patrimonio a la derecha), optimizando el espacio de presentación ejecutiva.

---

## 4. Instalación en el Servidor

Para instalar y utilizar estos módulos en una instancia de Odoo 18.0:

1.  Asegúrate de agregar la ruta de este repositorio a tu parámetro `addons_path` en el archivo de configuración `odoo.conf` de tu servidor.
2.  No olvides clonar e incluir las rutas de los repositorios dependientes mencionados en la sección [2. Dependencias del Sistema](#2-dependencias-del-sistema).
3.  Reinicia el servicio del servidor Odoo:
    ```bash
    service odoo restart
    ```
4.  Accede a Odoo con un usuario administrador, activa el **Modo Desarrollador** desde los ajustes.
5.  Ve al menú **Aplicaciones**, haz clic en **Actualizar lista de aplicaciones**.
6.  Busca el módulo específico que deseas instalar (por ejemplo, `account_financial_report` o `partner_statement`) y haz clic en **Instalar**.
