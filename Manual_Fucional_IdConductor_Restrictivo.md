<link rel="stylesheet" type="text/css" href="style\style.css" />

<img src="images/satrack_head.png" alt="Flexbox Introduction">

# Manual funcional *ID Conductor Restrictivo*

## Objetivo

Los clientes `Koba` y `Petroleos Delta` necesitan activar y/o administrar la restricción de vehículos por la plataforma de Satrack. Adicional  quieren que los conductores permitidos queden guardados en la memoria de la unidad para evitar bloqueos de los vehículos por mala señal o que las tarjetas no sean identificadas.

Cuando se active el IDrestrictivo, se debe generar un acción o comportamiento en el vehículo o la unidad es decir, según la necesidad del cliente:
Para el cliente KOBA si se habilita el ID restrictivo la unidad mantendrá un comando de apagado si no se registra una tarjeta o serial permitido.
Para Petroleos Delta no se bloquea el vehículo, pero si se activa el Buzzer cuando se enciende el vehículo sin registrar ID o con ID no valido.


## Activación

Se define que el ingreso y manejo de id Conductor Restrictivo se activa por medio del esquema de `Aplicaciones` por base de datos para el módulo `ENCENDIDO RESTRICTIVO` con referencia `ER`

```sql
--Hades
USE BDClientes
GO
SELECT m.* 
FROM Aplicaciones.Modulos AS m 
WHERE m.Referencia = 'ER'
```

Se puede relacionar el módulo en cualquiera de las siguientes tablas del esquema de `Aplicaciones`

* Aplicaciones.**ModulosIdCliente**
* Aplicaciones.**ModulosPlaca**
* Aplicaciones.**ModulosUsuarios**

## Unidades, Firmware y Capacidades

El _id conductor restrictivo_ cuenta con una capacidad limitada en la memoria de la unidad que almacenará las llaves y estas unidades están integradas para su funcionamiento apartir de un versionamiento del **_firmware_ (igual o superior)** como se evidencia en la siguiente tabla:

| Unidad             | Versión firmware | Capacidad |
| ------------------ | ---------------- | --------- |
| CELLOCATOR-CR3003G | 44d              | 30        |
| CELLO-IQ40         | 36v              | 30        |
| CANIQ50-3G         | 36v              | 100       |

su representación lógica esta dada en la siguiente tabla de `sql`

```sql
--Hades
USE BDClientes
GO
SELECT * FROM dbo.IC_Firmware_Capacidad_Restictivo AS ifcr
```
Resultado:

<img src="images\IC_Firmware_Capacidad_Restictivo.png">

> Para que la unidad CELLO-IQ40 aplique Id Conductor restrictivo debe tener como mínimo la versión de firmware 36v donde el _FirmwareVersion_= 36 y el _FirmwareSubversion_= 22 (posición de la letra en el abecedario)

## Ingreso

1. **Acción del usuario:** Click en Servicios
   
    ![ingreso1](images\ingreso1.png)

2. **Acción del usuario:** Click en Identificación de conductor
   
   ![ingreso2](images\ingreso2.png)

3. **Acción del usuario:** Click en Asignar>Conductor a vehículo

    ![ingreso2](images\ingreso3.png)

4. Se evidencia la visualización de:
   * Pestaña `Encendido restrictivo`
   * En la pestaña `Asignación conductores a vehículos` en el paso `3` los campos `Estado` y `Capacidad de memoria`
  

    ![ingreso2](images\ingreso4.png)


## Encendido restrictivo

Pantalla en la que se permite la activación o desactivación del id Restrictivo por placa.

![EncendidoRestrictivo](images\EncendidoRestrictivo.png)

Se tiene dos tablas con los campos

* **Vehículos disponibles:** Placas del usuario
* **#Conductores/Capacidad:** Muestra la relación entre la cantidad de llaves que tiene asignada la unidad y la capacidad total de la unidad teniendo| en cuenta la [Activación](#Activación)

>> _**Nota:** Si la cantidad de llaves sobrepasa la capacidad de la unidad esta placa no podrá ser activada y se mostrará un comentario con el simbolo ![comentario](images\comentario.png) el cual dice "Para activar el encendido restrictivo, solo se permite asignar los conductores según la capacidad disponible"_

>> _**Nota:** Si la capacidad de una de las placas es igual a `0 (cero)` esta placa no prodrá ser activada y se mostrará un comentario con el simbolo ![comentario](images\comentario.png) el cual dice "La unidad no es compatible con el accesorio de ID conductor"_

### Operatividad

Se selecciona el check del vehiculo(s) ha activar o desactivar el Id restrictivo, aquellos que no estén disponibles no se permitirán chequear, la interación se realiza con los botones `Asignar` y `Desasigar`, pasando los vehículos de izquierda a derecha y veceversa.
Una vez modificados los estados de los vehículos de la manera adecuada se finalizará la operación con el boton `Aplicar Cambios`.
Aplicados los cambios se mostrará el siguiente popup:

![Aplicastecambios](images\Aplicastecambios.png)

## Asignación conductores a vehículos

Pantalla responsable de la administración de asignación y desasignación de los conductores(llaves) a los vehículos.

![Asignacionconductoresavehiculos](images\Asignacionconductoresavehiculos.png)

Realizada la [activación](#Activación) al cliente se mostrará los campos `Estado` y `Capacidad de memoria`

La tabla de asignaciones cuenta con los siguientes campos:

**Placa:** Identificador del vehículo en el que se agrupa todos los conductores autorizados para su manejo.

**Nombre conductor:** En este se muestra el nombre completo del conductor asignado

**Estado:** Identifica si el vehiculo tiene activo (`CON ENCENDIDO RESTRICTIVO`) o no (`SIN ENCENDIDO RESTRICTIVO`) el Id restrictivo que se administra en [encendido restrictivo](##Encendido-restrictivo).

**Capacidad de memoria:** Muestra la relación entre la cantidad de llaves (Conductores) que tiene asignada la unidad o se están asignando y la capacidad total de la unidad teniendo en cuenta la [Activación](#Activación)

>> _**Nota:** Los vehículos con `CON ENCENDIDO RESTRICTIVO` no permiten una asignación de conductores que sobrepasen su capacidad, se mostrará el siguiente popup:

![Nopuedesasignar](images\Nopuedesasignar.png)
