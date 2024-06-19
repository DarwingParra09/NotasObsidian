Cuando una aplicación es vulnerable a la inyección SQL y los resultados de la consulta se devuelven dentro de las respuestas de la aplicación, puede usar la `UNION`palabra clave para recuperar datos de otras tablas dentro de la base de datos. Esto se conoce comúnmente como ataque UNION de inyección SQL.

La `UNION`palabra clave le permite ejecutar una o más `SELECT`consultas adicionales y agregar los resultados a la consulta original. Por ejemplo:

`SELECT a, b FROM table1 UNION SELECT c, d FROM table2`

Esta consulta SQL devuelve un único conjunto de resultados con dos columnas, que contiene valores de columnas `a`y `b`en `table1`y columnas `c`y `d`en `table2`.

Para que una `UNION`consulta funcione, se deben cumplir dos requisitos clave:

- Las consultas individuales deben devolver el mismo número de columnas.
- Los tipos de datos de cada columna deben ser compatibles entre las consultas individuales.

Para llevar a cabo un ataque UNION de inyección SQL, asegúrese de que su ataque cumpla con estos dos requisitos. Esto normalmente implica descubrir:

- Cuántas columnas se devuelven de la consulta original.
- Qué columnas devueltas por la consulta original tienen un tipo de datos adecuado para contener los resultados de la consulta inyectada
