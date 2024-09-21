# Actividad_20_Sep
Actividad dia 20 de septiembre REPASO DE PDD EXPUESTOS
Emiliano García Cordero - 20211779

## Actividad: Implementación de Patrones de Diseño en C#
Objetivo: Invitar a los estudiantes a elaborar ejercicios prácticos implementando los patrones de diseño estudiados, utilizando ejemplos del mundo real y presentando el código funcionando en DotNetFiddle.

Instrucciones:
1. Selecciona un Patrón de Diseño:
- Elige uno de los patrones de diseño que hemos visto en clase (por ejemplo, Singleton, Factory Method, Observer, Strategy, etc.).
2. Ejemplo del Mundo Real:
- Piensa en una situación real donde el patrón seleccionado pueda resolver un problema común.
- Describe brevemente el contexto y cómo el patrón ayuda a solucionar el problema.
3. Implementación en Código:
- Implementa el patrón de diseño en C#, aplicándolo al ejemplo que has descrito.
- Asegúrate de que tu código sea claro, esté bien estructurado y comentado.
- Utiliza DotNetFiddle para escribir y ejecutar tu código.
- Verifica que tu programa funcione correctamente.
4. Compartir y Explicar:
- Comparte el enlace a tu código en DotNetFiddle.
- Explica cómo tu implementación aplica el patrón de diseño y resuelve el problema.
- Destaca las partes clave de tu código donde se implementa el patrón

[Link de .NET Fiddle](https://dotnetfiddle.net/)


## Patrón Singleton
En aplicaciones grandes, es común tener un sistema de registro de logs para rastrear eventos, errores y otras informaciones importantes. Queremos asegurarnos de que todas las partes de la aplicación utilicen el mismo objeto de registro para mantener la consistencia y evitar problemas como conflictos de acceso o duplicación de recursos.

El patrón Singleton nos permite garantizar que solo exista una única instancia de la clase de registro de logs en toda la aplicación.

[Link de .NET Fiddle del Patrón Singleton](https://dotnetfiddle.net/7pQT27)

```c#
using System;

public class Logger
{
    // Campo estático para mantener la única instancia de Logger
    private static Logger _instance;

    // Objeto para bloqueo de hilos en escenarios multi-hilo
    private static readonly object _lock = new object();

    // Constructor privado para prevenir instancias externas
    private Logger() { }

    // Propiedad pública para acceder a la instancia única de Logger
    public static Logger Instance
    {
        get
        {
            // Doble verificación para soporte de multi-hilo
            if (_instance == null)
            {
                lock (_lock)
                {
                    if (_instance == null)
                    {
                        _instance = new Logger();
                    }
                }
            }
            return _instance;
        }
    }

    // Método para registrar mensajes
    public void Log(string message)
    {
        // Usando concatenación de cadenas
        Console.WriteLine("[" + DateTime.Now + "] " + message);
        
        // O usando String.Format
        // Console.WriteLine(string.Format("[{0}] {1}", DateTime.Now, message));
    }
}

public class Program
{
    public static void Main()
    {
        // Obtiene la instancia única de Logger
        Logger logger1 = Logger.Instance;
        Logger logger2 = Logger.Instance;

        // Verifica que ambas referencias apuntan a la misma instancia
        if (logger1 == logger2)
        {
            Console.WriteLine("Ambas variables contienen la misma instancia de Logger.");
        }

        // Usa el Logger para registrar mensajes
        logger1.Log("Este es un mensaje de prueba.");
        logger2.Log("Este es otro mensaje de prueba.");
    }
}

```

### Explicación
En esta implementación:

- Clase Logger: Contiene un constructor privado y una propiedad estática Instance que controla la creación de la instancia única.

- Campo estático _instance: Almacena la única instancia de Logger.

- Bloqueo de hilos: Utilizamos un objeto _lock y una doble verificación en el acceso a Instance para asegurar que en escenarios multi-hilo solo se cree una instancia.

- Método Log: Permite registrar mensajes en la consola con un timestamp.

En el método Main:

- Obtenemos dos referencias (logger1 y logger2) a través de Logger.Instance y demostramos que ambas apuntan a la misma instancia.

- Utilizamos el Logger para registrar mensajes.

#### Partes Clave del Código
- Constructor Privado

```c#
private Logger() { }
```
Previene la creación de instancias desde fuera de la clase.
- Propiedad Instance con Doble Verificación:

```c#
public static Logger Instance
{
    get
    {
        if (_instance == null)
        {
            lock (_lock)
            {
                if (_instance == null)
                {
                    _instance = new Logger();
                }
            }
        }
        return _instance;
    }
}
```
Garantiza que solo se crea una instancia de Logger, incluso en entornos multi-hilo.
- Uso del Logger en Main:

```c#
Logger logger1 = Logger.Instance;
Logger logger2 = Logger.Instance;
```
Demuestra que logger1 y logger2 son la misma instancia.

## Patrón Factory Method
En aplicaciones que requieren la creación de objetos sin especificar la clase exacta del objeto que se va a crear, el Patrón Factory Method es muy útil. Por ejemplo, en una aplicación de logística, podemos tener diferentes tipos de transporte (Camión, Barco, Avión) y queremos que el sistema decida qué tipo de transporte utilizar en tiempo de ejecución sin cambiar el código cliente.

El patrón Factory Method nos permite definir una interfaz para crear objetos, pero deja que las subclases decidan qué clase instanciar. De esta manera, el código cliente trabaja con interfaces en lugar de clases concretas.

[Link de .NET Fiddle del Patrón Factory Method](https://dotnetfiddle.net/nEflnt)

```c#
using System;

public interface ITransporte
{
    void Entregar();
}

public class Camion : ITransporte
{
    public void Entregar()
    {
        Console.WriteLine("Entrega por carretera en camión.");
    }
}

public class Barco : ITransporte
{
    public void Entregar()
    {
        Console.WriteLine("Entrega por mar en barco.");
    }
}

public abstract class Logistica
{
    // Método Factory
    public abstract ITransporte CrearTransporte();

    public void PlanificarEntrega()
    {
        ITransporte transporte = CrearTransporte();
        transporte.Entregar();
    }
}

public class LogisticaTerrestre : Logistica
{
    public override ITransporte CrearTransporte()
    {
        return new Camion();
    }
}

public class LogisticaMaritima : Logistica
{
    public override ITransporte CrearTransporte()
    {
        return new Barco();
    }
}

public class Program
{
    public static void Main()
    {
        // Cliente decide el tipo de logística en tiempo de ejecución
        Logistica logistica;

        Console.WriteLine("Ingrese el tipo de logística (terrestre/maritima):");
        string tipo = Console.ReadLine();

        if (tipo.ToLower() == "terrestre")
        {
            logistica = new LogisticaTerrestre();
        }
        else if (tipo.ToLower() == "maritima")
        {
            logistica = new LogisticaMaritima();
        }
        else
        {
            Console.WriteLine("Tipo de logística desconocido.");
            return;
        }

        // Planifica la entrega usando el transporte adecuado
        logistica.PlanificarEntrega();
    }
}

```
### Explicación
En esta implementación:
- Interfaz ITransporte: Define el método Entregar que será implementado por los transportes concretos.
- Clases Concretas Camion y Barco: Implementan la interfaz ITransporte y proporcionan la implementación específica del método Entregar.
- Clase Abstracta Logistica: Declara el método Factory abstracto CrearTransporte y un método PlanificarEntrega que utiliza el transporte creado.
- Clases Concretas de Logística: LogisticaTerrestre y LogisticaMaritima implementan el método Factory para devolver instancias de Camion y Barco, respectivamente.

En el método Main:
- Solicitamos al usuario que ingrese el tipo de logística.
- Creamos una instancia de la clase de logística adecuada en tiempo de ejecución.
- Llamamos al método PlanificarEntrega, que utiliza el método Factory CrearTransporte para obtener el transporte correcto y realizar la entrega.

### Partes Clave del Código
- Interfaz ITransporte:

```c#
public interface ITransporte
{
    void Entregar();
}
```
Define el contrato que todos los transportes deben cumplir.

### Clases Concretas de Transporte:
```c#
public class Camion : ITransporte { /* ... */ }
public class Barco : ITransporte { /* ... */ }

```
Implementan la interfaz ITransporte y proporcionan las implementaciones específicas.

- Clase Abstracta Logistica con Método Factory:

```c#
public abstract class Logistica
{
    public abstract ITransporte CrearTransporte();
    public void PlanificarEntrega()
    {
        ITransporte transporte = CrearTransporte();
        transporte.Entregar();
    }
}

```
Declara el método Factory CrearTransporte y utiliza el objeto creado en PlanificarEntrega.

- Clases Concretas que Implementan el Método Factory:
```c#
public class LogisticaTerrestre : Logistica
{
    public override ITransporte CrearTransporte()
    {
        return new Camion();
    }
}

```
Proporcionan la implementación concreta del método Factory para crear un tipo específico de transporte.

- Uso en el Método Main:

```c#
Logistica logistica;

Console.WriteLine("Ingrese el tipo de logística (terrestre/maritima):");
string tipo = Console.ReadLine();

if (tipo.ToLower() == "terrestre")
{
    logistica = new LogisticaTerrestre();
}
else if (tipo.ToLower() == "maritima")
{
    logistica = new LogisticaMaritima();
}
else
{
    Console.WriteLine("Tipo de logística desconocido.");
    return;
}

logistica.PlanificarEntrega();

```
El código cliente decide en tiempo de ejecución qué tipo de logística utilizar y, por ende, qué tipo de transporte se creará.






## Patrón Abstract Factory


## Patrón Builder


## Patrón Bridge

