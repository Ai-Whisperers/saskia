# Spanish (vos) copy bank — request for Kiki/Saskia

> **For Kiki or Saskia to fill.** This is a request for Spanish strings, not a draft.
>
> I (Hermes) don't speak Paraguayan Spanish well enough to write the UI copy. This file documents **what strings are needed**, with placeholders, and asks the right person to fill them in **before Task 3 of the dev plan starts**.
>
> **Why this matters:** the dev plan says "Spanish (vos)" but doesn't define which vos. Paraguayan Spanish ≠ Argentine Spanish ≠ Mexican Spanish. Without a copy bank approved by Saskia, every UI screen becomes a debate.

---

## How to fill this

For each string:
1. Replace `<...>` with the actual Spanish (vos) copy Saskia would naturally read.
2. Use the **Paraguayan convention** ("guardá", "tenés", "querés", period as thousands separator) — not Argentine ("salvá", "tenés" same as PY but different idioms), not Mexican.
3. Money format: "Gs. 729.167" (period thousands separator, no decimals).
4. Date format: "31/08/2026" (DD/MM/YYYY, Paraguayan standard).
5. If she doesn't have an opinion, mark "(default)" and use the suggested value.

## Buttons / actions

| English placeholder | Spanish (vos) copy | Notes |
|---|---|---|
| Save | Guardar / Guardá | Suggestion: "Guardá" |
| Cancel | Cancelar | Standard |
| Delete | Eliminar / Borrar | Suggestion: "Eliminar" |
| Edit | Editar | Standard |
| Add new | Agregar nuevo | Or "Añadir" — Saskia's call |
| Confirm | Confirmar | Standard |
| Back | Volver | Standard |
| Search | Buscar | Standard |
| Export | Exportar | Standard |
| Import | Importar | Standard |
| Print | Imprimir | Standard |
| Refresh | Actualizar | Or "Refrescar" — Saskia's call |
| Filter | Filtrar | Standard |
| Close | Cerrar | Standard |

## Form labels

| English placeholder | Spanish (vos) copy | Notes |
|---|---|---|
| Name | Nombre | Standard |
| Description | Descripción | Standard |
| Quantity | Cantidad | Standard |
| Unit | Unidad | Standard |
| Price (Gs.) | Precio (Gs.) | Always "(Gs.)" suffix |
| Date | Fecha | DD/MM/YYYY |
| Notes | Notas | Standard |
| Category | Categoría | Standard |
| Yield | Rinde | Or "Rinde (porciones)" — explicit |
| Min stock | Stock mínimo | Standard |
| Current stock | Stock actual | Standard |

## Empty states / messages

| Context | Spanish (vos) copy | Notes |
|---|---|---|
| No ingredients yet | "Todavía no cargaste ingredientes. Hacé clic en Agregar." | |
| No sales today | "Hoy todavía no registraste ventas." | |
| No recipes match | "No hay recetas que coincidan con tu búsqueda." | |
| Sale saved | "Venta registrada: 12 muffins a Gs. 86.000." | Should mention qty + product + total |
| Stock below minimum | "Stock bajo: <ingredient> tiene <qty> <unit>, mínimo es <min>" | Replace `<...>` with values |
| Stock negative | "¡Stock negativo! <ingredient> está en <qty> <unit>. Hacé un recuento." | "¡" emphasizes urgency |
| Recipe without price | "Receta sin precio de ingrediente: <recipe>. Carga los precios para ver el costo." | |
| Sale without recipe | "Venta sin receta: <product>. Asignale una receta para ver el margen." | |
| Import preview | "Vas a importar 63 ingredientes, 20 recetas, 0 líneas con errores. ¿Continuar?" | Adjust numbers per import |
| Import success | "Listo. Importaste 63 ingredientes y 20 recetas. Tu backup está en <path>." | Mention backup location |
| Import error | "No pude importar. Tu backup anterior está en <path>. Avisame y lo restauro." | Offer rollback |
| Delete confirm | "¿Eliminar <thing>? Esta acción no se puede deshacer." | Always ask |
| Void sale confirm | "¿Anular esta venta? Se devuelve el stock." | Stock language |

## Errors / validation

| Context | Spanish (vos) copy | Notes |
|---|---|---|
| Required field | "Este campo es obligatorio" | Standard |
| Invalid number | "Ingresá un número válido (ej: 1234)" | |
| Invalid money | "Ingresá un monto en Gs. (ej: 729167)" | Note: no decimals, no thousands sep in input |
| Invalid unit | "Unidad no reconocida. Usá: g, kg, ml, l, und" | List canonical units |
| Insufficient permission | "Esta acción no está disponible" | (No permission system in v1; placeholder) |
| Network error | "No pude conectar. Revisá tu internet." | (App is local; rarely seen) |
| Generic error | "Algo salió mal. Avisame y lo reviso." | Operator handles |

## Status badges

| English placeholder | Spanish (vos) copy | Notes |
|---|---|---|
| Active | Activo | Standard |
| Inactive | Inactivo | Standard |
| Pending | Pendiente | Standard |
| Done | Listo | Standard |
| Draft | Borrador | Standard |
| Archived | Archivado | Standard |
| Low stock | Stock bajo | Red color |
| Out of stock | Sin stock | Red color |
| Negative stock | ¡Stock negativo! | Red color, flashing |

## Numbers in copy

| Number | Spanish (vos) copy | Notes |
|---|---|---|
| 1 unit | "1 unidad" or "1 und" | Convention: short form |
| 12 units | "12 unidades" or "12 und" | |
| 0 units | "0 unidades" or "sin stock" | Prefer "sin stock" |
| 729167 Gs. | "Gs. 729.167" | Period thousands sep |
| 17.500.000 Gs. | "Gs. 17.500.000" | Same |

## Specific UI strings (longer)

### Welcome banner (dashboard)

```
¡Hola! Hoy es <day>, <date>.

[Ventas de hoy: <amount> Gs.]
[Margen de hoy: <margin> Gs. (<pct>%)]
[Stock bajo: <count> ingredientes]
```

### Dashboard widget titles

- "Ventas de hoy" (Today sales)
- "Costo de lo vendido" (Cost of goods sold)
- "Margen" (Margin)
- "Ranking de productos" (Product ranking — by margin)
- "Avisos" (Alerts)

### Sale form

```
Nueva venta
Fecha: [2026-08-31 14:30] (default = ahora)
Producto: [Muffin ▾]
Cantidad: [12]
Notas: [opcional]

Vista previa:
  Stock actual de harina de trigo: 1000 g
  Después de esta venta: 970 g (stock mínimo: 500 g) ✓
  Costo estimado: Gs. 24.000
  Margen estimado: Gs. 78.000 (75%)

[Cancelar]  [Registrar venta]
```

### Empty sale preview (when product has no recipe)

```
Vista previa:
  Este producto no tiene receta asignada.
  No se puede calcular el costo ni descontar stock.
  
  [Asignar receta]  [Continuar de todos modos]
```

---

## When to fill this

**Before Task 3 of the dev plan starts.** That's the "Inventario + recetas CRUD (Spanish)" task, which is the first task with significant UI copy.

## How to commit

Once filled:
1. Save as `app/docs/copy-vos.md` (in the engagement repo)
2. Saskia reviews and ticks each line: ✅ or "change to X"
3. Kiki implements per the copy bank
4. Round 2 of dev plan review = copy review

---

*Filled by: _____________________ (Saskia or Kiki)*
*Date: _____________________*
