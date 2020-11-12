# Tutorial de PostgreSQL

Este documento pretende ser una fuente rapida de informacion sobre
herramientas y cosas relacionadas o utiles al momento de trabajar
con una base de datos PostgreSQL.

# Temas.

1. Listado de herramientas utiles.
1. Administracion de postgreSQL con PgAdmin4.
1. Introduccion estandar SQL.
1. Implementacion PostgreSQL de SQL.
1. Extraccion de datos con pandas.
1. Extraccion de datos con SqlAlchemy.
1. Insercion de registros.

---

# Listado de herramientas utiles.

Cuando trabajamos con una base de datos normalmente nos encontraremos
realizando una de las siguientes tareas/actividades:

1. Exploracion de datos.

   Esta tarea consiste en identificar el contenido de las tablas,
   y sus tipos de datos. Tambien analisis sencillos como cuentas y
   sumas agrupadas.

1. Lectura de datos para procesamiento.

   Esta tarea consiste en leer grandes cantidades de datos (mas de un registro)
   y realizar alguna operacion con estos datos, por ejemplo analisis
   matematicos/estadicos, generacion de reportes entre otros.

1. Escritura de datos.

   Esta tarea consiste bien sea un crear nuevos registros dentro de una tabla,
   o actualizar registros previamente existentes.

1. Administracion.

   Esta tarea consiste relizar actividades no relacionadas directamente con los
   datos, sino con las estructuras y modelos donde estos datos son almacenados;
   por ejemplo; la creacion, eliminacion e identificacion de tablas y campos;
   identificacion y creacion de indices sobre una columna; entre otras
   actividades.

Para la realizacion de estas actividades, a dia de hoy, encuentro que las
siguientes herramientas podran ser utiles:

1. PgAdmin https://www.pgadmin.org/

   Este es el administrador oficial de PostgreSQL. Consiste en una aplicacion
   que podemos instalar facilmente en todos los OS y conectarnos a ella
   mediante un navegador web.

   ![](pgadmin.PNG)

   Algunas de sus caracteristicas de interes son:

   1. Muy intuitivo.
   1. Te permite realizar casi todas las operacion con cero (0) conocimiento de
      SQL.
   1. Permite hacer y restaurar backups.
   1. Permite conectarnos a diferentes servidores.
   1. Nos muestra, y permite editar, objetos que en muchos casos ni sabemos que
      existen, lo cual nos da una gran visibilidad de todo el sistema.
   1. Permite monitorear el servidor, cosas de nerds, como numero de sesiones,
      transacciones por segundo y otro monton de cosas.

1. Pandas https://pandas.pydata.org/

   Pandas es una herramienta para manipulacion y analisis de datos, casi un
   estandar de facto cuando se trabaja con datos desde el lenguaje python.

   Algunas de sus caraceristicas de interes son:

   1. Conexion mediante una URI.
   1. Permite leer datos desde una base de datos SQL sin escribir SQL.
   1. Permite realizar complejas consultas a una base de datos SQl y obtener
      los resultados en un DataFrame.
   1. Muy util para leer varias tablas (completas) y posteriormente hacer joins
      en memoria cuando es muy pesado para la base de datos.
   1. Permite guardar un DataFrame en una base de datos SQL.

      ### ADVERTENCIA!!!

      ### despues no digas que no te avisamos.

      ### Esto puede borrar datos pre-existentes en la tabla y su estructura!!!.

1) SqlAlchemy https://www.sqlalchemy.org/

   SqlAlchemy es un ORM (object relational mapper), es decir convierte
   objetos sql(registros) en objetos python, muy utilizado por su ligereza,
   que en este caso es de interes por las abstracciones que implementa, las
   cuales nos permitiran conectarnos facilmente
   a la base de datos usando simplemente una URI.

   Caracteristicas de interes:

   1. Conexion mediante una URI.
   1. Puede conectarse multiples bases de datos.
   1. Implementa cursores para lectura de datos, esto permite procesar grandes
      cantidades de datos sin cargarlos todos en memoria.
   1. Permite realizar complejas consultas de escritura sobre una o multiples
      tablas.

Teniendo en cuentas estas herramientas, y bajo la mirada de estas
actividades/tareas, se presenta a continuacion una pequeña tabla, donde se
relacionan cada herramienta y la facilidad que esta herramienta presta al
momento de realizar cada tarea.

| Herramienta | Exploracion de datos | Lectura de datos para procesamiento | Escritura de datos | Administracion |
| ----------- | -------------------- | ----------------------------------- | ------------------ | -------------- |
| PgAdmin4    | 2                    | 0                                   | 2                  | 3              |
| Pandas      | 3                    | 2                                   | 1                  | 0              |
| SQLAlchemy  | 1                    | 3                                   | 3                  | 1              |

\* Un valor mas alto indica mas facilidad, y un valor de 0 indica imposibilidad
de realizar la tarea.

# Introduccion al estandar SQL.

https://es.wikipedia.org/wiki/SQL

El lenguaje estandar SQL, es un lenguaje de programacion declarativo,
inicialmente desarrollado por matematicos en los años 70 y en evolucion
continua hasta el dia de hoy, cuando es ampliamente utilizado por diversos
motores de bases de datos, entre los mas comunes:
MySQL, PostgreSQL, SQL Server, Oracle y SQlite ademas de otras herramientas de
ETL y procesamiento de datos.

Aunque es un estandar, cada implementacion es diferente, y pueden haber grandes
cambios de un motor a otro.

Este documento solo pretende registrar la forma de leer, escribir, actualizar y
borrar datos de una base de datos, por lo que se descartan todas las sentencias
relacionadas a la administracion de objetos y estructuras del motor, tampoco se
toca la configuracion del motor mediante la interfaz SQL.

para propositos de ejemplos se tendran las siguientes tablas:

- Alumnos

  | id  | nombre        | telefono   | edad | curso_id |
  | --- | ------------- | ---------- | ---- | -------- |
  | 1   | Simon pedro   | 111111111  | 30   | 12       |
  | 2   | Juan pablo II | 264264264  | 25   | 11       |
  | 3   | Benedicto     | 5656656556 | 30   | 11       |
  | 4   | Francisco     | 45656453   | 15   | 11       |
  | 5   | José chepe    | 87987210   | 18   | 12       |

- Cursos

  | id  | nombre | horas_semanales |
  | --- | ------ | --------------- |
  | 11  | latin  | 8               |
  | 12  | hebreoo  | 16              |

## Sentencias basicas.

Una sentencia hace referencia a una instruccion o conjunto de ordenes que se le da al servidor de la base de datos para ser procesadas.

1.  INSERT, Esta es la sentencia encargada de ingresar nuevos registros en la
    tabla de destino, su sintaxis mas tipica es de la siguiente forma:

    ````sql
    -- Registrar a Juana e inscribirla en el curso de latin
    INSERT INTO "Alumnos"("id", "nombre", "telefono", "curso_id")
    VALUES (6, "Juana", "09898909", 11);

    -- Registrar varias personas al mismo tiempo
    INSERT INTO "Alumnos"("id", "nombre", "telefono", "curso_id")
    VALUES (6, "Juana", "09898909", 11), (7, "Willian", "0258963", 12);
    ````

1.  UPDATE, Esta es la sentencia encargada de ingresar actualizar o cambiar
    registros previamente existentes, algunos algunos ejemplos:

    ````sql
    -- Cambiar la duracion de todos curso a 10 horas
    UPDATE "Cursos"
    SET "horas_semanales" = 10;

    -- Aumentar la duracion del curso en dos horas
    UPDATE "Cursos"
    SET "horas_semanales" = "horas_semanales" + 2;

    -- Cambiar a todos los Alumnos que se llamen "Juana" de curso
    UPDATE "Alumnos"
    SET "curso_id" = 11
    WHERE "nombre" = 'Juana';

    -- Varias columnas al mismo tiempo
    UPDATE "Alumnos"
    SET
        "curso_id" = 11,
        "edad" = "edad" + 1
    WHERE "nombre" ~ 'Juana';

    -- el operador `~` permite usar regex POSIX
    ````

1.  DELETE, esta sentencia nos permite borrar registros.

    ### ADVERTENCIA!!!

    ### Se recomienda poner siempre un WHERE y un LIMIT

    ### Lean 10 o 20 veces si es necesario, y no cometan los

    ### mismos errores mios.

    ```sql
    -- Eliminar a benedicto
    DELETE FROM "Alumnos"
    WHERE "nombre" ~* 'benedicto'
    LIMIT 1;

    -- el operador `~*` tambien permite usar regex POSIX, pero
    -- a diferencia de `~` es "case insensitive"

    -- Eliminar a todos los alumnos de latin, por que nadie les entiende
    DELETE FROM "Alumnos"
    INNER JOIN "Cursos"
        ON "Cursos"."id" = "Alumnos"."curso_id"
    WHERE "Cursos"."nombre" = 'latin';
    ```

1.  SELECT, Esta es la sentencia mas usada, ya que es la encargada de obtener
    datos desde la tabla y retornarlos, tambien permite obtener resultados de
    calculos asi como hacer joins entre tablas.

        La estructura "general" (realmente puede complicarse mas) de esta sentencia
        es la siguiente:

    ````sql
    SELECT [{ALL|DISTINCT}]
        <nombre_campo>[, <nombre_campo>...]

    FROM {<nombre_tabla>}
    [WHERE <condición> [{AND|OR} <condición>...]]

    [GROUP BY <nombre_campo>[, <nombre_campo>...]]

    [HAVING <condición> [{AND|OR} <condición>...]]

    [ORDER BY {<nombre_campo>|<indice_campo>} [{ASC|DESC}][,
    {<nombre_campo>|<indice_campo>} [{ASC|DESC}]]];
    [LIMIT limite_registros]
    ````

        Algunos ejemplos mas concretos pueden ser:

    ````sql
    -- Seleccionar toda la informacion de la tabla de alumnos
    SELECT * FROM "Alumnos";

    -- Traer el nombre curso con mayor duracion
    SELECT "nombre"
    FROM "Cursos"
    ORDER BY "horas_semanales" DESC
    LIMIT 1

    -- Traer la duracion del curso mas largo
    SELECT MAX("horas_semanales") AS "duracion maxima"
    FROM "Cursos"

    -- Traer el nombre de los alumnos, y las horas a la semana que estudian
    SELECT "Alumnos"."nombre", "Cursos"."horas_semanales"
    FROM "Alumnos"
    INNER JOIN "Cursos"
        ON "Cursos"."id" = "Alumnos"."curso_id"

    -- Distintos nombre de alumnos
    SELECT DISTINCT "nombre"
    FROM "Alumnos"

    -- Numero de alumnos por curso
    SELECT "Cursos."."nombre", COUNT("Alumnos"."id")
    FROM "Alumnos"
    INNER JOIN "Cursos"
        ON "Cursos"."id" = "Alumnos"."curso_id"
    GROUP BY "Cursos"."nombre"

    -- Los dos cursos con mas "alumno-hora"
    -- notese que "ORDER BY 2 DESC" quiere decir que ordene por la segunda(2)
    -- columna de resultados, es decir, "alumno-hora"
    SELECT
        "Cursos."."nombre",
        COUNT("Alumnos"."id")*"Cursos"."horas_semanales" AS "Alumno-hora"
    FROM "Alumnos"
    INNER JOIN "Cursos"
        ON "Cursos"."id" = "Alumnos"."curso_id"
    GROUP BY "Cursos"."nombre"
    ORDER BY 2 DESC
    LIMIT 2
    ````

# Implementacion de postgreSQL.

### Texto vs nombres de columnas.

    En este punto ya debio notar que se usa comilla sencilla para indicarle al
    motor que se hace referencia a una cadena de texto, y las comillas dobles
    quedan reservadas para hacer refencia a objetos del motor; por ejemplo
    nombres de las bases de datos, de los esquemas o las tablas; aunque no
    siempre es necesario usar estas comillas dobles.

    Estas comillas son obligatorias si al momento de crear el objeto se creo
    con ellas.

### DISTINCT

Hay que tener en cuenta que la siguiente sintaxis es valida en implementaciones
como SQlite y MySQL, pero son **INVALIDAS/ILEGALES** en otras como PostgreSQL y SQL Server, aunque son muy convenientes.

Por ejemplo en MySql podemos hacer lo siguiente:

```sql
-- si encontrar al alumno de mayor edad por cada curso.
--
-- Notese que en mysql las comillas (") no se usan de la misma forma.
SELECT nombre
FROM Alumnos
GROUP BY curso_id
ORDER BY edad desc
```

La anterior sentencia es **INVALIDA** en **PostgreSQL** ya que no se puede hacer un
**GROUP BY** y poner en la lista de campos a seleccionar una columna que, o no esta en el **GROUP BY** o no esta dentro de una funcion de agregacion
[MIN, MAX, COUNT...]

La forma adecuada en PostgreSQL seria:

```sql
SELECT DISTINCT ON("curso_id") nombre
FROM "Alumnos"
ORDER BY "curso_id", edad DESC
```

El operador `DISTINCT ON(column1[, column2, ... columnN])` es muy similar
al operador `GROUP BY` y puede entenderse de la siguiente forma:
Para cada grupo de columnas `"column1[, column2, ... columnN]"` seleccione
**el primer registro** y retorne las columnas de las sentencia `SELECT`,
en nuestro ejemplo, esto es la columna `nombre`.

Notese que **"el primer registro"** podria ser cualquiera, y podria no ser
siempre lo mismo, por lo que PostgreSQL recomienda que las columnas
`"column1[, column2, ... columnN]"` esten todas en el comienzo del
`ORDER BY`

### OVER PARTITION BY

Otra caracteristica que esta presente en postgreSQL y no esta disponible en
otros motores son las funciones de ventanas, estas funciones nos permiten
realizar una operacion, tipicamente una agregacion, sobre un conjunto de datos
que es diferente a los datos que estan siendo retornados.

El siguente ejemplo muestra como calcular la edad de cada alumno
comparada con la edad promedio del curso.

```sql
SELECT "nombre", "edad", "curso_id", avg("edad") OVER (PARTITION BY "curso_id") FROM "Alumnos";

-- para cada alumno se calcula el "avg" de edad sobre
-- las celdas que esten en la misma "partition"
-- la cual corresponde a "curso_id"
```

| nombre        | edad | curso_id | avg   |
| ------------- | ---- | -------- | ----- |
| Simon pedro   | 30   | 12       | 24    |
| José chepe    | 18   | 12       | 24    |
| Juan pablo II | 25   | 11       | 23,33 |
| Benedicto     | 30   | 11       | 23,33 |
| Francisco     | 15   | 11       | 23,33 |

Estas particiones tambien pueden tener un `ORDER BY` por ejemplo,
`OVER (PARTITION BY "curso_id" ORDER BY "edad" DESC` pero en este caso
la particion tiene un significado un poco diferente, ya que para cada fila se
realizara la agregacion con los datos "anteriores" o que estan primero sobre
la particion que el dato actual, por ejemplo:

```sql
SELECT
    "nombre",
    "edad",
    "curso_id",
    avg("edad") OVER (PARTITION BY "curso_id" ORDER BY "edad" DESC) AS "avg_desc",
    avg("edad") OVER (PARTITION BY "curso_id" ORDER BY "edad" ASC) AS "avg_asc"
    FROM "Alumnos";
```

| nombre        | edad | curso_id | avg_desc | avg_asc |
| ------------- | ---- | -------- | -------- | ------- |
| Simon pedro   | 30   | 12       | 30       | 24      |
| José chepe    | 18   | 12       | 24       | 18      |
| Juan pablo II | 25   | 11       | 27.5     | 20      |
| Benedicto     | 30   | 11       | 30       | 23.33   |
| Francisco     | 15   | 11       | 23.33    | 15      |

# Extraccion de datos con pandas.

Nada que decir... Simple

```bash
python -m pip install "pandas==1.1.1" "psycopg2-binary==2.8.5" "SQLAlchemy==1.3.19"
```

```python
import pandas as pd
URI="postgresql+psycopg2://CiscoUser:CiscoCon@somedb.cluster-sdsad.us-east-1.rds.amazonaws.com:5432/test"

df_alumnos = pd.read_sql("Alumnos", URI)

SQL = """
SELECT
    "Alumnos"."nombre" AS "NombreAlumno",
    "Cursos"."nombre" AS "NombreCurso"
FROM "Alumnos"
INNER JOIN "Cusos"
    ON "Alumnos"."curso_id" = "Cursos"."id"
"""

df_alumnos_con_nombre_Y_curso = pd.read_sql(SQL, URI)
```

# Extraccion de datos con SQLAlchemy.

Supongamos el siguiente codigo base para todos los ejemplos.

```python
from sqlalchemy import create_engine
URI="postgresql+psycopg2://CiscoUser:CiscoCon@somedb.cluster-sdsad.us-east-1.rds.amazonaws.com:5432/test"

DB_ENGINE = create_engine(URI)
```

Si queremos convertir la lista de alumnos en un documento JSON o un DataFrame

```python
import json
SQL = 'SELECT * FROM "Alumnos"'

with DB_ENGINE.connect() as conn:
    alumnos_cursor = conn.execute(SQL)
    alumnos = [dict(alumno) for alumno in alumnos_cursor]

alumnos_json = json.dumps(alumnos)
df_alumnos = pd.DataFrame(alumnos)
```

Notese que en el codigo anterior, la sentencia SQL podria ser de cualquier tipo,
por ejemplo un ``UPDATE``, la unica diferencia es la respuesta de 
``conn.execute`` no seria un iterable con los resultados.

```python
SQL = 'SELECT "id","nombre"  FROM "Alumnos"'

with DB_ENGINE.connect() as conn:
    alumnos_cursor = conn.execute(SQL)
    for alumno in alumnos_cursor:

        # la variable alumno es una instancia de <class 'sqlalchemy.engine.result.RowProxy'>
        # y puede ser accedida como tupla o diccionario, se recomienda
        # cambiar el tipo antes de enviarla a otra funcion

        # imprime el id, ya que se accede como tupla
        print(alumno[0])

        # imprime el nombre
        print(alumno[1])

        # imprime el id
        print(alumno["id"])

        # imprime el nombre
        print(alumno["nombre"])

```

Si la tarea que se va a realizar con cada alumno
consume demasiada memoria, o base de datos es muy grande y cada
registro puede ser procesado de forma independiente, lo ideal es
no traerlos todos al mismo tiempo, en lugar de ello, lo
aconsejable es traer a cada alumno y realizar la operacion,
por ejemplo:

```python
import time
SQL = 'SELECT * FROM "Alumnos"'

with DB_ENGINE.connect() as conn:
    # alumnos_cursor es solo una variable, no toda la base
    # de datos.
    alumnos_cursor = conn.execute(SQL)

    # trae a cada alumno de a 1 en 1, y los golpea durante 60 seg
    for alumno in alumnos_cursor:
        t0 = time.time()
        while time.time() - t0 < 60:
            golpear(dict(alumno))

```

Para escribir datos lo podemos hacer de la siguiente forma

```python

alumnos_list = [
    {"name": "Daniel", "age": 11},
    {"name": "Carolina", "age": 13}
]
SQL = """INSERT INTO Alumnos("Nombre", "edad)
VALUES(%(name)s, %(age)s)"""

with DB_ENGINE.connect() as conn:
    print("Registrando alumnos")
    conn.execute(SQL, *alumnos_list)
    print(f"{len(alumnos_list)} alumnos fueron registrados con exito")

## Lo siguiente es equivalente
alumnos_list = [
    ("Daniel", 11),
    ("Carolina", 13)
]
SQL = """INSERT INTO Alumnos("Nombre", "edad)
VALUES(%s, %s)"""

with DB_ENGINE.connect() as conn:
    print("Registrando alumnos")
    conn.execute(SQL, *alumnos_list)
    print(f"{len(alumnos_list)} alumnos fueron registrados con exito")

```
