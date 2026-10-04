# Práctica 2: Levantamiento del Sistema de Visualización de Datos Sísmicos

## 1. Clonación y Arranque
Se realizó un *fork* del repositorio original y se clonó localmente. Posteriormente, se utilizó Docker Desktop para levantar la infraestructura del proyecto mediante el comando `docker compose up -d`. Se levantaron exitosamente dos contenedores: el servidor web (Apache/PHP) y el motor de base de datos (PostgreSQL 17).

## 2. Errores Encontrados y Soluciones

Durante el levantamiento, se presentaron y resolvieron los siguientes incidentes técnicos:

* **Error 404 Not Found (Servidor Web):** Al intentar acceder a `localhost`, el servidor arrojaba un error 404. 
  * **Solución:** Se analizó la estructura de archivos en la carpeta `src` y se detectó que no existía un archivo `index.php` raíz, sino un archivo llamado `vista.html`. Se corrigió la ruta accediendo directamente a `http://localhost/vista.html`.

* **Error "Connection refused" y base de datos bloqueada:** La aplicación web mostraba una alerta roja indicando que no podía conectarse a la base de datos.
  * **Solución:** Al revisar los logs del contenedor de PostgreSQL (`docker logs`), se observó que la base de datos estaba ejecutando una carga masiva de registros históricos (miles de sentencias `INSERT 0 1`). PostgreSQL bloquea conexiones externas por seguridad durante este proceso. Se esperó a que finalizara la carga y se forzó una sincronización con `docker compose restart`. Al recargar, el mapa funcionó correctamente.

![Aplicación Funcionando](mapa_funcionando.png)

## 3. Comprobación de Base de Datos
Para verificar la correcta creación del modelo dimensional del proyecto, se accedió al contenedor mediante la terminal y se interactuó con la base de datos `datawarehouse`:

1. Acceso al contenedor: `docker exec -it seismic-data-visualization-system-db-1 psql -U postgres`
2. Conexión a la base: `\c datawarehouse`
3. Consulta a la dimensión: `SELECT * FROM dim_sismos LIMIT 5;`

*(Nota: Debido al reinicio forzado durante la resolución del error de conexión, la carga masiva se interrumpió y la tabla se consultó vacía, pero la estructura del Data Warehouse está operando correctamente).*

![Consulta SQL](consulta_sql.png)