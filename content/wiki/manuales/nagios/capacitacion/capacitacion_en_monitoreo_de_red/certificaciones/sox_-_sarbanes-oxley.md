---
title: "SOX"
date: 2019-09-04T10:59:55-03:00
---

La Ley Sarbanes-Oxley, cuyo título oficial en inglés es Sarbanes-Oxley Act of 2002, Pub. L. No. 107-204, 116 Stat. 745, es una ley de Estados Unidos también conocida como la Ley de Reforma de la Contabilidad Pública de Empresas y de Protección al Inversionista, aunque también es llamada SOx, SarbOx o SOA.​

La Ley Sarbanes Oxley se promulgó en Estados Unidos con el propósito de monitorizar a las empresas que cotizan en bolsa de valores, evitando que la valorización de las acciones de las mismas sean alteradas de manera dudosa, mientras que su valor es menor. Su finalidad es evitar fraudes y riesgo de bancarrota, protegiendo al inversionista.

Esta ley, más allá del ámbito local, también involucra a todas las empresas que cotizan en NYSE (Bolsa de Nueva York), así como a sus filiales.

## Nagios y SOX

Nagios puede ayudar a una organización afectada por SOX, en áreas que van desde sistemas y servicios de monitoreo hasta asistencia para verificar que estén en un estado de confiable.

Ref : Nagios 3 Enterprise Network Monitoring: Including Plug-Ins and Hardware Devices

Con respecto a COBIT,, Nagios puede cumplir directamente todos los objetivos de control (ME1-4) :

- ME1 Supervisar y evaluar procesos de TI
- ME2 Supervisar y evaluar el control interno
- ME3 Garantizar el cumplimiento normativo
- ME4 Proporcionar Gobierno de TI

### Algunas consideraciones técnicas

El modo de operar del núcleo de Nagios no es el foco, sino como realiza los chequeos y mensaje que maneja con los sistemas a monitorear.

- Usar SSH para monitoreo no era una opción debido a problemas de seguridad y posibles problemas con algunos controles SOX.
- Es mandatorio usar SNMPv3, en los casos que no sea posible, debe estar debidamente justificado.
