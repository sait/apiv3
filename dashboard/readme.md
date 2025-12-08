# Rutas Dashboard Usuario

## Ventas totales por año y mensuales

| filtro       | tipo   | descripcion                                                                              |
|--------------|--------|------------------------------------------------------------------------------------------|
| ano          | string | Obtiene los datos de un año en especifico (ejemplo: ano=2025)                            |
| mes          | string | Obtiene los datos de un mes en especifico (ejemplo: mes=11)                              |
| mostrarmeses | string | TRUE: Retorna los meses del año o el especificado, FALSE: Retorna solo los datos anuales |

**GET** api/v3/dashboard/ventas_totales?filters...

Response:
```json
{
    "importe": 60469.2,
    "descuento": 0,
    "subtotal": 60469.2,
    "iva": 6362.07,
    "total": 66831.27,
    "devol": 60469.2,
    "meses": [
      {
        "importe": 50,
        "descuento": 0,
        "subtotal": 50,
        "iva": 8,
        "total": 58,
        "devol": 50,
        "mes": "September"
      },
      {
        "importe": 58130.53,
        "descuento": 0,
        "subtotal": 58130.53,
        "iva": 6060.77,
        "total": 64191.3,
        "devol": 58130.53,
        "mes": "October"
      },
      {
        "importe": 2288.67,
        "descuento": 0,
        "subtotal": 2288.67,
        "iva": 293.3,
        "total": 2581.97,
        "devol": 2288.67,
        "mes": "November"
      }
    ]
  }
```



## Ventas por sucursal

| filtro   | descripcion                                                                              |
|----------|------------------------------------------------------------------------------------------|
| fechaini | fecha inicial (20250101)                                                                 |
| fechafin | fecha final (20251201)                                                                   |
| numalm   | numero de almacen (" 1","99")                                                            |

**GET** api/v3/dashboard/ventas_sucursal?filters...

ejemplo: /api/v3/dashboard/ventas_sucursales?fechaini=20240101&fechafin=20241201&numalm=1

Response:
```json
[
    {
        "importe": 16423.3,
        "descuento": 0,
        "subtotal": 16423.3,
        "iva": 2436.29,
        "total": 18859.59,
        "devol": 16423.3,
        "numalm": " 1",
        "nomalm": "MATRIZ"
    }
]
```