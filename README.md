# Planificador Horarios en Prolog
Explicación del planificador horario creado por Markus Triska (https://www.metalevel.at/simsttab/).


# Introducción 

## ¿Qué es Prolog?.

Prolog, por sus siglas (Programmation en Logique) es un lenguaje de programación declarativo usado principalmente en el campo de la inteligencia artificial, fué creado a principio de los años 70 en la Universidad de Aix-Marseille I (Francia) por un profesor y un alumno de doctorado. Se empezó a popularizar en la década de los 80 cuando aparecieron los primeros intérpretes para micro-ordenadores de 8 bits y para ordenadores domésticos de 16 bits, junto la adopción del mismo para el desarrollo del proyecto de la quinta generación de computadoras (Proyecto del gobierno de Japón orientado a crear clases de computadores que utilizarían técnicas y tecnologías de inteligencia artificial punteras en ese momento tanto en hardware como en software). En la actualidad, el lenguaje Prolog es usado para introducir la programación logica/declarativa en entornos académicos.  

Prolog permite resolver muchas tareas con programas cortos y elegantes. Los programas en Prolog consisten en predicados. Cada uno de estos predicados define una relación entre sus argumentos. Los predicados de Prolog son más versátiles que los de cualquier otro lenguaje de programación, ya que permiten utilizarlos de manera reversible [[2]](https://www.metalevel.at/prolog/introduction).  


## Programación Imperativa vs Programación Declarativa

El gran grueso de lenguajes de programación existentes usan una lógica de programación imperativa, la cual consiste en ejecutar una secuencia claramente definida de instrucciones ordenadas, permitiendo saltos, de un conjunto de instrucciones de un ordenador, de esta forma, se le permite al programador un control total del sistema permitiendo estructuras como bucles o estructuras anidadas de código.  

Frente a estos lenguajes de programación se encuentran los lenguajes de programación declarativa (Como el propio Prolog, Scala, SQL ...) que están basados en describir el problema declarando propiedades o reglas que deben cumplirse, lo cual permite al programador ahorrarse la pregunta de como realizar dicha acción. Estos lenguajes pueden ser funcionales (Haskell) o lógicos (Prolog).  
Por poner un ejemplo de como funciona un lenguaje declarativo, Prolog, permite la resolución de problemas usando las Claúsulas de Horn, las cuales permiten realizar siguiendo la regla de "Si X es un antecedente, entonces es verdad el consecuente", siguiendo esta regla ( con la única variación de que la forma de escribir estas clausulas en Prolog es primero el consecuente y luego el antecende) Prolog ejecuta su algoritmo de backtracking y permite unificar las soluciones con dos posibles salidas, true si la ejecución termina en éxito o false, si termina en fracaso.  

Gracias a este método de resolución se nos permite el uso de predicados reversibles, lo cual nos permite intercambiar los roles de entrada o salida de las variables pasadas por parametros a los predicados, permitiendo, con un solo predicado, poder extraer diferente información según lo que desee el programador.

## Programación Declarativa con Restricciones ( CLP(Z) )  

Para apreciar la aritmética declarativa en Prolog, primero debemos considerar la aritmética sobre los números naturales. Estos están definidos mediante los axiomas de Peano. Sin embargo, existen diversos problemas con la representación de los números que nos ofrecen dichos axiomas. Por ello, en Prolog se utilizan restricciones de dominio finito (CLP(FD) o CLP(Z)). En Prolog, todos los predicados se consideran restricciones. Los más importantes se denominan restricciones aritméticas.  

También podemos restringir el dominio de las variables utilizando el predicado *in*. Además, es posible combinar diferentes restricciones utilizando la propagación de restricciones [[4]](https://www.metalevel.at/prolog/clpz).



## Aplicación de la programación declarativa a un planificador de horarios.

Un horario es el ejemplo perfecto para trabajar con programación con restricciones.  
Los hechos de prolog (*facts*) se pueden utilizar para especificar los requisitos del horario. De esta forma, la entrada del programa será una lista de hechos que describirán qué restricciones queremos que cumpla nuestro horario [[5]](https://www.metalevel.at/simsttab/).

# Código del Planificador de Horarios
## Documento de Requisitos

Para el funcionamiento del código principal de este planificador, necesitamos un documento el cual nos defina los requisitos para determinar una clasificación posible para las distintas restricciones de horas entre clases, profesores y asignaturas impartidas por los profesores.
Vamos a ver las estructuras de informacion que tenemos dentro de este documento:
````prolog
 slots_per_week (+N). /* Requisito que impone que haya N huecos en una misma semana. Ej: slots_per_week (35).*/
 slots_per_day (+N).  /* Requisito que impone que haya N huecos en un mismo día. Ej: slots_per_day (7).*/

 class_subject_teacher_times (+Class, +Subject, +Teacher, +Times).
 /* Requisito que impone cuantos huecos de la semana (Times) da cada profesor (Teacher) de cada asignatura (Subject) en cada clase (Class). Ej: class_subject_teacher_times ('1a', sjk, sjk1, 4).*/

 coupling (+Class, +Subject, +Times1, +Times2). /* Requisito que impone que una una asignatura (Subject) sea impartida en una clase (Class) durante 2 huecos consecutivos (Times1 y Times2). Ej: coupling ('1a', sjk, 2, 3).*/

 class_freeslot (+Class, +Slot). /* Requisito que impone que una clase (Class) no tenga ninguna asignatura en el hueco Slot de la semana. Ej: class_freeslot ('1a', 0).*/

 /* Revisar esta de room_alloc, porque no se si está bien del todo*/
 room_alloc(+Room, +Class, +Subject, +Slot). /* Requisito que impone que un aula (Room) está ocupada por una clase (Class) a la que se le imparte una asignatura (Subject) y en un determinado hueco (Slot). Ej: room_alloc (r1, '1a', sjk, 0).*/

 teacher_freeday (+Teacher, +Day). /* Requisito que impone el dia de descanso (+Day) de un profesor (Teacher). Ej: teacher_freeday (ume1, 4).*/
````

## Lógica del programa

# Conclusiones

Prolog es un lenguaje muy potente para hacer programación con restricciones ya que, con pocas líneas de código, podemos generar programas complejos que en otros lenguajes de programación ocuparían más código. En este documento hemos visto como funciona el planificador de horarios creado por Markus Triska en Prolog. Para ello hemos desgranado el código que está publicado en su página web línea por línea.



Finalmente, hemos podido comprender, de forma general, como utilizar el código del planificador de horarios de Markus Triska para ajustarlo a ciertas restricciones que nosotros decidamos. Sin embargo, la parte del formato y de como transformar el programa en una aplicación web no hemos podido entenderla completamente.


# Referencias
[1] The Power Of Prolog. Markus Triska: <https://www.metalevel.at/prolog>  
[2] The Power of Prolog Introduction. Markus Triska: <https://www.metalevel.at/prolog/introduction>  
[3] Librería reif. Ulrich Newmerkel: <https://github.com/meditans/reif>  
[4] The Power Of Prolog. CLPZ, Markus Triska: <https://www.metalevel.at/prolog/clpz>  
[5] The Power Of Prolog. Simsttab, Markus Triska: <https://www.metalevel.at/simsttab/>  
