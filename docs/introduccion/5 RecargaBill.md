# Cobro de facturas

##### Otra funcion que cumplen nuestros metodos son los "Cobros de facturas", Para esto utilizaremos el metodo " /sales/billPayment/ " modificandolo para cada accion que queremos hacer



#
##  Obtener las empresas disponibles

Este metodo sirve para obtener todas las empresas habilitadas para operar. Estas poseen una estructura la cual veremos a continuacion.

### Estructura de una compañia
Como datos importantes podemos encontrar

```json
{
    "id": "16CB1F68156830E1CC92",
    "description": "CIMES AGUA",
    "name": "CIMES AGUA",
    "code": "9312",
    "enable": true,
    "externalName": "CIMES AGUA",
    "allowVoluntaryAmount": false,
    "allowBillManualPayment": true,
    "allowBillRelationPayment": true,
    "allowPartialAmount": false,
    "customerIdentifierDescription": "NUMERO DE CUIT",
    "billModes": [
        {
            "billMode": {
                "id": "1811231643dfVbFIjorX",
                "name": "COBRANZA SIN FACTURA (CONSULTA DE DEUDA)"
            },
            "parameters": [
                {
                    "id": "CBF",
                    "name": "NUMERO DE CUIT",
                    "inputType": "T",
                    "order": 0,
                    "length": 19,
                    "readOnly": false,
                    "optional": false
                }
            ]
        }
    ]
},
```