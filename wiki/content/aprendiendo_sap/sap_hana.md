---
title: "SAP HANA"
date: 2017-02-01T15:39:52-03:00
---

## Base de datos HANA

Procesos del Servidor

- **hdbdaemon** se encarga de iniciar los servicios mencionados a continuación en el orden correcto.

<!-- -->

- **hdbnameserver** este proceso se encarga de saber la ubicación de las tablas en el index.

<!-- -->

- **hdbpreprocessor** se encarga de analizar los datos de texto y la extracción de la información en que se basan las capacidades de búsqueda de texto.

<!-- -->

- **hdbindexserver** servidor de índice de datos, responsable de la gestión de la conexión, el proceso de SQL, manipulación de metadatos, almacenamiento fila y columnas

<!-- -->

- **hdbstatisticsserver** Contener datos / vistas de administrador del sistema de base de datos de SAP HANA para supervisar el estado del sistema, de sus servicios y el consumo de recursos del sistema.

### Referencias

- <http://gerardnico.com/wiki/hana/start>

<!-- -->

- [SAP HANA Database Architecture](http://help.sap.com/saphelp_hanaplatform/helpdata/en/d8/d14ef53e244f6cabd10dd3f5e8c11e/content.htm?frameset=/en/89/4b429de9054190b42eee8b321edee9/frameset.htm&current_toc=/en/34/29fc63a1de4cd6876ea211dc86ee54/plain.htm&node_id=9)
- [SAP HANA Developer Guides](http://help.sap.com/saphelp_hanaplatform/helpdata/en/3f/8ef5ad563b4ea5a7c3a155cb7ba160/content.htm?frameset=/en/a8/199cdde87842fe8812a33caff19961/frameset.htm&current_toc=/en/34/29fc63a1de4cd6876ea211dc86ee54/plain.htm&node_id=13)
- [1523337 - SAP HANA Database 1.00 - Central Note](https://service.sap.com/sap/support/notes/1523337)
- [Use case - Master Guide](http://help.sap.com/saphelp_hanaplatform/helpdata/en/0c/9b3b1363634183ab8cd370a66c78df/content.htm?frameset=/en/d3/d1cf20bb5710149b57fd794c827a4e/frameset.htm&current_toc=/en/d4/3377c2bb57101489ebe2e6a1813cfc/plain.htm&node_id=6&show_children=false)
