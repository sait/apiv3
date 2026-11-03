## Bi Cobranza

| Accion                                        | Ruta                                                             |
|-----------------------------------------------|------------------------------------------------------------------|
| [Totales](#totales)                           | 📅 POST   /api/v3/bi_cobranza/:pk/totales?ano=..&mes=..           |
| [Antigüedad de saldos](#antiguedad-de-saldos) | 📅 POST   /api/v3/bi_cobranza/:pk/antiguedad_saldos?ano=..&mes=.. |
| [Clientes deudores](#clientes-deudores)       | 📅 POST   /api/v3/bi_cobranza/:pk/clientes_deudores?ano=..&mes=.. |
| [Cartera](#cartera)                           | 📅 POST   /api/v3/bi_cobranza/:pk/cartera?ano=..&mes=..           |
| [Índice de rotación](#indice-de-rotacion)     | 📅 POST   /api/v3/bi_cobranza/:pk/indice_rotacion?ano=..&mes=..   |

> Header requerido en todas las rutas: `Sait-x-api-key: <token>`

---

### Conceptos generales

| Termino           | Significado                                                      |
|-------------------|------------------------------------------------------------------|
| Cartera total     | Suma de todo el dinero (vigente y vencido) de todos los clientes |
| Cartera vigente   | Dinero que está en el rango de 0-30 días                         |
| Cartera vencida   | Dinero que está en el rango de 31 días o más                     |
| % Vencido         | Porcentaje de cartera vencida respecto a la cartera total        |
| Clientes deudores | Clientes con deudas después de 30 días                           |

En las rutas de cartera (totales, antigüedad de saldos, clientes deudores y cartera) el filtro de fechas se aplica sobre la fecha del documento (`cxc.fecha`): se consideran los saldos pendientes de los documentos emitidos dentro del periodo. La antigüedad (vigente/vencido) se sigue calculando contra el día de hoy.

Todos los importes se normalizan a moneda nacional: si `divisa='D'` se multiplica por el tipo de cambio (`tc`), de lo contrario se toma el saldo tal cual.

---

### Filtros de fecha

Las rutas marcadas con 📅 filtran por fecha usando la tabla `fechas` (igual que `bi/ventas`). Es obligatorio enviar al menos uno de los siguientes parámetros:

| parametro | descripcion                                                                  |
|-----------|------------------------------------------------------------------------------|
| dia       | Día especificado (será necesario especificar mes y ano)                      |
| semana    | Semana especificada (será necesario especificar ano)                         |
| mes       | Mes especificado (será necesario especificar ano)                            |
| trimestre | Trimestre especificado (será necesario especificar ano)                      |
| semestre  | Semestre especificado (será necesario especificar ano)                       |
| ano       | Año especificado                                                             |
| fini      | Fecha inicial a considerar (20250101), debe enviarse junto con ffin          |
| ffin      | Fecha límite a considerar  (20251231), debe enviarse junto con fini          |

Ejemplos: `?ano=2025&mes=3`, `?ano=2025&trimestre=2`, `?fini=20250101&ffin=20250630`

---

### Totales

POST /api/v3/bi_cobranza/:pk/totales

Regresa el resumen general de cartera: total, vigente, vencida, porcentaje vencido y numero de clientes deudores.

Campos
| Campo            | Tipo  | Significado                                                      |
|------------------|-------|------------------------------------------------------------------|
| carteratotal     | float | Suma total de saldos (vigentes + vencidos) de todos los clientes |
| carteravigente   | float | Suma de saldos con vencimiento dentro de los últimos 30 días     |
| carteravencida   | float | Suma de saldos con vencimiento de 31 días o más                  |
| pjevencido       | float | Porcentaje que representa `carteravencida` sobre `carteratotal`  |
| clientesdeudores | int   | Cantidad de clientes distintos con saldo vencido a 31 días o más |

Response
```json
{
  "result": {
    "carteratotal": "1040675.5000",
    "carteravencida": "1040675.5000",
    "carteravigente": "0.0000",
    "clientesdeudores": "71",
    "pjevencido": "100.00"
  }
}
```

---

### Antigüedad de saldos

POST /api/v3/bi_cobranza/:pk/antiguedad_saldos

Desglosa la cartera total en rangos de antigüedad (0-30, 31-60, 61-90 y 91+ días).

Campos
| Campo        | Tipo  | Significado                                                  |
|--------------|-------|--------------------------------------------------------------|
| vigente30    | float | Suma de saldos con vencimiento dentro de los últimos 30 días |
| vencido60    | float | Suma de saldos con vencimiento entre 31 y 60 días            |
| vencido90    | float | Suma de saldos con vencimiento entre 61 y 90 días            |
| mas90vencido | float | Suma de saldos con vencimiento de 91 días o más              |
| carteratotal | float | Suma total de saldos (todos los rangos combinados)           |

Response
```json
{
  "result": {
    "carteratotal": "1040675.5000",
    "mas90vencido": "1011097.5600",
    "vencido60": "0.0000",
    "vencido90": "29577.9400",
    "vigente30": "0.0000"
  }
}
```

---

### Clientes deudores

POST /api/v3/bi_cobranza/:pk/clientes_deudores

Regresa el detalle de cartera por cliente, desglosado en los mismos rangos de antigüedad que `antiguedad_saldos`.

Campos
| Campo        | Tipo   | Significado                                                     |
|--------------|--------|-----------------------------------------------------------------|
| clave        | string | Numero de identificacion del cliente (numcli)                   |
| nombre       | string | Nombre del cliente (nomcli)                                     |
| vigente30    | float  | Saldo del cliente con vencimiento dentro de los últimos 30 días |
| vencido60    | float  | Saldo del cliente con vencimiento entre 31 y 60 días            |
| vencido90    | float  | Saldo del cliente con vencimiento entre 61 y 90 días            |
| mas90vencido | float  | Saldo del cliente con vencimiento de 91 días o más              |
| carteratotal | float  | Suma total del saldo del cliente (todos los rangos combinados)  |

Response
```json
{
  "result": [
    {
      "clave": " A512",
      "nombre": "CARLOS EDUARDO NAVARRO REYES",
      "vigente30": "0.0000",
      "vencido60": "0.0000",
      "vencido90": "0.0000",
      "mas90vencido": "4910.3800",
      "carteratotal": "4910.3800"
    },
    {
      "clave": " A191",
      "nombre": "EDGAR MIRELES PERALTA",
      "vigente30": "0.0000",
      "vencido60": "0.0000",
      "vencido90": "0.0000",
      "mas90vencido": "177.8000",
      "carteratotal": "177.8000"
    },
    {}
  ]
}
```

---

### Cartera

POST /api/v3/bi_cobranza/:pk/cartera

Regresa el estado de la cartera al día de hoy: total, vigente y vencida. A diferencia de [Totales](#totales), aquí el corte de vigencia es exactamente `CURDATE()` (hoy), en vez del rango de 30 días.

Campos
| Campo           | Tipo  | Significado                                                      |
|-----------------|-------|------------------------------------------------------------------|
| cartera_total   | float | Suma total de saldos (vigentes + vencidos) de todos los clientes |
| cartera_vigente | float | Suma de saldos cuyo vencimiento (`venc`) es hoy o en el futuro   |
| cartera_vencida | float | Suma de saldos cuyo vencimiento (`venc`) ya pasó (antes de hoy)  |

Response
```json
{
  "result": {
    "cartera_total": "1040675.5000",
    "cartera_vigente": "150230.0000",
    "cartera_vencida": "890445.5000"
  }
}
```

---

### Índice de rotación

POST /api/v3/bi_cobranza/:pk/indice_rotacion?ano=2025&mes=3

Calcula el índice de rotación de cartera y la velocidad de cobro (en días) dentro del periodo indicado con los [filtros de fecha](#filtros-de-fecha), a partir del saldo inicial, saldo final y las facturas generadas a crédito en ese rango.

> El rango (fecha inicial, fecha final y cantidad de días) se resuelve con la tabla `fechas` a partir de los filtros enviados. Reemplaza al parámetro `periodo=mes|anio`, que ya no se acepta.

Campos
| Campo           | Tipo  | Significado                                                                                                     |
|-----------------|-------|-----------------------------------------------------------------------------------------------------------------|
| indice_rotacion | float | Facturas a crédito en el periodo, dividido entre el saldo promedio de cartera (saldo_inicial + saldo_final) / 2 |
| velocidad_cobro | float | Días promedio que tarda en recuperarse la cartera: `días del periodo / indice_rotacion`                         |

> El saldo inicial y saldo final se calculan igual que en las demás rutas: facturas (`ca=0`) suman, abonos (`ca=1`) restan, y los importes en moneda extranjera (`divisa != 'P'`) se convierten a moneda nacional multiplicando por el tipo de cambio (`tc`) del movimiento.

Response
```json
{
  "result": {
    "indice_rotacion": "23.55424878",
    "velocidad_cobro": "15.4961"
  }
}
```