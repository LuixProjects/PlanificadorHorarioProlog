# PlanificadorHorarioProlog
Explicación del planificador horario creado por Markus Triska (https://www.metalevel.at/simsttab/).


## Introducción, ¿Qué es Prolog?.

Prolog, por sus siglas (Programmation en Logique) es un lenguaje de programación declarativo usado principalmente en el campo de la inteligencia artificial, fué creado a principio de los años 70 en la Universidad de Aix-Marseille I (Francia) por un profesor y un alumno de doctorado. Se empezó a popularizar en la década de los 80 cuando aparecieron los primeros intérpretes para micro-ordenadores de 8 bits y para ordenadores domésticos de 16 bits, junto la adopción del mismo para el desarrollo del proyecto de la quinta generación de computadoras (Proyecto del gobierno de Japón orientado a crear clases de computadores que utilizarían técnicas y tecnologías de inteligencia artificial punteras en ese momento tanto en hardware como en software). En la actualidad, el lenguaje Prolog es usado para introducir la programación logica/declarativa en entornos académicos.

## Programación Imperativa vs Programación Declarativa

El gran grueso de lenguajes de programación existentes usan una lógica de programación imperativa, la cual consiste en ejecutar una secuencia claramente definida de instrucciones ordenadas, permitiendo saltos, de un conjunto de instrucciones de un ordenador, de esta forma, se le permite al programador un control total del sistema permitiendo estructuras como bucles o estructuras anidadas de código.
Frente a estos lenguajes de programación se encuentran los lenguajes de programación declarativa (Como el propio Prolog, Scala, SQL ...) que están basados en describir el problema declarando propiedades o reglas que deben cumplirse, lo cual permite al programador ahorrarse la pregunta de como realizar dicha acción.
Por poner un ejemplo de como funciona un lenguaje declarativo, Prolog, permite la resolución de problemas usando las Claúsulas de Horn, las cuales permiten realizar siguiendo la regla de "Si X es un antecedente, entonces es verdad el consecuente", siguiendo esta regla ( con la única variación de que la forma de escribir estas clausulas en Prolog es primero el consecuente y luego el antecende) Prolog ejecuta su algoritmo de backtracking y permite unificar las soluciones con dos posibles salidas, true si la ejecución termina en éxito o false, si termina en fracaso.
Gracias a este método de resolución se nos permite el uso de predicados reversibles, lo cual nos permite intercambiar los roles de entrada o salida de las variables pasadas por parametros a los predicados, permitiendo, con un solo predicado, poder extraer diferente información según lo que desee el programador.

## Programación Declarativa con Restricciones ( CLP(Z) )
## Aplicación de la programación declarativa a un planificador de horarios.

# Código del Planificador de Horarios
## Lógica del programa

# Conclusiones

# Referencias
