# MUNICIPIO-TECNO-VALLE
SISTEMA DE GESTION DE TRANSPORTE
# Municipio de Tecno Valle - TecnoMovil Data

## Procesamiento funcional de datos para un sistema de transporte urbano

Este repositorio contiene la solución de la **EA3 de Programación Orientada a Objetos 2**. La actividad se presenta como una simulación académica del **Municipio de Tecno Valle** y de su problemática en materia de transporte.

En el ejercicio se tomaron nombres de estaciones conocidas del Metro de Medellín para representar los recorridos.

> **Nota:** El proyecto no representa datos reales de operación del Metro de Medellín.

## Idea del ejercicio

El punto de referencia del ejemplo es la estación **San Antonio**. A partir de ella se simulan recorridos hacia otras estaciones, por ejemplo:

- **R2:** San Antonio - San Javier.
- **R4:** San Antonio - Alpujarra.
- **R6:** San Antonio - Parque Berrío.
- **R8:** San Antonio - Industriales.

También se presentan registros de San Javier, Alpujarra, Parque Berrío y Poblado como puntos de entrada o salida para mostrar que un usuario puede iniciar o terminar su recorrido en diferentes estaciones.

## Qué hace el programa

El código procesa una lista de registros y realiza las seis tareas solicitadas en el caso de estudio:

1. Clasifica la ruta crítica como regla del límite del umbral de 4 entradas, con lo cual alerta como crítica.
2. Detecta las 4 entradas de rutas críticas con este umbral simulado.
3. Calcula la afluencia por estación y agrupa entradas por hora para identificar los momentos de mayor flujo.
4. Cuenta las rutas más usadas.
5. Organiza las estaciones visitadas por cada usuario.
6. Calcula el promedio de tiempo entre registros consecutivos de usuarios.

## Programación funcional utilizada

### Funciones puras

Los métodos de filtrado y análisis reciben datos y entregan un resultado sin modificar las listas originales.

### Inmutabilidad

`RegistroTransporte` utiliza atributos `final` y la colección principal se construye con `List.of`, es decir, no se modifica durante los análisis.

### Lambdas

Se utilizan expresiones lambda para indicar la condición que se debe aplicar:

```java
r -> r.getAccion().equals("entrada")
```

### Streams

Los `Streams` permiten filtrar, ordenar, agrupar y transformar los registros de forma declarativa.

### Funciones de orden superior

Una función puede recibir otra función como parámetro.

- `filtrar` recibe un `Predicate`.
- `transformar` recibe un `Function`.

### Composición

`andThen` se utiliza al final del programa. Primero se obtiene la estación y después se convierte el texto a mayúsculas. Esta es la última acción en la ejecución de sentencias.

## Cómo ejecutar

El programa puede ejecutarse utilizando Java Online, Visual Studio Code o un entorno con **JDK 8 o superior**. No se utilizan frameworks externos.

### Compilar

```bash
javac SistemaTransporte.java
```

### Ejecutar

```bash
java SistemaTransporte
```

## Resultado de la prueba

El tiempo promedio de los registros simulados es de **20.00 minutos**.

La ejecución del programa fue validada en Java Online, Visual Studio Code, `javac` y `java`.

El programa genera los siguientes resultados de salida:

- Afluencia por estación.
- Registros por hora.
- Rutas más utilizadas.
- Patrones de viaje por usuario.
- Tiempo promedio.
- Estado de las rutas.

## Estructura del repositorio

```text
Municipio-Tecno-Valle/
──README.md
─ SistemaTransporte.java
```

La solución se mantiene en la rama principal `main`.

## Comandos para subir la solución

```bash
git push -u origin main
```
