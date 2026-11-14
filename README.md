# Sistema de Gestión – Centro de Rescate Animal v1.1

> Primer Parcial de **Programación II**, desarrollado en **Java** con Programación Orientada a Objetos.
> Tecnicatura Universitaria en Programación – UTN Avellaneda.

![Java](https://img.shields.io/badge/Java-11%2B-orange)
![POO](https://img.shields.io/badge/Paradigma-POO-blue)
![IDE](https://img.shields.io/badge/IDE-NetBeans-1B6AC6)

---

## Datos académicos

| | |
|---|---|
| **Materia** | Programación II |
| **Instancia** | Primer Parcial |
| **Cuatrimestre** | 2º Cuatrimestre 2026 |
| **Fecha** | 23 de octubre de 2026 |
| **División** | 322-2 |
| **Docentes** | Baus / Bustos |
| **Alumno** | Papetti, Fabricio |

---

## Descripción

El Centro de Rescate **FaunaSur** recibe animales silvestres y administra su cuidado hasta que se
recuperan o, cuando corresponde, son liberados. El sistema registra **aves**, **mamíferos** y
**reptiles**, calcula el costo diario de mantenimiento de cada uno y permite consultar y filtrar los
animales registrados.

Todos los animales tienen **código**, **nombre identificatorio** y **estado de salud**
(`CRITICO`, `EN_RECUPERACION` o `ESTABLE`). Cada especie agrega un dato propio:

| Tipo | Dato específico | Costo diario de mantenimiento | Acciones |
|---|---|---|---|
| `Ave` | envergadura alar (cm) | `envergaduraAlar × 2` | rehabilitar y liberar |
| `Mamifero` | peso (kg) | `peso × 10` | rehabilitar |
| `Reptil` | longitud (cm) | `longitud + 15` | liberar |

---

## Reglas del sistema

- Las medidas deben ser **estrictamente positivas**.
- El código y el nombre **no pueden ser nulos ni estar en blanco**; el estado de salud **no puede ser nulo**.
- Dos animales son **iguales** si coinciden su **código** y su **nombre**, sin importar tipo,
  estado de salud o medida.
- No se admiten animales repetidos en el centro.
- Rehabilitar y liberar devuelven un mensaje que identifica al animal.

---

## Funcionalidades

La clase `CentroDeRescate` ofrece:

| Método | Descripción |
|---|---|
| `agregarAnimal(animal)` | Incorpora cualquier tipo de animal. Si ya existe uno igual, lanza `AnimalDuplicadoException`. No admite `null`. |
| `obtenerAnimales()` | Devuelve los animales en orden de carga, en una **nueva lista**. |
| `filtrarPorEstado(estado)` | Devuelve los animales con el estado indicado. No admite `null`. |
| `obtenerAnimalesConCostoMaximo(maximo)` | Devuelve los animales cuyo costo diario es **menor o igual** al máximo. El máximo debe ser mayor que cero. |

---

## Diseño

### Jerarquía

```
        «interface»          «interface»
        Rehabilitable          Liberable
            ▲    ▲                ▲   ▲
            ┆    └────────┐  ┌────┘   ┆
            ┆             ┆  ┆        ┆
         Mamifero        Ave        Reptil
            │             │           │
            └─────────────┼───────────┘
                          ▼
                  Animal (abstracta)

   CentroDeRescate ◆───── 0..* Animal
   Animal ───────► EstadoSalud «enum»
```

### Conceptos de POO aplicados

| Concepto | Aplicación |
|---|---|
| **Herencia** | `Animal` abstracta con los atributos comunes; una subclase por tipo. |
| **Polimorfismo** | `calcularCostoDiario()` se declara en `Animal` y cada subclase lo implementa a su manera. |
| **Interfaces** | `Rehabilitable` y `Liberable` reparten las acciones de forma cruzada: el ave tiene ambas, el mamífero una y el reptil la otra. |
| **Enumerado** | `EstadoSalud` (`CRITICO`, `EN_RECUPERACION`, `ESTABLE`). |
| **Excepción personalizada** | `AnimalDuplicadoException`, con un mensaje que indica código y nombre del animal repetido. |
| **Encapsulamiento** | Atributos `private final`; las subclases acceden por getters. |
| **Composición** | `CentroDeRescate` es dueño de su lista de animales y controla cómo se modifica. |

### Decisiones de diseño

- **`Animal` es abstracta**: el costo diario depende del tipo y no tiene sentido crear un animal genérico.
- **Interfaces para las acciones**, porque ni la herencia sola ni una interfaz única pueden modelar que
  unas especies rehabiliten, otras se liberen y el ave haga ambas cosas.
- **`equals` y `hashCode`** usan solo código y nombre, con `instanceof Animal`: un `Reptil` y un `Ave` con
  el mismo código y nombre se consideran el mismo animal.
- **Duplicados**: `agregarAnimal` verifica con `contains` (que usa `equals`) antes de agregar.
- **Copia defensiva**: `obtenerAnimales()` devuelve `new ArrayList<>(animales)`, así quien la reciba
  no puede modificar la colección interna.
- **Constantes `static final`** (`MULTIPLICADOR_COSTO`, `FACTOR_COSTO`, `COSTO_BASE`) en lugar de
  números sueltos en los cálculos.
- **Validaciones centralizadas** en `validarTexto` y `validarPositivo` (`protected static` en `Animal`),
  reutilizadas por las subclases.
- Se utilizó únicamente código visto en la cátedra.

---

## Estructura del proyecto

```
SistemaGestionRecateAnimal/
├── docs/
│   └── Diagrama_UML_sistema_gestion_rescate_animal.pdf
├── nbproject/
├── src/
│   └── sistemagestionrecateanimal/
│       ├── sistemagestionrecateanimal.java   (programa principal)
│       ├── Animal.java
│       ├── Ave.java
│       ├── Mamifero.java
│       ├── Reptil.java
│       ├── Rehabilitable.java
│       ├── Liberable.java
│       ├── EstadoSalud.java
│       ├── CentroDeRescate.java
│       └── AnimalDuplicadoException.java
├── build.xml
└── README.md
```

---

## Cómo ejecutar

### Requisitos

- JDK 11 o superior
- NetBeans (el proyecto ya incluye su configuración) o cualquier terminal

> El proyecto de NetBeans está configurado con `source/target 26`. Si tu JDK es anterior, cambialo en
> **Properties → Sources → Source/Binary Format**.

### Desde NetBeans

1. **File → Open Project** y seleccionar la carpeta `SistemaGestionRecateAnimal`.
2. Clic derecho sobre el proyecto → **Clean and Build**.
3. Clic derecho sobre el proyecto → **Run** (clase principal: `sistemagestionrecateanimal`).

### Desde la terminal

```bash
# Compilar
javac -d out src/sistemagestionrecateanimal/*.java

# Ejecutar
java -cp out sistemagestionrecateanimal.sistemagestionrecateanimal
```

---

## Salida esperada

El programa principal carga cuatro animales, intenta cargar un quinto repetido (`AV-10` / `Luna`) y
muestra los resultados de cada consulta:

```
Error: Ya existe un animal con codigo AV-10y nombre Luna
Animales registrados:
Ave [AV-10]Luna-ESTABLE
Mamifero [MA-20]Puma-EN_RECUPERACION
Reptil [RE-30]Sol-ESTABLE
Ave [AV-40]Nube-CRITICO
Rehabilitaciones:
Rehabilitando al ave Luna AV-10
Rehabilitando al mamifero Puma MA-20
Rehabilitando al ave Nube AV-40
Liberaciones:
Liberando al ave Luna AV-10
Liberando al reptil Sol RE-30
Liberando al ave Nube AV-40
Costo diario de cada animal:
Luna: 160.0
Puma: 120.0
Sol: 80.0
Nube: 100.0
Animales ESTABLES:
Ave [AV-10]Luna-ESTABLE
Reptil [RE-30]Sol-ESTABLE
Animales con costo diario hasta 100:
Reptil [RE-30]Sol-ESTABLE
Ave [AV-40]Nube-CRITICO
```

- El reptil duplicado se rechaza aunque sea de otro tipo y tenga otro estado.
- Rehabilitan las aves y los mamíferos; se liberan las aves y los reptiles.
- `Nube` entra en el filtro de costo máximo porque su costo (100) es **igual** al límite.

---

## Documentación

Las clases y los métodos principales están documentados con **Javadoc**. Para generar el HTML:

```bash
javadoc -d docs/javadoc -sourcepath src sistemagestionrecateanimal
```

---

## Autor

**Fabricio Papetti**
Tecnicatura Universitaria en Programación – UTN Avellaneda
