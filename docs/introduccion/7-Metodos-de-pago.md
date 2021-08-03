# Metodos de pago
---
En el presente documento se especifica la API para ser utilizada para los metodos de pago externos.

Las operaciones que pueden realizarse incluyen:
- Obtener Metodos de pagos disponibles
- Abonar con el metodo de pago elegido
- Respuestas posibles
## Obtener metodos de pagos disponibles
El primer método a utilizar es un **POST** con el método **/payment-methods-transaction**.
Este método devolverá los métodos de pagos externos para las transacciones a realizar, para esto se le deberá enviar en el Body un monto (amount) con un valor entero y el tipo de transacción con el parámetro sale como veremos a continuación 

```json
{
    "amount": 100,
    "transactionType": "Sale"
}
```
Este request devolvería un response con los métodos habilitados, Lo que deberemos hacer es guardar el “id” del método de pago a utilizar ya que en el próximo paso lo deberemos enviar dentro del body en un objeto llamado “paymentMethod”.

#### Ejemplo Response

```json
[
    {
        "id": "2F730F540B823850C91B",
        "description": "Efectivo",
        "name": "Efectivo",
        "code": "010609",
        "enable": true,
        "minimiumAmount": 1.00000000,
        "maximiumAmount": 999999.00000000,
        "paymentMethodType": "cash",
        "requireFundingSource": false,
        "availableFundingSources": [],
        "discount": 0,
        "paymentMethodDefinitionClassName": "CashDefinition",
        "paymentDataClassName": "CashData"
    },
    {
        "id": "2F730F54C89338A2764C",
        "description": "Maestro",
        "name": "Maestro",
        "code": "010610",
        "enable": true,
        "minimiumAmount": 1.00000000,
        "maximiumAmount": 9999999.00000000,
        "paymentMethodType": "debit_card",
        "requireFundingSource": false,
        "availableFundingSources": [],
        "discount": 0,
        "paymentMethodDefinitionClassName": "DebitCardDefinition",
        "paymentDataClassName": "CardData"
    },    
    {
        "id": "2F730F54469D3665A2EE",
        "description": "Visa Debito",
        "name": "Visa Debito",
        "code": "010611",
        "enable": true,
        "minimiumAmount": 1.00000000,
        "maximiumAmount": 999999.00000000,
        "paymentMethodType": "debit_card",
        "requireFundingSource": false,
        "availableFundingSources": [],
        "discount": 0,
        "paymentMethodDefinitionClassName": "DebitCardDefinition",
        "paymentDataClassName": "CardData"
    }
]
```
## Abonar con metodo externo 

Para ejecutar el pago de la transacción deberemos utilizar un POST con el método 
**/payments**. Para este método deberemos recurrir a una función con un protocolo encriptación (que se les enviara junto con los datos para operar) para encriptar los datos de la tarjeta, Los datos a encriptar son: firstNumbers, lastSixNumbers, securityCode, expirationYear, expirationMonth. 

En el request del método podemos ver los siguientes parámetros

Dato | nombre | Descripcion |
---------|----------|----------|
 transactionType | Tipo de transaccion  |Determina el tipo de transaccion a realizar
 amount |monto |Importe entero o decimal que se asignará al abonado.
 comments | Comentario  |Comentario 
 clientTransactionId | ID único de transacción  |Este es el ID de la transacción (debe ser único) 
 payment | Tipo de pago  |Este objeto representa el tipo de pago que se utilizara en esta transacción, determinando los parámetros requeridos.
|


Dentro del objeto payment podemos observar varios parámetros que definen el tipo de pago con sus respectivos datos requeridos.

Dato | nombre | Descripcion |
---------|----------|----------|
transactionType | Tipo de transaccion  |Determina el tipo de transaccion a realizar 
clientTransactionId | ID único de transacción    |Este es el ID de la transacción (debe ser único y puede ser igual que el anterior)
currency | Tipo de moneda  |Codigo del tipo de moneda en la que se expresa la factura en modo ISO
paymentMethod | Tipo del método de pago  |Objeto que envía el ID que determina que método de pago se utiliza
transactionData | Dato transaccional  |Este objeto posee los datos requeridos del método de pago a utilizar.

### Datos requeridos para abonar


Dato |  Descripcion |
---------|----------|
firstNumbers | Primeros 10 números de la tarjeta 
lastSixNumbers | Ultimos 6 numeros de la tarjeta 
securityCode | Codigo de seguridad 
holderName | Nombre que figura en la tarjeta 
expirationYear | Año de expiración 
expirationMonth | Mes de expiración  
holderIdentificationNumber | Numero de documento 
installments | Cuotas 
cardCapturedType | Tipo de captura de tarjeta 



A continuacion un ejemplo de como se deberia enviar el request

#### Ejemplo


```json
{
    "transactionType": "Sale",
    "amount": 5000.0,
    "comments": "BLABLA",
    "clientTransactionId": "50822614",
    "payment": {
        "transactionType": "Payment",
        "clientTransactionId": "935785395",
        "currency": {
            "code": "032"
        },
        "paymentMethod": {
            "id": "2F7319613B5B19F0E8DF"
        },
        "transactionData": {
            "firstNumbers": "4517660913",
            "lastSixNumbers": "611819",
            "securityCode": "267",
            "holderName": "ORIOLO LIDIA NOEMI",
            "expirationYear": 26,
            "expirationMonth": 7,
            "holderIdentificationNumber": "10719444",
            "installments": null,
            "cardCapturedType": "Manual"
        }
    }   
}

```

### Respuestas Posibles

El Request anterior puede obtener dos respuestas finales, Una aprobada y una rechazada como se mostrara en los ejemplos a continuación

#### Ejemplo Aprobado


```json
{
    "id": "4BF67C18358C05E9D63C",
    "number": 743708536,
    "created": "2021-07-26T15:16:37-03:00",
    "confirmationDate": "2021-07-26T15:16:43-03:00",
    "amount": 100.00,
    "description": "Cobro - 743708536",
    "state": "approved",
    "stateDetail": {
        "message": "Confirmada",
        "transactionType": "Sale"
    },
    "transactionType": "Sale",
    "payment": {
        "id": "215A7C18358C06AEE3FE",
        "number": 743708535,
        "created": "2021-07-26T15:16:37-03:00",
        "confirmationDate": "2021-07-26T15:16:42-03:00",
        "amount": 100.00,
        "description": "Deposito - 743708535",
        "state": "approved",
        "stateDetail": {
            "message": "Confirmada",
            "transactionType": "Payment"
        },
        "transactionType": "Payment",
        "clientTransactionId": "93578445397",
        "confirmationId": "1610809 - 000000575199",
        "transactionData": {
            "id": "660C7C183E372E6FE956",
            "className": "CardData",
            "depositDate": "2021-07-26T15:16:39.653-03:00",
            "lastSixNumbers": "088000",
            "holderName": "NOMBRE APELLIDO"
        },
        "paymentMethod": {
            "id": "2F73001164A91E0256C6",
            "description": "Maestro",
            "name": "Maestro",
            "code": "011132",
            "enable": true,
            "minimiumAmount": 0.00000000,
            "maximiumAmount": 999999.00000000,
            "paymentMethodType": "debit_card",
            "requireFundingSource": false,
            "availableFundingSources": [],
            "discount": 0,
            "paymentMethodDefinitionClassName": "DebitCardDefinition",
            "paymentDataClassName": "CardData"
        }
    },
    "clientTransactionId": "50822612337",
    "confirmationId": "",
    "product": {
        "id": "04623702D50920C2501E",
        "name": "Cobros varios",
        "code": "010087",
        "enable": true,
        "smallDescription": "Cobros",
        "vendor": {
            "id": "3B26138E97FF343A2456",
            "description": "Telerecargas",
            "name": "Telerecargas",
            "code": "004487",
            "enable": true,
            "imageUrl": "gv/telerecargas/logoTelerecargas.png"
        },
        "productGroup": {
            "id": "59840B8C51C320CAD6F2",
            "description": "Normal.",
            "name": "Normal.",
            "code": "000399",
            "enable": true
        },
        "minimium": 0,
        "maximium": 100000,
        "imageUrl": "gv/externalFiles/cobrosvarios-01.jpg",
        "productType": "Service",
        "transactionDataClassName": "PayoutData",
        "salePrice": 1.00000000
    },
    "transactionData": {
        "id": "3DEE7C18358C23D37E4C",
        "className": "PayoutData"
    }
}

```


#### Ejemplo rechazado

```json
{
    "id": "4BF67C18AC7915CBBBFF",
    "number": 743708542,
    "created": "2021-07-26T15:22:35-03:00",
    "amount": 100.00,
    "description": "Cobro - 743708542",
    "state": "rejected",
    "stateDetail": {
        "message": " -  TARJETA_INVALIDA",
        "transactionType": "Payment"
    },
    "transactionType": "Sale",
    "payment": {
        "id": "215A7C18AC792B49D4C4",
        "number": 743708541,
        "created": "2021-07-26T15:22:35-03:00",
        "amount": 100.00,
        "description": "Deposito - 743708541",
        "state": "rejected",
        "stateDetail": {
            "message": " -  TARJETA_INVALIDA",
            "transactionType": "Payment"
        },
        "transactionType": "Payment",
        "clientTransactionId": "93578445398",
        "transactionData": {
            "id": "660C7C18BDAF391BCD33",
            "className": "CardData",
            "depositDate": "2021-07-26T15:22:39.97-03:00",
            "lastSixNumbers": "088000",
            "holderName": "NOMBRE APELLIDO"
        },
        "paymentMethod": {
            "id": "2F73001164A91E0256C6",
            "description": "Maestro",
            "name": "Maestro",
            "code": "011132",
            "enable": true,
            "minimiumAmount": 0.00000000,
            "maximiumAmount": 999999.00000000,
            "paymentMethodType": "debit_card",
            "requireFundingSource": false,
            "availableFundingSources": [],
            "discount": 0,
            "paymentMethodDefinitionClassName": "DebitCardDefinition",
            "paymentDataClassName": "CardData"
        }
    },
    "clientTransactionId": "50822612338",
    "confirmationId": "",
    "product": {
        "id": "04623702D50920C2501E",
        "name": "Cobros varios",
        "code": "010087",
        "enable": true,
        "smallDescription": "Cobros",
        "vendor": {
            "id": "3B26138E97FF343A2456",
            "description": "Telerecargas",
            "name": "Telerecargas",
            "code": "004487",
            "enable": true,
            "imageUrl": "gv/telerecargas/logoTelerecargas.png"
        },
        "productGroup": {
            "id": "59840B8C51C320CAD6F2",
            "description": "Normal.",
            "name": "Normal.",
            "code": "000399",
            "enable": true
        },
        "minimium": 0,
        "maximium": 100000,
        "imageUrl": "gv/externalFiles/cobrosvarios-01.jpg",
        "productType": "Service",
        "transactionDataClassName": "PayoutData",
        "salePrice": 1.00000000
    },
    "transactionData": {
        "id": "3DEE7C18AC791F2E39C4",
        "className": "PayoutData"
    }
}

```
