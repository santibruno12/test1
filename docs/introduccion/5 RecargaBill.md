# Cobro de facturas

##### Otra funcion que cumplen nuestros metodos son los "Cobros de facturas", Para esto utilizaremos el metodo " /sales/billPayment/ " modificandolo para cada accion que queremos hacer



#
##  Obtener las empresas disponibles

El metodo "/sales/billPayment/availablesCompanies" sirve para obtener todas las empresas habilitadas para operar. Estas poseen una estructura la cual veremos a continuacion.

### Estructura de una compañia
Como datos importantes podemos encontrar:

Column A | Column B | 
---------|----------|
 name | Nombre de la empresa |
 code | Codigo de la empresa |
 enable | Habilitado o desabilitado | 
 A3 | B3 | 
 A3 | B3 | 
 A3 | B3 | 
 A3 | B3 | 
 A3 | B3 | 

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


## Obtener Factura por codigo de barras

La obtencion de este es muy sencilla. Utilizar un POST en el metodo /sales/billPayment/availablesBill/findByBarCode. El body requerido en el request pide el dato "billBarCode" con un codigo de barras como en el siguiente ejemplo.

```json
{
    "billBarCode": "579201910127747008036409003400000101000004270050"    
}
```
Para este metodo hay 3 casos posibles de reponse.


### 1- Encuentra la factura correctamente 
El primero caso es cuando la factura es encontrada y muestra su estructura de factura correctamente

```json
[
    {
        "className": "BillPaymentData",
        "externalData": "93A1028111DA0012497073477643454461746F7353616C69646181DC0013A7696D706F727465AB636F6469676F4261727261AB6465736372697063696F6EAB6861736846616374757261A8646574616C6C6573AF746974756C6F73446574616B5671386433646B44576F92A104A8636C69656E74496492A104AA3030383631303832373992A104AA6D6178416D6F756E7431CB40A578000000000092A104AB636F6D70616E794E616D6592A104A944495245435420545692A104A47479706592A104A7626172636F646592A104AA6D696E416D6F756E7431CB40A578000000000092A104AA616D6F756E745479706592A104A345534392A104A765787069726564C292A104AE65787069726174696F6E44617465C092A104AD7061796D656E744D6F6465496492A104DA0014303831323033303933367247725A4C424644447692A104A7626172636F646592A104DA002C303537303038363130383237393030303338333231353238383231303230363030303030323734383030303592A104A7616D6F756E7431CB40A578000000000092A104AE6164646974696F6E616C44617461C092A104A97265666572656E6365C0C0C293A102E993DA00100000000000000000000000000274800C060293A102E993DA00100000000000000000000000000274800C0602C0C0C0C0",
        "billCompany": {
            "id": "16CB1F6815771858E95F",
            "description": "DIRECT TV",
            "name": "DIRECT TV",
            "code": "57",
            "enable": true,
            "externalName": "DIRECT TV",
            "allowVoluntaryAmount": false,
            "allowBillManualPayment": true,
            "allowBillRelationPayment": true,
            "allowPartialAmount": false,
            "customerIdentifierDescription": "Nro. de Cliente",
            "billModes": [
                {
                    "billMode": {
                        "id": "61953167179400013610",
                        "name": "COBRANZA SIN FACTURA 10 D"
                    },
                    "parameters": [
                        {
                            "id": "CBF",
                            "name": "Nro. de Cliente",
                            "inputType": "T",
                            "order": 0,
                            "length": 10,
                            "readOnly": false,
                            "optional": false
                        }
                    ]
                },
                {
                    "billMode": {
                        "id": "46505976008500000337",
                        "name": "COBRANZA SIN FACTURA (ONLINE)"
                    },
                    "parameters": [
                        {
                            "id": "CBF",
                            "name": "Nro. de Cliente",
                            "inputType": "T",
                            "order": 0,
                            "length": 8,
                            "readOnly": false,
                            "optional": false
                        }
                    ]
                },
                {
                    "billMode": {
                        "id": "0812030936xfRlxsGlAx",
                        "name": "COBRANZA SIN FACTURA"
                    },
                    "parameters": [
                        {
                            "id": "CBF",
                            "name": "Nro. de Cliente",
                            "inputType": "T",
                            "order": 0,
                            "length": 8,
                            "readOnly": false,
                            "optional": false
                        }
                    ]
                }
            ]
        },
        "currencyIso": "ARS",
        "customerIdentifier": "0086108279",
        "billBarCode": "05700861082790003832152882102060000027480005",
        "description": "Cliente: 0086108279\n",
        "totalAmount": 2748.00,
        "openAmount": false,
        "requireAditionalData": false,
        "aditionalData": [],
        "minAmount": 2748.00,
        "maxAmount": 2748.00
    }
]
```





### 2- No encuentra factura

Este caso ocurre cuando el metodo no encuentra una factura devolviendo una respuesta vacia



### 3- Los datos no alcanzan para determinar la factura
En este caso el codigo de barras ingresado trae facturas de varias empresas y no alcanza para determinar cual es la correcta a pagar, si se da este suceso deberiamos especificar de la siguiente manera.


```json
{
    "billBarCode": "579201910127747008036409003400000101000004270050",
    "billCompany": {
            "code": "3145"
        }
}
```


Dicho code, lo obtendriamos del metodo "/sales/billPayment/availablesCompanies" en el dato "code" de la compañia que vamos a utilizar





## Obtener por datos de Factura


La segunda forma de obtener una factura es a traves de datos ingresados sobre la factura y la compañia a utilizar. 
Este metodo exige escribir varios datos dentro del body para poder ejecutar el metodo correctamente.

• En primera instancia un objeto "billCompany" el cual contiene un codigo de la empresa a utilizar. 

• Luego un "billMode" el cual determina el tipo de factura que se desea obtener, este contiene un "id" con el codigo del tipo de factura y un "name" que contiene el nombre del tipo de factura. 

• Por ultimo el parametro el cual define el valor a cobrar y su ID.




```json
{
	"billCompany": {
		"code": "482"
	},
	"billMode": {
		"id": "39030785707000000527",
		"name": "COBRANZA SIN FACTURA - CUIT / DNI"
	},
	"parameters": [
		{
			"id": "C11",
			"value": "27249286090"
		}
	]
}
```







