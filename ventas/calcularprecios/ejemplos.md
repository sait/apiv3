### 1) Precio normal (precio1 y preciopub)

GET /api/v3/calcularprecios?numart=0164&numcli=0&cant=1&unidad=PZA&numalm=1&divisadoc=P&formapago=1

response:
```json
{
    "precio": 25,
    "preciopub": 27,
    "pjedesc": 0,
    "opcionapli": 1,
    "preciolista": "1"
}
```

---
### 2) Precio segun volumen de compra

GET /api/v3/calcularprecios?numart=0164&numcli=0&cant=10&unidad=PZA&numalm=1&divisadoc=P&formapago=1

response:
```json
{
    "precio": 20.3704,
    "preciopub": 22,
    "pjedesc": 0,
    "opcionapli": 2,
    "preciolista": "1"
}
```

---
### 3) Precio de oferta, asignado al articulo

GET /api/v3/calcularprecios?numart=1040&numcli=0&cant=10&unidad=PZA&numalm=1&divisadoc=P&formapago=1

```json
{
    "precio": 120,
    "preciopub": 120,
    "pjedesc": 0,
    "opcionapli": 3,
    "preciolista": "1"
}
```

---
### 4) Precio normal y descuento asignado al articulo

GET /api/v3/calcularprecios?numart=LAS1317553&numcli=0&cant=1&unidad=PZA&numalm=1&divisadoc=P&formapago=1

```json
{
    "precio": 26.8519,
    "preciopub": 29.00,
    "pjedesc": 10,
    "opcionapli": 4,
    "preciolista": "1"
}
```

---
### 5) %Descuento segun promociones de articulos

5.1

promocion con precio especial

GET /api/v3/calcularprecios?numart=8A1204&numcli=0&cant=1&unidad=PZA&numalm=1&divisadoc=P&formapago=1

```json
{
    "precio": 128.7037037037037,
    "preciopub": 139,
    "pjedesc": 0,
    "opcionapli": 5,
    "preciolista": "1"
}
```

5.2

promocion con descuento

GET /api/v3/calcularprecios?numart=ZTT605S&numcli=0&cant=1&unidad=PZA&numalm=1&divisadoc=P&formapago=1

```json
{
    "precio": 36.1111,
    "preciopub": 38.999988,
    "pjedesc": 10,
    "opcionapli": 5,
    "preciolista": "2"
}
```

---
### 6) Precio de lista asignado al cliente en el catalogo

GET /api/v3/calcularprecios?numart=6230&numcli=A399&cant=1&unidad=PZA&numalm=1&divisadoc=P&formapago=1

```json
{
    "precio": 18.5185,
    "preciopub": 19.99998,
    "pjedesc": 0,
    "opcionapli": 6,
    "preciolista": "5"
}
```

---
### 7) Descuento asignado al cliente en el catalogo

GET /api/v3/calcularprecios?numart=6230&numcli=MA6&cant=1&unidad=PZA&numalm=1&divisadoc=P&formapago=1

```json
{
    "precio": 23.1481,
    "preciopub": 24.999948,
    "pjedesc": 15,
    "opcionapli": 7,
    "preciolista": "1"
}
```

---
### 8) Precio de lista y descuento, segun la tabla de descuentos al cliente

GET /api/v3/calcularprecios?numart=PEB120090&numcli=A180&cant=1&unidad=PZA&numalm=1&divisadoc=P&formapago=1

```json
{
    "precio": 1439.8148,
    "preciopub": 1554.99998,
    "pjedesc": 20,
    "opcionapli": 8,
    "preciolista": "3"
}
```

---
### 9) Precio de lista y descuento, segun el descuento en factura

GET /api/v3/calcularprecios?numart=0164&numcli=0&cant=1&unidad=PZA&numalm=1&divisadoc=P&formapago=1&pjedescfact=10

```json
{
    "precio": 25,
    "preciopub": 27,
    "pjedesc": 10,
    "opcionapli": 9,
    "preciolista": "1"
}
```