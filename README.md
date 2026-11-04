# Garden Guardian - Guía de defensa

Fecha: 2026-11-04

## Estado del repositorio

Los cinco ejercicios del repositorio (último commit `c79935b`, 4 de noviembre de 2026) se ejecutan sin errores y su salida coincide con los ejemplos del subject.

| Ejercicio | Archivo | Se ejecuta | flake8 | mypy |
| --- | --- | --- | --- | --- |
| ex0 | `ex0/ft_first_exception.py` | Sí | Limpio | Limpio |
| ex1 | `ex1/ft_raise_exception.py` | Sí | Limpio | Limpio |
| ex2 | `ex2/ft_different_errors.py` | Sí | Limpio | 1 error esperado |
| ex3 | `ex3/ft_custom_errors.py` | Sí | Limpio | Limpio |
| ex4 | `ex4/ft_finally_block.py` | Sí | Limpio | Limpio |

- El error de mypy en ex2 es el de la línea `"texto" + 5`. Es intencionado: el subject pide dejarlo para provocar el `TypeError`.
- La versión anterior de ex2 tenía `else operation_number == 4:`, que es un error de sintaxis. El último commit lo corrige con `else:`.
- Los nombres de carpetas y archivos coinciden con los que pide el subject.

## Qué hace el programa

El proyecto enseña a que un programa no se caiga cuando algo falla: cada ejercicio añade una pieza al manejo de errores.

| Ejercicio | Qué hace | Idea clave |
| --- | --- | --- |
| ex0 | `input_temperature` convierte un texto en entero. Con `"abc"` falla, y `test_temperature` captura el error y el programa sigue. | `try` / `except` |
| ex1 | Igual que ex0, pero además rechaza temperaturas fuera de 0-40 °C (límites incluidos) lanzando un error con `raise`. | `raise` |
| ex2 | `garden_operations(n)` provoca a propósito cuatro errores distintos (0 a 3) y una operación sin error (4). Un test los captura uno por uno y después con un solo `try`. | Varios tipos de error |
| ex3 | Define errores propios: `GardenError` y sus hijas `PlantError` y `WaterError`, cada una con mensaje por defecto. Muestra que `except GardenError` captura a las dos hijas. | Herencia de excepciones |
| ex4 | `water_plant` riega si el nombre empieza con mayúscula y, si no, lanza `PlantError`. El sistema de riego se abre, se riega, y se cierra siempre en un `finally`. | `finally` |

## Conceptos que hay que dominar

Una excepción es el aviso que Python lanza cuando una operación no se puede completar; si nadie la captura, el programa termina con un *traceback*. Todo el proyecto gira en torno a decidir qué pasa entonces.

**`try` / `except`.** Python ejecuta el `try`. Si salta una excepción del tipo indicado, se salta lo que quedaba del `try` y se ejecuta el `except`. Si no salta nada, el `except` no se ejecuta.

**`as e`.** Guarda la excepción en una variable. Al imprimirla sale su mensaje, por ejemplo `invalid literal for int() with base 10: 'abc'`.

**Tipos de error.** Cada tipo indica una causa distinta, y por eso se pueden tratar de forma distinta:

| Tipo | Cuándo ocurre | Ejemplo |
| --- | --- | --- |
| `ValueError` | El tipo es correcto, pero el valor no sirve | `int("abc")` |
| `ZeroDivisionError` | Dividir entre cero | `1/0` |
| `FileNotFoundError` | Abrir un archivo que no existe | `open('/non/existent/file')` |
| `TypeError` | Mezclar tipos incompatibles | `"texto" + 5` |

**Capturar varios tipos con un solo `try`.** Se escribe una tupla en el `except`: `except (ValueError, ZeroDivisionError) as e:`. Si una operación del `try` lanza, el resto del `try` no se ejecuta.

**`raise`.** Lanzas tú la excepción. En ex1, `"100"` es un número válido para `int()`, pero no lo es para una planta; por eso se lanza `ValueError` a mano, con un mensaje claro.

**`finally`.** Se ejecuta siempre: con error, sin error e incluso cuando hay un `return` dentro del `try` o del `except`. Sirve para limpiar recursos. En ex4, aunque el `except` haga `return`, "Closing watering system" se imprime antes de que la función termine.

**Excepciones propias y herencia.** Una excepción propia es una clase que hereda de `Exception`. Como `PlantError` y `WaterError` heredan de `GardenError`, un `except GardenError` captura a las dos. Sirve para que el error diga con precisión qué pasó en tu dominio y para tratar toda una familia de errores con un solo `except`.

**Anotaciones de tipo, flake8 y mypy.** flake8 revisa el estilo (PEP 8) y mypy revisa que los tipos anotados sean coherentes. El error de mypy en ex2 es esperado: el subject pide dejar `"texto" + 5` para provocar el `TypeError`.

## La defensa

La defensa sale bien si puedes explicar con tus palabras qué hace cada `try`, qué se salta cuando algo falla y por qué `finally` siempre se ejecuta.

**Apertura (30 segundos).** "El proyecto trata de que el programa no se caiga ante errores. Uso `try/except` para capturarlos, `raise` para lanzar los míos, clases propias que heredan de `Exception` para organizarlos y `finally` para limpiar siempre."

### Qué enseñar en cada ejercicio

| Ejercicio | Qué enseñar |
| --- | --- |
| ex0 | Ejecutar y señalar que, tras el error de `"abc"`, el programa llega al mensaje final. |
| ex1 | `0` y `40` son válidos (límites incluidos); `41` y `-1` saltan. Un valor que `int()` acepta puede no servir para una planta. |
| ex2 | Hay un `except` distinto por cada tipo de error, y el último bloque usa una tupla en un solo `except`. |
| ex3 | `except GardenError` captura tanto `PlantError` como `WaterError`, porque heredan de ella. |
| ex4 | "Closing watering system" sale aunque haya error y `return`. |

### Preguntas probables

- **¿Por qué `except ValueError` y no `except Exception`?** Porque `Exception` esconde errores que no esperas. Capturar el tipo concreto significa que solo tratas lo que sabes tratar.
- **¿Qué pasa si una excepción no se captura?** El programa termina con un traceback.
- **¿Por qué `ValueError` en ex1?** Porque el dato tiene el tipo correcto pero un valor inaceptable, que es lo que significa `ValueError`.
- **¿Por qué se ejecuta el `finally` aunque haya `return`?** Porque está diseñado para garantizar la limpieza sea cual sea la salida.
- **¿Por qué mypy da error en ex2?** Es el `TypeError` intencionado de `"texto" + 5`, que el subject exige dejar.
- **¿Por qué no cierras el archivo del `open()` en ex2?** Porque la apertura falla: no hay archivo abierto que cerrar.
- **¿Por qué redefines `PlantError` en ex4?** Porque cada ejercicio entrega un solo archivo y el subject solo autoriza `print()` y `str.capitalize()`, así que no se puede importar de ex3. Por eso se copian `GardenError` y `PlantError` con la misma jerarquía.

### Puntos débiles que conviene conocer

1. **ex2, el bloque de la tupla.** Llama a `garden_operations(0)` y luego a `(1)` dentro del mismo `try`. La primera ya lanza, así que la segunda no se ejecuta y solo sale un `Caught:`. Cumple el subject; si preguntan, la respuesta es que una excepción corta el resto del `try`.
2. **ex4, `capitalize()`.** Con `capitalize()`, `"Aloe Vera"` se rechaza (el resto de letras pasa a minúscula) y una cadena vacía se acepta. El subject autoriza `str.capitalize()` y define así "con mayúscula", por lo que se explica sin cambiar el código.
3. **ex1, bloques repetidos.** Los tres `try/except` de `test_temperature` repiten estructura. Funciona y es claro; no hace falta cambiarlo.
