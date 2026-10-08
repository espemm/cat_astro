# Fichas de Java

**Proyecto Catálogo Astronómico · Prácticas P1, P2 y P3**  
**Objetivo:** repasar los conceptos necesarios para ampliar un proyecto Java existente. Los ejemplos son deliberadamente pequeños y no sustituyen a los constructores y métodos exigidos por los enunciados.

## Índice

1. [Clases y objetos](#1-clases-y-objetos)
2. [Herencia y `super`](#2-herencia-y-super)
3. [`toString()`](#3-tostring)
4. [`equals()` y `hashCode()`](#4-equals-y-hashcode)
5. [`ArrayList`](#5-arraylist)
6. [`HashSet`](#6-hashset)
7. [`HashMap`](#7-hashmap)
8. [Enumerados (`enum`)](#8-enumerados-enum)
9. [Polimorfismo e `instanceof`](#9-polimorfismo-e-instanceof)
10. [Diagrama](#10-DIAGRAMA-DE-CLASES)

---

## 1. Clases y objetos

**Idea clave.** Una clase define atributos y métodos. Un objeto es una instancia creada a partir de esa clase.

```java
public class Astro {
    private String nombre;
    private double distancia;

    public Astro(String nombre, double distancia) {
        this.nombre = nombre;
        this.distancia = distancia;
    }

    public String getNombre() { return nombre; }
    public void setNombre(String nombre) { this.nombre = nombre; }
}
```

```java
Astro a = new Astro("Sirio", 8.7);
System.out.println(a.getNombre()); // Sirio
a.setNombre("Sirius");
```

| Elemento | Significado |
|---|---|
| `class` | Declara una clase |
| `private` | Limita el acceso a los atributos |
| `new` | Crea un objeto |
| Constructor | Inicializa los atributos |
| `this` | Se refiere al objeto actual |
| Getter / setter | Consulta / modifica un atributo |

**En el proyecto:** `Astro`, `Estrella`, `Galaxia` y `Planeta` son clases; cada astro concreto es un objeto.

**Comprueba que lo has entendido:**
1. ¿Qué diferencia hay entre la clase `Astro` y el objeto `a`?
2. ¿Qué representa `this.nombre` en el constructor?

---

## 2. Herencia y `super`

**Idea clave.** La herencia permite crear una clase hija a partir de una clase padre y reutilizar sus métodos accesibles.

```java
public class Astro {
    private String nombre;
    public Astro(String nombre) { this.nombre = nombre; }
    public String getNombre() { return nombre; }
}

public class Estrella extends Astro {
    private int planetas;

    public Estrella(String nombre, int planetas) {
        super(nombre);
        this.planetas = planetas;
    }

    public int getPlanetas() { return planetas; }
}
```

```java
Estrella e = new Estrella("Sol", 8);
System.out.println(e.getNombre());  // Sol: heredado
System.out.println(e.getPlanetas()); // 8: propio
```

**Recuerda:** `extends` expresa herencia; `super(...)` llama al constructor del padre; `@Override` marca la redefinición de un método heredado. Los atributos `private` del padre no son accesibles directamente desde la hija.

**En el proyecto:** `EstrellaConTipo` hereda de `Estrella`, que hereda de `Astro`; `Planeta` y `Galaxia` también heredan de `Astro`.

**Comprueba que lo has entendido:**
1. ¿Por qué el constructor de `Estrella` llama a `super(nombre)`?
2. ¿Puede `EstrellaConTipo` utilizar `getNombre()` sin volver a escribirlo?

---

## 3. `toString()`

**Idea clave.** Permite definir la representación de texto de un objeto.

```java
public class Astro {
    private String nombre;
    public Astro(String nombre) { this.nombre = nombre; }

    @Override
    public String toString() {
        return "Astro: " + nombre;
    }
}
```

```java
Astro a = new Astro("Sirio");
System.out.println(a); // Astro: Sirio
```

**Recuerda:** `toString()` **devuelve** un `String`; no debe imprimirlo. `println(objeto)` invoca automáticamente ese método.

**En el proyecto:** los tests comparan el texto esperado con el devuelto. ¡Importan los espacios, las mayúsculas y los signos!

**Comprueba que lo has entendido:**
1. ¿Qué diferencia hay entre `return` y `System.out.println()`?
2. ¿Por qué puede fallar un test aunque el contenido sea parecido?

---

## 4. `equals()` y `hashCode()`

**Idea clave.** `==` compara si dos referencias señalan el mismo objeto; `equals()` define cuándo dos objetos se consideran iguales por sus datos.

```java
import java.util.Objects;

public class Astro {
    private String nombre;
    public Astro(String nombre) { this.nombre = nombre; }

    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;
        if (!(obj instanceof Astro otro)) return false;
        return Objects.equals(nombre, otro.nombre);
    }

    @Override
    public int hashCode() {
        return Objects.hash(nombre);
    }
}
```

```java
Astro a1 = new Astro("Sirio");
Astro a2 = new Astro("Sirio");
System.out.println(a1 == a2);      // false
System.out.println(a1.equals(a2)); // true
```

**Recuerda:** si se redefine `equals()`, también hay que redefinir `hashCode()` de forma coherente. El criterio de igualdad de este ejemplo es solo el nombre; en las prácticas puede incluir más atributos.

**En el proyecto:** `contains()` puede apoyarse en `equals()` para detectar duplicados.

**Comprueba que lo has entendido:**
1. ¿Por qué `a1 == a2` es `false`?
2. ¿Qué problema puede causar redefinir `equals()` sin `hashCode()`?

---

## 5. `ArrayList`

**Idea clave.** Es una lista dinámica, ordenada, con acceso por índice; admite duplicados.

```java
import java.util.ArrayList;

ArrayList<String> nombres = new ArrayList<>();
nombres.add("Sol");
nombres.add("Sirio");
nombres.add("Vega");

System.out.println(nombres.get(0)); // Sol
System.out.println(nombres.size()); // 3

for (String nombre : nombres) {
    System.out.println(nombre);
}

nombres.remove("Sirio");
```

| Método | Función |
|---|---|
| `add(x)` | Añade |
| `get(i)` | Obtiene por índice |
| `set(i, x)` | Sustituye |
| `remove(x)` | Elimina |
| `contains(x)` | Comprueba existencia |
| `size()` / `isEmpty()` | Cuenta / comprueba si está vacía |

**En la P3:** `ArrayList<Astro>` almacena objetos de `Astro` y de sus subclases.

**Comprueba que lo has entendido:**
1. ¿Qué índice tiene el primer elemento?
2. ¿Cómo evitarías añadir un astro que ya está en la lista?

---

## 6. `HashSet`

**Idea clave.** Un conjunto no admite elementos duplicados y no garantiza el orden de inserción.

```java
import java.util.HashSet;

HashSet<String> galaxias = new HashSet<>();
galaxias.add("Vía Láctea");
galaxias.add("Andrómeda");
galaxias.add("Vía Láctea");

System.out.println(galaxias.size()); // 2
System.out.println(galaxias.contains("Andrómeda")); // true
```

| Característica | `ArrayList` | `HashSet` |
|---|---|---|
| Admite duplicados | Sí | No |
| Acceso por índice | Sí | No |
| Orden de inserción | Sí | No garantizado |

**Recuerda:** si guardamos objetos propios en un `HashSet`, son importantes `equals()` y `hashCode()`.

**En la P3:** `HashSet<String>` registra nombres de galaxias sin repeticiones.

**Comprueba que lo has entendido:**
1. ¿Por qué el conjunto solo tiene dos nombres?
2. ¿Es válido llamar a `galaxias.get(0)`?

---

## 7. `HashMap`

**Idea clave.** Almacena pares **clave → valor**. Una clave identifica un único valor asociado.

```java
import java.util.HashMap;

HashMap<String, Integer> cantidades = new HashMap<>();
cantidades.put("Enana Blanca", 3);
cantidades.put("Gigante Roja", 2);

int actual = cantidades.getOrDefault("Enana Blanca", 0);
cantidades.put("Enana Blanca", actual + 1);
System.out.println(cantidades.get("Enana Blanca")); // 4
```

| Método | Función |
|---|---|
| `put(k, v)` | Inserta o actualiza |
| `get(k)` | Consulta (puede devolver `null`) |
| `getOrDefault(k, d)` | Consulta con valor alternativo |
| `containsKey(k)` | Comprueba una clave |
| `remove(k)` | Elimina |
| `keySet()` | Devuelve las claves |

**En la P3:** `Map<TipoEstrella, Integer>` cuenta estrellas por tipo. En los genéricos se usa `Integer`, no el primitivo `int`.

**Comprueba que lo has entendido:**
1. ¿Qué pasa si llamas dos veces a `put()` con la misma clave?
2. ¿Por qué `getOrDefault(tipo, 0)` es útil para contadores?

---

## 8. Enumerados (`enum`)

**Idea clave.** Define un conjunto limitado de valores posibles. También puede tener atributos, constructor y métodos.

```java
public enum TipoEstrella {
    ENANA_BLANCA("Enana Blanca"),
    GIGANTE_ROJA("Gigante Roja");

    private final String nombre;

    TipoEstrella(String nombre) {
        this.nombre = nombre;
    }

    public String getNombre() { return nombre; }
}
```

```java
TipoEstrella tipo = TipoEstrella.ENANA_BLANCA;
System.out.println(tipo.getNombre()); // Enana Blanca

for (TipoEstrella t : TipoEstrella.values()) {
    System.out.println(t.getNombre());
}
```

**En la P3:** el enumerado completo incluye `ENANA_AMARILLA`, `ENANA_BLANCA`, `GIGANTE_ROJA` y `SUBGIGANTE_BLANCA`; cada constante tiene **nombre y URL**. El ejemplo se ha reducido intencionadamente.

**Comprueba que lo has entendido:**
1. ¿Qué ventaja tiene un `enum` frente a escribir libremente un `String`?
2. ¿Para qué sirve `TipoEstrella.values()`?

---

## 9. Polimorfismo e `instanceof`

**Idea clave.** Una referencia de tipo padre puede almacenar objetos de clases hijas. `instanceof` permite comprobar el tipo real.

```java
// Usamos las clases simplificadas de la ficha 2.
Astro a = new Estrella("Sol", 8);

if (a instanceof Estrella e) {
    System.out.println(e.getPlanetas()); // 8
}
```

La forma clásica equivalente es:

```java
if (a instanceof Estrella) {
    Estrella e = (Estrella) a;
    System.out.println(e.getPlanetas());
}
```

**Recuerda:** antes de hacer una conversión (*cast*) hay que comprobar que el objeto pertenece al tipo esperado. La primera sintaxis es válida en Java 21.

**En la P3:** el catálogo guarda referencias `Astro`, pero necesita identificar `Estrella` y `EstrellaConTipo` para actualizar sus contadores.

**Comprueba que lo has entendido:**
1. ¿Por qué un `ArrayList<Astro>` puede contener objetos `Estrella`?
2. ¿Qué podría pasar si conviertes un `Astro` a `Estrella` sin comprobar su tipo?

---

## 10. DIAGRAMA DE CLASES

![Diagrama UML](./UML%20del%20Catálogo%20Astronómico.png)

