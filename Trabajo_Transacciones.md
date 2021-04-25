# Transacciones
En innoDB podemos trabajar con transacciones, esto es una forma de agrupar y asegurar las operaciones. 

Sucede gracias a que, al **iniciar** una transacción, se crea un área de trabajo segura que nos ofrece:

    1 - Deshacer operaciones con rollback.
    
    2 - Seguridad, ya que si algo falla y la operación se queda a medias, no sucede nada puesto que la 
    peración/es deben terminar para finalizar la transacción.

    3 - Por contra puede llegar generar tráfico.

## Sintaxis

Antes de nada, habría que valorar si queremos tener activada la autoconfirmación o no, esto añade una capa más de seguridad a las transacciones si lo deshabilitamos, forzando a realizar el commit por nosotros mismos.


El comando para hacerlo es: 

    - Deshabilitar --> **set autocommit=0** / **set autocommit=OFF**

    - Habilitar --> **set autocommit=1** / **set autocommit=ON**

Toda transacción se empieza con: **START TRANSACTION** / **BEGIN** / **BEGIN WORK**

Seguido de la consulta a realizar, ya sea un **SELECT**, un **INSERT**, un **ALTER**, etc.

Si queremos volver a un estado anterior a esta transacción, hacemos un **ROLLBACK**.

y para acabar debemos usar un **COMMIT** si creemos que todo ha ido correctamente. 


Para volver a un estado anterior definido por nosotros, primero debe existir ese estado anterior, así que debemos realizar un **SAVEPOINT**, esto es como una copia de seguridad y se utiliza dentro de una transacción. La sintaxis de este comando es: **SAVEPOINT NOMBREDELSAVEPOINT**. Ahora podemos usar **ROLLBACK TO NOMBREDELSAVEPOINT**. Podemos eliminar este SAVEPOINT con **RELEASE SAVEPOINT NOMBREDELSAVEPOINT**

### *Problemas a tener en cuenta*

#### Acceso concurrente
Esto sucede cuando dos transacciones intenta realizar acceder a los mismos datos y se divide en 3 tipos:

    - **Lectura sucia ó Dirty Read**. Cuando otra transacción intenta leer datos que están siendo modificados por una primera transacción ANTES de realizar un *COMMIT*
    - **Lectura no repetible ó Nonrepeatable Read**. Cuando una transacción lee datos que ya leyó anteriormente y que han sido modificados.
    - **Lectura fantasma ó Phantom Read**. Cuando una transacción lee datos que no estaban al iniciar la transacción.

Para evitar estas situaciones, se establecen **Niveles de Aislamiento**:

Para consultar el nivel de aislamiento actual;
    Podemos usar: 
    
    SELECT @@global.tx_isolation; 
    SELECT @@tx_isolation;

    - *Read Uncommited:* SIN BLOQUEOS, NO RECOMENDABLE.
    - *Read Commited:* Los datos pueden ser modificados por varias transacciones, por lo que se pueden dar los problemas de lectura no repetible y lectura fantasma. 
    - *Repeatable Read:* Solo puede ocasionarse la Lectura fantasma, ya que no puede modificarse ningún registro visualizado con un SELECT en otra transacción
    - *Serializable:* Reduce la posibilidad de redundancia creando una cola de transacciones. Se ejecutarán una detrás de otra.

# Tipos de Bloqueo
### Bloqueo por filas
    - Compartido - Esto permite leer una fila.
    - Exclusivo - Permite actualizar o eliminar una fila.

### Bloqueo de granularidad múltiple
Permite bloqueos tanto en registros como en tablas enteras.
Necesita Bloqueos de intención para que sea efectivo ya que estos permiten bloquear por tabla parecida a los bloqueos por fila.
    - Intención compartido
    - Intención exclusiva

