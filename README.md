# Planificador Horarios en Prolog
Explicación del [planificador de horarios](https://www.metalevel.at/simsttab/) creado por Markus Triska.


# Introducción 

## ¿Qué es Prolog?

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

 coupling (+Class, +Subject, +Times1, +Times2). 
 /* Requisito que impone que una una asignatura (Subject) sea impartida en una clase (Class) durante 2 huecos consecutivos (Times1 y Times2). Ej: coupling ('1a', sjk, 2, 3).*/

 class_freeslot (+Class, +Slot). 
 /* Requisito que impone que una clase (Class) no tenga ninguna asignatura en el hueco Slot de la semana. Ej: class_freeslot ('1a', 0).*/

 room_alloc(+Room, +Class, +Subject, +Slot). 
 /* Requisito que impone que un aula (Room) está ocupada por una clase (Class) a la que se le imparte una asignatura (Subject) y en un determinado hueco (Slot). Ej: room_alloc (r1, '1a', sjk, 0).*/

 teacher_freeday (+Teacher, +Day). 
 /* Requisito que impone el dia de descanso (+Day) de un profesor (Teacher). Ej: teacher_freeday (ume1, 4).*/
````

## Lógica del programa

A continuación  se adjunta el código comentado:

Cabecera del programa: Apartado donde se importan las bibliotecas que se van a utilizar y se pone una breve explicación sobre la utilidad del programa.  

```prolog
/* - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
   Simsttab -- Simplistic school time tabler
   Copyright (C) 2005-2022 Markus Triska triska@metalevel.at
   This program is free software; you can redistribute it and/or modify
   it under the terms of the GNU General Public License as published by
   the Free Software Foundation; either version 2 of the License, or
   (at your option) any later version.
   This program is distributed in the hope that it will be useful,
   but WITHOUT ANY WARRANTY; without even the implied warranty of
   MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
   GNU General Public License for more details.
   You should have received a copy of the GNU General Public License
   along with this program; if not, write to the Free Software
   Foundation, Inc., 59 Temple Place, Suite 330, Boston, MA  02111-1307  USA
   For more information about this program, visit:
          https://www.metalevel.at/simsttab/
          ==================================
   Tested with Scryer Prolog.
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - */


:- use_module(library(clpz)).
:- use_module(library(dcgs)).
:- use_module(library(reif)).
:- use_module(library(pairs)).
:- use_module(library(lists)).
:- use_module(library(format)).
:- use_module(library(pio)).

:- dynamic(class_subject_teacher_times/4).
:- dynamic(coupling/4).
:- dynamic(teacher_freeday/2).
:- dynamic(slots_per_day/1).
:- dynamic(slots_per_week/1).
:- dynamic(class_freeslot/2).
:- dynamic(room_alloc/4).

:- discontiguous(class_subject_teacher_times/4).
:- discontiguous(class_freeslot/2).


/* - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
			 Posting constraints
   The most important data structure in this CSP are pairs of the form
      Req-Vs
   where Req is a term of the form req(C,S,T,N) (see below), and Vs is
   a list of length N. The elements of Vs are finite domain variables
   that denote the *time slots* of the scheduled lessons of Req. We
   call this list of Req-Vs pairs the requirements.
   To break symmetry, the elements of Vs are constrained to be
   strictly ascending (it follows that they are all_different/1).
   Further, the time slots of each teacher are constrained to be
   all_different/1.
   For each requirement, the time slots divided by slots_per_day are
   constrained to be strictly ascending to enforce distinct days,
   except for coupled lessons.
   The time slots of each class, and of lessons occupying the same
   room, are constrained to be all_different/1.
   Labeling is performed on all slot variables.
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - */
```
Predicado *requirements(-Rs)* es cierto cuando Rs unifica con los requisitos del sistema.  

Funciona de la siguiente forma:
Primero unifica la variable *Goal* con los requisitos que se quieren alcanzar. Rs0 unifica con la lista de posibles soluciones que cumplen las restricciones de *Goal*. Esta lista es un conjunto ordenado y sin repeticiones. Por último, se llama a la función *req_with_slots* para cada miembro de la lista de soluciones.  

```prolog
/* 
1º Guarda los requisitos en Goal
2º Guarda en Rs0 la lista de todas las posibles soluciones que cumplan las restricciones no duplicadas y ordenadas
3º Ejecuta req_with_slots en todo Rs0 y en Rs como salida
*/
requirements(Rs) :-
        Goal = class_subject_teacher_times(Class,Subject,Teacher,Number),
        setof(req(Class,Subject,Teacher,Number), Goal, Rs0),
        maplist(req_with_slots, Rs0, Rs).

```

Predicado *req_with_slots(-R, -R-Slots)* es cierto cuando Slots unifica con una lista del tamaño del número total de restricciones.  

Utiliza la capacidad de Prolog de utilizar los predicados de forma reversible para crear una lista de tamaño N.  
Cabe destacar que R-Slot no es una variable, sino dos variables (R y Slots) separadas por un guion.

```prolog
req_with_slots(R, R-Slots) :- 
        R = req(_,_,_,N), length(Slots, N).

```

Predicado *classes(-Classes)* es cierto cuando Classes unifica con el conjunto de las clases disponibles.
Predicado *teachers(-Teachers)* es cierto cuando Classes unifica con el conjunto de las clases disponibles.
Predicado *rooms(-Rooms)* es cierto cuando Classes unifica con el conjunto de las clases disponibles.  

Estos tres predicados sirven para obtener una lista de todas las variables del problema.  

```prolog
classes(Classes) :-
        setof(C, S^N^T^class_subject_teacher_times(C,S,T,N), Classes).

teachers(Teachers) :-
        setof(T, C^S^N^class_subject_teacher_times(C,S,T,N), Teachers).

rooms(Rooms) :-
        findall(Room, room_alloc(Room,_C,_S,_Slot), Rooms0),
        sort(Rooms0, Rooms).

```

Predicado *requirements_variables(Rs,Vars)* es cierto cuando Vars unifica con una lista de Posibles soluciones.  
Para ello:
Primero utiliza el predicado *requirements(Rs)* para conseguir las posibles soluciones que cumplen los requisitos seguida de una lista vacía de tamaño N.  

Con el predicado *pairs_slots(Rs,Vars)* unifica Vars con una lista vacía del par Requisito-Solucion. La variable SPM unifica con los huecos que hay por cada semana y 
unifica la variable Max con el número de huecos por semana menos 1.  

Establece el dominio de las variables (0..Max). Cada valor del dominio representará cada uno de los días de la semana:  
0 - Lunes  
1 - Martes  
2 - Miércoles  
3 - Jueves  
4 - Viernes  

A continuación, establece las restricciones de las asignaturas, clases, profesores y aulas.  

```prolog
/*
1º Consigue las posibles soluciones en Rs que cumplen los requisitos seguido de una lista vacía de las soluciones
2º Guarda en Vars la lista vacía del par Requisito-Solucion
3º Guarda en SPM huecos por semana
4º Instancia en Max el numero de huecos semanales - 1
5º Establece el dominio máximo de las variables
6º Aplica las restricciones a las asignaturas sucesivas (continuas)
7º Instancia las clases
8º Instancia los profesores
9º Instancia las aulas
10º Aplica las restricciones a los profesores según las restricciones de asignaturas
11º Aplica las restricciones a los clases según las restricciones de asignaturas
12º Aplica las restricciones a los aulas según las restricciones de asignaturas
*/
requirements_variables(Rs, Vars) :-
        requirements(Rs),
        pairs_slots(Rs, Vars),
        slots_per_week(SPW),
        Max #= SPW - 1,
        Vars ins 0..Max,
        maplist(constrain_subject, Rs),
        classes(Classes),
        teachers(Teachers),
        rooms(Rooms),
        maplist(constrain_teacher(Rs), Teachers),
        maplist(constrain_class(Rs), Classes),
        maplist(constrain_room(Rs), Rooms).

```
El predicado *slot_quotient(+S,-Q)* es cierto cuando Q unifica con el cociente entre el número de Slots totales y el número de Slots por día. 

```prolog

/*
1º Instancia en SPD los huecos por día
2º Establece en Q el cociente en fracción de lo que ocupa del total por día
*/
slot_quotient(S, Q) :-
        slots_per_day(SPD),
        Q #= S // SPD.

```
El predicado *list_without_nths(Es0,Ws,Es)* es cierto cuando *Es* unifica con los valores de la lista *Es0* sin los valores que ocupan los índices indicados en *Ws*.

```prolog
list_without_nths(Es0, Ws, Es) :-
        phrase(without_(Ws, 0, Es0), Es).
without_([], _, Es) --> seq(Es).
without_([W|Ws], Pos0, [E|Es]) -->
        { Pos #= Pos0 + 1,
          zcompare(R, W, Pos0) },
        without_at_pos0(R, E, [W|Ws], Ws1),
        without_(Ws1, Pos, Es).
without_at_pos0(=, _, [_|Ws], Ws) --> [].
without_at_pos0(>, E, Ws0, Ws0) --> [E].
```

Aquí se ofrece un ejemplo del predicado anterior.

```prolog
%:- list_without_nths([a,b,c,d], [3], [a,b,c]).
%:- list_without_nths([a,b,c,d], [1,2], [a,d]).
```

El predicado *slots_couplings(Slots,F-S)* se encarga de que dos asignaturas se impartan de forma consecutiva.  

```prolog
/*
1º En S1 se establece el índice de la posicion que ocupa en Slots F
2º En S2 se establece el índice de la posicion que ocupa en Slots S
3º Establece que S2 será S1 + 1 para indicar que son 2 clases seguidas
*/
slots_couplings(Slots, F-S) :-
        nth0(F, Slots, S1),
        nth0(S, Slots, S2),
        S2 #= S1 + 1.
```

```prolog
/*
Predicado que será cierto si Slots unifica con las asignaturas que cumplen las restricciones ???????????????????????????
1º Establece el orden creciente estricto de los slots vacios
2º Por cada requisito, saca el coeficiente (en fraccion) de los huecos del requisito
3º Guarda en Cs todas las clases que son sucesivas de este requisito (clase y asignatura)
4º Comprueba que se cumpla que las clases sean sucesivas las de este requisito (clase y asignatura)
5º Elimina de la Hash table Cs los Keys para quedarse en Seconds0 solo con los values
6º Ordena Seconds0 ascendentemente en Seconds
7º Elimina de la lista Qs0 las posiciones Seconds en Qs (clases siguientes sucesivas)
8º Establece el orden creciente estricto de Qs (clases siguientes sucesivas)
*/
constrain_subject(req(Class, Subj, _Teacher, _Num)-Slots) :-
        strictly_ascending(Slots), % break symmetry
        maplist(slot_quotient, Slots, Qs0),
        findall(F-S, coupling(Class,Subj,F,S), Cs),
        maplist(slots_couplings(Slots), Cs),
        pairs_values(Cs, Seconds0),
        sort(Seconds0, Seconds),
        list_without_nths(Qs0, Seconds, Qs),
        strictly_ascending(Qs).
```
El predicado *all_diff_from(Vs,F)* es cierto cuando los valores de F son distintos de todos los valores de Vs.

```prolog

all_diff_from(Vs, F) :- 
        maplist(#\=(F), Vs).

```
El predicado *constrain_class(Rs,Class)* es cierto cuando Class unifica con una lista de clases que cumplen las restricciones dadas por Rs.
El predicado *constrain_teacher(Rs,Teachers)* es cierto cuando Teachers unifica con una lista de profesores que cumplen las restricciones de Rs.
```prolog
/*
1º Filtra según los requisitos los huecos que tiene cada clase para cada asignatura
2º Guarda en Vs la lista vacía del par Clase-Asignatura 
3º Restringe para sean únicos los valores de asignaturas de Vs
4º Encuentra los huecos que haya para la clase teniendo en cuenta las horas que la clase no
curse asignaturas
5º Se comprueba que los valores de Vs son todos distintos de Frees
*/
constrain_class(Rs, Class) :-
        tfilter(class_req(Class), Rs, Sub),
        pairs_slots(Sub, Vs),
        all_different(Vs),
        findall(S, class_freeslot(Class,S), Frees),
        maplist(all_diff_from(Vs), Frees).

/*
1º Filtra según los requisitos los huecos que tiene cada profesor para cada asignatura
2º Guarda en Vs la lista vacía del par Profesor-Asignatura
3º Restringe para sean únicos los valores de asignaturas de Vs
4º Encuentra los huecos que haya para el profesor teniendo en cuenta los días que no trabaja
5º Por cada asignatura, saca el coeficiente (en fraccion) de los huecos de la asignatura en Qs
6º Se comprueba que los valores de Qs son todos distintos de Fs
*/
constrain_teacher(Rs, Teacher) :-
        tfilter(teacher_req(Teacher), Rs, Sub),
        pairs_slots(Sub, Vs),
        all_different(Vs),
        findall(F, teacher_freeday(Teacher, F), Fs),
        maplist(slot_quotient, Vs, Qs),
        maplist(all_diff_from(Qs), Fs).

```



```prolog
/*
1º Comprueba que el par requisito-slot pertenece a la lista de requisitos Reqs
2º En Var se establece el índice de la posicion que ocupa en Slots Lesson
*/
sameroom_var(Reqs, r(Class,Subject,Lesson), Var) :-
        memberchk(req(Class,Subject,_Teacher,_Num)-Slots, Reqs),
        nth0(Lesson, Slots, Var).
```

El predicado *constrain_room(Reqs,Room)* es cierto cuando Room unifica con una lista de aulas que cumplen los requisitos dados por Reqs.
```prolog
/*
1º Busca las habitaciones que cumplan las restricciones RReqs
2º Unifica cuando las habitaciones distintas cumplen los requisitos
3º Restringe para sean únicos los valores de aulas de Roomvars
*/
constrain_room(Reqs, Room) :-
        findall(r(Class,Subj,Less), room_alloc(Room,Class,Subj,Less), RReqs),
        maplist(sameroom_var(Reqs), RReqs, Roomvars),
        all_different(Roomvars).

```


```prolog
/*
#< es una lista de variables de dominio finito que son 
una cadena con respecto al orden parcial Ls,
en el orden en que aparecen en la lista.
*/
strictly_ascending(Ls) :- 
        chain(#<, Ls).

```
El predicado *class_req(C0,req(C1,_S,_T,_N)-_, T)* es cierto cuando C0, C1 y T unifican entre sí.
```prolog

class_req(C0, req(C1,_S,_T,_N)-_, T) :- 
        =(C0, C1, T).
```
El predicado *teacher_req(T0, req(_C,_S,T1,_N)-_, T)* es cierto cuando T0, T1 y T unifican entre sí.
```prolog
teacher_req(T0, req(_C,_S,T1,_N)-_, T) :- 
        =(T0,T1,T).

```

```prolog
/*
1º Elimina de la Hash table Ps los Keys para quedarse en Vs0 solo con los values
2º Une la lista de values de Vs0 a la lista de Vs
*/
pairs_slots(Ps, Vs) :-
        pairs_values(Ps, Vs0),
        append(Vs0, Vs).


/* - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
   Relate teachers and classes to list of days.
   Each day is a list of subjects (for classes), and a list of
   class/subject terms (for teachers). The predicate days_variables/2
   yields a list of days with the right dimensions, where each element
   is a free variable.
   We use the atom 'free' to denote a free slot, and the compound terms
   class_subject(C, S) and subject(S) to denote classes/subjects.
   This clean symbolic distinction is used to support subjects
   that are called 'free', and to improve generality and efficiency.
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - */


```
A partir de este extracto, se presenta el formato final y su representación en consola, cosas que no nos ha sido posible entender.
```prolog
/*
1º Instancia en SPW los huecos por semana
2º Instancia en SPD los huecos por día
3º Instancia en NumDays la division entera de huecos por semana 
entre huecos por día para obtener los días totales
4º Instancia una lista Days de NumDays elementos
5º Instancia una lista Day de SPD elementos
6º Toma los huecos de un día y los replica como días haya en la semana
7º Une a Vs la lista Days
*/
days_variables(Days, Vs) :-
        slots_per_week(SPW),
        slots_per_day(SPD),
        NumDays #= SPW // SPD,
        length(Days, NumDays),
        length(Day, SPD),
        maplist(same_length(Day), Days),
        append(Days, Vs).


class_days(Rs, Class, Days) :-
        days_variables(Days, Vs),
        tfilter(class_req(Class), Rs, Sub),
        foldl(v(Sub), Vs, 0, _).

v(Rs, V, N0, N) :-
        (   member(req(_,Subject,_,_)-Times, Rs),
            member(N0, Times) -> V = subject(Subject)
        ;   V = free
        ),
        N #= N0 + 1.

teacher_days(Rs, Teacher, Days) :-
        days_variables(Days, Vs),
        tfilter(teacher_req(Teacher), Rs, Sub),
        foldl(v_teacher(Sub), Vs, 0, _).

v_teacher(Rs, V, N0, N) :-
        (   member(req(C,Subj,_,_)-Times, Rs),
            member(N0, Times) -> V = class_subject(C, Subj)
        ;   V = free
        ),
        N #= N0 + 1.

/* - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
   Print objects in roster.
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - */

print_classes(Rs) :-
        classes(Cs),
        phrase_to_stream(format_classes(Cs, Rs), user_output).

format_classes([], _) --> [].
format_classes([Class|Classes], Rs) -->
        { class_days(Rs, Class, Days0),
          transpose(Days0, Days) },
        format_("Class: ~w~2n", [Class]),
        weekdays_header,
        align_rows(Days),
        format_classes(Classes, Rs).

align_rows([]) --> "\n\n\n".
align_rows([R|Rs]) -->
        align_row(R),
        "\n",
        align_rows(Rs).

align_row([]) --> [].
align_row([R|Rs]) -->
        align_(R),
        align_row(Rs).

align_(free)               --> align_(verbatim('')).
align_(class_subject(C,S)) --> align_(verbatim(C/S)).
align_(subject(S))         --> align_(verbatim(S)).
align_(verbatim(Element))  --> format_("~t~w~t~8+", [Element]).

print_teachers(Rs) :-
        teachers(Ts),
        phrase_to_stream(format_teachers(Ts, Rs), user_output).

format_teachers([], _) --> [].
format_teachers([T|Ts], Rs) -->
        { teacher_days(Rs, T, Days0),
          transpose(Days0, Days) },
        format_("Teacher: ~w~2n", [T]),
        weekdays_header,
        align_rows(Days),
        format_teachers(Ts, Rs).

weekdays_header -->
        { maplist(with_verbatim,
                  ['Mon','Tue','Wed','Thu','Fri'],
                  Vs) },
        align_row(Vs),
        format_("~n~`=t~40|~n", []).

with_verbatim(T, verbatim(T)).

/* - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
   ?- consult('reqs_example.pl'),
      requirements_variables(Rs, Vs),
      labeling([ff], Vs),
      print_classes(Rs).
   %@ Class: 1a
   %@
   %@   Mon     Tue     Wed     Thu     Fri
   %@ ========================================
   %@   mat     mat     mat     mat     mat
   %@   eng     eng     eng
   %@    h       h
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - */
```

# Conclusiones

Prolog es un lenguaje muy potente para hacer programación con restricciones ya que, con pocas líneas de código, podemos generar programas complejos que en otros lenguajes de programación ocuparían más código. En este documento hemos visto como funciona el planificador de horarios creado por Markus Triska en Prolog. Para ello hemos desgranado el código que está publicado en su página web línea por línea.



Finalmente, hemos podido comprender, de forma general, como utilizar el código del planificador de horarios de Markus Triska para ajustarlo a ciertas restricciones que nosotros decidamos. Sin embargo, la parte del formato y de como transformar el programa en una aplicación web no hemos podido entenderla completamente.


# Referencias
[1] The Power Of Prolog. Markus Triska: <https://www.metalevel.at/prolog>  
[2] The Power of Prolog Introduction. Markus Triska: <https://www.metalevel.at/prolog/introduction>  
[3] Librería reif. Ulrich Newmerkel: <https://github.com/meditans/reif>  
[4] The Power Of Prolog. CLPZ, Markus Triska: <https://www.metalevel.at/prolog/clpz>  
[5] The Power Of Prolog. Simsttab, Markus Triska: <https://www.metalevel.at/simsttab/>  
