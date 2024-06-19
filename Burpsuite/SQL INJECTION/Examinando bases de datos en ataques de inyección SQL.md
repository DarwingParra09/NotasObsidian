
Para explotar las vulnerabilidades de inyección SQL, a menudo es necesario encontrar información sobre la base de datos. Esto incluye:

- El tipo y versión del software de base de datos.
- Las tablas y columnas que contiene la base de datos.

## Consultar el tipo y la versión de la base de datos.

Potencialmente, puede identificar tanto el tipo como la versión de la base de datos inyectando consultas específicas del proveedor para ver si alguna funciona.

Las siguientes son algunas consultas para determinar la versión de la base de datos para algunos tipos de bases de datos populares:

|                       |                           |
| --------------------- | ------------------------- |
| Tipo de base de datos | Consulta                  |
| Microsoft, MySQL      | `SELECT @@version`        |
| Oráculo               | `SELECT * FROM v$version` |
| PostgreSQL            | `SELECT version()`        |

Por ejemplo, podrías utilizar un `UNION`ataque con la siguiente entrada:

`' UNION SELECT @@version--`

Esto podría devolver el siguiente resultado. En este caso, puedes confirmar que la base de datos es Microsoft SQL Server y ver la versión utilizada:

`Microsoft SQL Server 2016 (SP2) (KB4052908) - 13.0.5026.0 (X64) Mar 18 2018 09:11:49 Copyright (c) Microsoft Corporation Standard Edition (64-bit) on Windows Server 2016 Standard 10.0 <X64> (Build 14393: ) (Hypervisor)`

# Caso 20: ataque de inyección SQL, consulta del tipo y versión de la base de datos en MySQL y Microsoft

Esta práctica de laboratorio contiene una vulnerabilidad de inyección SQL en el filtro de categoría de producto. Puede utilizar un ataque UNION para recuperar los resultados de una consulta inyectada.

Para resolver la práctica de laboratorio, muestre la cadena de versión de la base de datos

Se identifica los números de columnas que encontramos y con ello enviamos la siguiente petición para que nos devuelva la versión de la base de datos que esta siendo utilizada, para esta ocasión la base de datos es MySQL

![[Pasted image 20240607165035.png]]

Ya cuando tenemos claro el numero de columnas que se trabajan en esta base de datos, buscamos cual de estas posiciones nos devuelve un valor de cadena o texto, con la petición que se hace en la imagen anterior nos devuelve un código de estado 200 y si bajamos podemos encontrar la versión de la base de datos en función.

![[Pasted image 20240607165409.png]]

## Listado del contenido de la base de datos.

La mayoría de los tipos de bases de datos (excepto Oracle) tienen un conjunto de vistas llamado esquema de información. Esto proporciona información sobre la base de datos.

Por ejemplo, puede realizar una consulta `information_schema.tables`para enumerar las tablas de la base de datos:

`SELECT * FROM information_schema.tables`

Esto devuelve un resultado como el siguiente:
```
TABLE_CATALOG TABLE_SCHEMA TABLE_NAME TABLE_TYPE ===================================================== 
MyDatabase dbo Products BASE TABLE 
MyDatabase dbo Users BASE TABLE 
MyDatabase dbo Feedback BASE TABLE`
```

Este resultado indica que hay tres tablas, llamadas `Products`, `Users`y `Feedback`.

Luego puede realizar consultas `information_schema.columns`para enumerar las columnas en tablas individuales:

`SELECT * FROM information_schema.columns WHERE table_name = 'Users'`

Esto devuelve un resultado como el siguiente:

```
TABLE_CATALOG TABLE_SCHEMA TABLE_NAME COLUMN_NAME DATA_TYPE ================================================================= 
MyDatabase dbo Users UserId int 
MyDatabase dbo Users Username varchar 
MyDatabase dbo Users Password varchar
```

Este resultado muestra las columnas de la tabla especificada y el tipo de datos de cada columna.

## Caso 21: ataque de inyección SQL, enumerando el contenido de la base de datos en bases de datos que no son Oracle

Esta práctica de laboratorio contiene una vulnerabilidad de inyección SQL en el filtro de categoría de producto. Los resultados de la consulta se devuelven en la respuesta de la aplicación, por lo que puede utilizar un ataque UNION para recuperar datos de otras tablas.

La aplicación tiene una función de inicio de sesión y la base de datos contiene una tabla que contiene nombres de usuario y contraseñas. Debe determinar el nombre de esta tabla y las columnas que contiene, luego recuperar el contenido de la tabla para obtener el nombre de usuario y la contraseña de todos los usuarios.

Para resolver la práctica de laboratorio, inicie sesión como `administrator`usuario.

El siguiente laboratorio no indica que la culminación de este es obteniendo las credenciales del usuario administrador.
Primero debemos saber con que versión de base de datos estamos tratando para poder agilizar la búsqueda de las bases de datos.

![[Pasted image 20240607201647.png]]

Conocemos ya versión de la base de datos, ahora con esto puede ser mas ágil la búsqueda de los datos

![[Pasted image 20240607201714.png]]

La petición que encontramos en la imagen nos sirve para mostrarnos las tablas que pueden haber dentro de la base de datos

![[Pasted image 20240607202737.png]]

Encontramos una columna con el nombre de users, podemos investigar para saber si podemos llegar a encontrar información útil

![[Pasted image 20240607202754.png]]

Dentro de la siguiente petición estamos pidiendo las columnas que puedan haber dentro de la tabla users_ojzcaw, nos devuelve un código de estado 200 con la información de dos columnas

![[Pasted image 20240607204248.png]]

![[Pasted image 20240607204318.png]]

Ya que tenemos estas dos columnas, es hora de investigar que hay dentro de ellas, pero haremos que salga toda la información en una sola petición, así que pedimos que tanto la columna username como password pueda ser mostrada teniendo como referencia que vienen de la tabla users_ojzc

![[Pasted image 20240607204847.png]].

Nos devuelve las credenciales de 3 usuarios, más nuestro objetivo son las credenciales de usuario administrador, nos dirigimos a Log in de la pagina y esta nos da acceso a la cuenta

![[Pasted image 20240607204943.png]]

![[Pasted image 20240607205109.png]]
