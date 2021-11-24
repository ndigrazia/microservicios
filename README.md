- [Micro Servicios - Prácticas y Lineamientos](#micro-servicios---prácticas-y-lineamientos)
  - [Objetivo](#objetivo)
  - [Conjunto de lineamientos](#conjunto-de-lineamientos)
    - [Reporte de estado de salud](#reporte-de-estado-de-salud)
    - [Configuración elástica](#configuración-elástica)
    - [Reporte de métricas o telemetrías](#reporte-de-métricas-o-telemetrías)
    - [Trazabilidad distribuida](#trazabilidad-distribuida)
    - [Registro de eventos](#registro-de-eventos)
    - [Mecanismo de comunicación](#mecanismo-de-comunicación)
    - [Tolerancia a fallas](#tolerancia-a-fallas)
    - [Altamente cohesivos](#altamente-cohesivos)
    - [Mínimamente acoplados](#mínimamente-acoplados)
    - [Empaquetado como imagen de contenedor](#empaquetado-como-imagen-de-contenedor)
    - [Plataforma centrada en contenedores](#plataforma-centrada-en-contenedores)
    - [Despliegue mediante pipeline](#despliegue-mediante-pipeline)

# Micro Servicios - Prácticas y Lineamientos

## Objetivo

El objetivo del presente documento es que los usuarios/desarrolladores conozcan las prácticas y  los lineamientos sobre micro servicios para que sean tenidos en cuenta durante el desarrollo de los mismos.

## Conjunto de lineamientos

### Reporte de estado de salud

|         |  | 
| :---          |:----        | 
| Declaración   |Reportar el estado de salud del micro servicio para tomar acciones apropiadas basado este reporte.       | 
| Racional      |Los micro servicios deberán reportar y/o publicar su estado de salud para indicar si están o no disponibles. Luego, la plataforma de orquestación, usando la información de salud indicada, puede decidir si es necesario que sea finalizado y/o reemplazado por otra instancia tendiendo a mantener un ambiente estable y operativo.|
| Implicaciones | - Uso de frameworks o de librerias para implementar el reporte de estado de salud. <br>- Desarrollo y publicación de la prueba de vida (Liveness) y la prueba de aceptación de pedidos (Readiness) en el micro servicio.<br> - Empleo de una plataforma de orquestación que tome decisiones en función del estado de salud del micro servicio.<br>|

### Configuración elástica

|         |  | 
| :---          |:----        | 
| Declaración   |Ofrecer mecanismos para poder modificar los datos de configuración sin necesidad de construir o empaquetar nuevamente la imagen del micro servicio.  | 
| Racional      |Cuando desarrollamos un micro servicio es una buena práctica externalizar los aspectos de la configuración para que podamos cambiar su comportamiento en tiempo de ejecución sin la necesidad de cambiar el código o empaquetarlo nuevamente. La información de configuración en el ambiente productivo generalmente se desconoce en el momento del desarrollo, solo en tiempo de ejecución podemos aplicar la información correcta para configurar nuestro micro servicio. Para abordar la necesidad de aplicar y modificar dinámicamente las configuraciones en una implementación de micro servicios, se necesita un mecanismo estándar capaz de permitir a los micro servicios modificar su comportamiento sin necesidad de ser desarrollados nuevamente.|
| Implicaciones | - Uso de frameworks o de librerias para implementar configuraciones externalizadas.<br>|

### Reporte de métricas o telemetrías

|             |  | 
| :---          |:----        | 
| Declaración   |Exponer métricas o telemetrías que ayuden a administrar el micro servicio, permitiendo detectar si el mismo se vuelve inestable en tiempo de ejecución.  | 
| Racional      |Saber cómo están funcionando las instancias del micro servicio es crucial para garantizar el éxito. En producción, poder conocer la memoria del contenedor, el consumo de disco, el consumo de red, la JVM que están usando las instancias, cuántas veces se llama a un servicio, cuánto tiempo tardó en ejecutarse y varias otras métricas, ayudan al equipo de operaciones a administrar los servicios antes de que se vuelvan inestables. Con este objetivo, los micro servicios deberán proporcionar en forma estándar sus métricas centrales, las cuales incluyen: Used Heap Memory, Max Heap Memory, Garbage Collection Count, entre otras. Como así también, las métricas del vendedor o de la aplicación que se consideren importante para el dominio problema.|
| Implicaciones | Uso de alguna de las siguientes estrategias:<br><br>- Empleo de un service mesh: Una malla de servicio puede recopilar datos de telemetría de toda la red de micro servicios y producir métricas consistentes en cada elemento de la red. La malla permite generar métricas del consumo de los recursos de los micro servicios. Finalmente, los micro servicios solo deben enfocarse en brindar las métricas del vendedor y/o de la aplicación que se consideren mediante el uso de librerias.<br>- Utilización de librerias o de frameworks: El micro servicio debe implementar funcionalidades para la recopilación de métricas centrales (tienen que ver con el estado de los recursos usados por el micro servicio), métricas específicas del vendedor y métricas específicas de la aplicación (tienen que ver con indicadores propios del domino del negocio) mediante el uso de librerias.<br><br>En ambos casos se necesita un sistema de monitoreo donde se puedan analizar las métricas informadas.|

### Trazabilidad distribuida

|         |  | 
| :---          |:----        | 
| Declaración   |Ofrecer mecanismos para poder rastrear el flujo de un pedido a través del sistema distribuido con el objetivo de diagnosticar problemas o detectar anomalías.  | 
| Racional      |Conocer el camino que sigue una petición desde su inicio hasta su final puede ayudar en la resolución de problemas. El rastreo distribuido muestra la ruta que sigue una solicitud a medida que viaja a través de un sistema distribuido. A medida que las solicitudes viajan entre los servicios, cada segmento se registra. Todos los tramos de una solicitud se combinan en un solo rastreo distribuido para dar una imagen de una solicitud completa. Una vez capturados los rastros de una petición, se puede proceder a analizarlos y resolver inconvenientes de una forma más fácil y rápida.|
| Implicaciones |Uso de alguna de las siguientes estrategias:<br><br>- Uso de un service mesh para recopilar la traza de los pedidos, mediante la propagación del contexto de traza (Trace Context Propagation).<br>- Publicación de la traza mediante el uso de librerias o bibliotecas externas.<br><br>En ambos casos se necesita un sistema de monitoreo donde se puedan analizar las trazas informadas.|

### Registro de eventos

|         |  | 
| :---          |:----        | 
| Declaración   |Ofrecer mecanismos para registrar o mostrar mensajes con información de lo que está sucediendo en el micro servicio.| 
| Racional      |El registro de mensajes con información de lo que sucede dentro de un micro servicio es comúnmente conocido como log, y es de gran utilidad para los administradores/desarrolladores y en especial para el equipo de soporte. Los mensajes con información ahorran muchas horas valiosas tanto para el equipo de soporte como para los desarrolladores en el diagnostico como en la resolución de problemas. |
| Implicaciones |- Uso de librerias o de bibliotecas externas para implementar el registro de mensajes.<br>- Publicación de mensajes en la salida estándar (stdout) con diferentes niveles de severidad.<br>- Configuración de la estrategia de registro de eventos, sin necesidad de cambiar el código o construir nuevamente la imagen del micro servicio (Configuración elástica).|

### Mecanismo de comunicación

|         |  | 
| :---          |:----        | 
| Declaración   |Emplear mecanismos estándares de comunicación entre los micro servicios.| 
| Racional      |El micro servicio por sí solo no tiene sentido, es necesario que interactúe con otros para resolver un pedido o una necesidad. Cada micro servicio es capaz de recibir mensajes, procesar datos y enviar mensajes hacia otros micro servicios. Para poder establecer la comunicacion, debe existir un estándar de comunicación que defina un conjunto de normas, acuerdos y recomendaciones técnicas que regulan la transmisión de mensajes entre los micro servicios. Comúnmente los mecanismos de comunicación usados entre los micro servicios son: el sincrónico (API REST) y el asincrónico (mensajería ligera AMQP). |
| Implicaciones |- Comunicación sincrónica mediante API REST: Disponer de un artefacto que permita describir su contrato, documentar sus reglas y sus especificaciones.<br>- Comunicación mediante mensajería ligera: Acordar la estructura del mensaje que es enviado del emisor al receptor y, acordar la instancia de cola de mensajes a utilizar.

### Tolerancia a fallas

|         |  | 
| :---          |:----        | 
| Declaración   |Ofrecer estrategias de tolerancia a fallas.| 
| Racional      |Es sumamente importante, para aquellos micro servicios que utilizan comunicación sincrónica, construir servicios con tolerancia a fallas. La tolerancia a fallas trata de aprovechar diferentes estrategias para guiar la ejecución y el resultado de cierta lógica. Las políticas de reintento, los Fallbacks y los CircuitBreaker son conceptos de esta área que establecen la lógica de toleracia a falla que debe llevarse a cabo y el momento en ser ejecutada, ofreciendo un resultado alternativo cuando una ejecución no se completa con éxito. |
| Implicaciones |Uso de alguna de las siguientes estrategias:<br><br>- Disponer de un service mesh para implementar diferentes estrategias para tratar las fallas, previa configuración en dicho contexto.<br>- Uso de librerias o bibliotecas para lograr diferentes políticas de tolerancia a fallas como timeout, retry, bulkhead, Circuit-Breaker, Fallback, entre otras. 

### Altamente cohesivos

|         |  | 
| :---          |:----        | 
| Declaración   |Desarrollar micro servicios altamente cohesivos.| 
| Racional      |Los micro servicios tienen muchas responsabilidades o que son responsables de realizar muchas funciones y muy diferentes, adolecen de los problemas de dificultad para su entendimiento, dificultad en el mantenimiento y son afectados frecuentemente por los cambios que puedan surgir. |
| Implicaciones |Diseñar e implementar micro servicios con un solo propósito y que no realicen gran cantidad de trabajo, aplicando el patrón de simple responsabilidad (SRP).

### Mínimamente acoplados

|         |  | 
| :---          |:----        | 
| Declaración   |Desarrollar micro servicios con un acoplamiento bajo.| 
| Racional      |Los micro servicios que están conectados a, tienen conocimiento de o confían en muchos otros micro servicios, adolecen de los problemas de dificultad para su entendimiento de manera aislada, son complejos de reutilizar o probar puesto que su uso requiere de la presencia adicional de otros micro servicios, de los cuales este depende. Finalmente, pueden verse altamente afectados por las modificaciones realizadas en algunos de sus vecinos.|
| Implicaciones |Diseñar e implementar micro servicios con mínimas dependencias. Es altamente recomendado desacoplar el emisor y el receptor mediante mensajería ligera.

### Empaquetado como imagen de contenedor

|         |  | 
| :---          |:----        | 
| Declaración   |Empaquetar el micro servicio como una imagen de contenedor.| 
| Racional      |Los contenedores son un mecanismo de implementación moderno y liviano que simplifica el desarrollo, la prueba y el despliegue de aplicaciones en múltiples entornos, requiriendo menos recursos del sistema que los entornos de máquinas virtuales tradicionales y, permitiendo que las aplicaciones se implementen, reparen o escalen más rápidamente.|
| Implicaciones |- Uso de una imagen de construcción del contenedor que describe el entorno de ejecución.<br>- Empleo del fuente de la aplicación a ser inyectada.<br>- Uso de un proceso de construcción que transforma el código fuente en una imagen de contenedor ejecutable, usando la imagen de construcción del contenedor.

### Plataforma centrada en contenedores

|         |  | 
| :---          |:----        | 
| Declaración   |Emplear un entorno de orquestación centrado en contenedores para ejecutar los micro servicios.| 
| Racional      |Contar con un entorno de orquestación permite gestionar los micro servicios, eliminando muchos de los procesos manuales involucrados en la implementación, instanciación, ejecución, balanceo y escalabilidad de los mismos.|
| Implicaciones |- Uso de un entorno de orquestación centrado en contenedores.<br>- Configurar/programar el entorno de orquestación para realizar las actividades de instanciación, ejecución, balanceo y escalabilidad elástica de los micro servicios.

### Despliegue mediante pipeline

|         |  | 
| :---          |:----        | 
| Declaración   |Desplegar el micro servicio usando un pipeline de despliegue.| 
| Racional      |El pipeline de despliegue es una implementación automatizada de la construcción, testeo y despliegue de la aplicación, permitiendo una rapida retro-alimentación de los problemas, disponer de un mismo proceso en distintos entornos, reducir los tiempos de ejecución de las tareas y al ejecutar varias veces diariamente, aumenta la confianza en el proceso.|
| Implicaciones |- Uso de un entorno de ejecución del pipeline de despliegue.<br>- Definición de las tareas del pipeline de despliegue.<br>- Determinación del momento de ejecución del pipeline de despliegue.
