# Preguntas de Entrevista sobre Multihilo

## P. ¿Cuáles son los estados en el ciclo de vida de un Thread?

Un hilo en Java puede estar en cualquiera de los siguientes estados durante su ciclo de vida, es decir: `New`, `Runnable`, `Blocked`, `Waiting`, `Timed Waiting` o `Terminated`. Estos también se conocen como los eventos del ciclo de vida de un thread en Java.

* New  
* Runnable  
* Running  
* Non-Runnable (Blocked)  
* Terminated  

**1. New**: El hilo está en estado *new* si creas una instancia de la clase `Thread` pero aún no se ha invocado el método `start()`.  
**2. Runnable**: El hilo está en estado *runnable* después de invocar `start()`, pero el planificador de hilos aún no lo ha seleccionado para ejecutarse.  
**3. Running**: El hilo está en estado *running* si el planificador de hilos lo ha seleccionado para ejecutarse.  
**4. Non-Runnable (Blocked)**: El hilo sigue vivo, pero actualmente **no es elegible para ejecutarse**.  
**5. Terminated**: Un hilo está en estado *terminated* o muerto cuando su método `run()` ha finalizado.

---

## P. ¿Cuáles son las diferentes formas de implementar un thread?

Hay dos formas de crear un hilo en Java:

* extender la clase `Thread`  
* implementar la interfaz `Runnable`

---

**1. Extender la clase Thread**  
Crea un hilo mediante una nueva clase que extienda `Thread` y crea una instancia de esa clase. La clase extendida debe sobrescribir el método `run()`, que es el punto de entrada del nuevo hilo.

```java
class MyThread extends Thread {

    public void run() {
        System.out.println("Thread started running..");
    }

    public static void main(String args[]) {
        MyThread t1 = new MyThread();
        t1.start();
    }
}
```
Output
```
Thread started running..
```
**2. Implementando la Interfaz Runnable**
Después de implementar la interfaz runnable, la clase necesita implementar el método run(), que es `public void run()`.
* El método run() introduce un hilo concurrente en tu programa. Este hilo terminará cuando run() retorne.
* Debes especificar el código para tu hilo dentro del método run().
* El método run() puede llamar otros métodos, puede usar otras clases y declarar variables como cualquier otro método normal.
```java
class MyThread implements Runnable {
 public void run() {
 System.out.println("Thread started running..");
 }
 public static void main(String args[]) {
 MyThread mt = new MyThread();
 Thread t = new Thread(mt);
 t.start();
 }
}
```
**Diferencia entre Runnable vs Thread**
* Implementar Runnable es la forma preferida. Aquí, realmente no estás especializando ni modificando el comportamiento del hilo. Solo estás dando al hilo algo para ejecutar. Eso significa que la composición es la mejor opción.
* Java solo soporta herencia simple, por lo que solo puedes extender una clase.
* Instanciar una interfaz da una separación más limpia entre tu código y la implementación de los hilos.
* Implementar Runnable hace tu clase más flexible. Si extiendes Thread entonces la acción que estás haciendo siempre se ejecutará en un hilo. Sin embargo, si implementas Runnable no tiene que ser así. Puedes ejecutarlo en un hilo, pasarlo a un servicio executor, o simplemente usarlo como tarea en una aplicación de un solo hilo.

<div align="right">
    <b><a href="#">↥ back to top</a></b>
</div>

## P. ¿Cuál es la diferencia entre un Proceso y un Thread?
Tanto procesos como hilos son secuencias de ejecución independientes. La diferencia típica es que los hilos comparten el mismo espacio de memoria, mientras que los procesos tienen espacios de memoria separados.

**Proceso**  

* Una instancia en ejecución de un programa se llama proceso.
* Algunos sistemas operativos usan el término tarea para referirse a un programa en ejecución.
* Un proceso siempre se almacena en memoria principal (RAM).
* Por lo tanto, un proceso es una entidad activa. Desaparece si se reinicia el sistema.
* Puede haber varios procesos asociados al mismo programa.
* En sistemas multiprocesador, múltiples procesos pueden ejecutarse en paralelo.
* En sistemas monoprocesador, aunque no hay paralelismo real, se usa un algoritmo de planificación para dar la ilusión de concurrencia.

Hilo (Thread)

* Un hilo es un subconjunto de un proceso.
* Se le conoce como proceso liviano, ya que se ejecuta dentro del contexto del proceso y comparte los recursos asignados por el kernel.
* Usualmente un proceso tiene un solo hilo de control — un conjunto de instrucciones ejecutándose.
* Un proceso también puede tener múltiples hilos ejecutando instrucciones en paralelo.
* Múltiples hilos pueden aprovechar el verdadero paralelismo en sistemas multiprocesador.
* En sistemas monoprocesador, se usa un algoritmo de planificación para ejecutar cada hilo uno a la vez.
* Todos los hilos de un proceso comparten el mismo espacio de direcciones, descriptores de archivos, pila y otros atributos del proceso.
* Dado que comparten memoria, la sincronización del acceso a los datos compartidos se vuelve crítica.

## Q. What is difference between user Thread and daemon Thread?
Los hilos daemon son hilos de baja prioridad que siempre se ejecutan en segundo plano, mientras que los hilos de usuario son de alta prioridad y se ejecutan en primer plano. Los hilos de usuario están diseñados para tareas específicas y complejas, mientras que los hilos daemon sirven como apoyo.

**Diferencias entre hilos Daemon y hilos de Usuario en Java**  

* Los hilos de usuario son creados por la aplicación para realizar tareas específicas, mientras que los daemon son creados por la JVM para tareas en segundo plano (como el garbage collector).
* La JVM espera que los hilos de usuario terminen antes de salir. No ocurre lo mismo con los daemon: la JVM se cierra cuando todos los hilos de usuario han terminado, incluso si hay daemon activos.
* Los hilos de usuario tienen mayor prioridad y están pensados para ejecutar tareas importantes.
* Los hilos daemon nunca deben realizar tareas críticas; están destinados a apoyar a los hilos de usuario.
* La JVM no forzará la terminación de hilos de usuario. En cambio, sí forzará a los hilos daemon si todos los hilos de usuario han finalizado.

**Crear un hilo Daemon**  
```java
/**
 * Programa Java para demostrar la diferencia entre un hilo daemon y un hilo de usuario.
 *
 */
public class DaemonThreadDemo {

    public static void main(String[] args) throws InterruptedException {

        // El hilo principal (main) es un hilo de usuario (no daemon)
        String name = Thread.currentThread().getName();
        boolean isDaemon = Thread.currentThread().isDaemon();

        System.out.println("name: " + name + ", isDaemon: " + isDaemon);

        // Cualquier nuevo hilo generado desde el principal también es un hilo de usuario
        Runnable task = new Task();
        Thread t1 = new Thread(task, "T1");
        System.out.println("Thread spawned from main thread");
        System.out.println("name: " + t1.getName() + ", isDaemon: " + t1.isDaemon());

        // Podemos convertirlo en daemon antes de iniciarlo
        t1.setDaemon(true);
        t1.start();

         // Esperar a que T1 finalice
        t1.join();
    }

    private static class Task implements Runnable {

        @Override
        public void run() {
            Thread t = Thread.currentThread();
            System.out.println("Thread made daemon by calling setDaemon() method");
            System.out.println("name: " + t.getName() + ", isDaemon: " + t.isDaemon());

            // Cualquier nuevo hilo creado desde un hilo daemon también será daemon
            Thread t2 = new Thread("T2");
            System.out.println("Thread spawned from a daemon thread");
            System.out.println("name: " + t2.getName() + ", isDaemon: " + t2.isDaemon());
        }
    }
}
```
Output
```
name: main, isDaemon: false
Thread spawned from main thread
name: T1, isDaemon: false
Thread made daemon by calling setDaemon() method
name: T1, isDaemon: true
Thread spawned from a daemon thread
name: T2, isDaemon: true
```
<div align="right">
    <b><a href="#">↥ back to top</a></b>
</div>

## P. ¿Cómo se comunican los hilos entre sí?
**La comunicación entre hilos (inter-thread communication) es un mecanismo mediante el cual un hilo se detiene mientras está en su sección crítica, y otro hilo puede entrar (o bloquear) en esa misma sección crítica para ejecutarse. Esto se implementa mediante los siguientes métodos de la clase Object:

* wait()
* notify()
* notifyAll()

```java
class ThreadA {

    public static void main(String [] args) {
      ThreadB b = new ThreadB();
      b.start();
      synchronized(b) {
       try {
            System.out.println("Waiting for b to complete...");
            b.wait(); //// Espera hasta que b llame a notify()
       } catch (InterruptedException e) {}
            System.out.println("Total is: " + b.total);
      }
    }
}

class ThreadB extends Thread {
 int total;

 public void run() {
   synchronized(this) {
    for(int i = 0; i < 100; i++) {
        total += i;
    }
    notify(); // Despierta al hilo que está esperando en 'this'
   }
  }
}
```
## P. ¿Qué entiendes por prioridad de un Thread?
Cada hilo en Java tiene una prioridad que ayuda al planificador de hilos (thread scheduler) a determinar el orden en que se deben ejecutar los hilos. Los hilos con mayor prioridad suelen ejecutarse antes y con más frecuencia que aquellos con menor prioridad. Por defecto, todos los hilos tienen la misma prioridad, es decir, el planificador los trata como si tuvieran la misma importancia. Cuando se crea un hilo, este hereda la prioridad del hilo que lo creó.

La prioridad predeterminada de un hilo es 5 (NORM_PRIORITY). El valor de MIN_PRIORITY es 1 y el valor de MAX_PRIORITY es 10.

* public static int MIN_PRIORITY
* public static int NORM_PRIORITY
* public static int MAX_PRIORITY

```java
class TestMultiPriority1 extends Thread {  
  
    public void run() {  
        System.out.println("Running thread name is:" + Thread.currentThread().getName());  
        System.out.println("Running thread priority is:" + Thread.currentThread().getPriority());  
    }  

    public static void main(String args[]) {  
        TestMultiPriority1 m1 = new TestMultiPriority1();  
        TestMultiPriority1 m2 = new TestMultiPriority1();  
        m1.setPriority(Thread.MIN_PRIORITY);  
        m2.setPriority(Thread.MAX_PRIORITY);  
        m1.start();  
        m2.start();  
    }  
}     
```
Output
```
Running thread name is: Thread-0
Running thread priority is: 10
Running thread name is: Thread-1
Running thread priority is: 1    
```
<div align="right">
    <b><a href="#">↥ back to top</a></b>
</div>

## P. ¿Qué es el Thread Scheduler y el Time Slicing?
**Thread scheduler (planificador de hilos)** en Java es la parte de la JVM que decide qué hilo debe ejecutarse. El planificador utiliza principalmente dos enfoques: planificación preemptiva y división de tiempo (time slicing).

**Preemptive scheduling (Planificación con interrupciones)**: La tarea de mayor prioridad se ejecuta hasta que entra en estado de espera (waiting) o muerto (dead), o hasta que aparece una tarea con mayor prioridad.

**Time slicing (División de tiempo)**: Una tarea se ejecuta durante un tiempo predefinido, luego vuelve a la cola de tareas listas. El planificador decide cuál será la siguiente tarea a ejecutar basándose en la prioridad y otros factores.

Ejemplo: Thread Scheduler
```java
class FirstThread extends Thread {
	public void run(){
		for(int i = 0; i < 10; ++i) {
			System.out.println("I am in first thread");
			try{
				Thread.sleep(1000);
			}
			catch(InterruptedException ie) {
				System.out.println("Exception occurs ");
			}
		}
	}
}

class SecondThread {
	public static void main(String[] args){
		FirstThread ft = new FirstThread();
		ft.start();
		for(int j = 1; j < 10; ++j) {
			System.out.println("I am in second thread");
		}
	}
}
```
Output
```
cmd> java SecondThread
I am in second thread
I am in first thread
I am in second thread
I am in second thread
I am in second thread
I am in second thread
I am in second thread
I am in second thread
I am in second thread
I am in second thread
I am in first thread
I am in first thread
I am in first thread
I am in first thread
I am in first thread
I am in first thread
I am in first thread
I am in first thread
I am in first thread
```
<div align="right">
    <b><a href="#">↥ back to top</a></b>
</div>

## P. ¿Qué es el cambio de contexto (context switching) en multithreading?
El cambio de contexto es el proceso de guardar y restaurar el estado de la CPU para que la ejecución del Thread pueda reanudarse desde el mismo punto más adelante. Es una característica esencial en sistemas operativos multitarea y es clave para soportar entornos con múltiples hilos.

## P. ¿Qué es un Deadlock? ¿Cómo analizarlo y evitarlo?
**Deadlock (bloqueo mutuo)** es una situación en programación donde dos o más hilos quedan bloqueados indefinidamente, cada uno esperando un recurso que el otro mantiene bloqueado. Esto ocurre con al menos dos hilos y dos o más recursos.

```java
class HelloClass {
	public synchronized void first(HiClass hi) {
		try {
			Thread.sleep(1000);
		}
		catch(InterruptedException ie) {}
		System.out.println(" HelloClass is calling 	HiClass second() method");
		hi.second();
	}

	public synchronized void second() {
		System.out.println("I am inside second method of HelloClass");
	}
}

class HiClass {
	public synchronized void first(HelloClass he) {
		try {
			Thread.sleep(1000);
		}
		catch(InterruptedException ie){}
		System.out.println(" HiClass is calling HelloClass second() method");
		he.second();
	}

	public synchronized void second() {
		System.out.println("I am inside second method of HiClass");
	}
}

class DeadlockClass extends Thread {
	HelloClass he = new HelloClass();
	HiClass hi = new HiClass();

	public void demo() {
		this.start();
		he.first(hi);
	} 
	public void run() {
		hi.first(he);
	}

	public static void main(String[] args) {
		DeadlockClass dc = new DeadlockClass();
		dc.demo();
	}
}
```
Output
```
cmd> java DeadlockClass
HelloClass is calling HiClass second() method
HiClass is calling HelloClass second() method
```
**Cómo evitar un deadlock**  

**1. Evitar bloqueos anidados (Nested Locks)**: Este es el motivo más común de los deadlocks. Evita obtener un nuevo bloqueo si ya tienes otro. Si trabajas con un solo objeto sincronizado, es casi imposible que ocurra un deadlock.
```java
public void run() {
    String name = Thread.currentThread().getName();
    System.out.println(name + ' acquiring lock on ' + obj1);
    synchronized (obj1) {
        System.out.println(name + ' acquired lock on ' + obj1);
        work();
    }
    System.out.println(name + ' released lock on ' + obj1);
    System.out.println(name + ' acquiring lock on ' + obj2);
    synchronized (obj2) {
        System.out.println(name + ' acquired lock on ' + obj2);
        work();
    }
    System.out.println(name + ' released lock on ' + obj2);
    System.out.println(name + ' finished execution.');
}
```
**2. Bloquear solo lo necesario:**: Obtén el bloqueo solo sobre los recursos que realmente necesitas. Por ejemplo, si solo vas a acceder a un campo de un objeto, no bloquees el objeto entero.

**3. Evita esperar indefinidamente**: El deadlock puede ocurrir si dos hilos están esperando uno al otro de forma indefinida con join(). Usa join con un tiempo máximo para prevenirlo.

<div align="right">
    <b><a href="#">↥ back to top</a></b>
</div>

## P. ¿Qué es un Thread Pool? ¿Cómo podemos crear un Thread Pool en Java?
Un Thread Pool (grupo de hilos) reutiliza hilos previamente creados para ejecutar tareas actuales. Esto proporciona una solución al problema del costo de creación de hilos y el agotamiento de recursos. Dado que el hilo ya existe cuando llega una solicitud, se elimina la demora provocada por la creación de nuevos hilos, lo que hace que la aplicación sea más ágil y receptiva.

Java ofrece el framework Executor, que gira en torno a la interfaz Executor, su subinterfaz ExecutorService y la clase ThreadPoolExecutor, que implementa ambas. Usando el Executor, solo es necesario implementar objetos Runnable y enviarlos al ejecutor para que los ejecute.

Para usar un thread pool, primero se crea un objeto de tipo ExecutorService y se le asigna un conjunto de tareas. La clase ThreadPoolExecutor permite establecer el número de hilos mínimos (core pool size) y máximos (maximum pool size). Los objetos Runnable que son ejecutados por un hilo en particular se ejecutan de forma secuencial (uno detrás de otro dentro del mismo hilo).
```java
// Java program to illustrate  
// ThreadPool 
import java.text.SimpleDateFormat;  
import java.util.Date; 
import java.util.concurrent.ExecutorService; 
import java.util.concurrent.Executors; 
  
// Task class to be executed (Step 1) 
class Task implements Runnable    
{ 
    private String name; 
      
    public Task(String s) { 
        name = s; 
    } 
      
    // Prints task name and sleeps for 1s 
    // This Whole process is repeated 5 times 
    public void run() { 
        try { 
            for (int i = 0; i<=5; i++) { 
                if (i == 0) { 
                    Date d = new Date(); 
                    SimpleDateFormat ft = new SimpleDateFormat("hh:mm:ss"); 
                    System.out.println("Initialization Time for"
                            + " task name - "+ name +" = " +ft.format(d));    
                    //prints the initialization time for every task  
                } 
                else { 
                    Date d = new Date(); 
                    SimpleDateFormat ft = new SimpleDateFormat("hh:mm:ss"); 
                    System.out.println("Executing Time for task name - "+ 
                            name +" = " +ft.format(d));    
                    // prints the execution time for every task  
                } 
                Thread.sleep(1000); 
            } 
            System.out.println(name+" complete"); 
        } 
        catch(InterruptedException e) { 
            e.printStackTrace(); 
        } 
    } 
} 
public class Test 
{ 
     // Maximum number of threads in thread pool 
    static final int MAX_T = 3;              
  
    public static void main(String[] args) { 
        // creates five tasks 
        Runnable r1 = new Task("task 1"); 
        Runnable r2 = new Task("task 2"); 
        Runnable r3 = new Task("task 3"); 
        Runnable r4 = new Task("task 4"); 
        Runnable r5 = new Task("task 5");       
          
        // creates a thread pool with MAX_T no. of  
        // threads as the fixed pool size(Step 2) 
        ExecutorService pool = Executors.newFixedThreadPool(MAX_T);   
         
        // passes the Task objects to the pool to execute (Step 3) 
        pool.execute(r1); 
        pool.execute(r2); 
        pool.execute(r3); 
        pool.execute(r4); 
        pool.execute(r5);  
          
        // pool shutdown ( Step 4) 
        pool.shutdown();     
    } 
} 
```
Output
```
Initialization Time for task name - task 2 = 02:32:56
Initialization Time for task name - task 1 = 02:32:56
Initialization Time for task name - task 3 = 02:32:56
Executing Time for task name - task 1 = 02:32:57
Executing Time for task name - task 2 = 02:32:57
Executing Time for task name - task 3 = 02:32:57
Executing Time for task name - task 1 = 02:32:58
Executing Time for task name - task 2 = 02:32:58
Executing Time for task name - task 3 = 02:32:58
Executing Time for task name - task 1 = 02:32:59
Executing Time for task name - task 2 = 02:32:59
Executing Time for task name - task 3 = 02:32:59
Executing Time for task name - task 1 = 02:33:00
Executing Time for task name - task 3 = 02:33:00
Executing Time for task name - task 2 = 02:33:00
Executing Time for task name - task 2 = 02:33:01
Executing Time for task name - task 1 = 02:33:01
Executing Time for task name - task 3 = 02:33:01
task 2 complete
task 1 complete
task 3 complete
Initialization Time for task name - task 5 = 02:33:02
Initialization Time for task name - task 4 = 02:33:02
Executing Time for task name - task 4 = 02:33:03
Executing Time for task name - task 5 = 02:33:03
Executing Time for task name - task 5 = 02:33:04
Executing Time for task name - task 4 = 02:33:04
Executing Time for task name - task 4 = 02:33:05
Executing Time for task name - task 5 = 02:33:05
Executing Time for task name - task 5 = 02:33:06
Executing Time for task name - task 4 = 02:33:06
Executing Time for task name - task 5 = 02:33:07
Executing Time for task name - task 4 = 02:33:07
task 5 complete
task 4 complete
```
**Riesgos al usar Thread Pools**   

* Deadlock (bloqueo mutuo): cuando varios hilos se bloquean entre sí esperando recursos.
* Pérdida de hilos (Thread Leakage): sucede cuando los hilos no se devuelven al pool correctamente.
* Agotamiento de recursos (Resource Thrashing): ocurre cuando se crean y destruyen muchos hilos repetidamente o se abusa del uso de CPU/RAM debido a mala gestión de tareas.

<div align="right">
    <b><a href="#">↥ back to top</a></b>
</div>

## P. ¿Por qué wait(), notify() y notifyAll() deben ser llamados dentro de un bloque o método synchronized?
El método `wait()` uerza al hilo a liberar su bloqueo (lock). Esto significa que debe ser dueño del bloqueo de un objeto antes de llamar al método wait() sobre ese mismo objeto. Por lo tanto, el hilo debe estar dentro de un método o bloque synchronized del objeto antes de poder llamar a wait().

Cuando un hilo invoca `notify()` o `notifyAll()` sobre un objeto, uno (aleatorio) o todos los hilos que están esperando en la cola de espera de ese objeto son movidos a la cola de entrada. Estos hilos entonces compiten activamente por el bloqueo del objeto, y aquel que lo obtenga será el que continúe ejecutándose.

## P. ¿Cuál es la diferencia entre los métodos wait() y sleep()?
**1.  Clase a la que pertenecen**:  wait() pertenece a la clase `java.lang.Object` , por lo tanto puede ser llamado sobre cualquier objeto.

**2. Contexto de uso**:  wait() solo puede ser llamado dentro de un contexto sincronizado, es decir, dentro de un bloque o método synchronized.

**3. Comportamiento del bloqueo (lock)**:  wait() libera el bloqueo del objeto para permitir que otros hilos accedan. sleep() no libera el bloqueo durante el tiempo especificado o hasta que sea interrumpido.

**4. Condición de reanudación**:  Un hilo en espera (wait()) puede despertarse mediante notify() o notifyAll(). Un hilo dormido (sleep()) se despierta por interrupción o al agotarse el tiempo.

**5. Ejecución**:  Cada objeto tiene su propio método wait() usado para comunicación entre hilos. sleep() es un método estático de la clase Thread. Un error común es escribir t.sleep(1000), pero sleep() pausará el hilo actual, no necesariamente el hilo t.
```java
synchronized(LOCK) {
    Thread.sleep(1000); // LOCK is held
}


synchronized(LOCK) {
    LOCK.wait(); // LOCK is not held
}
```
## P. ¿Qué es la sincronización estática (static synchronization)?
La sincronización estática se logra mediante métodos static synchronized. Un método static synchronized bloquea a nivel de clase, mientras que un método synchronized (no estático) bloquea a nivel de instancia (objeto actual). Esto significa que ambos tipos de métodos pueden ejecutarse al mismo tiempo en diferentes contextos.

Si se invoca un método static synchronized, se adquiere un bloqueo a nivel de clase. Entonces, si otro hilo intenta acceder a un método synchronized no estático del mismo objeto, no podrá hacerlo si ese objeto está bloqueado a nivel de clase, lo que puede generar problemas de inconsistencia.
```java
/**
 * This program is used to show the multithreading 
 * example with synchronization using static synchronized method.
 * @author codesjava
 */
class PrintTable {    
    public synchronized static void printTable(int n) {
	   System.out.println("Table of " + n);
       for(int i = 1; i <= 10; i++) {
           System.out.println(n*i);  
           try {  
        	 Thread.sleep(500);  
           } catch(Exception e) {
        	 System.out.println(e);
           }  
        }         
    }  
}  
 
class MyThread1 extends Thread {    
    public void run() { 
    	PrintTable.printTable(2);  
    }        
}  
 
class MyThread2 extends Thread {   
	public void run() {  
		PrintTable.printTable(5);  
	}  
}  
 
public class MultiThreadExample {  
    public static void main(String args[]) {
 
    	//creating threads.
	    MyThread1 t1 = new MyThread1();  
	    MyThread2 t2 = new MyThread2();  
 
	    //start threads.
	    t1.start();  
	    t2.start();  
    }  
}
```
Output
```
Table of 2
2
4
6
8
10
12
14
16
18
20
Table of 5
5
10
15
20
25
30
35
40
45
50
```
<div align="right">
    <b><a href="#">↥ back to top</a></b>
</div>

## P. ¿Cómo se logra la seguridad de un hilo (thread safety)?
* Los objetos inmutables son por defecto seguros para el uso con hilos porque su estado no puede modificarse una vez creado. Por ejemplo, la clase String en Java es inmutable, por lo tanto es inherentemente thread-safe.
* Las variables de solo lectura o declaradas como final en Java también son seguras para su uso con múltiples hilos.
* El uso de bloqueos (locks) es una forma común de lograr seguridad en hilos en Java.
* Las variables estáticas (static) pueden causar problemas de seguridad si no están correctamente sincronizadas.
* Ejemplos de clases seguras para hilos en Java: Vector, Hashtable, ConcurrentHashMap, String, etc.
* Las operaciones atómicas en Java son seguras para hilos. Por ejemplo, leer un int de 32 bits desde la memoria es una operación atómica, lo que significa que no puede ser interrumpida ni mezclada con otras operaciones.
* Las variables locales también son seguras para hilos porque cada hilo tiene su propia copia. Usar variables locales es una buena práctica para escribir código thread-safe.
* Para evitar problemas de concurrencia, se debe minimizar el uso compartido de objetos entre múltiples hilos.
* La palabra clave volatile puede utilizarse para indicar que una variable no debe ser almacenada en caché por los hilos y que siempre debe leerse desde la memoria principal. También puede instruir a la JVM a no reordenar ni optimizar el acceso a dicha variable en contextos multihilo.

## P. ¿Cuál es la diferencia entre los métodos start() y run() de la clase Thread?
* Cuando el programa llama al método `start()`, se crea un nuevo hilo y se ejecuta el código dentro del método `run()` en ese nuevo hilo.
* Si se llama directamente al método run(), no se crea un nuevo hilo, y el código dentro de `run()` se ejecuta en el mismo hilo actual que hizo la llamada.
* El método `start()` no puede ser invocado más de una vez sobre el mismo objeto Thread, de lo contrario lanzará una excepción `java.lang.IllegalStateException`.
* En cambio, el método `run()` puede ser invocado múltiples veces como cualquier otro método normal.

```java
class MyThread extends Thread
{
    @Override
    public void run() {
        System.out.println("I am executed by " +currentThread().getName());
    }
}
 
public class ThreadExample
{
    public static void main(String[] args) {

        MyThread myThread = new MyThread();
 
        // Calling run() method directly 
        myThread.run();
 
        // Calling start() method. It creates a new thread which executes run() method
        myThread.start();
    }
}
```
Output
```
I am executed by main
I am executed by Thread-0
```
<div align="right">
    <b><a href="#">↥ back to top</a></b>
</div>

## P. ¿Por qué usamos la clase Vector?
Vector implementa un arreglo dinámico, lo que significa que puede crecer o reducirse según sea necesario. Al igual que un arreglo (array), contiene elementos que pueden ser accedidos mediante un índice entero.
Es muy similar a ArrayList, pero Vector es **sincronizado**, lo que lo hace seguro para uso con múltiples hilos (thread-safe). Además, contiene algunos métodos heredados (legacy) que no están presentes en el framework moderno de colecciones.
Vector extiende AbstractList e implementa las interfaces List.
```java
import java.util.*; 
class Vector_demo { 

    public static void main(String[] arg) { 

        // create default vector 
        Vector v = new Vector(); 
        v.add(10); 
        v.add(20); 
        v.add("Numbers");   
        System.out.println("Vector is " + v); 
    } 
}
```
<div align="right">
    <b><a href="#">↥ back to top</a></b>
</div>

## P. ¿Qué es un Thread Group? ¿Por qué se recomienda no usarlo?
ThreadGroup permite crear un grupo de hilos. Ofrece una forma conveniente de manejar múltiples hilos como una unidad. Esto resulta útil especialmente en situaciones donde se desea suspender y reanudar varios hilos relacionados al mismo tiempo.

* Los grupos de hilos forman una estructura en árbol, donde cada grupo de hilos (excepto el grupo inicial) tiene un grupo padre.
* Un hilo tiene permitido acceder a la información de su propio grupo de hilos, pero no puede acceder a la información del grupo padre ni de otros grupos de hilos ajenos.

```java
// Java code illustrating Thread Group 
import java.lang.*; 
class NewThread extends Thread  
{ 
    NewThread(String threadname, ThreadGroup tgob) { 
        super(tgob, threadname); 
        start(); 
    } 
    public void run() { 
  
        for (int i = 0; i < 1000; i++) { 
            try { 
                Thread.sleep(10); 
            } 
            catch (InterruptedException ex) { 
                System.out.println("Exception encounterted"); 
            } 
        } 
    } 
}  
public class ThreadGroupExample  
{ 
    public static void main(String arg[]) { 
        // creating the thread group 
        ThreadGroup tg = new ThreadGroup("Parent Thread Group"); 
  
        NewThread t1 = new NewThread("One", tg); 
        System.out.println("Starting One"); 
        NewThread t2 = new NewThread("Two", tg); 
        System.out.println("Starting two"); 
  
        // checking the number of active thread 
        System.out.println("Number of active thread: "
                           + tg.activeCount()); 
    } 
} 
```
## Q. ¿Cómo se detiene un hilo en Java?
Un hilo se destruye automáticamente cuando el método `run()` ha finalizado. Pero puede ser necesario detener/terminar un hilo antes de que complete su ciclo de vida. Las formas modernas de suspender/detener un hilo son usando una **bandera booleana** y el método `Thread.interrupt()`.
```java
class MyThread extends Thread
{
    //Initially setting the flag as true
    private volatile boolean flag = true;
     
    //This method will set flag as false
    public void stopRunning() {
        flag = false;
    }
     
    @Override
    public void run() {
         
        //This will make thread continue to run until flag becomes false
        while (flag) {
            System.out.println("I am running....");
        }
        System.out.println("Stopped Running....");
    }
}
 
public class MainClass 
{   
    public static void main(String[] args) {

        MyThread thread = new MyThread();
        thread.start();
        try {
            Thread.sleep(100);
        } catch (InterruptedException e) {
            e.printStackTrace();
        } 
        //call stopRunning() method whenever you want to stop a thread
        thread.stopRunning();
    }   
}
```
Output 
```
I am running….
I am running….
I am running….
I am running….
I am running….
I am running….
I am running….
I am running….
Stopped Running….
```
<div align="right">
    <b><a href="#">↥ back to top</a></b>
</div>

## Q. ¿Podemos llamar al método run() de la clase Thread?
No, no se puede llamar directamente al método `run()` para iniciar un hilo. Es necesario llamar al método `start()` para crear un nuevo hilo.  
Si llamas directamente al método `run()`, no se creará un nuevo hilo y se ejecutará en la misma pila (stack) que el hilo principal (`main`).
```java
class CustomThread extends Thread {
 
 public void run() {
  for (int i = 0; i < 5; i++) {
    try {
        Thread.sleep(300);
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
     System.out.println("Thread is running :"+i);
  }
 }
}
 
public class StartThreadAgainMain {
 
 public static void main(String[] args) {
   CustomThread ct1 = new CustomThread();
   CustomThread ct2 = new CustomThread();
   ct1.run();
   ct2.run();
 }
}
```
Output
```
Thread is running :0
Thread is running :1
Thread is running :2
Thread is running :3
Thread is running :4
Thread is running :0
Thread is running :1
Thread is running :2
Thread is running :3
Thread is running :4
```
<div align="right">
    <b><a href="#">↥ back to top</a></b>
</div>

## P. ¿Cuál es la diferencia entre los métodos yield() y sleep() en Java?
**1. Estado del hilo en ejecución**: El método sleep() hace que el hilo en ejecución se detenga (duerma) durante el número de milisegundos especificado como argumento.
El método yield() pausa temporalmente el hilo actual para darle una oportunidad a otros hilos en espera con la misma prioridad de ejecutarse.
Si no hay hilos esperando o todos tienen prioridad más baja, entonces el hilo actual continúa su ejecución.

**2. Excepción de interrupción**: El método sleep() lanza una excepción InterruptedException si otro hilo interrumpe al hilo que está dormido. El método yield() no lanza InterruptedException.

**3. Liberación de monitores (locks)**:  El método Thread.sleep() no libera ningún monitor que el hilo pueda tener.El método yield() puede liberar el uso de la CPU, pero no garantiza la liberación de monitores.

## P. ¿Qué es ThreadLocal?
La clase ThreadLocal en Java permite crear variables que solo pueden ser leídas y escritas por el mismo hilo.
Esto significa que, aunque dos hilos ejecuten el mismo código y tengan referencia a la misma variable ThreadLocal, no compartirán su valor.
Cada hilo tendrá su propia copia de la variable, lo cual proporciona una forma sencilla de escribir código thread-safe que de otro modo no lo sería.

**Crear un ThreadLocal**   
```java
private ThreadLocal threadLocal = new ThreadLocal();
```
**Asignar un valor**
```java
threadLocal.set("A thread local value");
```
**Obtener el valor**
```java
String threadLocalValue = (String) threadLocal.get();
```
**Eliminar el valor**
```java
threadLocal.remove();
```
Ejemplo
```java
// Java code illustrating get() and set() method 
public class ThreadLocalExample { 
  
    public static void main(String[] args) { 
  
        ThreadLocal<Number> tlObj = new ThreadLocal<Number>(); 
  
        // setting the value 
        tlObj.set(100);  
        System.out.println("value = " + tlObj.get()); 
    } 
} 
```
<div align="right">
    <b><a href="#">↥ back to top</a></b>
</div>

## P. ¿Qué es un Java Thread Dump y cómo se puede obtener un Thread Dump de un programa?
Una volcada de hilos (thread dump) en Java es una forma de averiguar qué está haciendo cada hilo en la JVM en un momento determinado. Esto es especialmente útil si tu aplicación Java parece colgarse al ejecutarse bajo carga, ya que un análisis del thread dump mostrará dónde están atascados los hilos.

Puedes generar un thread dump en Unix/Linux ejecutando `kill -QUIT <pid>`, y en Windows presionando `Ctl + Break`.

Un thread dump es la lista de todos los hilos; cada entrada muestra información sobre un hilo, incluyendo lo siguiente en el orden en que aparece:

**Java Thread Dump Example**  
```
2019-12-26 22:28:39
Full thread dump Java HotSpot(TM) 64-Bit Server VM (23.5-b02 mixed mode):

"Attach Listener" daemon prio=5 tid=0x00007fb7d8000000 nid=0x4207 waiting on condition [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"Timer-0" daemon prio=5 tid=0x00007fb7d4867000 nid=0x5503 waiting on condition [0x00000001604d9000]
   java.lang.Thread.State: TIMED_WAITING (sleeping)
	at java.lang.Thread.sleep(Native Method)
	at com.journaldev.threads.MyTimerTask.completeTask(MyTimerTask.java:19)
	at com.journaldev.threads.MyTimerTask.run(MyTimerTask.java:12)
	at java.util.TimerThread.mainLoop(Timer.java:555)
	at java.util.TimerThread.run(Timer.java:505)

"Service Thread" daemon prio=5 tid=0x00007fb7d482c000 nid=0x5303 runnable [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"C2 CompilerThread1" daemon prio=5 tid=0x00007fb7d482b800 nid=0x5203 waiting on condition [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"C2 CompilerThread0" daemon prio=5 tid=0x00007fb7d4829800 nid=0x5103 waiting on condition [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"Signal Dispatcher" daemon prio=5 tid=0x00007fb7d4828800 nid=0x5003 runnable [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"Finalizer" daemon prio=5 tid=0x00007fb7d4812000 nid=0x3f03 in Object.wait() [0x000000015fd26000]
   java.lang.Thread.State: WAITING (on object monitor)
	at java.lang.Object.wait(Native Method)
	- waiting on <0x0000000140a25798> (a java.lang.ref.ReferenceQueue$Lock)
	at java.lang.ref.ReferenceQueue.remove(ReferenceQueue.java:135)
	- locked <0x0000000140a25798> (a java.lang.ref.ReferenceQueue$Lock)
	at java.lang.ref.ReferenceQueue.remove(ReferenceQueue.java:151)
	at java.lang.ref.Finalizer$FinalizerThread.run(Finalizer.java:177)

"Reference Handler" daemon prio=5 tid=0x00007fb7d4811800 nid=0x3e03 in Object.wait() [0x000000015fc23000]
   java.lang.Thread.State: WAITING (on object monitor)
	at java.lang.Object.wait(Native Method)
	- waiting on <0x0000000140a25320> (a java.lang.ref.Reference$Lock)
	at java.lang.Object.wait(Object.java:503)
	at java.lang.ref.Reference$ReferenceHandler.run(Reference.java:133)
	- locked <0x0000000140a25320> (a java.lang.ref.Reference$Lock)

"main" prio=5 tid=0x00007fb7d5000800 nid=0x1703 waiting on condition [0x0000000106116000]
   java.lang.Thread.State: TIMED_WAITING (sleeping)
	at java.lang.Thread.sleep(Native Method)
	at com.journaldev.threads.MyTimerTask.main(MyTimerTask.java:33)

"VM Thread" prio=5 tid=0x00007fb7d480f000 nid=0x3d03 runnable 
"GC task thread#0 (ParallelGC)" prio=5 tid=0x00007fb7d500d800 nid=0x3503 runnable 
"GC task thread#1 (ParallelGC)" prio=5 tid=0x00007fb7d500e000 nid=0x3603 runnable 
"GC task thread#2 (ParallelGC)" prio=5 tid=0x00007fb7d5800000 nid=0x3703 runnable 
"GC task thread#3 (ParallelGC)" prio=5 tid=0x00007fb7d5801000 nid=0x3803 runnable 
"GC task thread#4 (ParallelGC)" prio=5 tid=0x00007fb7d5801800 nid=0x3903 runnable 
"GC task thread#5 (ParallelGC)" prio=5 tid=0x00007fb7d5802000 nid=0x3a03 runnable 
"GC task thread#6 (ParallelGC)" prio=5 tid=0x00007fb7d5802800 nid=0x3b03 runnable 
"GC task thread#7 (ParallelGC)" prio=5 tid=0x00007fb7d5803800 nid=0x3c03 runnable 
"VM Periodic Task Thread" prio=5 tid=0x00007fb7d481e800 nid=0x5403 waiting on condition 

JNI global references: 116
```
* **Thread Name**: Nombre del hilo
* **Thread Priority**: Prioridad asignada al hilo
* **Thread ID**: Identificador único del hilo.
* **Thread Status**: Estado actual del hilo (por ejemplo: RUNNABLE, WAITING, BLOCKED). Al analizar deadlocks, es fundamental observar los hilos en estado BLOCKED y los recursos que están intentando bloquear.
* **Thread callstack**: Información de la pila de llamadas del hilo. Aquí se puede ver qué métodos está ejecutando el hilo, qué bloqueos ha adquirido y si está esperando algún otro.

**Herramientas para obtener y analizar un Thread Dump**  

* jstack – herramienta de línea de comandos para generar dumps.
* JVisualVM – herramienta gráfica para monitorear JVMs en tiempo real.
* JMC (Java Mission Control) – herramienta de monitoreo y análisis de rendimiento.
* ThreadMXBean – API para inspeccionar hilos desde código Java.
* APM Tool (como AppDynamics) – monitoreo de rendimiento de aplicaciones.
* JCMD – herramienta de línea de comandos más avanzada que jstack.
* VisualVM Profiler – para hacer profiling y analizar el uso de CPU y memoria por hilo.
<div align="right">
    <b><a href="#">↥ back to top</a></b>
</div>

## P. ¿Qué sucede si no sobreescribimos el método run() de la clase Thread?
Si no sobreescribimos el método run() en nuestra clase personalizada que extiende Thread, entonces se ejecutará el método run() definido por defecto en la clase Thread, el cual tiene una implementación vacía. Por esta razón, no se obtendrá ninguna salida ni se ejecutará ninguna lógica.

Es altamente recomendado sobreescribir el método run(), ya que:
* Permite definir la lógica/tarea que el hilo debe ejecutar.
* Mejora el comportamiento y rendimiento general del sistema multihilo.
* Sin la sobreescritura, el hilo creado no realizará ninguna operación útil.
```
abstract class NotOverridableRunMethod extends Thread {
	abstract public void run();
}

class ParentMain {
	public static void main(String[] args) {
		OverrideRunMethod orn = new OverrideRunMethod();
		orn.start();
		System.out.println("Thread class run() method will be executed with empty implementation");
	}
}
```
Output
```
cmd> java ParentMain
Thread class run() method will be executed with empty implementation
I am in run() method 
```

## P. ¿Cuál es la diferencia entre la clase Thread y la interfaz Runnable para crear un hilo?
Un hilo (Thread) puede definirse de dos maneras en Java:
* Extendiendo la clase Thread, que ya implementa internamente la interfaz Runnable.
* Implementando directamente la interfaz Runnable.

**Diferencias**  

|CLASE  THREAD                              |INTERFAZ  RUNNABLE                                             |
|-------------------------------------------|---------------------------------------------------------------|
|Cada hilo crea un objeto único y se asocia con él.|	Varios hilos pueden compartir el mismo objeto.|
|Al crear un objeto por hilo, se requiere más memoria.|Al compartir el mismo objeto, se reduce el uso de memoria.|
|Java no permite herencia múltiple, por lo que si una clase extiende Thread, no puede extender otra clase.|	Una clase que implementa Runnable puede extender otra clase.|
|Se debe extender Thread solo si se desea sobreescribir otros métodos de la clase.|Si solo se necesita definir el comportamiento del método run(), es mejor implementar Runnable.|
|Extender Thread implica un acoplamiento fuerte: la clase contiene la lógica del hilo y la tarea que realiza.|	Implementar Runnable promueve un acoplamiento débil: el hilo y la tarea están desacoplados.|

## P. ¿Qué hace el método join()?
La clase `java.lang.Thread` proporciona el método join(), el cual permite que un hilo espere a que otro hilo termine su ejecución antes de continuar.
```java
public class ThreadJoinExample {

    public static void main(String[] args) {
        Thread t1 = new Thread(new MyRunnable(), "t1");
        Thread t2 = new Thread(new MyRunnable(), "t2");
        Thread t3 = new Thread(new MyRunnable(), "t3");
        
        t1.start();
        
        //start second thread after waiting for 2 seconds or if it's dead
        try {
            t1.join(2000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        
        t2.start();
        
        //start third thread only when first thread is dead
        try {
            t1.join();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        
        t3.start();
        
        //let all threads finish execution before finishing main thread
        try {
            t1.join();
            t2.join();
            t3.join();
        } catch (InterruptedException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
        
        System.out.println("All threads are dead, exiting main thread");
    }

}

class MyRunnable implements Runnable {

    @Override
    public void run() {
        System.out.println("Thread started: "+Thread.currentThread().getName());
        try {
            Thread.sleep(4000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("Thread ended: "+Thread.currentThread().getName());
    }
}
```
Output
```
Thread started: t1
Thread started: t2
Thread ended: t1
Thread started: t3
Thread ended: t2
Thread ended: t3
All threads are dead, exiting main thread
```
<div align="right">
    <b><a href="#">↥ back to top</a></b>
</div>

## P. ¿Qué es una condición de carrera (race condition)?
Una race condition en Java ocurre en un entorno multihilo (multi-threaded) cuando dos o más hilos intentan acceder a un recurso compartido (modificarlo o escribir en él) al mismo tiempo.
Como varios hilos compiten entre sí para terminar de ejecutar un método primero, a esta situación se le da el nombre de condición de carrera.
Este tipo de problemas puede provocar resultados inesperados o erróneos, ya que la ejecución simultánea sin sincronización puede interrumpir la coherencia del estado del recurso compartido.

**Ejemplo de condición de carrera en Java**   
```java
class Counter  implements Runnable{
    private int c = 0;

    public void increment() {
        try {
            Thread.sleep(10);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        c++;
    }

    public void decrement() {    
        c--;
    }

    public int getValue() {
        return c;
    }
    
    @Override
    public void run() {
        //incrementing
        this.increment();
        System.out.println("Value for Thread After increment " 
        + Thread.currentThread().getName() + " " + this.getValue());
        //decrementing
        this.decrement();
        System.out.println("Value for Thread at last " 
        + Thread.currentThread().getName() + " " + this.getValue());        
    }
}

public class RaceConditionExample {
    public static void main(String[] args) {
        Counter counter = new Counter();
        Thread t1 = new Thread(counter, "Thread-1");
        Thread t2 = new Thread(counter, "Thread-2");
        Thread t3 = new Thread(counter, "Thread-3");
        t1.start();
        t2.start();
        t3.start();
    }    
}
```
Output
```
Value for Thread After increment Thread-2 3
Value for Thread at last Thread-2 2
Value for Thread After increment Thread-1 2
Value for Thread at last Thread-1 1
Value for Thread After increment Thread-3 1
Value for Thread at last Thread-3 0
```

**Usando sincronización para evitar la condición de carrera en Java**  
Para solucionar una race condition, es necesario restringir el acceso al recurso compartido a un solo hilo a la vez.
Esto se logra usando la palabra clave synchronized, que permite sincronizar el acceso al recurso compartido y garantiza que solo un hilo pueda ejecutar una sección crítica del código al mismo tiempo.
```java
//This class' shared object will be accessed by threads
class Counter  implements Runnable{
    private int c = 0;

    public  void increment() {
        try {
            Thread.sleep(10);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        c++;
    }

    public  void decrement() {    
        c--;        
    }

    public  int getValue() {
        return c;
    }
    
    @Override
    public void run() {
        synchronized(this) {
            // incrementing
            this.increment();
            System.out.println("Value for Thread After increment " 
             + Thread.currentThread().getName() + " " + this.getValue());
            //decrementing
            this.decrement();
            System.out.println("Value for Thread at last " + Thread.currentThread().getName() 
                + " " + this.getValue());
        }        
    }
}

public class RaceConditionExample {
    public static void main(String[] args) {
        Counter counter = new Counter();
        Thread t1 = new Thread(counter, "Thread-1");
        Thread t2 = new Thread(counter, "Thread-2");
        Thread t3 = new Thread(counter, "Thread-3");
        t1.start();
        t2.start();
        t3.start();
    }    
}
```
Output
```
Value for Thread After increment Thread-2 1
Value for Thread at last Thread-2 0
Value for Thread After increment Thread-3 1
Value for Thread at last Thread-3 0
Value for Thread After increment Thread-1 1
Value for Thread at last Thread-1 0
```
##Ventajas del uso de synchronized:##
* Se evita que varios hilos modifiquen al mismo tiempo un recurso compartido.
* Se asegura que las operaciones críticas se ejecuten de forma atómica (completa).
* Protege la consistencia del estado del objeto compartido.

<div align="right">
    <b><a href="#">↥ back to top</a></b>
</div>

## P. ¿Qué es la interfaz Lock en la API de concurrencia de Java? ¿Cuál es la diferencia entre ReentrantLock y synchronized?
`java.util.concurrent.locks.Lock` es un mecanismo de sincronización de hilos, similar a los bloques synchronized. Sin embargo, Lock es más flexible y avanzado que synchronized. Dado que Lock es una interfaz, necesitas usar una de sus implementaciones para poder utilizarlo. Una de las implementaciones más comunes es ReentrantLock.
```java
Lock lock = new ReentrantLock();
 
lock.lock();
 
//critical section
lock.unlock();
```

**Diferencias entre la interfaz Lock y la palabra clave synchronized**  

* No es posible establecer un tiempo de espera al intentar acceder a un bloque `synchronized`. Usando `Lock.tryLock(long timeout, TimeUnit timeUnit)`, sí es posible.
* El bloque `synchronized` debe estar completamente contenido dentro de un solo método. Un `Lock` puede tener sus llamadas a `lock()` y `unlock()` en métodos separados.

```java
import java.util.concurrent.TimeUnit;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class ConcurrencyLockExample implements Runnable {

	private Resource resource;
	private Lock lock;
	
	public ConcurrencyLockExample(Resource r) {
		this.resource = r;
		this.lock = new ReentrantLock();
	}
	
	@Override
	public void run() {
		try {
			if(lock.tryLock(10, TimeUnit.SECONDS)) {
			  resource.doSomething();
			}
		} catch (InterruptedException e) {
			e.printStackTrace();
		} finally {
			//release lock
			lock.unlock();
		}
		resource.doLogging();
	}
}
```
## P. ¿Cuál es la diferencia entre las interfaces Runnable y Callable?
Tanto Runnable como Callable son interfaces utilizadas en entornos multihilo (multithreading) para definir tareas que pueden ejecutarse en paralelo.
* Runnable es la interfaz básica disponible desde las primeras versiones de Java para representar tareas multihilo.
* Callable es una versión mejorada de Runnable, introducida en Java 1.5 dentro del paquete java.util.concurrent.

**Diferencias entre Callable y Runnable en Java**  

**1. Excepciones comprobadas (Checked Exception):**: El método call() de Callable puede lanzar excepciones comprobadas. El método run() de Runnable no puede lanzar excepciones comprobadas.

**2. Valor de retorno**: El método run() de Runnable tiene tipo de retorno void → no puede devolver ningún valor. El método call() de Callable devuelve un resultado (genérico) → puede devolver un Future, que representa el ciclo de vida de una tarea y permite consultar su estado y resultado.

**3. Implementación**: Runnable requiere implementar el método run(). Callable requiere implementar el método call().

**4. Ejecución**: Callable no puede ser pasado directamente a un objeto Thread como sí ocurre con Runnable. La clase Thread no tiene un constructor que acepte un Callable, por eso se usa ExecutorService para ejecutarlos.

Example: Callable Interface
```java
// Java program to illustrate Callable and FutureTask 
// for random number generation 
import java.util.Random; 
import java.util.concurrent.Callable; 
import java.util.concurrent.FutureTask; 
  
class CallableExample implements Callable 
{ 
  
  public Object call() throws Exception { 
    Random generator = new Random(); 
    Integer randomNumber = generator.nextInt(5); 
    Thread.sleep(randomNumber * 1000); 
    return randomNumber; 
  } 
} 
  
public class CallableFutureTest 
{ 
  public static void main(String[] args) throws Exception { 
  
    // FutureTask is a concrete class that 
    // implements both Runnable and Future 
    FutureTask[] randomNumberTasks = new FutureTask[5]; 
  
    for (int i = 0; i < 5; i++) { 
      Callable callable = new CallableExample(); 
  
      // Create the FutureTask with Callable 
      randomNumberTasks[i] = new FutureTask(callable); 
  
      // As it implements Runnable, create Thread 
      // with FutureTask 
      Thread t = new Thread(randomNumberTasks[i]); 
      t.start(); 
    } 
  
    for (int i = 0; i < 5; i++) { 
      // As it implements Future, we can call get() 
      System.out.println(randomNumberTasks[i].get()); 
    } 
  } 
}
```
Output
```
4
2
3
3
0
```
Example: Runnable Interface
```java
// Java program to illustrate Runnable 
// for random number generation 
import java.util.Random; 
import java.util.concurrent.Callable; 
import java.util.concurrent.FutureTask; 
  
class RunnableExample implements Runnable 
{ 
    // Shared object to store result 
    private Object result = null; 
  
    public void run() { 
        Random generator = new Random(); 
        Integer randomNumber = generator.nextInt(5); 
  
        // As run cannot throw any Exception 
        try { 
            Thread.sleep(randomNumber * 1000); 
        } catch (InterruptedException e) { 
            e.printStackTrace(); 
        } 
  
        // Store the return value in result when done 
        result = randomNumber; 
  
        // Wake up threads blocked on the get() method 
        synchronized(this) { 
            notifyAll(); 
        } 
    } 
  
    public synchronized Object get() throws InterruptedException  { 
        while (result == null) 
            wait(); 
  
        return result; 
    } 
} 

public class RunnableTest 
{ 
    public static void main(String[] args) throws Exception { 
        RunnableExample[] randomNumberTasks = new RunnableExample[5]; 
  
        for (int i = 0; i < 5; i++) { 
            randomNumberTasks[i] = new RunnableExample(); 
            Thread t = new Thread(randomNumberTasks[i]); 
            t.start(); 
        } 
  
        for (int i = 0; i < 5; i++) 
            System.out.println(randomNumberTasks[i].get()); 
    } 
} 
```
Output
```
0
4
3
1
4
```
<div align="right">
    <b><a href="#">↥ back to top</a></b>
</div>

## P. ¿Qué es el flag de interrupción de un Thread? ¿Cómo se relaciona con InterruptedException?
Cuando un hilo está en estado de espera o sueño (es decir, si se invocan métodos como sleep() o wait()), y se llama al método interrupt() sobre ese hilo, este saldrá de dicho estado lanzando una excepción InterruptedException.
Si el hilo no está en estado de espera ni de sueño, al llamar a interrupt():
* No se interrumpe inmediatamente, pero
* Se activa su flag de interrupción, es decir, se marca internamente como "interrumpido".

Ejemplo: **Interrumpiendo un hilo que deja de funcionar**
```java
// Programa Java para ilustrar el concepto del método interrupt()
// cuando un hilo está en reposo
class ThreadsInterruptExample extends Thread { 

    public void run() { 
        try { 
            Thread.sleep(1000); 
            System.out.println("Task"); 
        } catch (InterruptedException e) { 
            throw new RuntimeException("Thread interrupted"); 
        } 
    } 
    public static void main(String args[]) { 
        ThreadsInterruptExample t1 = new ThreadsInterruptExample(); 
        t1.start(); 
        try { 
            t1.interrupt(); 
        } 
        catch (Exception e) { 
            System.out.println("Exception handled"); 
        } 
    } 
} 
```
Output
```
Exception in thread "Thread-0" java.lang.RuntimeException: Thread interrupted
```
## P. ¿Qué es el Java Memory Model (JMM)? Describe su propósito e ideas básicas.
El modelo de memoria de Java (Java Memory Model), utilizado internamente por la JVM, divide la memoria entre las pilas de hilos (thread stacks) y el heap. Cada hilo que se ejecuta en la máquina virtual de Java tiene su propia pila de hilos. Esta pila contiene información sobre qué métodos ha llamado el hilo para llegar al punto actual de ejecución.

La pila del hilo también contiene todas las variables locales de cada método que se está ejecutando (todos los métodos en la pila de llamadas). Un hilo solo puede acceder a su propia pila. Las variables locales creadas por un hilo son invisibles para todos los demás hilos, excepto para el hilo que las creó. Incluso si dos hilos están ejecutando exactamente el mismo código, cada uno de ellos creará sus propias variables locales en su respectiva pila. Por lo tanto, cada hilo tiene su propia versión de cada variable local.

Todas las variables locales de tipos primitivos (boolean, byte, short, char, int, long, float, double) se almacenan completamente en la pila del hilo y, por lo tanto, no son visibles para otros hilos. Un hilo puede pasar una copia de una variable primitiva a otro hilo, pero no puede compartir directamente la variable local primitiva.

El heap contiene todos los objetos creados en tu aplicación Java, sin importar qué hilo haya creado el objeto. Esto incluye las versiones en objeto de los tipos primitivos (por ejemplo, Byte, Integer, Long, etc.). No importa si un objeto fue creado y asignado a una variable local o como variable miembro de otro objeto, el objeto seguirá almacenándose en el heap.

<div align="right">
    <b><a href="#">↥ back to top</a></b>
</div>

## Q. Describe las condiciones de livelock y starvation
**Livelock** ocurre cuando dos o más procesos repiten continuamente la misma interacción en respuesta a los cambios de los otros procesos sin realizar ningún trabajo útil. Estos procesos no están en estado de espera, y se están ejecutando concurrentemente. Esto es diferente a un deadlock porque en un deadlock todos los procesos están en estado de espera.
```java
var l1 = .... // lock object like semaphore or mutex etc 
var l2 = .... // lock object like semaphore or mutex etc 
      
    // Thread1 
    Thread.Start( ()=> { 
              
    while (true) { 
          
        if (!l1.Lock(1000)) { 
            continue; 
        } 
        if (!l2.Lock(1000)) { 
            continue; 
        } 
          
        // do some work 
    }); 
  
    // Thread2 
    Thread.Start( ()=> { 
            
      while (true) { 
            
        if (!l2.Lock(1000)) { 
            continue; 
        }   
        if (!l1.Lock(1000)) { 
            continue; 
        }   
        // do some work 
    }); 
```

**Starvation** describe una situación donde un hilo codicioso mantiene un recurso durante mucho tiempo, por lo que otros hilos quedan bloqueados para siempre. Los hilos bloqueados están esperando adquirir el recurso pero nunca tienen la oportunidad. Por lo tanto, se "mueren de hambre".

La starvation puede ocurrir debido a las siguientes razones:

* Los hilos están bloqueados infinitamente porque un hilo tarda mucho en ejecutar algún código sincronizado (por ejemplo, operaciones de E/S pesadas o un bucle infinito).

* Un hilo no recibe tiempo de CPU para ejecutarse porque tiene baja prioridad en comparación con otros hilos que tienen mayor prioridad.

* Los hilos están esperando un recurso indefinidamente, pero permanecen esperando porque otros hilos son notificados constantemente en lugar de los "hambrientos".

<div align="right">
    <b><a href="#">↥ back to top</a></b>
</div>

## P. ¿Cómo comparto una variable entre 2 hilos en Java?

Debemos declarar dichas variables como `static` y `volatile`.

Las **variables volatile** son compartidas entre múltiples hilos. Esto significa que los hilos individuales no almacenan en caché su copia en el almacenamiento local del hilo. Pero cada objeto tendría su propia copia de la variable, por lo que los hilos podrían almacenar el valor localmente.

Sabemos que los campos `static` se comparten entre todos los objetos de la clase, y pertenecen a la clase y no a los objetos individuales. Pero incluso para variables `static` y no `volatile`, los hilos pueden almacenar la variable localmente en caché.

## Q. ¿Cuáles son los componentes principales de la API de concurrencia?

La API de concurrencia está diseñada y optimizada específicamente para acceso multihilo sincronizado. Están agrupadas bajo el paquete `java.util.concurrent`.

**Componentes principales**  

* Executor
* ExecutorService
* ScheduledExecutorService
* Future
* CountDownLatch
* CyclicBarrier
* Semaphore
* ThreadFactory
* BlockingQueue
* DelayQueue
* Locks
* Phaser

## Q. ¿Cuál es la diferencia entre CyclicBarrier y CountDownLatch en Java?
Tanto `CyclicBarrier` como `CountDownLatch` se utilizan para implementar un escenario donde un hilo espera que uno o más hilos completen su trabajo antes de comenzar a procesar. Las diferencias son:

**1. Definición**: `CountDownLatch` es una ayuda de sincronización que permite que uno o más hilos esperen hasta que un conjunto de operaciones realizadas en otros hilos se complete.

`CyclicBarrier` es una ayuda de sincronización que permite que un conjunto de hilos esperen entre sí hasta alcanzar un punto de barrera común.

**2. Reutilizable**: Un `CountDownLatch` se inicializa con una cuenta dada. La cuenta llega a cero al llamar al método `countDown()`. La cuenta no se puede reiniciar.

`CyclicBarrier` permite reiniciar la cuenta. La barrera se llama "cíclica" porque se puede reutilizar después de que los hilos que estaban esperando sean liberados (o cuando la cuenta llegue a cero).  

## Q. ¿Qué es un Semaphore en la concurrencia de Java?
Un `Semaphore` es una construcción de sincronización de hilos que puede utilizarse para enviar señales entre hilos y así evitar señales perdidas, o para proteger una sección crítica como lo harías con un `lock`. Java 5 incluye implementaciones de semáforos en el paquete `java.util.concurrent`.

**1. Semaphore Simple**:
```java
public class Semaphore {
  private boolean signal = false;

  public synchronized void take() {
    this.signal = true;
    this.notify();
  }

  public synchronized void release() throws InterruptedException{
    while(!this.signal) wait();
    this.signal = false;
  }
}
```
El método `take()` envía una señal que se almacena internamente en el `Semaphore`. El método `release()` espera una señal. Cuando se recibe la señal, el indicador (flag) se borra nuevamente y el método `release()` finaliza su ejecución.

**2. Semaphore Contador**:
```java
public class CountingSemaphore {
  private int signals = 0;

  public synchronized void take() {
    this.signals++;
    this.notify();
  }

  public synchronized void release() throws InterruptedException{
    while(this.signals == 0) wait();
    this.signals--;
  }
}
```
**3. Semaphore Acotado** 
```java
public class BoundedSemaphore {
  private int signals = 0;
  private int bound   = 0;

  public BoundedSemaphore(int upperBound){
    this.bound = upperBound;
  }

  public synchronized void take() throws InterruptedException{
    while(this.signals == bound) wait();
    this.signals++;
    this.notify();
  }

  public synchronized void release() throws InterruptedException{
    while(this.signals == 0) wait();
    this.signals--;
    this.notify();
  }
}
```
**4. Uso de Semáforos como Locks**  
```java
BoundedSemaphore semaphore = new BoundedSemaphore(1);

...

semaphore.take();

try{
  //critical section
} finally {
  semaphore.release();
}
```
<div align="right">
    <b><a href="#">↥ back to top</a></b>
</div>

## Q. ¿Qué es Callable y Future en la concurrencia de Java?
`Future` y `FutureTask` en Java permiten escribir código asincrónico. La interfaz `Future` proporciona métodos **para verificar si el cálculo ha finalizado, para esperar su finalización y para recuperar los resultados del cálculo**. El resultado se recupera utilizando el método `get()` de `Future` una vez que el cálculo ha finalizado, y este método bloquea hasta que se complete. Necesitamos un objeto `Callable` para crear una `FutureTask` y luego podemos usar un `Thread Pool Executor` de Java para procesarlas de forma asincrónica.

```java
import java.util.concurrent.Callable; 
import java.util.concurrent.ExecutionException; 
import java.util.concurrent.ExecutorService; 
import java.util.concurrent.Executors; 
import java.util.concurrent.Future; 
import java.util.logging.Level; 
import java.util.logging.Logger; 
/** 
* Java program to show how to use Future in Java. Future allows to write 
* asynchronous code in Java, where Future promises result to be available in 
* future  
**/ 
public class FutureExample { 

    private static final ExecutorService threadpool = Executors.newFixedThreadPool(3); 

    public static void main(String args[]) throws InterruptedException, ExecutionException { 
        
        FactorialCalculator task = new FactorialCalculator(10); 
        System.out.println("Submitting Task ..."); 
        
        Future future = threadpool.submit(task); 
        
        System.out.println("Task is submitted"); 
        
        while (!future.isDone()) { 
            System.out.println("Task is not completed yet...."); 
            Thread.sleep(1); //sleep for 1 millisecond before checking again 
        } 
        System.out.println("Task is completed, let's check result"); 
        long factorial = future.get(); 
        System.out.println("Factorial of 1000000 is : " + factorial); 
        
        threadpool.shutdown(); 
    } 
    private static class FactorialCalculator implements Callable { 
        
        private final int number; 
        
        public FactorialCalculator(int number) { 
            this.number = number; 
        } 
        @Override public Long call() { 
            long output = 0; 
            try { 
                output = factorial(number); 
            } catch (InterruptedException ex) { 
                Logger.getLogger(Test.class.getName()).log(Level.SEVERE, null, ex); 
            } 
            return output; 
        } 
        private long factorial(int number) throws InterruptedException { 
            if (number < 0) { 
                throw new IllegalArgumentException("Number must be greater than zero"); 
            } 
            long result = 1; 
            while (number > 0) { 
                Thread.sleep(1); // adding delay for example 
                result = result * number; 
                number--; 
            } 
            return result; 
        } 
    } 
} 
```
Output
```
Submitting Task ... 
Task is submitted Task is not completed yet.... 
Task is not completed yet.... 
Task is not completed yet.... 
Task is completed, let's check result 
Factorial of 1000000 is : 3628800
```
<div align="right">
    <b><a href="#">↥ back to top</a></b>
</div>

## Q. ¿Qué es un método bloqueante en Java?
Los métodos bloqueantes en Java son aquellos que bloquean el hilo en ejecución hasta que su operación ha finalizado. Un ejemplo de método bloqueante es el método `InputStream.read()`, que se bloquea hasta que todos los datos del `InputStream` han sido completamente leídos.

```java
public class BlcokingCallTest {

    public static void main(String args[]) throws FileNotFoundException, IOException  {
      System.out.println("Calling blocking method in Java");
      int input = System.in.read();
      System.out.println("Blocking method is finished");
    }  
}
```
**Ejemplos de métodos bloqueantes en Java:**  

* **InputStream.read()**: se bloquea hasta que los datos de entrada estén disponibles, se lance una excepción o se detecte el final del flujo.
* **ServerSocket.accept()**: escucha conexiones entrantes por socket en Java y se bloquea hasta que se realiza una conexión.
* **InvokeAndWait()**: espera hasta que el código sea ejecutado desde el hilo del despachador de eventos (Event Dispatcher Thread).

## Q. ¿Qué es una variable atómica en Java?
Las variables atómicas permiten realizar operaciones atómicas sobre las variables. Las clases de variables atómicas más comúnmente usadas en Java son `AtomicInteger`, `AtomicLong`, `AtomicBoolean` y `AtomicReference`. Estas clases representan un `int`, `long`, `boolean` y una referencia a objeto respectivamente, las cuales pueden ser actualizadas de manera atómica. Los métodos principales que exponen estas clases son:

*  `get()`: obtiene el valor desde la memoria, de modo que los cambios realizados por otros hilos sean visibles; equivalente a leer una variable `volatile`.
*  `set()`: escribe el valor en memoria, de modo que el cambio sea visible para otros hilos; equivalente a escribir una variable `volatile`.
*  `lazySet()`: eventualmente escribe el valor en memoria, pudiendo reordenarse con operaciones relevantes de memoria posteriores. Un caso de uso es anular referencias para la recolección de basura, cuando nunca se volverán a usar. En este caso, se logra un mejor rendimiento al retrasar la escritura `volatile` del `null`.
*  `compareAndSet()`: igual que se describió anteriormente, retorna `true` cuando tiene éxito, en caso contrario `false`.
*  `weakCompareAndSet()`: igual que `compareAndSet()`, pero más débil, ya que no crea ordenamientos de tipo *happens-before*. Esto significa que puede no ver las actualizaciones realizadas a otras variables.

```java
public class SafeCounterWithoutLock {

    private final AtomicInteger counter = new AtomicInteger(0);
     
    public int getValue() {
        return counter.get();
    }
    public void increment() {
        while(true) {
            int existingValue = getValue();
            int newValue = existingValue + 1;
            if(counter.compareAndSet(existingValue, newValue)) {
                return;
            }
        }
    }
}
```
<div align="right">
    <b><a href="#">↥ back to top</a></b>
</div>

## Q. ¿Qué es el Framework Executors?
El framework `Executor` ayuda a **desacoplar el envío de una tarea de su ejecución**. En el paquete `java.util.concurrent` existen tres interfaces principales:

**1. Executor** — Se utiliza para enviar una nueva tarea.

**2. ExecutorService** — Es una subinterfaz de `Executor` que añade métodos para gestionar el ciclo de vida de los hilos que ejecutan las tareas enviadas, y métodos para producir un `Future` para obtener el resultado de un cálculo asincrónico.

**3. ScheduledExecutorService** — Es una subinterfaz de `ExecutorService`, utilizada para ejecutar comandos periódicamente o después de un cierto retraso.

Ejemplo: **Java ExecutorService**
```java
ExecutorService executorService = Executors.newFixedThreadPool(10);

executorService.execute(new Runnable() {
    public void run() {
        System.out.println("Asynchronous task");
    }
});

executorService.shutdown();
```
Primero, se crea un `ExecutorService` usando el método de fábrica `Executors.newFixedThreadPool()`. Esto crea un pool de 10 hilos que ejecutan tareas.

Segundo, se pasa una implementación anónima de la interfaz `Runnable` al método `execute()`. Esto hace que el `Runnable` sea ejecutado por uno de los hilos del `ExecutorService`.

<div align="right">
    <b><a href="#">↥ back to top</a></b>
</div>

## Q. ¿Cuáles son las implementaciones disponibles de ExecutorService en la biblioteca estándar?
La interfaz `ExecutorService` tiene tres implementaciones estándar:

* **ThreadPoolExecutor**: para ejecutar tareas usando un pool de hilos. Una vez que un hilo termina de ejecutar la tarea, vuelve al pool. Si todos los hilos están ocupados, entonces la tarea debe esperar su turno.
* **ScheduledThreadPoolExecutor**: permite programar la ejecución de tareas en lugar de ejecutarlas inmediatamente cuando hay un hilo disponible. También puede programar tareas con tasa fija o con retraso fijo.
* **ForkJoinPool**: es un `ExecutorService` especial para trabajar con tareas de algoritmos recursivos. Si usas un `ThreadPoolExecutor` regular para un algoritmo recursivo, encontrarás que todos los hilos están ocupados esperando que se completen los niveles inferiores de la recursión. `ForkJoinPool` implementa el algoritmo llamado "work-stealing" que permite utilizar los hilos disponibles de manera más eficiente.

## Q. ¿Qué tipo de hilo es el hilo del recolector de basura?
* Hilo daemon

## Q. ¿Cómo podemos pausar la ejecución de un hilo por un tiempo específico?
* **Thread.sleep(...)**: hace que el hilo que se está ejecutando actualmente duerma (detenga su ejecución) durante el número de milisegundos y nanosegundos especificados, sujeto a la precisión y exactitud del sistema. El hilo no pierde la propiedad de ningún monitor.
* **Thread.yield()**: hace que el hilo que se está ejecutando actualmente se pause temporalmente y permita que otros hilos se ejecuten.
* **Monitores**: se llama `wait()` sobre el objeto que se desea bloquear y se libera el bloqueo llamando a `notify()` sobre el mismo objeto.

## Q. ¿Cuál es la diferencia entre los métodos Executor.submit() y Executor.execute()?
**execute()**:

* Recibe un objeto `Runnable` como parámetro.
* Devuelve `void`.
* Útil cuando deseas ejecutar una tarea asincrónica en un pool de hilos pero no te interesa su resultado.
* Ejemplo: Delegar una solicitud (para la que no se necesita respuesta) a otro servicio, enviar un correo.

**submit()**:

* Recibe un objeto `Runnable` o `Callable` como parámetro.
* Devuelve un objeto `Future`.
* Útil cuando el hilo llamador necesita el resultado de la tarea ejecutada. Usando el objeto `Future` se puede obtener el resultado, verificar si la tarea se completó sin errores o cancelar la tarea antes de que finalice.
* Ejemplo: Búsqueda en paralelo usando stream en Java 8.

<div align="right">
    <b><a href="#">↥ back to top</a></b>
</div>

## Q. ¿Qué es Phaser en la concurrencia de Java?
`Phaser` nos permite construir lógica en la que **los hilos deben esperar en una barrera antes de pasar al siguiente paso de ejecución**. `Phaser` es similar a otras utilidades de sincronización como `CountDownLatch` y `CyclicBarrier`.

**CountDownLatch vs CyclicBarrier vs Phaser**

**1. CountDownLatch**:

* Se crea con un número fijo de hilos.
* No puede reiniciarse.
* Permite que los hilos esperen (`CountDownLatch#await()`) o continúen con su ejecución (`CountDownLatch#countDown()`).

**2. CyclicBarrier**:

* Puede reiniciarse.
* No proporciona un método para avanzar manualmente. Los hilos deben esperar hasta que todos lleguen.
* Se crea con un número fijo de hilos.

**3. Phaser**:

* No es necesario conocer el número de hilos al momento de crear el `Phaser`. Pueden añadirse dinámicamente.
* Puede reiniciarse y, por lo tanto, es reutilizable.
* Permite que los hilos esperen (`Phaser#arriveAndAwaitAdvance()`) o continúen con su ejecución (`Phaser#arrive()`).
* Soporta múltiples fases (de ahí su nombre `phaser`).

**PhaserExample.java**  
```java
import java.util.concurrent.Phaser;
 
public class PhaserExample
{
    public static void main(String[] args) throws InterruptedException
    {
          Phaser phaser = new Phaser();
          phaser.register();//register self... phaser waiting for 1 party (thread)
          int phasecount = phaser.getPhase();
          
          System.out.println("Phasecount is "+phasecount);
          new PhaserExample().testPhaser(phaser,2000);//phaser waiting for 2 parties
          new PhaserExample().testPhaser(phaser,4000);//phaser waiting for 3 parties
          new PhaserExample().testPhaser(phaser,6000);//phaser waiting for 4 parties
          //now that all threads are initiated, we will de-register main thread 
          //so that the barrier condition of 3 thread arrival is meet.
          phaser.arriveAndDeregister();
                  Thread.sleep(10000);
                  phasecount = phaser.getPhase();
          System.out.println("Phasecount is "+phasecount);
 
    }
 
    private void testPhaser(final Phaser phaser,final int sleepTime) {
        phaser.register();
        new Thread() {
            @Override
            public void run() {
                        try {
                            System.out.println(Thread.currentThread().getName()+" arrived");
                            phaser.arriveAndAwaitAdvance();//threads register arrival to the phaser.
                            Thread.sleep(sleepTime);
                        } catch (InterruptedException e) {
                            e.printStackTrace();
                        }
                    System.out.println(Thread.currentThread().getName()+" after passing barrier");
                }
            }.start();
    }
}
```
Output
```
Phasecount is 0
Thread-0 arrived
Thread-2 arrived
Thread-1 arrived
Thread-0 after passing barrier
Thread-1 after passing barrier
Thread-2 after passing barrier
Phasecount is 1
```
<div align="right">
    <b><a href="#">↥ back to top</a></b>
</div>

## Q. ¿Cómo detener un Thread en Java?
Un thread se destruye automáticamente cuando el método `run()` ha finalizado. Pero puede ser necesario matar/detener un thread antes de que haya completado su ciclo de vida. Las formas modernas de suspender/detener un thread son mediante el uso de una **bandera booleana** y el método **Thread.interrupt()**.

**Ejemplo: Detener un thread usando una variable booleana**
```java
/**
* Java program to illustrate 
* stopping a thread using boolean flag 
*
**/
class MyThread extends Thread {

    // Initially setting the flag as true 
    private volatile boolean flag = true;
     
    // This method will set flag as false
    public void stopRunning() {
        flag = false;
    }
     
    @Override
    public void run() {
                 
        // This will make thread continue to run until flag becomes false 
        while (flag) {
            System.out.println("I am running....");
        }
        System.out.println("Stopped Running....");
    }
}
 
public class MainClass {

    public static void main(String[] args) {

        MyThread thread = new MyThread();
        thread.start();
         
        try {
            Thread.sleep(100);
        } 
        catch (InterruptedException e) {
            e.printStackTrace();
        }
         
        // call stopRunning() method whenever you want to stop a thread
        thread.stopRunning();
    }   
}
```
Output:
```java
I am running….
I am running….
I am running….
I am running….
I am running….
Stopped Running….
```

**Ejemplo: Detener un thread usando el método interrupt()**
```java
/**
* Java program to illustrate 
* stopping a thread using interrupt() method 
*
**/
class MyThread extends Thread {

    @Override
    public void run() {

        while (!Thread.interrupted()) {
            System.out.println("I am running....");
        }
        System.out.println("Stopped Running.....");
    }
}
 
public class MainClass {

    public static void main(String[] args) {

        MyThread thread = new MyThread(); 
        thread.start();
         
        try {
            Thread.sleep(100);
        } 
        catch (InterruptedException e) {
            e.printStackTrace();
        }

        // interrupting the thread         
        thread.interrupt();
    }   
}
```
Output:
```java
I am running….
I am running….
I am running….
I am running….
I am running….
Stopped Running….
```
<div align="right">
    <b><a href="#">↥ back to top</a></b>
</div>

## Q. ¿Por qué implementar Runnable es mejor que extender Thread?
| Características	       | implements Runnable	    | extends Thread |
|------------------------|--------------------------|----------------|
|Opción de herencia	   |Puede extender cualquier clase | No         |
|Reusabilidad	       |Sí	                       | No             |
|Diseño orientado a objetos|Bueno, permite composición | Malo           |
|Débil acoplamiento	   |Sí 	                       | No             |
|Sobrecarga funcional	   |No	                       | Sí             |

Ejemplo:
```java
/**
* Java program to illustrate defining Thread 
* by extending Thread class 
*
**/
// Here we cant extends any other class 
class ThreadExample extends Thread  
{ 
    public void run() { 
        System.out.println("Run method executed by child Thread"); 
    } 
    public static void main(String[] args) { 
        ThreadExample obj = new ThreadExample(); 
        obj.start(); 
        System.out.println("Main method executed by main thread"); 
    } 
} 
```
Output
```
Main method executed by main thread
Run method executed by child Thread
```
```java
/**
* Java program to illustrate defining Thread 
* by implements Runnable interface 
*
**/
class RunnableExample 
{ 
    public static void m1() { 
        System.out.println("Runnable interface Example"); 
    } 
} 
  
// Here we can extends any other class 
class Test extends RunnableExample implements Runnable 
{ 
    public void run() { 
        System.out.println("Run method executed by child Thread"); 
    } 
    public static void main(String[] args) { 
        Test t = new Test(); 
        t.m1(); 
        Thread t1 = new Thread(t); 
        t1.start(); 
        System.out.println("Main method executed by main thread"); 
    } 
} 
```
Output
```
Runnable interface Example
Main method executed by main thread
Run method executed by child Thread
```
<div align="right">
    <b><a href="#">↥ back to top</a></b>
</div>

## Q. Háblame sobre los métodos join() y wait()
Los métodos `wait()` y `join()` se utilizan para pausar el hilo actual. El método `wait()` se utiliza junto con los métodos `notify()` y `notifyAll()`, mientras que `join()` se utiliza en Java para esperar hasta que un hilo finalice su ejecución.

El método `wait()` se usa principalmente para recursos compartidos; un hilo notifica a otros hilos en espera cuando un recurso queda libre. Por otro lado, `join()` se usa para esperar a que un hilo muera.

## Q. ¿Cómo implementar código seguro para hilos sin usar la palabra clave synchronized?
* **Actualizaciones atómicas**: Técnica en la que se invocan instrucciones atómicas como `compareAndSet` provistas por la CPU.
* **java.util.concurrent.locks.ReentrantLock**: Una implementación de bloqueo que ofrece más flexibilidad que los bloques sincronizados.
* **java.util.concurrent.locks.ReentrantReadWriteLock**: Un bloqueo en el cual las lecturas no bloquean otras lecturas.
* **java.util.concurrent.locks.StampedLock**: Un bloqueo de lectura-escritura no reentrante con la posibilidad de realizar lecturas optimistas.
* **java.lang.ThreadLocal**: No se necesita sincronización si el estado mutable está confinado a un solo hilo. Esto se puede lograr usando variables locales o `ThreadLocal`.

#### Q. ¿Cuál es la diferencia entre ArrayBlockingQueue y LinkedBlockingQueue en la concurrencia de Java?
#### Q. ¿Qué es PriorityBlockingQueue en la concurrencia de Java?
#### Q. ¿Qué es DelayQueue en la concurrencia de Java?
#### Q. ¿Qué es SynchronousQueue en Java?
#### Q. ¿Qué es Exchanger en la concurrencia de Java?
#### Q. ¿Qué es Busy Spinning? ¿Por qué usarías Busy Spinning como estrategia de espera?
#### Q. ¿Qué es Multithreading en Java?

<div align="right">
    <b><a href="#">↥ back to top</a></b>
</div>
