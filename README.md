# BEXCOIN (Blockchain Exchange Coin)

## Introducción

BEXCOIN es una moneda con un objetivo bien especifico: Realizar el intercambio de criptomonedas de una forma desentralizada y segura.

Esta desentralización es lograda gracias al uso de la misma tecnologia utilizada por las criptomonedas: blockchain.

## Planteo del Problema

Supongamos que tenemos dos personas P1 y P2 interesadas en realizar un intercambio de de moneda:
* P1 tiene moneda MA y P2 tiene moneda MB.
* P1 y P2 acuerdan intercambiar XA cantidad de moneda MA por XB cantidad de moneda MB.
* P1 tiene la cuenta C1MA para la moneda A y C1MB para la moneda B.
* P2 tiene la cuenta C2MA para la moneda A y C2MB para la moneda B.

Esta claro que para que el intercambio sea considerado exitoso, tienen que llevarse a cabo las siguientes operaciones con éxito:
* C1MA --- XA MA ---> C2MA (P1 transfiere la cantidad XA de moneda MA a P2).
* C2MB --- XB MB ---> C1MB (P2 transfiere la cantidad XB de moneda MB a P1).

El problema de realizar estas operaciones es que es necesario confianza, de forma que se garantice que nadie va a estafar al otro.

Si P1 y P2 tienen confianza uno en el otro, entonces lo más probable es que nadie sea estafado.

Cuando no existe confianza, llegamos al modelo que existe hoy: Agregamos un tercero de confianza.

El problema es que el tercero de confianza termina centralizando las operaciones, con las siguientes consecuencias:
* Aplica tarifas por realizar el intercambio.
* Nadie asegura que en algún momento pueda estafar a todos sus clientes.

Con lo cual volvemos al problema principal: la confianza.

## Solución

Para solucionar el problema de la confianza, con mirar las criptomonedas, vemos que su implementación solucionó justamente este problema al eliminar la confianza como item clave de la solución, aplicando blockchain.

En el caso de los intercambios de moneda, se podría hacer algo similar, nada más que se precisa de una vuelta de tuerca adicional para que sea realmente posible implementar el intercambio eliminando la necesidad de confianza.

### Creando la Transacción

Siguiendo con el ejemplo anterior, P1, P2 o un tercero creará la transacción de intercambio, que a grandes razgos tendrá las siguientes informaciones:
1. C1MA --- XA MA ---> C2MA (P1 transfiere la cantidad XA de moneda MA a P2).
2. C2MB --- XB MB ---> C1MB (P2 transfiere la cantidad XB de moneda MB a P1).
* FLB: Fecha limite de realización de bloqueos.

Con la creación de la transacción, es generado un **IDBEX** (este ID será utilizado más adelante para identificar que una transferencia hecha en alguna de las monedas implicadas forma parte de la transacción).
Por cada operación de transferencia es generado un índice de forma de poder identificar dicha transferencia dentro de la transacción.

Simplemente la transacción será una descripción del intercambio a realizar.

Hasta ahora no se ha echo ninguna transferencia efectiva, con lo cual es una transacción a confirmar.

El resultado es el siguiente (transacción de intención de intercambio):

|BLOQUE de BEX|
|---|
|C1MA --- XA MA ---> C2MA (IDBEX-1, FLB)|
|C2MB --- XB MB ---> C1MB (IDBEX-2, FLB)|


### Bloqueo de Fondos

La vuelta de tuerca indicada antes se deberá ver en la incorporación de un mecanismo de **bloqueo de fondos** en toda criptomoneda que quiera formar parte de la red de intercambio BEX.

Este bloqueo de fondos sería realizado por el propietario de la cuenta con las intenciones de hacer un intercambio.
De esta forma dicho bloqueo de fondos pasaria a formar parte de del blockchain de la moneda bloqueda.

En nuestro ejemplo:
* C1MA bloquearia una cantidad XA de moneda MA con referencia a **IDBEX-1**.
* C2MB bloquearia una cantidad XB de moneda MB con referencia a **IDBEX-2**.

Ambos bloqueos en caso de ser válidos pasarían a ser parte del blockhain correspondiente a cada moneda.

Hasta ahora no se ha echo ninguna transferencia efectiva, simplemente fue realizado el bloqueo de los fondos a intercambiar por el propietario de cada cuenta de origen (en este caso las cuentas de origen son C1MA y C2MB).

El resultado es el siguiente:

|BLOQUE de MA|
|---|
|C1MA bloquea una cantidad XA de moneda MA con referencia a IDBEX-1|

|BLOQUE de MB|
|---|
|C2MB bloquea una cantidad XB de moneda MB con referencia a IDBEX-2|


### Confirmando los bloqueos

La transacción creada en el ejemplo será adicionada al blockchain solo en caso que sean efectivamente verificados los bloqueos.

Dada la naturaleza publica que presenta la solución de blockchain, su verificación será tan simple como la realización de una consulta al blockchain especifico de cada moneda, verificando que cuadren importes, cuentas de origen y el IDBEX.

En caso de ser verificados los bloqueos, la transacción será adicionada al blockchain BEX.

Hasta ahora no se ha echo ninguna transferencia efectiva, simplemente:
* Fueron bloqueados los fondos de las cuentas origen y adicionados a los respectivos blockchain.
* Fue adicionada la transacción de intercambio al blockchain BEX.

El resultado es el siguiente (transacción de intercambio efectivo):

|BLOQUE de BEX|
|---|
|C1MA --- XA MA ---> C2MA (IDBEX-1)|
|C2MB --- XB MB ---> C1MB (IDBEX-2)|

### Transferencia

Otra vuelta de tuerca tiene que ver con hacer efectivas las transferencias a partir de la confirmación de la transacción de intercambio en el blockchain BEX.

Una vez confirmada una transacción de intercambio BEX, utilizando sus informaciones y verificando el bloqueo existente en el blockchain, serán hechas efectivas las transferencias en cada moneda.

El resultado es el siguiente:

|BLOQUE de MA|
|---|
|C1MA --- XA MA ---> C2MA (IDBEX-1)|

|BLOQUE de MB|
|---|
|C2MB --- XB MB ---> C1MB (IDBEX-2)|

### Consideraciones de Fecha Limite y Desbloqueos
* La transacción de intercambio no podrá ser adicionada al blockchain BEX en una fecha posterior a la fecha limite FLV.
* En caso de haber superado la fecha limite FLV y no haber sido adicionada la transacción de intercambio al bloque BEX, los involucrados podrán realizar una operación de desbloqueo de fondos en el blockchain de la moneda correspondiente.

Si fuese realizada una operación de debloqueo, el resultado sería el siguiente:

|BLOQUE de MA|
|---|
|C1MA desbloquea XA con referencia a IDBEX-1|

|BLOQUE de MB|
|---|
---
|C2MB desbloquea XB con referencia a IDBEX-2|

### Cantidad de Transferencias

La cantidad de transferencias dentro de una transacción BEX no tiene por qué estar limitada a solo dos.

El beneficio de un sistema de intercambio descentralizado es que permitiria realizar intercambios múltiples en una misma transacción.

Por ejemplo:
* 2 personas podrían hacer un intercambio involucrando 7 monedas distintas.
* 10 personas podrían participar de un intercambio, utilizando 4 monedas distintas.
* Otro tipo de situaciones donde la complejidad no tendría límites a los existentes hoy en ekl mercado del intercambio.

### Mineración

Obviamente, para poder mantener el blockchain BEX sería necesario mineración de forma que sean generados los bloques.

En principio la recompensa por generación de bloques sería la emisión de moneda BEX para el que consiga generar el bloque.

### Tasas Para Generación de Transacciones

De forma que exista un compromiso por parte de los usuarios y que no creen transacciones de intensión de intercambio de forma indiscriminada, será cobrada una tasa a definir (en moneda BEX) por la creación de cada transacción.

Esta tasa será utilizada para solventar la transacción de intención de intercambio y la transacción de intercambio efectivo.
