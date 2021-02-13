# KUBERNETES

## 1. Introducción a Kubernetes 
El crecimiento del uso de los contenedores desembocó en aplicaciones compuestas por multitud de éstos. 

Gestionar un alto número de contenedores que interactúan entre sí es prácticamente imposible. Se produjo una alta demanda de aplicaciones que gestionen estos contenedores y detecten los fallos que se producen. A estas aplicaciones se les llama **herramientas de orquestación**.

La más famosa es **Kubernetes**.

Kubernetes nos ofrece:
+ Una alta disponibilidad
+ Alto rendimiento/escalabilidad
+ Funciones de recuperación ante desastres: backups.

<br>

### Arquitectura
Kubernetes está compuesto por Clusters que contienen dentro de sí:

+ Nodo Maestro (al que nos referiremos como Master)
+ Nodo Trabajador (al que nos referiremos como Worker)

Los **WORKER** contienen los distintos contenedores de la aplicación, distribuídos entre sí según lo dictamina el nodo maestro. 

El **MASTER**, corre múltiples procesos de Kubernetes que son fundamentales para el funcionamiento de la aplicación.

+ API SERVER: punto de entrada al cluster. Contiene los programas con los que se comunicará el cliente. 
+ CONTROLLER MANAGER: supervisa el comportamiento del cluster, notifica errores, etc.
+ SCHEDULER: Proceso inteligente que distribuye los contenedores de la aplicación de. 
+ ETCD: contiene el estado de cada uno de los componentes del cluster en todo momento. Cuando se tiene que tirar de un backup, se hace a partir de los snapshots que se han guardado aquí.

Entre el nodo Master y el nodo Worker existe una capa de virtualización que hace de todos los nodos del cluster una máquina virtual unificada (un sistema empotrado).

Se debe tener como mínimo dos nodos maestros.