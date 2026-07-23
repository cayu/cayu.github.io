---
title: "Capacitación en monitoreo de Red"
date: 2017-03-22T11:38:39-03:00
---

## Introducción

### Monitoreo continuo y gestión integral

Las redes de cómputo de las organizaciones, se vuelven cada vez más complejas y la exigencia de la operación es cada vez mas demandante. Las redes, cada vez mas, soportan aplicaciones y servicios estratégicos de las organizaciones. Por lo cual el análisis y monitoreo de redes se ha convertido en una labor cada vez mas importante y de carácter pro-activo para evitar problemas. Con el termino Monitoreo nos referimos a un sistema que constantemente monitorea una red de computadoras para detectar sistemas lentos o en mal funcionamiento y que notifica al administrador de la red en caso de falla vía correo electrónico, sms u otros medios.

Para prevenir errores en un sistema podemos utilizar una aplicación que se ocupe de estar “controlado y observando” el funcionamiento de la red, esto podemos realizarlo por medio de un software llamado Nagios. Nagios es un sistema de monitoreo de servidores, aplicaciones y redes. Comprueba clientes y servicios, especificados alertando en caso de problemas como caídas de servicio ó restauración de los mismos.

Nagios es un sistema de monitorización de equipos y de servicios de red, escrito en C y publicado bajo la GNU General Public License, el lenguage con el cual esta desarrollado nos asegura una rápida ejecución y su licencia que lo determina como Software Libre nos asegura que siempre tendremos actualizaciones disponibles y que hay una gran comunidad de desarrolladores soportándolo.

Creado para ayudar a los administradores a tener siempre el control de qué está pasando en la red que administran y conocer los problemas que ocurren en la infraestructura que administran antes de que los usuarios de la misma los perciban, para así no sólo poder tomar la iniciativa, sino asumir la responsabilidad de hacer que las cosas sucedan; decidir en cada momento lo que queremos hacer y cómo lo vamos a hacer, debido a que este software nos permite obtener datos, interpretarlos y tomar decisiones en base a ello como:

- Conservar y almacene datos de la red para manejar reportes y tendencias.
- Ver y analizar la red, así como el tráfico de la red a través del tiempo.
- Monitorear el estado de la red en comparación a los reportes de análisis.
- Generar reportes sustentados para justificar las necesidades de actualización de la red

Para facilitar tareas de explotación de datos, hay diferentes aditivos como un visor de reportes integrados, en el cual se puede ver el histórico de actividad y performance de servicios, y además un visor de diagramas de red con el estado actual de cada equipo.

El mismo, esta constituido por un Núcleo que construye la interfaz de usuario y por plugins los cuales representan los ojos y oídos de Nagios y por lo cual se encargan de recopilar información (bajo demanda). Los mismos pueden estar programados en diversos lenguajes como C, C++, Python, Perl, PHP, Java, Bash etc, ya que Nagios es independiente del lenguaje en el cual que se desarrolle el plugin y solo procesa los datos recibidos de este, para la posterior elaboración y envío de notificaciones a los encargados de la administración del sistema en cuestión.

## Implementación de sistema de monitoreo de TI

### Conceptos de monitoreo de TI

#### Necesidad

Para la implementación de un Sistema de Monitoreo de TI, dentro de la confección del Plan de Necesidades deberemos desarrollar preguntas y recabar datos acerca de la siguiente cuestión ¿Que se busca?

- Detección de caídas del sistemas
  - Disponibilidad del sistema
- Evaluación de cumplimiento de SLA

<!-- -->

- Capacity planing
  - Analisis de performance
    - Detección de errores de diseño y de programación

Acciones Indirectas que vienen de la mano de la implementación de un Sistema de Monitoreo de TI son : la “Confección de una CMDB”. Ya que sin su existencia, no es posible tener una visión del sistema.

- Planificación
  - Sofware
    - De base
      - Aplicaciones
        - Funcionales

<!-- -->

- Comunicaciones
  - Subsistemas interconectados
  - Enlaces y servicios de conectividad, vpn, gsm etc.

<!-- -->

- Capacidad General del Sistema para absorver crecimiento
- Capacidad General del Sistema para absorver picos de stress

<!-- -->

- Ubicaciones Geográficas y estado general de las instalaciones
- Costos de servicios contratados y tiempo de respuesta ante incidentes

<!-- -->

- Coste de caida de Servicio
  - Corte total, inactividad
  - Caída de la productividad
  - Incumplimiento de SLA en provision de servicios

Acciones Secundarias con la implementació de sistema de Monitoreo, son la mejora de una CMDB existente.

#### Objetivos

Conocer el estado de diferentes servicios brindados por un conjunto heterogéneo de dispositivos y equipos como servidores corriendo diferentes sistemas operativos, routers de los cuales dependen varios sistemas y subsistemas. Sistemas SCADA que afectan procesos industriales.

Para poder tener esta información se debe establecer un control que asegure el mantenimiento de los dispositivos y se puedan efectuar acciones en forma preventiva, correctiva ó reactiva con rápida respuesta según corresponda en tiempo y forma ante eventuales anomalías de los servicios.

Es posible conocer los estados y datos de estos diferentes equipos para una posterior elaboración de reportes etc, elaborando una configuración personalizada de monitoreo para cada caso en particular, por ejemplo por medio de testeo de paquetes de red, o haciendo uso de diferentes funciones como las que provee el protocolo SNMP (Simple Network Management Protocol) que nos permite gestionar y/o supervisar datos de diferentes elementos y componentes de la red como routers, switches, servidores etc y al ser un protocolo standard es posible monitorizar una amplia variedad de casos en escenarios con sistemas ó equipos diferentes.

**Con lo cual podremos concluir si el sistema :**

* Lleva a cabo eficazmente su finalidad
* Utiliza eficientemente los recursos.

**Para :**

* Optimizar los Procesos
* Reducir Costos Operativos
* Llevar a cabo un mejoramiento del Control de Calidad
    * Minimizar Tiempos => $$$$

**Ya que podemos :**

* Detectar de forma sistemática el uso de los recursos y los flujos de información dentro de una organización.
* Determinar qué información es crítica para el cumplimiento de su misión y objetivos, identificando necesidades, duplicidades, costos, valor y barreras, que obstaculizan flujos de información eficientes.
* Análizar de eficiencia del sistema.
* Verificar el cumplimiento de Normativas.
* Revisión de la gestión de recursos.

**Con esto podemos elaborar informes, responder ante evaluaciones externas y documentar la evaluación para reflejar el desarrollo y los resultados de la misma pero además vamos a :**

* Fortalecer bases de información para grupos y personas de apoyo que trabajan con los sistemas.

**Puntos principales a tener en cuenta :**

* Aplicación estable para entornos productivos.
* Licencia de código abierto.
* Debe monitorear equipos idenpendientemente de sistemas operativos : Windows, Linux, Unix, CISCO iOS. 
* Generar alertas cuando se identifican incidencias.
* Los datos se deben poder exportar para su posterior análisis. 
* El sistema debe poder trabajar tanto con agentes instalados en los equipos clientes como sin ellos.
* Poder generar complementos (plugs in) independientemente del lenguaje de programación o ubicación de los mismos.
* Documentación suficiente y clara disponible del sistema de monitoreo. 
* El sistema debe tener una comunidad que lo respalde y preferentemente empresas.
* El sistema debe ser muy conocido o utilizado. Existencia de empresas clientes o usuarios a los que se puede referenciar.
* Actualizaciones regulares.

**Como adicional debemos cubrir :**

- Necesidades de Automatización, Instrumentación y Optimización de los procesos industriales.

**Gestión proactiva para alcanzar los objetivos organizacionales**

Diseñar e implementar procesos operativos y administrativos eficaces aplicables a la Gestión de la Red de TI.

La **Gestión de Problemas** puede ser:

- **Reactiva:** Analiza los incidentes ocurridos para descubrir su causa y propone soluciones a los mismos.
- **Proactiva:** Monitorea la calidad de la infraestructura TI. Analiza los Registros de Incidentes y configuraciones utilizando datos de otros procesos de Gestión del Servicio de TI para identificar tendencias o problemas significativos con el objetivo de prevenir incidentes incluso antes de que estos ocurran.

Cuando se produce un problema que genera una anomália estamos ante una incidencia. La **Gestión de Incidencias** tiene como objetivo resolver, de la manera más rápida y eficaz posible, cualquier incidente que cause una interrupción en el servicio.

La meta a llegar para mejorar la eficiencia de un Área de TI, es poder ir adoptando una metodología de gestión proactiva de problemas.

Para esto deben haber fuentes que nos permitan generar **indicadores**. <img src="/capacitaciones/monitoreo/grafico-300x220.png" class="align-center" alt="grafico-300x220.png" />

#### Indicadores

Damos por nombre de **indicador** a un instrumento de medida, cuantitativo o cualitativo, que refleja la medición de calidad que posee una actividad o un servicio cualquiera. Basados en hechos y datos, que permiten evaluar la calidad de los procesos, productos y servicios para asegurar el nivel de cumplimiento de las especificaciones establecidas para una determinada actividad o proceso.

#### Indicadores en TI

Dentro de la administración de TI podemos tener infinidad de indicadores. Para lo que nosotros necesitamos, solo vamos a basarnos en lo que respecta a dispositivos que se conectan a la red, ya sean LAN, WAN o Internet.

Indicadores de red que podemos tener en cuenta : <img src="/capacitaciones/monitoreo/rta_pnp4nagios.png" class="align-center" alt="rta_pnp4nagios.png" />

- Tiempo de respuesta
- Paquetes perdidos
- Saltos

Indicadores de dispositivos de automatización industrial (Supervisión, Control y Adquisición de Datos) que podemos tener en cuenta :

- Indicadores de temperatura
- Datos obtenidos por medio de RS232

Indicadores de la capa de servidores que podemos tener en cuenta : <img src="/capacitaciones/monitoreo/loadaverage_pnp4nagios.jpg" class="align-center" alt="loadaverage_pnp4nagios.jpg" />

- Utilización de Memoria, CPU, Disco
- Procesos de Sistema Operativo
- Servicios

Indicadores de la capa de aplicación que podemos tener en cuenta :

- Tiempo de actividad de sistemas/aplicaciones críticas del negocio
- Datos internos de aplicación
- Estadisticas de Base de Datos

Indicadores operativos a tener en cuenta :

- Tiempo promedio para la solución de incidentes de producción
- Número de incidentes de producción por gravedad
- Porcentaje de solicitudes de servicio / tickets cerrados dentro de los SLAs
- Tiempo promedio de resolución de solicitudes de servicio

Indicadores de Seguridad de la Información:

- Número de violaciones de la seguridad / incidentes en los sistemas y la infraestructura
- Porcentaje de sistemas / aplicaciones compatibles con las Normas / políticas de seguridad
- Porcentaje de parches de seguridad aplicados en plazos / fechas límite

Datos de negocio :

- Tiempo perdido por el negocio debido a caídas no programadas
- Paradas y su expresión monetaria
- OEE (Overall Equipment Efficiency o Eficiencia General de los Equipos)

### Metas necesarias para cumplir dichos objetivos

#### Relevamiento y diseño del modelo

El relevamiento es la herramienta principal a desarrollar durante la fase de Planificación de un sistema integral de monitoreo, para que este a su vez pueda dar datos fieles de como se ven afectados los procesos tanto operativos y de apoyo, como los procesos esenciales de producción y de negocio.

#### Infraestructura de equipamiento, software de base y comunicaciones

Para conocer la base donde se apoyaran los servicios de TI se deberá analizar lo siguiente :

- Software de base, desarrollo y comunicaciones.
- La capacidad del equipamiento para absorber, en situaciones de exigencia, las operaciones del proceso que están automatizadas y el crecimiento permitido para incorporar las faltantes.
- Las comunicaciones instaladas y sus características técnicas (señalización, protocolos, software de red, etc.). Se relevarán las redes LAN y las WAN, como así también las posibilidades de instalación de áreas no cubiertas en las que se ejecuten los procesos analizados.
- Las ubicaciones geográficas y direcciones de los distintos inmuebles en los que se realizan los procesos con indicación de distancias entre ellos.
  - Estado general de las instalaciones.
- Los costos por servicios informáticos y de comunicaciones (Ej.: mantenimiento de hardware o software, comunicaciones, licencias de software de base, alquiler, leasing o financiamiento de equipamiento, etc.

#### Software de aplicación

Se puede comenzar a partir de un un inventario del software o desarrollarlo contenga como mínimo :

- Denominación del sistema o subsistema.
- Antigüedad promedio.
- Cantidad de aplicaciones que lo integran.
- Desarrollo propio o de terceros, indicando si la Organización tiene capacidad de automantenimiento.
- Lenguaje de desarrollo.
- Esfuerzo de mantenimiento exigido.
- Objeto y alcance: eventos o actividades del proceso que automatiza.
- Productos que brinda.
- Documentación funcional y técnica.
- Estimación en equivalencia de tiempo completo de la cantidad de personas afectadas al desarrollo, mantenimiento y operación del sistema.
- Costo de licencias y de mantenimiento, en caso de contratos con terceros.

#### Identificar las soluciones informáticas desarrolladas por terceros

- Nivel de automatización de los procesos sometidos a análisis y grado de colaboración para una solución integral de gestión.
- Portabilidad de la solución respecto de plataformas de equipamientos y sistemas operativos.
- Infraestructura de la empresa y cantidad de productos instalados.
- Facilidad de instalación y requisitos de entrenamiento del personal que seguirá con el mantenimiento.
- Nivel de parametrización.
- Facilidad de uso.
- Calidad de la documentación disponible.
- Flexibilidad ante la necesidad de ajustes.
- Frecuencia de aparición de nuevas versiones

#### Herramientas

##### Herramientas principales para el análisis

- Mapa de sistemas.
- Relevamiento de infraestructura informática y de comunicaciones.
- Inventario de infraestructura informática y de comunicaciones (Activos de TI, Hardware, Software, Aplicaciones etc).
- Informe preliminar de soluciones informáticas desarrolladas por terceros.
- Informe de costos informáticos y de comunicaciones.

##### Herramientas y productos principales a utilizar

- Gestión de Inventario
- CMDB
- Sistema de administración de Proyectos

### Estrategias de monitoreo

#### Monitoreo Activo

Este tipo de monitoreo se realiza enviando paquetes desde sl sistema de monitoreo a los clientes que necesitamos monitorear. Ya sea un PING o pedidos a determinadas aplicaciones en los mismos.

- Ventajas
  - No hay que instalar un agente especializado en el cliente. En algunos casos solo SNMP. Es una opción para casos en los que no es posible instalar aplicaciones en los clientes.

<!-- -->

- Desventajas
  - Tiene métricas menos especificas por consiguiente se pueden realizar análisis menos detallados. Pueden ser afectadas por hechos que sucedan en la red.

#### Monitoreo Pasivo

Esta estrategia se basa en la obtención de datos a partir desde los clientes a monitorear hacia el sistema de monitoreo. Este enfoque bien planificado puede ser mas perfomante a lo que trafico de red se refiere comparándolo con la técnica de Monitoreo Pasivo.

- Ventajas
  - Información más específica y más detallada. Mayor flexibilidad para realizar monitoreos personalizable. Posibilidad de crear soluciones de monitoreo que controlen estados de servicios o métricas no estándares sobre aplicaciones o hardware. El control de las aplicaciones y servicios se realiza directamente en el nodo monitoreado. Mayor seguridad en la red ya que se manejan protocolos encriptación. Menor riesgo de detección de inactividades.

<!-- -->

- Desventajas
  - Puede provocar mayor carga de actividad en el cliente. Se debe instalar el agente en todos los equipos que se van a monitorear.

### Que chequear y como hacerlo

#### Capas a chequear

- Aspectos generales
  - Monitoreo de objetos o cajas negras con agentes o sin agentes
  - Reportes estadísticos

<!-- -->

- Infraestructura / Funcionalidad de Hardware
  - Uptime
  - Respuesta
  - Infraestructura / Sistema Operativo
    - Recursos
    - Procesos
    - Núcleo
    - Servicios y aplicaciones
      - Procesos
      - Tiempos de respuesta
      - Usuarios
- Notificaciones y alertas en caso de incidente

#### Que estrategía utilizar

Por ejemplo, utilizamos una estrategia de Monitoreo Activo.

1.  Si el servicio que tenemos depende de un enlace de red
    1.  Si ese servicio de conectividad de red esta caído y es el único acceso al servicio :
        1.  Podemos decir que el servicio esta caído por ser inaccesible, igualmente a la hora de elaborar un reporte se detallará la razón como caída de enlace de red.
    2.  Si ese servicio de conectividad no es el único modo de acceder al servicio o solo se cayó un nodo de la red WAN, ya sea una Unidad Organizativa, como una sucursal etc.
        1.  Solo se mostrará como caído ese nodo de red, ejemplo “Sucursal 1”. Pero el servicio seguira figurando correctamente.

#### Notificaciones

##### Tipo edicto

Edicto tiene su origen en el latín *proclamar en voz alta*. Podemos decir que es un aviso público sobre un asunto de interés común para todo el grupo afectado.

En el caso de un sistema de monitoreo informático en el cual queremos que todos o un cierto grupo de la organización se den por notificados de un evento o incidente en forma colectiva dependiendo solo de su ubicación física sin considerar herramientas de comunicación individuales como lo son el correo electrónico o mensajes de texto al móvil SMS.

Para este tipo de lógica podemos diseñar e implementar mecanismos visuales, como por ejemplo pantallas gigantes con diagramas de red indicando su estado actual. A su vez se puede acompañar lo visual con alertas sonoras.

##### Notificaciones individualizadas

En este caso nos referimos a notificación como una comunicación enviada una persona para que actúe tomando acción sobre algún proceso de TI en el caso de que sea necesario.

En la mayoría de los casos, cuanta más información acerca del evento quede se muestre en la notificación, y cuanto mejor se determine el destinatario de dicha información, más fácil resultará tomar decisiones respecto al mismo. Una situación recurrente es que se registran mensajes de error complejos y el personal de **primer nivel** encargado de resolver los problemas no alcanza a comprender su significado completo y en algunos casos debe escalar el problema a un **segundo** o **tercer nivel** solo para su comprensión o análisis.

En otros caso pasa todo los contrario hay ciertos servicios que solo los debe atender personal técnico altamente calificado y por lo tanto con enviar un notificación breve solamente con la mínima información necesaria para la compresión del caso ya es suficiente y además mas eficiente.

Hay que considerar diferentes condiciones de envío de información ya que esta debe estar personalizada en base al conocimiento técnico del receptor y sus posibilidades de recepción del mismo, ya sea por dispositivo como teléfono móvil, computadora, tablet, a su vez si es un correo electrónico debe tener un rango de información y si es un mensaje de texto SMS debe tener otro rango para asegurar su eficiente lectura.
{{% hint info %}}
Los roles y responsabilidades deben ser bien definidos desde la fase de Diseño, ya que cuando se presente un evento es probable que se requiera una acción.
{{% /hint %}}
Las notificaciones pueden ser :

- **Informativas**: Se asigna a aquellos eventos que no requieren, en principio, ninguna respuesta y que por tanto no representan un problema.
- **Alerta**: Se asigna a aquellos eventos que indican que el servicio se aproxima a un umbral. Su objetivo es notificar a las personas, herramientas o procesos apropiados para que revisen la situación y tomen las medidas necesarias para evitar que se produzca un incidente.

## Costos y Disponibilidad de Servicio

### Costos y Servicio

**Disponibilidad de servicio** : Medida porcentual del total de un mes calendario en el que el Servicio se encuentra habilitado para su utilización.

#### Capacidad de gestión de los servicios informáticos

Nos referimos a la **Capacidad** de respuesta de los recursos humanos integrantes del servicio informático para afrontar los requerimientos de información que se plantean en la ejecución de los procesos estudiados.

Se constatará si existen normas, métodos, procedimientos y el nivel de retraso respecto de requerimientos de mantenimiento o nuevos sistemas.

Estimar los costos de los servicios informáticos

Los principales insumos para la determinación del costo de los servicios informáticos son:

- Contratos informáticos por alquiler, leasing o compra financiada de : equipamiento, software de base y comunicaciones.

Si la infraestructura es propiedad de la Organización se estimarán los costos de reposición para aplicar cuotas de amortización para un período de \*n\* años (plazo que se estima razonable para la depreciación de un sistema de información automatizado) y su antigüedad actual promedio.

- Contratos de alquiler de licencias de software aplicativo.
- Sueldos y costos operativos de los empleados del servicio informático.
- Insumos mensuales consumidos por el equipo humano de trabajo de TI (impresiones, tarjetas de credito corporativas, gastos de transporte etc).
- Servicios varios (aire acondicionado del datacenter, suministro eléctrio etc).

Cada uno de los ítems relevados deberá ser prorrateado conforme a la base de distribución que se considere más adecuada. Por ejemplo, para distribuir los costos de personal se puede utilizar el procedimiento equivalente de tiempo completo (ETC). Si una persona que trabaja 200 horas mensuales le dedica el 10% de su tiempo a mantener un sistema relacionado al proceso en estudio, estará afectando el ETC de 20 horas de trabajo. Luego, si su sueldo más leyes sociales es de **\$XX** por mes, resultará que el costo proporcional de dicha actividad es de **\$NN**.

En otras situaciones, como alquileres de inmuebles, la base de prorrateo puede ser metros cuadrados ocupados.

#### Análisis de costos y su relación con la Disponibilidad del Servicio - SLA

A la hora de contratar un servicio de Data Center por lo general se hace para suplir varias carencias que se pueden dar en la organización que si bien a veces esa carencia es de infraestructura y de inversión monetaria, aunque muchas otras veces es por la imposibilidad de disponibilizar tiempo y recursos humanos en el proyecto de implementación del mismo.

Cuando uno contrata un servicio de Data Center hay varios factores a tener en cuenta que deben ser provistos y desarrollados por el prestador del servicio y a la hora de ofrecer el mismo, esos factores suelen afectar directamente en su **costo** :

- Costo determinado por varios factores :
  - Disponibilidad
  - Redundancia
  - Escalabilidad
  - Eficiencia

<!-- -->

- Servicios extras con interdependencia y relación de unos con otros :
  - Backup administrador por el proovedor
  - Servicio manos y ojos
  - Alertas proactivas 7×24
  - Descuentos aleatorios

Todos estos factores deben estar esclarecidos ya que no solo puede existir la necesidad de asegurarle y garantizarle efectivamente esto a los utilizadores externos, sino también a los internos, ya que la Disponibilidad es el factor determinante de la contratación del servicio. Por lo general se establecen acuerdos de Disponibilidad de Servicio - SLA, con diferentes niveles y con un accionar y escalamiento determinado para cada caso.

Lo comentado anteriormente en la mayoría de los casos y por lógica y sentido común suele encuadrarse dentro de un marco legal por medio de un contrato y dentro del mismo suele haber cláusulas por multas monetarias debido a tiempos de indisponibilidad o mal funcionamiento del servicio. El proceso de costeo para realizar la calificación de incidentes y su posterior valorización monetaria debe estar detallado en una norma que regle dicho procedimiento definido en la contratación del servicio.

Por y para eso para nosotros es necesario como proovedores de servicio que somos y al estar abasteciendo a otras empresas con exigencias necesarias para el desarrollo de su actividad, **en este caso, la base material para correr su infraestructura informática** :
{{% hint info %}}
Que tengamos un registro minucioso y reportado de eventos ya que estos pueden afectarnos directamente de forma legal y monetaria en el desarrollo de nuestras actividades sino llevamos control de los mismos. Para a su vez así poder justificar y definir el costo y alcance de la arquitectura empresarial de la metodología de gestión de servicios de negocio poniendo foco en las necesidades del cliente.
{{% /hint %}}
### Estimación de porcentaje de disponibilidad de servicio

#### Formas de calcular la disponibilidad de servicio

**Ejemplo de cálculo SLA**
{{% hint info %}}
**D = A/B x 100**

**A** es el número de horas en las cuales cada una de los servicios estuvieron disponibles. Este parámetro se verá disminuido con respecto a las horas que el sistema no funciona correctamente, es decir, cuando se ve afectado por cualquiera de los siguientes problemas:

- Caída de cualquiera de los servicios.
- Latencias superiores a x segundos.
- Degradación del servicio, alterando los tiempos de respuesta.
- Errores en la configuración.
- Fallas humanas provocadas.
- Cualquier otro factor que se considere pertinente.

**B** es el número de horas en que los servicios deberían estar disponibles, cuyo valor es setecientos veinte (720) horas mensuales;

**D** es el porcentaje de Disponibilidad.

**El incumplimiento de la Disponibilidad acordada, puede llevar a penalizaciones previamente acordadas**
{{% /hint %}}
{{% hint info %}}
**D = (T - Ti)/T\*100%**

**D** disponibilidad

**T** tiempo total en un mes, medido en horas

**Ti** tiempo de interrupción del Servicio
{{% /hint %}}
{{% hint info %}}
Disponibilidad = ((A – B)/A) x 100 por ciento)

**A** Horas comprometidas de disponibilidad: 24 x 365 = 8,760 Horas/año.

**B** Horas de caída de servicio
{{% /hint %}}
#### Tabla de resultados

| Disponibilidad (%) | Tiempo offline/año | Tiempo offline/mes | Tiempo offline/día |
|--------------------|--------------------|--------------------|--------------------|
| 90%                | 36.5 días          | 73 hrs             | 2.4 hrs            |
| 95%                | 18.3 días          | 36.5 hrs           | 1.2 hrs            |
| 98%                | 7.3 días           | 14.6 hrs           | 28.8 min           |
| 99%                | 3.7 días           | 7.3 hrs            | 14.4 min           |
| 99.5%              | 1.8 días           | 3.66 hrs           | 7.22 min           |
| 99.9%              | 8.8 hrs            | 43.8 min           | 1.46 min           |
| 99.95%             | 4.4 hrs            | 21.9 min           | 43.8 s             |
| 99.99%             | 52.6 min           | 4.4 min            | 8.6 s              |
| 99.999%            | 5.26 min           | 26.3 s             | 0.86 s             |
| 99.9999%           | 31.5 s             | 2.62 s             | 0.08 s             |

**Atención al Cliente** Se debe prestar un servicio de soporte acordado mediante contrato. En caso de que el cliente reporte una indisponibilidad o fallas en el servicio, nosotros una vez reportada la falla y entregado el ticket se deberá de informar al cliente el seguimiento y solución que se le haya dado, en intervalos definidos. Todo esto definidos en :

1.  Acuerdos de niveles de servicio
    1.  Tiempo de atención a fallas
    2.  Niveles de Escalamiento – aseguramiento para los servicios
    3.  Tiempo de atención al usuario.

**Punto de restauración de servicio**, se debe verificar con el Cliente por medio de **Pruebas de Aceptación del Servicio** (a definir contractualmente) que el Cliente contratante del servicio realizará a fin de acusar conformidad con la restauración de la disponibilidad del servicio.

### Costo por caída de Servicio

#### Calculando el costo por caída de Servicio

Factores a tener en cuenta en un caso de ejemplo para una compañía muy dependiente de recursos de TI en el cual la indisponibilidad de servicio tiene mayor incidencia :

- **Coste de Inactividad de los empleados / Perdida de Productividad**
  - Básicamente es “Cuánto cuesta que nuestros empleados estén parados”, para ello, podemos utilizar el coste medio por hora de los empleados y lo multiplicaremos por el tiempo de inactividad y por el número de empleados afectados por la caída.
{{% hint info %}}
**Coste Medio de Inactividad** = Coste por hora de empleado \* Nº de empleados afectados \* Duración de la interrupción de servicio.
{{% /hint %}}
- **Pérdida de Operaciones**
  - Este concepto hace referencia al número de operaciones que no se llevan a cabo por la caída de sistemas, ya sean ventas, transacciones, pedidos o cualquier otra operación objeto del negocio de la compañía. Por ejemplo, si nuestra compañía se dedica a vender productos por internet podemos determinar en función de las estadísticas de venta diarias cuanto nos cuesta tener parado nuestro portal web.

<!-- -->

- **Incumplimientos de normativa, acuerdos o SLA**
  - Nuestra compañía puede tener contratos de prestación de servicios con otras empresas en las cuales se recogen penalizaciones por incumplimiento del mismo. Una caída de nuestro sistema puede afectar a estas empresas ocasionándolos un agravio y por ende reclamarnos una compensación económica. Por otro lado, algunas empresas por el sector al que se dedican o por el tipo de servicio que prestan, están sujetas a normativas o leyes que ante una parada del sistema pueden acarrearles sanciones o multas.

<!-- -->

- **Impacto en marca, pérdida de confianza**
  - Esto es algo muy relativo y tiene un impacto según la cultura de la zona donde se preste el servicio, pero puede ser un parámetro a considerar a la hora de determinar el coste por caída de servicio. Muchas compañías emplean gran cantidad de recursos en campañas publicitarias, redes sociales, patrocinios etc., para afianzar y reforzar su marca en el mercado, una interrupción de servicio en los sistemas informáticos puede dañar su imágen o en algunos casos que el cliente desestime en ese momento utilizar sus productos o servicios.

*Podemos determinar el costo en función del gasto económico que tiene que realizar la empresa para recuperar los niveles de confianza previos a la caída, pero para eso tenemos que establecer una ponderacíón de cada uno de los factores tenidos en cuenta para luego elaborar el cálculo de disponibilidad de servicio y el porcentaje de perjuicio a la metodología de provisión del servicio en la cuál se enmarca la empresa en cuestión.*

### Documentación de SLA

#### Documentación de Disponibilidad de Servicio

La documentación de SLA posee la vida y desarrollo de versiones del servicio. El documento posee revisión y firmas entre las partes que dan fé de la evolución y modificación de los acuerdos.

El documento debe contener las siguientes observaciones:

1.  Histórico
    1.  Ajuste de la revisión, fecha, descripción del histórico y autor de la revisión.
2.  Aprobaciones
    1.  Nombre, Fecha, título, firma y dirección de correo electrónico.
3.  Alcance
    1.  Audiencia
    2.  Propósito
    3.  Supuestos
    4.  Contactos
4.  Garantías y recomendaciones y del detalle del servicio
    1.  Formatos de archivo
    2.  Envío y Expectativas
        1.  Tipo de archivo, frecuencia esperada
    3.  Acciones de escalamiento
    4.  Recursos para escalar
    5.  Tiempo para la solución de problemas
    6.  Histórico de desempeño
5.  Gestión de Problemas
6.  Gestión del Desempeño
7.  Funciones y Responsabilidades del Cliente
8.  Terminación

- Crear documentación de ayuda adicional
  - Información de contacto
    - Nombre función, teléfono, teléfono celular, etc
- Definiciones, términos, acrónimos

#### Disposiciones legales

Además con los datos proporcionados por Nagios en la mayoría de los casos se puede obtener una explicación de cuál es el problema que está experimentando en su infraestructura, aunque sea aparentemente invisible, además esta operatoria ayuda en nuestra estrategia implementación de **BS 10012:2009 Data Protection - Specification for a Personal Information Management System (PIMS)**, la cual por ejemplo en Mexico es necesaria para cumplir los requerimientos de la **LFPDPPP** (LEY FEDERAL DE PROTECCIÓN DE DATOS PERSONALES EN POSESIÓN DE LOS PARTICULARES), ya que necesitamos capacidad de monitoreo para rastrear cualquier cambio en la información y poder llegar a establecer quién está utilizando cuales datos y cómo lo hace.

## Gestión de TI

### Gestión de activos de TI

#### Information Technology Infrastructure Library - ITIL

**ITIL** es un conjunto de Mejores Prácticas en la **Gestión de Servicios de TI**. Es una guía, y no un manual de cómo han de hacerse las cosas.

Situándonos en el marco ITIL (Biblioteca de Infraestructura de Tecnologías de Información) para la provisión de servicios TI con calidad, los tres objetivos claves de la Gestión de Servicios son:

- Alinear los servicios informáticos con las necesidades actuales y futuras
- Mejorar la calidad de los servicios informáticos entregados
- Reducir el coste a largo plazo del suministro de servicios

**Su objeto de aplicación parte de estas premisas**

- No se puede gestionar lo que no se puede controlar.
- No se puede controlar lo que no se puede medir.
- No se puede medir lo que no se puede definir.

**¿Como encuandra el Monitoreo de TI en ITIL?**

En ITIL, los consultores acompañan a las empresas a diseñar y/o implementar sus procesos. También realizan GAPs para evaluar cuan cerca se encuentra la organización de las actividades que se recomiendan en las mejores prácticas y se recomiendan posibles mejoras para acercarse.

Con respecto a **GAP**, nos referimos a el análisis para verificar que el sistema cumple con los requisitos de determinada norma, esquema o conjunto de criterios de evaluación.

El Monitoreo de TI cubre estas áreas de ITIL :

- Service Desk
- Incident Management
- Service Level Management
- Capacity Management
- IT Service Continuity Management
- Availability Management
- ICT infrastructure Management

**Gestión de la disponibilidad**

- La disponibilidad “Availability Management” es un proceso del “Service Delivery”, definido en las especificaciones de ITIL.
  - Su meta es asegurar que el nivel de disponibilidad requerido esté proporcionado.
  - La supervisión y análisis de informes de la disponibilidad es una actividad clave para asegurar que los niveles del servicio se estén cumpliendo.
  - En la base de la gestión se debe supervisar contínuamente la disponibilidad de la Infraestructura, servicios y alertar a los administradores para iniciar los procedimientos oportunos.

**Tiempo resolución de Incidencias**

- El tiempo de identificación de un problema mejora notablemente con la utilización de una herramienta de Monitoreo de TI.
  - Su meta es asegurar que el administrador identifique el problema antes que lo hagan los usuarios.
  - La supervisión y análisis de todos los servicios críticos y la notificación correcta es clave para reducir el tiempo de resolución.
  - Los informes de incidencias y performance ayudarán en la predicción de problemas y en identificar la necesidad de crecimientos.
  - Mejorando la Gestion de capacidad, estando directamente conectado con el processo de ITIL “Capacity Management”.

### Incidentes

#### Incidente

*Cualquier evento que no forma parte del desarrollo habitual del servicio y que causa, o puede causar una interrupción del mismo o una reducción de la calidad de dicho servicio. El objetivo de ITIL es reiniciar el funcionamiento normal tan rápido como sea posible con el menor impacto para el negocio y el usuario con el menor coste posible.*

**Ciclo de vida de un incidente** <img src="/manuales/nagios/itil_incident.png" class="align-center" data-query="?800" width="800" alt="itil_incident.png" />

**Administración de la capacidad de resolución y tratamiento** <img src="/manuales/nagios/itil_capacity.png" class="align-center" data-query="?800" width="800" alt="itil_capacity.png" />

### Implementación de las normas ITIL

#### Aplicando las normas ITIL

- Alinear los servicios de TI con las necesidades de la empresa (el negocio), actuales y futuras.
- Mejorar la calidad de los servicios de TI.
- Reducir los costos por la proveeduría de servicios de TI en el mediano y largo plazos.
- Mejora de rendimiento de la inversión de TI.
- Se mide el sistema de TI de la organización evaluando los procesos de Soporte Técnico y Entrega de Servicios comparándolos con las Mejores Prácticas.
- Identificar un **problema** y diferenciarlo de un **error conocido**.
- Llevar adelante un **capacity planning**.

#### Objetivos a mediano y corto plazo

##### Nuestras necesidades

Estan en orden de prioridad, pero todas van atadas de la mano una con otra :
{{% hint danger %}}
Solución de Monitoreo Integral
{{% /hint %}}
{{% hint warning %}}
Registro y Correlación de Eventos, tanto en equipos de red como en servidores
{{% /hint %}}
{{% hint info %}}
Documentación Rápida, Participativa,Colaborativa\
Debe ser vía Web, con edición individual de secciones y versionado
{{% /hint %}}
#### Objetivos a largo plazo

##### CMDB Biblioteca para organizar el servicio de TI

- Relaciones de muchos a muchos
- Inventario de equipos
  - Detalle de responsable técnico
  - IP
  - Software Instalado
- Base de IP
  - Detalle de las subredes
  - Equipos asociados con sus ip y MAC si coresponde
- Listado de reglas de ruteo indivuales por equipo, en caso de que existan
- Base de Certificados SSL
  - Certificado con fecha de emisión y fecha de caducidad
  - Detalles tecnicos y equipos donde se utilizan los certificados
- Base de Proovedores, Contratos y Contactos
  - Contactos determinados por tipo, y detalles de como contactarlos
  - Detalles de un proovedor que presta servicios y le compramos equipos
  - Contratos detallados, con servicios y equipos asociados a los que aplica
  - Poder realizar **reclamos**, SLA, corte / degradación de servicio
- Base de Usuarios y Contraseñas
  - Nombre de Usuario, Contraseña y equipo u aplicación asociado
- Necesario para enmarcar en ITIL
  - Historico de modificaciónes
  - Inventario de los activos del Departamento de IT
  - Estructura del inventario y relaciones entre los diferentes elementos
  - Manejo de Licencias
  - Gestión de problemas, incidentes
  - Quejas y reclamos
  - Posibilidad de adjuntar documentación de cada item presente
  - SLA basados ​​en contratos y horarios
  - Inventariado y gestion de Incidencias

## Capacitación en implementación del producto

### Nagios

#### Que es Nagios

Nagios es un sistema de monitoreo de redes, de código abierto, que vigila los equipos y servicios que se especifiquen, alertando determinado comportamiento de los mismos.

Se trata de un software que proporciona una gran versatilidad para consultar prácticamente cualquier parámetro de interés de un sistema, y genera alertas, que pueden ser recibidas por los responsables correspondientes mediante (entre otros medios) correo electrónico y mensajes SMS, cuando estos parámetros exceden de los márgenes definidos por el administrador de red.

Nagios por sí solo no hace nada, es solo un programador de tareas y estas tareas a su vez ejecutan comandos (plugins de Nagios). Dichos comandos son los encargados de enviar los datos a Nagios para :

- Almacenar datos estadísticos
- Generar alertas
- Ejecutar acciones

<img src="/capacitaciones/monitoreo/infografia_nagios.png" class="align-center" data-query="?800" width="800" alt="infografia_nagios.png" />

#### Que se puede hacer con Nagios

- Monitorización de servicios de red (SMTP, POP3, HTTP, NTTP, ICMP, SNMP).
- Monitorización de los recursos de un host (carga del procesador, uso de los discos, logs del sistema) en varios sistemas operativos, incluso Microsoft Windows con el plugin NRPE_NT o también por medio del protocolo SNMP.
- Monitorización remoto, a través de túneles SSL cifrados o SSH.
- Diseño simple de plugins, que permiten a los usuarios desarrollar sus propios chequeos de servicios dependiendo de sus necesidades, usando sus herramientas preferidas (Bash, C++, Perl, Ruby, Python, PHP, C#, Java, etc.).
- Chequeo de servicios paralizados.
- Posibilidad de definir la jerarquía de la red, permitiendo distinguir entre host caídos y host inaccesibles.
- Notificaciones a los contactos cuando ocurren problemas en servicios o hosts, así como cuando son resueltos ( Vía email, pager, Jabber, SMS o cualquier método definido por el usuario junto con su correspondiente complemento).
- Posibilidad de definir disparadores de eventos que se ejecuten al ocurrir una situación detectada dentro de un servicio o host para la resolución de problemas en forma proactiva.
- Rotación automática del archivo de registro.
- Soporte para implementar hosts de monitores redundantes.
- Interfaz web opcional, para observar el estado de la red actual, notificaciones, historial de problemas, archivos de registros, etc.
- Reportes y estadísticas del estado cronológico de disponibilidad de servicios y hosts.
- Definición de niveles o umbrales de alerta según periodos de tiempo.
- Consideración de tiempos de inoperatividad con anticipación para evitar envío innecesario de alertas.

#### Quien va a usar Nagios

- Administradores de Redes con alto conocimiento tecnico
- Operadores con minimo conocimiento tecnico de la situacion, o conocimiento puntual de algun servicio
  - Pudiedo avisar y ayudar a determinar posibles causas de efectos producidos
- Equipos de desarrollo
- Coordinadores de Mesas de Ayuda
- Areas relacionadas

#### Que se va a monitorear

Hay que definir o acotar que niveles y aspectos se van a monitorear

- Sistema Operativo
- Utilización de red
  - Trafico y consumo de ancho de banda
  - Tiempos de respuesta
- Servicios (SAP, Web, Bases de datos, DHCP)

#### Como impacta y para qué

- Mejora de productividad
- Antelacion de problemas
  - Conocer de donde proviene el problema
- Reporte y aviso de incidentes
  - Agilidad en su tratamiento
  - Compartir datos sobre la disponibilidad y SLA
- Mejor y mayor relacion e integracion de sectores adjuntos
- Detectar las brechas de seguridad
- Reducir y estimar periodos de inactividad y pérdidas empresariales
- Planificar actualizaciones de TI - Capacity Planning

### Requerimientos

#### Requerimientos Operativos

- Plan de acción bien diagramado
- Personal con conocimiento medio/avanzado en redes

<img src="/manuales/nagios/impacto_urgencia.png" class="align-center" alt="impacto_urgencia.png" />

##### Impacto, Urgencia, Prioridad

- **Categoría alta**
  - Corte de servicio perjudica actividades de negocio.
- **Categoría media**
  - Degradación de servicio afecta a actividades de negocio, genera demoras.
- **Categoría baja**
  - Servicio afectado pero no se impide su uso.

#### Recursos tecnológicos

- Hardware acorde a la inversion y criticidad de los datos
  - Si se van a monitorear \>= 600 host y \>= 2000 servicios hay que plantear bien esto, no sobrevaluar pero tampoco escatimar

#### Pasos específicos de Nagios

Las tareas de la configuración son las siguientes:

- Definición de usuario para la ejecución de los daemons.
- Configuración de apache para la visualización de la consola. Generalmente se accede a la consola de Nagios utilizando vínculos cifrados, por lo que es necesario crear los certificados correspondientes.
- Si el cliente posee un directorio donde centraliza todos sus usuarios, se podrá utilizar un usuario especialmente creado para consultar los perfiles de los usuarios que accederán a la consola de administración. Entre los directorios soportados se encuentra Microsoft Active Directory.
- Configuración de servidor de correo (MTA) y de SMS para el envío de todas las alertas que genera el sistema.
- Configuración de plugins básicos y de plugins específicos.
- Definición de Servicios a monitorear.
- Definición de dependencias de servicios (en aquellos servicios que dependan de la funcionalidad de otro servicio para poder ser monitoreados se deben configurar las dependencias, esto le indica a Nagios que no tiene que declarar fuera de línea un servicio si del que depende está caído).
- Definición de Commandos (los comandos son la forma en que se verifican el estado de los servicios declarados).
- Definición de HostGroups (los hostgroups son grupos de servidores que se monitorean, junto con el cliente de realiza la diagramación de estos grupos).
- Definición de Contacts (los contacts son personas o más bien direcciones de email/sms que integrarán los ContactGroups).
- Definición de ContactGroups (los contactgroups son grupos que se utilizarán para enviar las alertas que se generen en la monitoría).
- Creación de directivas (especificación de dónde se encuentran varias definiciones de Nagios como Hostgroups, ContactGroups, Contacts, etc.).
- Configuración de sistema de parsing de logs para producir las gráficas (históricas) de rendimiento de los servicios.
- Adaptación de notificaciones (las notificaciones, el texto del correo electrónico, puede ser adaptado para una mejor identificación de problemas a los administradores al igual que los mensajes SMS).
