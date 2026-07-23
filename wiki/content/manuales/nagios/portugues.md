---
title: "Monitoração de rede e análise com Nagios"
date: 2015-01-13T14:46:00-03:00
---

[Version del documento original en español](/manuales/nagios/)

<img src="/manuales/nagios/portugues/brasil_map_color.jpg" class="align-left" data-query="?200" width="200" /> <img src="/manuales/nagios/portugues/portugal_map_color.jpg" class="align-right" data-query="?200" width="200" alt="portugal_map_color.jpg" />

<img src="/manuales/nagios/nagios_logo.png" class="align-center" data-query="?" alt="nagios_logo.png" />

As redes de computadores das organizações tornam-se cada vez mais complexo e exigente operação está se tornando mais exigente. Redes, cada vez mais, as aplicações e as organizações de serviços de apoio estratégico. Portanto, a análise e monitoramento de rede tornou-se uma natureza cada vez mais importante e pró-ativa de trabalho para evitar problemas. Monitoramento com o termo que significa um sistema que monitora constantemente a rede de computadores para detectar sistemas lentos ou com defeito e notifica o administrador de rede em caso de falha via e-mail, SMS ou outros meios.

Para evitar erros em um sistema que pode usar um aplicativo que lida com o ser “controlado e assistindo” o funcionamento da rede, ele pode fazê-lo através de um software chamado Nagios. Nagios é um aplicativo de monitoramento de sistemas de servidores, aplicações e redes. Ele checa clientes e serviços, por você especificados, alertando quando as coisas estão indo mal ou se restabelecendo.

O Nagios é distribuído sob os termos da GNU General Public License Versão 2 como foi publicado pela Free Software Foundation.

O Nagios é um sistema de monitoramento de equipamentos e rede de serviços, é distribuído sob os termos da GNU General Public License Versão 2 como foi publicado pela Free Software Foundation, a linguagem foi desenvolvida com o qual garante a rápida implementação e licenciado como software livre determina o que nos assegura que sempre tem atualizações disponíveis e há uma grande comunidade de desenvolvedores de apoiá-lo.

## Descrição

#### O que pode ser feito com Nagios

- Monitora serviços de rede (SMTP, POP3, HTTP, NNTP, ICMP, SNMP).
- Monitora recursos de computadores ou equipamentos de rede (carga do processador, uso de disco, logs do sistema) na maioria dos sistemas operacionais com suporte a rede, mesmo o Microsoft Windows com o plugin NRPE_NT.
- Monitoração remota suportada através de túneis criptografados SSH ou SSL.
- Desenvolvimento simples de plugins que permite aos usuários facilmente criar seus próprios modos de monitoração dependendo de suas necessidades, usando a ferramenta de desenvolvimento da sua escolha (Bash, C, Perl, Python, PHP, C#, etc.)
- Checagem dos serviços paralelizadas, ou seja, se você tiver muitos itens monitorados não há risco de alguns deles não serem checados por falta de tempo.
- Capacidade de definir a rede hierarquicamente definindo equipamentos “pai”, permitindo distinção dos equipamentos que estão indisponíveis daqueles que estão inalcançáveis.
- Capacidade de notificar quando um serviço ou equipamento apresenta problemas e quando o problema é resolvido (via email, pager, SMS, ou qualquer outro meio definido pelo usuário por plugin).
- Capacidade de definir tratadores de eventos que executam tarefas em situações pré-determinadas ou para a resolução pró-ativas de problemas.
- Rotação automática de log.
- Suporte para implementação de monitoração redundante.
- Interface web para visualização do atual status da rede, notificações, histórico de problemas, arquivos de log, etc.
