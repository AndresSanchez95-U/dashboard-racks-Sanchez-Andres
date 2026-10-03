# Criterios y Pruebas de Aceptación del Sistema (v1.0.0)

**Proyecto:** Gestor de Cableado Estructurado e Inventario de Racks  
**Documento:** `docs/pruebas-aceptacion.md`  
**Versión:** 1.0.0  
**Fecha:** Octubre 2026  

---

## 1. Objetivo
Definir los casos de prueba de aceptación de usuario (UAT) y criterios de validación operacional para certificar que el sistema visualiza correctamente la ocupación de bastidores (racks), previene solapamientos de espacio $U$, calcula de forma precisa la carga eléctrica en esquemas redundantes A/B, mantiene la trazabilidad de auditoría (`sources` y `tag`) y permite la persistencia/portabilidad mediante importación y exportación JSON/CSV.

---

## 2. Matriz de Pruebas de Aceptación

| ID | Módulo / Función | Escenario / Criterio de Aceptación | Resultado Esperado | Estado |
| :--- | :--- | :--- | :--- | :---: |
| **UAT-01** | Visualización | **Alternancia de Ordenación de $U$ (Filtro Descendente / Ascendente)** | Al seleccionar `DESC` en el control de "Orden U", el bastidor se renderiza visualmente desde $42\text{U}$ hasta $1\text{U}$ (arriba a abajo). Al cambiar a `ASC`, la numeración se invierte de $1\text{U}$ a $42\text{U}$. | PASS |
| **UAT-02** | Visualización | **Conmutación Vista Frontal / Posterior** | Al cambiar el selector de vista a "Posterior", los equipos/accesorios cuya cara sea únicamente `front` dejan de renderizarse en el rack gráfico y solo se muestran los marcados como `rear` o `both` (profundidad completa). | PASS |
| **UAT-03** | Lógica $U$ | **Prevención de Solapamientos en la misma cara ($1/2$ profundidad)** | Intentar registrar un equipo en $U15$ (Cara Frontal) cuando ya existe otro en $U15$ (Cara Frontal). | **Bloqueado:** Muestra alerta de error por colisión $U$. | PASS |
| **UAT-04** | Lógica $U$ | **Permitir cohabitación en distinta cara ($1/2$ profundidad)** | Registrar un equipo en $U15$ (Cara Frontal, Medio) y posteriormente otro en $U15$ (Cara Posterior, Medio). | **Permitido:** Ambos equipos coexisten sin marcar solapamiento. | PASS |
| **UAT-05** | Lógica $U$ | **Bloqueo por Profundidad Completa (`both`)** | Intentar registrar un equipo de profundidad Completa (`both`) en $U15$ cuando existe un accesorio en $U15$ Posterior. | **Bloqueado:** La profundidad completa requiere despeje total en ambas caras. | PASS |
| **UAT-06** | Potencia | **Cálculo Balanceado de Alimentación Redundante A/B** | Al registrar un equipo con $350\text{W}$ y fuente `Dual A/B`, la carga sumada a la rama A debe ser $175\text{W}$ y a la rama B $175\text{W}$. | **Verificado:** Métrica general actualiza exactamente a $+175\text{W}$ por rama. | PASS |
| **UAT-07** | Potencia | **Cálculo de Alimentación Simple (No Redundante)** | Al registrar un equipo con $300\text{W}$ y fuente `Simple`, la carga total de $300\text{W}$ se asigna a la rama A. | **Verificado:** Carga rama A aumenta $+300\text{W}$, rama B se mantiene igual. | PASS |
| **UAT-08** | Alertas | **Alerta por Riesgo de Redundancia Eléctrica** | Al existir 1 o más equipos activos con fuente `Simple`, el sistema genera una advertencia global en el panel de monitoreo. | **Verificado:** Se muestra la alerta: *"Riesgo de Redundancia: X equipo(s) con fuente simple"*. | PASS |
| **UAT-09** | Reservas | **Bloqueo preventivo de espacio futuro** | Registrar una Reserva de espacio en $U20$ a $U23$. Posteriormente intentar montar un Switch en $U21$. | **Bloqueado:** El motor de validación trata el espacio reservado como ocupado. | PASS |
| **UAT-10** | Trazabilidad | **Etiquetado de Fuente de Verdad (Auditoría)** | Cada bastidor, equipo o reserva debe mostrar la etiqueta visual del grado de certeza (`[CONFIRMADO]`, `[INFERENCIA POR VALIDAR]`, etc.). | **Verificado:** Renderizado de badges con código de colores según el tag. | PASS |
| **UAT-11** | Persistencia | **Exportación e Importación de Estado Completo (JSON)** | Exportar el estado global a JSON, restablecer el sistema a valores por defecto y reimportar el archivo guardado. | **Verificado:** Restaura al 100% la estructura de racks, equipos, reservas y bitácora. | PASS |
| **UAT-12** | Auditoría | **Bitácora de Eventos (`changelog`)** | Cada acción de creación, edición o eliminación de bastidores/equipos registra un evento con timestamp UTC en la bitácora. | **Verificado:** Entrada generada correctamente y visible en el panel lateral. | PASS |

---

## 3. Formato de Evidencia para Ejecución de Pruebas

Para dar por aprobada cada prueba durante la auditoría, se debe completar la siguiente ficha:

```text
[ID PRUEBA]: UAT-01
[FECHA EJECUCIÓN]: 2026-10-02
[RESPONSABLE]: Auditor de Infraestructura / Lider TI
[DATOS DE ENTRADA]: Rack-01 (42U), Selección "Orden U: ASC"
[COMPORTAMIENTO OBSERVADO]: La fila inferior muestra U1 y la fila superior muestra U42.
[ESTADO FINAL]: APROBADO (PASS)