La inyección SQL (SQLi) es una vulnerabilidad de seguridad web que permite a un atacante interferir con las consultas que realiza una aplicación a su base de datos. Esto puede permitir que un atacante vea datos que normalmente no puede recuperar. Esto podría incluir datos que pertenecen a otros usuarios o cualquier otro dato al que pueda acceder la aplicación. En muchos casos, un atacante puede modificar o eliminar estos datos, provocando cambios persistentes en el contenido o el comportamiento de la aplicación.

En algunas situaciones, un atacante puede escalar un ataque de inyección SQL para comprometer el servidor subyacente u otra infraestructura de back-end. También puede permitirles realizar ataques de denegación de servicio.

## Cómo detectar vulnerabilidades de inyección SQL

Puede detectar la inyección de SQL manualmente utilizando un conjunto sistemático de pruebas en cada punto de entrada de la aplicación. Para hacer esto, normalmente enviarías:

- El carácter de comilla simple `'`y busca errores u otras anomalías.
- Alguna sintaxis específica de SQL que evalúa el valor base (original) del punto de entrada y un valor diferente, y busca diferencias sistemáticas en las respuestas de la aplicación.
- Condiciones booleanas como `OR 1=1`y `OR 1=2`y busque diferencias en las respuestas de la aplicación.
- Cargas útiles diseñadas para desencadenar retrasos de tiempo cuando se ejecutan dentro de una consulta SQL y buscar diferencias en el tiempo necesario para responder.
- Cargas útiles de OAST diseñadas para desencadenar una interacción de red fuera de banda cuando se ejecuta dentro de una consulta SQL y monitorear cualquier interacción resultante.

Alternativamente, puede encontrar la mayoría de las vulnerabilidades de inyección SQL de forma rápida y confiable utilizando Burp Scanner.

## Inyección SQL en diferentes partes de la consulta.

La mayoría de las vulnerabilidades de inyección SQL ocurren dentro de la `WHERE`cláusula de una `SELECT`consulta. Los evaluadores más experimentados están familiarizados con este tipo de inyección SQL.

Sin embargo, las vulnerabilidades de inyección SQL pueden ocurrir en cualquier ubicación dentro de la consulta y dentro de diferentes tipos de consulta. Algunas otras ubicaciones comunes donde surge la inyección SQL son:

- En `UPDATE`declaraciones, dentro de los valores actualizados o de la `WHERE`cláusula.
- En `INSERT`declaraciones, dentro de los valores insertados.
- En `SELECT`declaraciones, dentro del nombre de la tabla o columna.
- En `SELECT`declaraciones, dentro de la `ORDER BY`cláusula.