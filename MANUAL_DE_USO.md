# Manual de Uso: OCA Account Financial Reporting (Rama 19.0)

Este repositorio contiene un conjunto de módulos avanzados desarrollados por la **Odoo Community Association (OCA)** para la versión **19.0** de Odoo. Su principal objetivo es extender y mejorar las capacidades de informes financieros y contables de Odoo, ofreciendo mayor flexibilidad, soporte multidivisa avanzado y estados de cuenta estructurados para partners.

> [!IMPORTANT]
> En la rama **19.0**, solo se han migrado y están disponibles actualmente los tres módulos centrales (`account_financial_report`, `account_tax_balance`, y `partner_statement`). Los módulos de extensión de ventas, exportaciones XLSX directas y plantillas MIS adicionales se encuentran en proceso de migración o han sido integrados/reemplazados según el roadmap de la OCA para Odoo 19.

---

## Tabla de Contenidos
1. [Resumen de Módulos](#1-resumen-de-módulos)
2. [Dependencias del Sistema](#2-dependencias-del-sistema)
3. [Guía de Configuración y Uso por Módulo](#3-guía-de-configuración-y-uso-por-módulo)
   - [3.1. Account Financial Reports (`account_financial_report`)](#31-account-financial-reports-account_financial_report)
   - [3.2. Partner Statement (`partner_statement`)](#32-partner-statement-partner_statement)
   - [3.3. Tax Balance (`account_tax_balance`)](#33-tax-balance-account_tax_balance)
4. [Instalación en el Servidor](#4-instalación-en-el-servidor)

---

## 1. Resumen de Módulos

El repositorio para la versión 19.0 está compuesto por los siguientes módulos activos:

| Módulo | Versión | Mantenedor | Descripción Corta |
| :--- | :--- | :--- | :--- |
| **`account_financial_report`** | 19.0.0.0.13 | OCA | Reportes contables clásicos (Mayor, Sumas y Saldos, IVA) compatibles con Odoo 19. |
| **`partner_statement`** | 19.0.1.1.0 | ForgeFlow | Estados de cuenta interactivos para clientes/proveedores con antigüedad de saldos. |
| **`account_tax_balance`** | 19.0.1.0.2 | Tecnativa / Agile BG | Cálculo de balances de impuestos basados en un rango de fechas. |

---

## 2. Dependencias del Sistema

Para que estos módulos funcionen en tu entorno Odoo 19.0, debes clonar e instalar las siguientes dependencias de la OCA:

*   **`report_xlsx`** e **`report_xlsx_helper`**: Requeridos para las exportaciones estructuradas a Excel (del repositorio [OCA/reporting-engine](https://github.com/OCA/reporting-engine)).
*   **`date_range`**: Necesario para gestionar los rangos de fechas dinámicos en los reportes (del repositorio [OCA/server-ux](https://github.com/OCA/server-ux)).

---

## 3. Guía de Configuración y Uso por Módulo

### 3.1. Account Financial Reports (`account_financial_report`)

Este módulo sustituye y mejora los reportes financieros clásicos de Odoo 19, ofreciendo asistentes intuitivos (wizards) y descargas en PDF y Excel estructurado.

#### Reportes Disponibles:
1.  **Libro Mayor (General Ledger):** Detalle analítico por cuenta contable con filtros de fechas, diarios e información detallada de contrapartida.
2.  **Balance de Sumas y Saldos (Trial Balance):** Estructura jerárquica con filtros de niveles de jerarquía y posibilidad de ocultar las cuentas padre.
3.  **Partidas Abiertas (Open Items):** Detalle de facturas y transacciones pendientes de conciliación.
4.  **Antigüedad de Saldos (Aged Partner Balance):** Análisis de saldos pendientes divididos en columnas según sus días de vencimiento.
5.  **Libro Diario (Journal Ledger):** Registro cronológico detallado de todos los apuntes contables agrupados por diario.
6.  **Informe de IVA (VAT Report):** Resumen de bases y cuotas de impuestos basado en etiquetas analíticas de impuestos.

> [!NOTE]
> Los reportes de **Libro Mayor**, **Sumas y Saldos** y **Partidas Abiertas** soportan el manejo de divisas extranjeras. Si una cuenta tiene definida una moneda secundaria, los saldos se mostrarán en la divisa de origen además de la divisa de la compañía.

#### Ajuste de Intervalos de Antigüedad:
1.  Ve a **Ajustes ➔ Facturación ➔ OCA Aged Report Configuration**.
2.  Crea un nuevo registro para definir las columnas del reporte de antigüedad (ejemplo de configuración: `15 ➔ 30 ➔ 60` para rangos 0-15, 16-30, 31-60 y 61+).
3.  Define una plantilla predeterminada por compañía para agilizar la generación de reportes en el día a día.

---

### 3.2. Partner Statement (`partner_statement`)

Permite la emisión de estados de cuenta detallados o históricos para su envío directo a clientes y proveedores.

#### Tipos de Reporte:
*   **Activity Statement (Estado de Actividad):** Lista cronológicamente todos los movimientos (facturas, cobros, pagos) ocurridos en un rango de fechas. Las transacciones anteriores se consolidan en una línea de "Saldo Inicial".
*   **Detailed Activity Statement (Actividad Detallada):** Muestra el desglose completo de transacciones con bloques explícitos de saldo inicial y saldo final acumulado por divisa.
*   **Outstanding Statement (Saldos Pendientes):** Muestra únicamente aquellas transacciones que siguen abiertas (sin conciliar) hasta la fecha de consulta especificada.

#### Configuración de Uso:
1.  Activa los estados de cuenta desde **Facturación ➔ Configuración ➔ Ajustes** bajo la sección **Partner Statements**.
2.  Selecciona uno o más contactos en la vista de lista de Odoo (Clientes o Proveedores), presiona el menú **Acción** en la barra superior y selecciona **Partner Activity Statement** o **Partner Outstanding Statement**.
3.  Utiliza filtros avanzados en el wizard para excluir saldos en cero, omitir saldos negativos o filtrar partidas no vencidas.

---

### 3.3. Tax Balance (`account_tax_balance`)

Calcula de manera dinámica la base imponible y el importe total de impuestos aplicados a transacciones durante un periodo de fechas.

#### Instrucciones de Uso:
1.  Ve a **Contabilidad ➔ Informes ➔ Taxes Balance**.
2.  Indica las fechas de consulta (a través de los periodos de `date_range`) y selecciona si se deben incluir apuntes en borrador o solo los asentados.
3.  Presiona **Calcular/Abrir impuestos** para ver el balance consolidado por impuesto.

---

## 4. Instalación en el Servidor

1.  Agrega el directorio `/OCA-account-financial-reporting/OCA-account-financial-reporting-19` a la directiva `addons_path` de tu archivo `odoo.conf`.
2.  Asegúrate de instalar los módulos requeridos `report_xlsx` y `date_range` (versión 19.0) en tus rutas de addons.
3.  Reinicia tu instancia de Odoo 19.
4.  Activa el Modo Desarrollador, ve a **Aplicaciones ➔ Actualizar lista de aplicaciones** e instala los módulos requeridos.
