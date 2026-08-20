## Bi Cobranza

| Accion                                          | Ruta                                                 |
|--------------------------------------------------|-------------------------------------------------------|
| [Totales](#totales)                               | POST   /api/v3/bi_cobranza/:pk/totales                |
| [Antigüedad de saldos](#antiguedad-de-saldos)      | POST   /api/v3/bi_cobranza/:pk/antiguedad_saldos       |
| [Clientes deudores](#clientes-deudores)            | POST   /api/v3/bi_cobranza/:pk/clientes_deudores       |

> `:pk` (obligatorio) actualmente no se usa para filtrar la consulta, pero es requerido por la ruta. Ejemplo: `1`.

---

### Conceptos generales

| Termino           | Significado                                                                                     |
|--------------------|---------------------------------------------------------------------------------------------------|
| Cartera total      | Suma de todo el dinero (vigente y vencido) de todos los clientes                                  |
| Cartera vigente    | Dinero que está en el rango de 0-30 días                                                          |
| Cartera vencida    | Dinero que está en el rango de 31 días o más                                                      |
| % Vencido          | Porcentaje de cartera vencida respecto a la cartera total                                         |
| Clientes deudores  | Clientes con deudas después de 30 días                                                            |

Todos los importes se normalizan a moneda nacional: si `divisa='D'` se multiplica por el tipo de cambio (`tc`), de lo contrario se toma el saldo tal cual.

---

### Totales

POST /api/v3/bi_cobranza/:pk/totales

Regresa el resumen general de cartera: total, vigente, vencida, porcentaje vencido y numero de clientes deudores.

Campos
| Campo             | Tipo   | Significado                                                          |
|--------------------|--------|-----------------------------------------------------------------------|
| carteratotal       | float  | Suma total de saldos (vigentes + vencidos) de todos los clientes      |
| carteravigente     | float  | Suma de saldos con vencimiento dentro de los últimos 30 días          |
| carteravencida     | float  | Suma de saldos con vencimiento de 31 días o más                       |
| pjevencido         | float  | Porcentaje que representa `carteravencida` sobre `carteratotal`       |
| clientesdeudores   | int    | Cantidad de clientes distintos con saldo vencido a 31 días o más      |

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
| Campo         | Tipo   | Significado                                                    |
|----------------|--------|-------------------------------------------------------------------|
| vigente30      | float  | Suma de saldos con vencimiento dentro de los últimos 30 días      |
| vencido60      | float  | Suma de saldos con vencimiento entre 31 y 60 días                 |
| vencido90      | float  | Suma de saldos con vencimiento entre 61 y 90 días                 |
| mas90vencido   | float  | Suma de saldos con vencimiento de 91 días o más                   |
| carteratotal   | float  | Suma total de saldos (todos los rangos combinados)                |

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
| Campo         | Tipo   | Significado                                                        |
|----------------|--------|------------------------------------------------------------------------|
| clave          | string | Numero de identificacion del cliente (numcli)                          |
| nombre         | string | Nombre del cliente (nomcli)                                            |
| vigente30      | float  | Saldo del cliente con vencimiento dentro de los últimos 30 días        |
| vencido60      | float  | Saldo del cliente con vencimiento entre 31 y 60 días                   |
| vencido90      | float  | Saldo del cliente con vencimiento entre 61 y 90 días                   |
| mas90vencido   | float  | Saldo del cliente con vencimiento de 91 días o más                     |
| carteratotal   | float  | Suma total del saldo del cliente (todos los rangos combinados)         |

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