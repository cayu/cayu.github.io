---
title: "Manejo de memoria en Linux"
date: 2014-01-02T17:48:06-03:00
---

Toda la documentación basada en el kernel se puede encontrar en el directorio ***Documentation*** dentro del archivo del código fuente de Linux.

### Comando VMSTAT

El comando vmstat brinda un informe del estado de los procesos, memoria, páginamiento etc.

```
procs -----------memory---------- ---swap-- -----io---- -system-- -----cpu------
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 1  7      0 558804 2378120 38024140    0    0  1361    85    3    1  5  1 86  9  0
```

Algo interesante de ver en este comando es el 0 0 de la sección de swap, el ideal es que este en este valor, esto querrá decir que el equipo esta con suficiente memoria como para no utilizar swap.

Retornando al tema, si observamos la parte de la memoria, observamos 3 valores ( free,buff,cache). La forma en que Linux utiliza la memoria es que el sistema considera que la memoria no usada es memoria perdida, así que en su funcionamiento utiliza la memoria libre para hacer cache, y las cosas que se van solicitando quedan en ella, siempre y cuando el equipo no requiera utilizar mas memoria, en cuyo caso se descargaran procesos del cache generando la memoria que se necesite.

### Comando PS

##### Lista los 10 procesos que consumen mas memoria

```
ps aux --width 30 --sort -rss | head
```

### Comando TOP

- **SWAP** Swapped size (kb)
  - The swapped out portion of a task’s total virtual memory image.
- **RES** Resident size (kb)
  - The non-swapped physical memory a task has used.
- **CODE** Code size (kb)
  - The amount of physical memory devoted to executable code, also known as the text resident set size or TRS.
- **DATA** Data+Stack size (kb)
  - The amount of physical memory devoted to other than executable code, also known as the data resident setsize or DRS.

Entonces: **VIRT** = **SWAP** + **RES**

**RES** = **CODE** + **DATA**

Lo que aparece en top es Data + StackSize

Por Ejemplo:

```
 top - 19:36:32 up 1 day, 12:40,  4 users,  load average: 1.77, 0.39, 0.70
 Tasks: 411 total,   9 running, 402 sleeping,   0 stopped,   0 zombie
 Cpu(s): 24.6%us,  0.5%sy,  0.0%ni, 74.9%id,  0.0%wa,  0.0%hi,  0.0%si,  0.0%st
 Mem:  16409888k total, 13440348k used,  2969540k free,   253980k buffers
 Swap: 18448376k total,        0k used, 18448376k free,  6487708k cached
 
 PID  PPID USER PR NI  VIRT SWAP  RES CODE DATA  SHR S %CPU %MEM    TIME+    COMMAND
 21169 21164 ach 25  0  982m 171m 810m  22m 869m  13m R 99.8  5.1   0:19.36 20 wrf.exe
 21170 21164 ach 25  0  977m 172m 805m  22m 865m  12m R 100.3 5.0   0:19.36 22 wrf.exe
 21167 21164 ach 25  0  972m 170m 802m  22m 860m  13m R 99.8  5.0   0:19.36 23 wrf.exe
 21168 21164 ach 25  0  969m 170m 798m  22m 857m  13m R 99.8  5.0   0:19.36 25 wrf.exe
 21165 21164 ach 25  0  957m 182m 774m  22m 844m  14m R 99.8  4.8   0:19.36 18 wrf.exe
 21166 21164 ach 25  0  920m 167m 752m  22m 809m  11m R 99.8  4.7   0:19.36 21 wrf.exe
 21171 21164 ach 25  0  907m 165m 741m  22m 795m  12m R 99.8  4.6   0:19.36 24 wrf.exe
 21172 21164 ach 25  0  904m 165m 738m  22m 791m  12m R 99.8  4.6   0:19.36 27 wrf.exe
```

PID 21169: VIRT = SWAP + RES 982m = 171m + 810m

RES = CODE + DATA 810m = 22m + 869m

**SHR** Shared Mem size (kb) The amount of shared memory used by a task. It simply reflects memory that could be potentially shared with other processes.

- **SWAP** Swapped size (kb)
  - The swapped out portion of a task’s total virtual memory image.
-  **RES** Resident size (kb)
  - The non-swapped physical memory a task has used.
-  **CODE** Code size (kb)
  - The amount of physical memory devoted to executable code, also known as the text resident set size or TRS.
-  **DATA** Data+Stack size (kb)
  - The amount of physical memory devoted to other than executable code, also known as the data resident setsize or DRS.

Entonces:

**VIRT** = **SWAP** + **RES**

**RES** = **CODE** + **DATA**

Lo que aparece en top es Data + StackSize

Por Ejemplo:

```
 top - 19:36:32 up 1 day, 12:40,  4 users,  load average: 1.77, 0.39, 0.70
 Tasks: 411 total,   9 running, 402 sleeping,   0 stopped,   0 zombie
 Cpu(s): 24.6%us,  0.5%sy,  0.0%ni, 74.9%id,  0.0%wa,  0.0%hi,  0.0%si,  0.0%st
 Mem:  16409888k total, 13440348k used,  2969540k free,   253980k buffers
 Swap: 18448376k total,        0k used, 18448376k free,  6487708k cached
 
 PID  PPID USER PR NI  VIRT SWAP  RES CODE DATA  SHR S %CPU %MEM    TIME+    COMMAND
 21169 21164 ach 25  0  982m 171m 810m  22m 869m  13m R 99.8  5.1   0:19.36 20 wrf.exe
 21170 21164 ach 25  0  977m 172m 805m  22m 865m  12m R 100.3 5.0   0:19.36 22 wrf.exe
 21167 21164 ach 25  0  972m 170m 802m  22m 860m  13m R 99.8  5.0   0:19.36 23 wrf.exe
 21168 21164 ach 25  0  969m 170m 798m  22m 857m  13m R 99.8  5.0   0:19.36 25 wrf.exe
 21165 21164 ach 25  0  957m 182m 774m  22m 844m  14m R 99.8  4.8   0:19.36 18 wrf.exe
 21166 21164 ach 25  0  920m 167m 752m  22m 809m  11m R 99.8  4.7   0:19.36 21 wrf.exe
 21171 21164 ach 25  0  907m 165m 741m  22m 795m  12m R 99.8  4.6   0:19.36 24 wrf.exe
 21172 21164 ach 25  0  904m 165m 738m  22m 791m  12m R 99.8  4.6   0:19.36 27 wrf.exe
```

PID 21169:

VIRT = SWAP + RES

982m = 171m + 810m

RES = CODE + DATA

810m = 22m + 869m

- **SHR** Shared Mem size (kb)
  - The amount of shared memory used by a task. It simply reflects memory that could be potentially shared with other processes.

Info útil sobre el manejo del comando TOP : <http://www.thegeekstuff.com/2010/01/15-practical-unix-linux-top-command-examples/>
