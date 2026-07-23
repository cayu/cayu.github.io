---
title: "Oracle"
date: 2019-11-19T18:05:24-03:00
---

Oracle Database es un sistema de gestión de base de datos objeto relacional ORDBMS.

## Monitoreo

Creación de usuario para monitoreo de la Base de Datos

```sql>
==== Infraestructura ====
^Capa de Sistema Operativo^^
|Estado de Host|
|Carga de sistema o de CPU|
|Consumo de memoria|
|Espacio en discos rígidos|
^Base de datos^^
|Procesos|Ej ora_*|
|Puertos de conexión|Ej 1527|
|Table Spaces o espacios|
|Tamaños de archivos de log, ej listener.log|Ej si el log del tnslistener tiene mas de 2GB en ciertas versiones cae la BD|
|Archiving|Espacios y regularidad|
^Capa de Aplicación^^
|Valores internos de la base de datos|Ej Querys con ciertos resultados|
=== Scripts ===
== check_microstrategy ==
**count_archlogs.pl**
<file perl count_archlogs.pl>
#!/usr/bin/perl
use strict;
use warnings;

my $ORACLE_SID=$ARGV[0];

sub get_sorted_files {
   my $path = shift;
   opendir my($dir), $path or die "no puedo abrir $path: $!";
   my %hash = map {$_ => (stat($_))[9] || undef} # saltar listas vacias
    map  { "$path$_" }
    grep { m/.dbf/i }
    readdir $dir;
    closedir $dir;
    return %hash;
}

my %files = get_sorted_files("/oracle/arclog/".$ORACLE_SID."/");
my $count = keys %files;

if($count < 60) {
    print "OK - Encontrados $count redo logs en /oracle/arclog/".$ORACLE_SID."/|'redologs'=$count\n";
    exit 0;
} else {
    print "CRITICAL - Encontrados $count redo logs en /oracle/arclog/".$ORACLE_SID."/|'redologs'=$count\n";
    exit 2;
}
</file>

**select_count.sh**

Si necesito alertar cuando crecen X registros en una tabla Oracle, en el dia de hoy
se puede ejecutar esto :
<code bash>
echo -e "set head off\nset pagesize 0\nSELECT COUNT(DATA) FROM APPREG.DATA WHERE DATA = TO_DATE(SYSDATE,'DD/MM/YY');" |  sqlplus -S "/ as sysdba" | awk '/^[ 0-9\.\t ]+$/ {print int($1)}'
```

Dentro de un script quedaria asi :

```bash
#!/bin/bash
# Chequear registros en Tabla y alertar al llegar al limite
CONTEO=`echo -e "set head off\nset pagesize 0\nSELECT COUNT(DATA) FROM APPREG.DATA WHERE DATA = TO_DATE(SYSDATE,'DD/MM/YY');" |  sqlplus -S "/ as sysdba" | awk '/^[ 0-9\.\t ]+$/ {print int($1)}'`
LIMITE=5
 
if [[ "$CONTEO" -ge "$LIMITE" ]]
then
    echo "CRITICAL - Hay $CONTEO registros";
    exit 2;
else
    echo "OK - Hay $CONTEO registros";
    exit 0;
fi
```

O para conocer el estado de la base de datos

```bash
DATABASE_STATUS=`echo -e "set head off\nset pagesize 0\nSELECT status, database_status FROM v\\$instance;" |  sqlplus -S "/ as sysdba"| cut -f1`

case "$DATABASE_STATUS" in
        MOUNTED)
            start
            echo "CRITICAL - Los tablespaces de la base de datos estan $DATABASE_STATUS -" `date '+DATE: %m/%d/%y TIME:%H:%M:%S'`
            exit 2;
            ;;
        OPEN)
            echo "OK - Los tablespaces de la base de datos estan $DATABASE_STATUS -" `date '+DATE: %m/%d/%y TIME:%H:%M:%S'`
            exit 1;
            ;;
        *)
            echo "CRITICAL - Hay algun error con la base de datos $DATABASE_STATUS -" `date '+DATE: %m/%d/%y TIME:%H:%M:%S'`
            exit 2;
esac
```

### Eventos

```
       278 Batched IO (bound) vector count
       276 Batched IO (full) vector count
       277 Batched IO (space) vector count
       274 Batched IO block miss count
       280 Batched IO buffer defrag count
       275 Batched IO double miss count
       279 Batched IO same unit count
       272 Batched IO single block count
       281 Batched IO slow jump count
       271 Batched IO vector block count
       270 Batched IO vector read count
       273 Batched IO zero block count
       314 Block Cleanout Optim referenced
       513 CCursor + sql area evicted
        17 CPU used by this session
        16 CPU used when call started
       146 CR blocks created
       313 Cached Commit SCN referenced
       527 Clusterwide global transactions
       528 Clusterwide global transactions spanning RAC nodes
       312 Commit SCN cached
        18 DB time
       110 DBWR checkpoint buffers written
       119 DBWR checkpoints
       120 DBWR fusion writes
       118 DBWR lru scans
       114 DBWR object drop buffers written
       113 DBWR parallel query checkpoint buffers written
       117 DBWR revisited being-written buffer
       112 DBWR tablespace checkpoint buffers written
       111 DBWR thread checkpoint buffers written
       115 DBWR transaction table writes
       116 DBWR undo block writes
       561 DDL statements parallelized
       562 DFO trees parallelized
       560 DML statements parallelized
       526 DX/BB enqueue lock background get time
       525 DX/BB enqueue lock background gets
       523 DX/BB enqueue lock foreground requests
       524 DX/BB enqueue lock foreground wait time
       507 EHCC Analyze CUs Decompressed
       510 EHCC Analyzer Calls
       486 EHCC Archive CUs Compressed
       495 EHCC Archive CUs Decompressed
       491 EHCC CU Row Pieces Compressed
       483 EHCC CUs Compressed
       492 EHCC CUs Decompressed
       509 EHCC Check CUs Decompressed
       498 EHCC Columns Decompressed
       487 EHCC Compressed Length Compressed
       496 EHCC Compressed Length Decompressed
       503 EHCC DML CUs Decompressed
       488 EHCC Decompressed Length Compressed
       497 EHCC Decompressed Length Decompressed
       508 EHCC Dump CUs Decompressed
       504 EHCC Normal Scan CUs Decompressed
       501 EHCC Pieces Buffered for Decompression
       485 EHCC Query High CUs Compressed
       494 EHCC Query High CUs Decompressed
       484 EHCC Query Low CUs Compressed
       493 EHCC Query Low CUs Decompressed
       506 EHCC Rowid CUs Decompressed
       489 EHCC Rows Compressed
       490 EHCC Rows Not Compressed
       499 EHCC Total Columns for Decompression
       502 EHCC Total Pieces for Decompression
       500 EHCC Total Rows for Decompression
       505 EHCC Turbo Scan CUs Decompressed
       235 Effective IO time
       529 Forwarded 2PC commands across RAC nodes
       530 GTX processes spawned by autotune
       531 GTX processes stopped by autotune
       452 HCC Conventional DMLs
       453 HCC non-CU Compressed Blocks
       454 HCC: Number of times tried to non-CU Compress a Block
       440 HSC Compressed Segment Block Changes
       441 HSC Heap Segment Block Changes
       439 HSC IDL Compressed Blocks
       438 HSC OLTP Compressed Blocks
       448 HSC OLTP Compression skipped rows
       447 HSC OLTP Drop Column
       442 HSC OLTP Non Compressible Blocks
       437 HSC OLTP Space Saving
       449 HSC OLTP compression block checked
       446 HSC OLTP inline compression
       444 HSC OLTP negative compression
       443 HSC OLTP positive compression
       445 HSC OLTP recursive compression
       450 Heap Segment Array Inserts
       451 Heap Segment Array Updates
       350 IMU CR rollbacks
       342 IMU Flushes
       352 IMU Redo allocation size
       347 IMU bind flushes
       341 IMU commits
       343 IMU contention
       346 IMU ktichg flush
       348 IMU mbu flush
       349 IMU pool not allocated
       344 IMU recursive-transaction flush
       351 IMU undo allocation size
       345 IMU undo retention flush
       353 IMU- failed to get a private strand
        61 IPC CPU used by this session
       580 LOB table id lookup cache misses
       354 Misses for writing mapping
       515 No. of Decrypt ops
       514 No. of Encrypt ops
       518 No. of Namespaces Created
       521 No. of Principal Cache Misses
       522 No. of Principal Invalidations
       520 No. of Roles Enabled or Disabled
       519 No. of User Callbacks Executed
       517 No. of XS Sessions Attached
       516 No. of XS Sessions Created
       236 Number of read IOs issued
       631 OS Block input operations
       632 OS Block output operations
         0 OS CPU Qt wait time
       625 OS Integral shared text size
       626 OS Integral unshared data size
       627 OS Integral unshared stack size
       637 OS Involuntary context switches
       624 OS Maximum resident set size
       629 OS Page faults
       628 OS Page reclaims
       635 OS Signals received
       634 OS Socket messages received
       633 OS Socket messages sent
       630 OS Swaps
       623 OS System time used
       622 OS User time used
       636 OS Voluntary context switches
       602 OTC commit optimization attempts
       604 OTC commit optimization failure - setup
       603 OTC commit optimization hits
       570 PX local messages recv'd
       569 PX local messages sent
       572 PX remote messages recv'd
       571 PX remote messages sent
       568 Parallel operations downgraded 1 to 25 pct
       567 Parallel operations downgraded 25 to 50 pct
       566 Parallel operations downgraded 50 to 75 pct
       565 Parallel operations downgraded 75 to 99 pct
       564 Parallel operations downgraded to serial
       563 Parallel operations not downgraded
         1 Requests to/from client
       301 RowCR - resume
       300 RowCR - row contention
       298 RowCR attempts
       299 RowCR hits
       370 SMON posted for dropping temp segment
       368 SMON posted for instance recovery
       367 SMON posted for txn recovery for other instances
       366 SMON posted for undo segment recovery
       369 SMON posted for undo segment shrink
       591 SQL*Net roundtrips to/from client
       594 SQL*Net roundtrips to/from dblink
       364 TBS Extension: bytes extended
       363 TBS Extension: files extended
       361 TBS Extension: tasks created
       362 TBS Extension: tasks executed
       607 Workload Capture: dbtime
       614 Workload Capture: errors
       606 Workload Capture: size (in bytes) of recording
       610 Workload Capture: unreplayable user calls
       613 Workload Capture: unsupported user calls
       608 Workload Capture: user calls
       609 Workload Capture: user calls flushed
       612 Workload Capture: user logins
       611 Workload Capture: user txns
       615 Workload Replay: dbtime
       621 Workload Replay: deadlocks resolved
       616 Workload Replay: network time
       617 Workload Replay: think time
       618 Workload Replay: time gain
       619 Workload Replay: time loss
       620 Workload Replay: user calls
       308 active txn count during cleanout
        21 application wait time
       316 auto extends on undo tablespace
       238 background checkpoints completed
       237 background checkpoints started
        33 background timeouts
       169 blocks decrypted
       168 blocks encrypted
       414 branch node splits
       574 buffer is not pinned count
       573 buffer is pinned count
       590 bytes received via SQL*Net from client
       593 bytes received via SQL*Net from dblink
       589 bytes sent via SQL*Net to client
       592 bytes sent via SQL*Net to dblink
       596 bytes via SQL*Net vector from client
       598 bytes via SQL*Net vector from dblink
       595 bytes via SQL*Net vector to client
       597 bytes via SQL*Net vector to dblink
       173 calls to get snapshot scn: kcmgss
       172 calls to kcmgas
       170 calls to kcmgcs
       171 calls to kcmgrs
       406 cell CUs processed for compressed
       405 cell CUs processed for uncompressed
       403 cell CUs sent compressed
       404 cell CUs sent head piece
       402 cell CUs sent uncompressed
       407 cell IO uncompressed bytes
       395 cell blocks helped by commit cache
       396 cell blocks helped by minscn optimization
       389 cell blocks processed by cache layer
       391 cell blocks processed by data layer
       392 cell blocks processed by index layer
       390 cell blocks processed by txn layer
       393 cell commit cache queries
       605 cell flash cache read hits
       424 cell index scans
       262 cell num fast response sessions
       263 cell num fast response sessions continuing to smart scan
       256 cell num smart IO sessions in rdbms block IO due to big payload
       255 cell num smart IO sessions in rdbms block IO due to no cell mem
       254 cell num smart IO sessions in rdbms block IO due to user
       258 cell num smart IO sessions using passthru mode due to cellsrv
       259 cell num smart IO sessions using passthru mode due to timezone
       257 cell num smart IO sessions using passthru mode due to user
       260 cell num smart file creation sessions using rdbms block IO mode
       246 cell physical IO bytes eligible for predicate offload
       247 cell physical IO bytes saved by storage index
       245 cell physical IO bytes saved during optimized RMAN file restore
       244 cell physical IO bytes saved during optimized file creation
       248 cell physical IO bytes sent directly to DB node to balance CPU
        56 cell physical IO interconnect bytes
       261 cell physical IO interconnect bytes returned by smart scan
       388 cell scans
       400 cell simulated physical IO bytes eligible for predicate offload
       401 cell simulated physical IO bytes returned by predicate offload
       252 cell smart IO session cache hard misses
       250 cell smart IO session cache hits
       253 cell smart IO session cache hwm
       249 cell smart IO session cache lookups
       251 cell smart IO session cache soft misses
       264 cell statistics spare1
       265 cell statistics spare2
       266 cell statistics spare3
       267 cell statistics spare4
       268 cell statistics spare5
       269 cell statistics spare6
       394 cell transactions found in commit cache
       398 chained rows processed by cell
       399 chained rows rejected by cell
       397 chained rows skipped by cell
       129 change write time
       152 checkpoint clones created for ADG recovery
       309 cleanout - number of ktugct calls
       297 cleanouts and rollbacks - consistent read gets
       295 cleanouts only - consistent read gets
       386 cluster key scan block gets
       385 cluster key scans
        19 cluster wait time
       162 cold recycle reads
       328 commit batch performed
       325 commit batch requested
       327 commit batch/immediate performed
       324 commit batch/immediate requested
       137 commit cleanout failures: block lost
       140 commit cleanout failures: buffer being written
       141 commit cleanout failures: callback failure
       138 commit cleanout failures: cannot pin
       139 commit cleanout failures: hot backup in progress
       136 commit cleanout failures: write disabled
       142 commit cleanouts
       143 commit cleanouts successfully completed
       329 commit immediate performed
       326 commit immediate requested
       334 commit nowait performed
       331 commit nowait requested
       307 commit txn count during cleanout
       335 commit wait performed
       332 commit wait requested
       333 commit wait/nowait performed
       330 commit wait/nowait requested
        20 concurrency wait time
        85 consistent changes
        72 consistent gets
        75 consistent gets - examination
        76 consistent gets direct
        73 consistent gets from cache
        74 consistent gets from cache (fastpath)
       147 current blocks converted for CR
       558 cursor authentications
       293 data blocks consistent reads - undo records applied
        84 db block changes
        68 db block gets
        71 db block gets direct
        69 db block gets from cache
        70 db block gets from cache (fastpath)
       106 db corrupt blocks detected
       107 db corrupt blocks recovered
       306 deferred (CURRENT) block cleanout applications
       311 deferred CUR cleanouts (index blocks)
       132 dirty buffers inspected
       319 doubling up with imu segment
       317 drop segment calls in space pressure
        42 enqueue conversions
        40 enqueue deadlocks
        43 enqueue releases
        41 enqueue requests
        38 enqueue timeouts
        39 enqueue waits
       130 exchange deadlocks
       588 execute count
       416 failed probes on index block reclamation
       206 file io service time
       207 file io wait time
        99 flash cache eviction: aged out
        98 flash cache eviction: buffer pinned
        97 flash cache eviction: invalidated
       101 flash cache insert skip: DBWR overloaded
       105 flash cache insert skip: corrupt
       102 flash cache insert skip: exists
       104 flash cache insert skip: modification
       100 flash cache insert skip: not current
       103 flash cache insert skip: not useful
        93 flash cache inserts
       166 flashback cache read optimizations for block new
       167 flashback direct read optimizations for block new
       243 flashback log write bytes
       242 flashback log writes
       587 frame signature mismatch
       135 free buffer inspected
       131 free buffer requested
       229 gc CPU used by this session
       225 gc blocks compressed
       228 gc blocks corrupt
       226 gc blocks lost
       227 gc claim blocks lost
       231 gc cr block build time
       209 gc cr block flush time
       218 gc cr block receive time
       232 gc cr block send time
       217 gc cr blocks received
       208 gc cr blocks served
       216 gc current block flush time
       215 gc current block pin time
       220 gc current block receive time
       233 gc current block send time
       219 gc current blocks received
       214 gc current blocks served
       224 gc kbytes saved
       223 gc kbytes sent
       221 gc local grants
       212 gc read wait failures
       210 gc read wait time
       213 gc read wait timeouts
       211 gc read waits
       230 gc reader bypass grants
       222 gc remote grants
        62 gcs messages sent
        63 ges messages sent
        64 global enqueue CPU used by this session
        46 global enqueue get time
        45 global enqueue gets async
        44 global enqueue gets sync
        47 global enqueue releases
       336 global undo segment hints helped
       337 global undo segment hints were stale
       436 heap block compress
       134 hot buffers moved to head of LRU
       305 immediate (CR) block cleanout applications
       304 immediate (CURRENT) block cleanout applications
       310 immediate CR cleanouts (index blocks)
        26 in call idle wait time
       410 index crx upgrade (found)
       411 index crx upgrade (positioned)
       409 index crx upgrade (prefetch)
       427 index fast full scans (direct read)
       425 index fast full scans (full)
       426 index fast full scans (rowid ranges)
       428 index fetch by key
       418 index reclamation/extension switch
       429 index scans kdiixs1
       546 java call heap collected bytes
       545 java call heap collected count
       544 java call heap gc count
       542 java call heap live object count
       543 java call heap live object count max
       538 java call heap live size
       539 java call heap live size max
       540 java call heap object count
       541 java call heap object count max
       534 java call heap total size
       535 java call heap total size max
       536 java call heap used size
       537 java call heap used size max
       557 java session heap collected bytes
       556 java session heap collected count
       555 java session heap gc count
       553 java session heap live object count
       554 java session heap live object count max
       549 java session heap live size
       550 java session heap live size max
       551 java session heap object count
       552 java session heap object count max
       547 java session heap used size
       548 java session heap used size max
       413 leaf node 90-10 splits
       412 leaf node splits
       421 lob reads
       422 lob writes
       423 lob writes unaligned
       338 local undo segment hints helped
       339 local undo segment hints were stale
        77 logical read bytes from cache
         2 logons cumulative
         3 logons current
        65 max cf enq hold time
        32 messages received
        31 messages sent
       315 min active SCN optimization applied on CR
       419 native hash arithmetic execute
       420 native hash arithmetic fail
       575 no buffer to keep pinned count
       294 no work - consistent read gets
        25 non-idle wait count
        24 non-idle wait time
       241 number of map misses
       240 number of map operations
         4 opened cursors cumulative
         5 opened cursors current
       586 parse count (describe)
       585 parse count (failures)
       584 parse count (hard)
       583 parse count (total)
       581 parse time cpu
       582 parse time elapsed
        82 physical read IO requests
        83 physical read bytes
        80 physical read flash cache hits
        50 physical read requests optimized
        48 physical read total IO requests
        52 physical read total bytes
        51 physical read total bytes optimized
        49 physical read total multi block requests
        78 physical reads
        79 physical reads cache
       154 physical reads cache prefetch
        81 physical reads direct
       160 physical reads direct (lob)
        94 physical reads direct temporary tablespace
       165 physical reads for flashback new
       155 physical reads prefetch warmup
       159 physical reads retry corrupt
        92 physical write IO requests
        96 physical write bytes
        53 physical write total IO requests
        55 physical write total bytes
        54 physical write total multi block requests
        89 physical writes
        90 physical writes direct
       161 physical writes direct (lob)
        95 physical writes direct temporary tablespace
        91 physical writes from cache
       108 physical writes non checkpoint
       133 pinned buffers inspected
        11 pinned cursors current
       127 prefetch clients - 16k
       124 prefetch clients - 2k
       128 prefetch clients - 32k
       125 prefetch clients - 4k
       126 prefetch clients - 8k
       123 prefetch clients - default
       121 prefetch clients - keep
       122 prefetch clients - recycle
       157 prefetch warmup blocks aged out before use
       158 prefetch warmup blocks flushed out before use
       156 prefetched blocks aged out before use
        28 process last non-idle time
       559 queries parallelized
       431 queue flush
       434 queue ocp pages
       432 queue position update
       435 queue qno pages
       433 queue single row
       430 queue splits
       408 queue update without cp update
       239 read-only violation count
       145 recovery array read time
       144 recovery array reads
       153 recovery block gets from cache
        86 recovery blocks read
        87 recovery blocks read for lost write detection
        88 recovery blocks skipped lost write checks
       417 recursive aborts on index block reclamation
         9 recursive calls
        10 recursive cpu usage
        37 recursive system API invocations
       202 redo KB read
       203 redo KB read (memory)
       205 redo KB read (memory) for transport
       204 redo KB read for transport
       187 redo blocks checksummed by FG (exclusive)
       188 redo blocks checksummed by LGWR
       174 redo blocks read for recovery
       185 redo blocks written
       182 redo buffer allocation retries
       177 redo entries
       179 redo entries for lost write detection
       175 redo k-bytes read for recovery
       176 redo k-bytes read for terminal recovery
       189 redo log space requests
       190 redo log space wait time
       191 redo ordering marks
       178 redo size
       181 redo size for direct writes
       180 redo size for lost write detection
       192 redo subscn max counts
       199 redo synch long waits
       200 redo synch poll writes
       201 redo synch polls
       196 redo synch time
       197 redo synch time (usec)
       198 redo synch writes
       183 redo wastage
       194 redo write broadcast ack count
       193 redo write broadcast ack time
       195 redo write broadcast lgwr post count
       186 redo write time
       184 redo writes
        34 remote Oradebug requests
       302 rollback changes - undo records applied
       296 rollbacks only - consistent read gets
       415 root node splits
       387 rows fetched via callback
        23 scheduler wait time
       473 securefile add dedupd lob to set
       455 securefile allocation bytes
       456 securefile allocation chunks
       467 securefile bytes cleartext
       470 securefile bytes deduplicated
       466 securefile bytes encrypted
       464 securefile bytes non-transformed
       468 securefile compressed bytes
       471 securefile create dedup set
       479 securefile dedup callback oper final
       481 securefile dedup fits inline
       478 securefile dedup flush too low
       480 securefile dedup hash collision
       476 securefile dedup prefix hash match
       482 securefile dedup wapp cache miss
       472 securefile destroy dedup set
       457 securefile direct read bytes
       459 securefile direct read ops
       458 securefile direct write bytes
       460 securefile direct write ops
       463 securefile inode ioreap time
       461 securefile inode read time
       462 securefile inode write time
       477 securefile number of flushes
       465 securefile number of non-transformed flushes
       475 securefile reject deduplication
       474 securefile rmv from dedup set
       469 securefile uncompressed bytes
       358 segment cfs allocations
       359 segment chunks allocation from disepnser
       357 segment dispenser allocations
       356 segment dispenser load empty
       355 segment dispenser load tasks
       373 segment prealloc bytes
       372 segment prealloc ops
       371 segment prealloc tasks
       374 segment prealloc time (ms)
       375 segment prealloc ufs2cfs bytes
       360 segment total chunk allocation
       285 serializable aborts
        27 session connect time
       533 session cursor cache count
       532 session cursor cache hits
        12 session logical reads
        13 session logical reads in local numa group
        14 session logical reads in remote numa group
        35 session pga memory
        36 session pga memory max
        15 session stored procedure space
        29 session uga memory
        30 session uga memory max
       163 shared hash latch upgrades - no wait
       164 shared hash latch upgrades - wait
       283 shared io pool buffer get failure
       282 shared io pool buffer get success
       600 sorts (disk)
       599 sorts (memory)
       601 sorts (rows)
       322 space was found by tune down
       323 space was not found by tune down
        57 spare statistic 1
        58 spare statistic 2
        59 spare statistic 3
        60 spare statistic 4
       512 sql area evicted
       511 sql area purged
       321 steps of tune down ret. in space pressure
       109 summed dirty queue length
       148 switch current to new buffer
       383 table fetch by rowid
       384 table fetch continued row
       382 table scan blocks gotten
       381 table scan rows gotten
       379 table scans (cache partitions)
       380 table scans (direct read)
       377 table scans (long tables)
       378 table scans (rowid ranges)
       376 table scans (short tables)
       284 temp space allocated (bytes)
        66 total cf enq hold time
        67 total number of cf enq holders
       234 total number of slots
       365 total number of times SMON posted
       318 total number of undo segments dropped
       289 transaction lock background get time
       288 transaction lock background gets
       286 transaction lock foreground requests
       287 transaction lock foreground wait time
       303 transaction rollbacks
       292 transaction tables consistent read rollbacks
       291 transaction tables consistent reads - undo records applied
       320 tune down retentions in space pressure
       290 undo change vector size
       340 undo segment header was pinned
        22 user I/O wait time
         8 user calls
         6 user commits
         7 user rollbacks
       579 workarea executions - multipass
       578 workarea executions - onepass
       577 workarea executions - optimal
       576 workarea memory allocated
       151 write clones created for recovery
       150 write clones created in background
       149 write clones created in foreground
```

Ejemplo de salida ALL EVENTS

```
 SQL*Net message from client waits 298.949613% of the time
 rdbms ipc message waits 20.658595% of the time
 db file sequential read waits 4.144422% of the time
 Space Manager: slave idle wait waits 1.237053% of the time
 DIAG idle wait waits 1.167248% of the time
 Streams AQ: qmn coordinator idle wait waits 0.584892% of the time
 Streams AQ: qmn slave idle wait waits 0.584888% of the time
 Streams AQ: waiting for time management or cleanup tasks waits 0.584825% of the time
 pmon timer waits 0.584044% of the time
 smon timer waits 0.583994% of the time
 Streams AQ: waiting for messages in the queue waits 0.478663% of the time
 VKRM Idle waits 0.209138% of the time
 read by other session waits 0.162078% of the time
 direct path read waits 0.113885% of the time
 db file parallel read waits 0.068105% of the time
 library cache lock waits 0.047580% of the time
 db file scattered read waits 0.035248% of the time
 log file sync waits 0.029443% of the time
 log file parallel write waits 0.020535% of the time
 enq: TX - row lock contention waits 0.020387% of the time
 db file parallel write waits 0.014167% of the time
 db file async I/O submit waits 0.007869% of the time
 SQL*Net message from dblink waits 0.005528% of the time
 SQL*Net message to client waits 0.003988% of the time
 direct path read temp waits 0.003214% of the time
 SQL*Net more data to client waits 0.001838% of the time
 PX Idle Wait waits 0.001607% of the time
 control file sequential read waits 0.001345% of the time
 control file parallel write waits 0.000682% of the time
 jobq slave wait waits 0.000624% of the time
 enq: TX - index contention waits 0.000550% of the time
 PX Deq: Execution Msg waits 0.000408% of the time
 cursor: pin S wait on X waits 0.000216% of the time
 log file switch (private strand flush incomplete) waits 0.000178% of the time
 enq: CR - block range reuse ckpt waits 0.000124% of the time
 cursor: pin S waits 0.000122% of the time
 SQL*Net break/reset to client waits 0.000093% of the time
 reliable message waits 0.000091% of the time
 log file sequential read waits 0.000086% of the time
 enq: RO - fast object reuse waits 0.000085% of the time
 os thread startup waits 0.000078% of the time
 Disk file operations I/O waits 0.000076% of the time
 direct path write temp waits 0.000073% of the time
 PX Deq: Table Q Normal waits 0.000066% of the time
 latch: cache buffers chains waits 0.000061% of the time
 latch: messages waits 0.000056% of the time
 enq: KO - fast object checkpoint waits 0.000055% of the time
 PX Deq: Execute Reply waits 0.000039% of the time
 log file switch completion waits 0.000036% of the time
 ADR block file read waits 0.000034% of the time
 direct path write waits 0.000033% of the time
 resmgr:internal state change waits 0.000033% of the time
 buffer busy waits waits 0.000032% of the time
 log buffer space waits 0.000028% of the time
 PX Deq Credit: send blkd waits 0.000019% of the time
 SQL*Net more data from client waits 0.000018% of the time
 PX Deq: Table Q Sample waits 0.000015% of the time
 Disk file I/O Calibration waits 0.000014% of the time
 LGWR wait for redo copy waits 0.000012% of the time
 enq: TM - contention waits 0.000010% of the time
 resmgr:cpu quantum waits 0.000009% of the time
 row cache lock waits 0.000005% of the time
 log file single write waits 0.000005% of the time
 enq: US - contention waits 0.000004% of the time
 rdbms ipc reply waits 0.000004% of the time
 JS coord start wait waits 0.000004% of the time
 single-task message waits 0.000003% of the time
 kksfbc child completion waits 0.000003% of the time
 library cache: mutex X waits 0.000003% of the time
 latch free waits 0.000003% of the time
 PX Deq Credit: need buffer waits 0.000002% of the time
 latch: In memory undo latch waits 0.000002% of the time
 undo segment extension waits 0.000002% of the time
 enq: CF - contention waits 0.000002% of the time
 PX Deq: Parse Reply waits 0.000002% of the time
 enq: TX - allocate ITL entry waits 0.000002% of the time
 control file single write waits 0.000002% of the time
 DBWR range invalidation sync waits 0.000002% of the time
 local write wait waits 0.000001% of the time
 ADR block file write waits 0.000001% of the time
 enq: TX - contention waits 0.000001% of the time
 latch: redo writing waits 0.000001% of the time
 Parameter File I/O waits 0.000001% of the time
 library cache load lock waits 0.000001% of the time
 enq: WF - contention waits 0.000001% of the time
 enq: HW - contention waits 0.000001% of the time
 latch: row cache objects waits 0.000001% of the time
 PX Deq: Slave Session Stats waits 0.000001% of the time
 class slave wait waits 0.000000% of the time
 asynch descriptor resize waits 0.000000% of the time
 PX Deq: Table Q Get Keys waits 0.000000% of the time
 PX Deq: Join ACK waits 0.000000% of the time
 control file heartbeat waits 0.000000% of the time
 PL/SQL lock timer waits 0.000000% of the time
 wait list latch free waits 0.000000% of the time
 latch: cache buffers lru chain waits 0.000000% of the time
 latch: call allocation waits 0.000000% of the time
 switch logfile command waits 0.000000% of the time
 ARCH wait for process start 3 waits 0.000000% of the time
 latch: object queue header operation waits 0.000000% of the time
 latch: shared pool waits 0.000000% of the time
 enq: TA - contention waits 0.000000% of the time
 latch: enqueue hash chains waits 0.000000% of the time
 PX Deq: Signal ACK RSG waits 0.000000% of the time
 buffer deadlock waits 0.000000% of the time
 SQL*Net more data from dblink waits 0.000000% of the time
 enq: TH - metric threshold evaluation waits 0.000000% of the time
 latch: redo allocation waits 0.000000% of the time
 enq: JS - queue lock waits 0.000000% of the time
 ADR file lock waits 0.000000% of the time
 PX Deq: Signal ACK EXT waits 0.000000% of the time
 latch: checkpoint queue latch waits 0.000000% of the time
 db file single write waits 0.000000% of the time
 SQL*Net break/reset to dblink waits 0.000000% of the time
 write complete waits waits 0.000000% of the time
 ARCH wait for archivelog lock waits 0.000000% of the time
 SQL*Net message to dblink waits 0.000000% of the time
 Log archive I/O waits 0.000000% of the time
 enq: PS - contention waits 0.000000% of the time
 CSS initialization waits 0.000000% of the time
 enq: PV - syncstart waits 0.000000% of the time
 PX Deq: Table Q qref waits 0.000000% of the time
 latch: parallel query alloc buffer waits 0.000000% of the time
 latch: undo global data waits 0.000000% of the time
 latch: active service list waits 0.000000% of the time
 enq: TC - contention waits 0.000000% of the time
 enq: SQ - contention waits 0.000000% of the time
 cursor: mutex S waits 0.000000% of the time
 enq: FB - contention waits 0.000000% of the time
 PX qref latch waits 0.000000% of the time
 Streams AQ: qmn coordinator waiting for slave to start waits 0.000000% of the time
 enq: CU - contention waits 0.000000% of the time
 latch: session allocation waits 0.000000% of the time
 ASM file metadata operation waits 0.000000% of the time
 PX Deq Credit: free buffer waits 0.000000% of the time
 instance state change waits 0.000000% of the time
 Data file init write waits 0.000000% of the time
 PX Deq: Msg Fragment waits 0.000000% of the time
 cursor: mutex X waits 0.000000% of the time
 L1 validation waits 0.000000% of the time
 latch: cache buffer handles waits 0.000000% of the time
 secondary event waits 0.000000% of the time
 cell manager cancel work request waits 0.000000% of the time
 cell worker retry  waits 0.000000% of the time
 cell worker online completion waits 0.000000% of the time
 enq: XD - ASM disk OFFLINE waits 0.000000% of the time
 enq: XD - ASM disk ONLINE waits 0.000000% of the time
 enq: XD - ASM disk drop/add waits 0.000000% of the time
 enq: JS - aq sync waits 0.000000% of the time
 enq: JS - evt notify waits 0.000000% of the time
 enq: JS - wdw op waits 0.000000% of the time
 enq: JS - evtsub drop waits 0.000000% of the time
 enq: JS - evtsub add waits 0.000000% of the time
 enq: JS - q mem clnup lck waits 0.000000% of the time
 enq: JS - sch locl enqs waits 0.000000% of the time
 enq: JS - job recov lock waits 0.000000% of the time
 enq: JS - job run lock - synchronize waits 0.000000% of the time
 enq: JS - contention waits 0.000000% of the time
 latch: WCR: processes HT waits 0.000000% of the time
 latch: WCR: sync waits 0.000000% of the time
 WCR: Sync context busy waits 0.000000% of the time
 WCR: capture file IO write waits 0.000000% of the time
 WCR: RAC message context busy waits 0.000000% of the time
 enq: RR - contention waits 0.000000% of the time
 enq: TK - Auto Task Slave Lockout waits 0.000000% of the time
 enq: TK - Auto Task Serialization waits 0.000000% of the time
 enq: TL - contention waits 0.000000% of the time
 enq: MO - contention waits 0.000000% of the time
 MMON (Lite) shutdown waits 0.000000% of the time
 MMON slave messages waits 0.000000% of the time
 enq: AF - task serialization waits 0.000000% of the time
 enq: SH - contention waits 0.000000% of the time
 enq: TB - SQL Tuning Base Cache Load waits 0.000000% of the time
 enq: TB - SQL Tuning Base Cache Update waits 0.000000% of the time
 AWR Metric Capture waits 0.000000% of the time
 AWR Flush waits 0.000000% of the time
 enq: MW - contention waits 0.000000% of the time
 enq: FU - contention waits 0.000000% of the time
 enq: WP - contention waits 0.000000% of the time
 ASM: OFS Cluster membership update waits 0.000000% of the time
 enq: AV - add/enable first volume in DG waits 0.000000% of the time
 enq: AV - AVD client registration waits 0.000000% of the time
 enq: AV - volume relocate waits 0.000000% of the time
 enq: AV - persistent DG number waits 0.000000% of the time
 ASM DG Unblock waits 0.000000% of the time
 ASM Volume Background waits 0.000000% of the time
 enq: KQ - access ASM attribute waits 0.000000% of the time
 lock close waits 0.000000% of the time
 no free locks waits 0.000000% of the time
 kfcl: instance recovery waits 0.000000% of the time
 GCS recovery lock convert waits 0.000000% of the time
 GCS recovery lock open waits 0.000000% of the time
 GCS lock esc waits 0.000000% of the time
 GCS lock esc X waits 0.000000% of the time
 GCS lock cvt X waits 0.000000% of the time
 GCS lock cvt S waits 0.000000% of the time
 GCS lock open waits 0.000000% of the time
 GCS lock open X waits 0.000000% of the time
 GCS lock open S waits 0.000000% of the time
 GCS lock cancel waits 0.000000% of the time
 dma prepare busy waits 0.000000% of the time
 lock release pending waits 0.000000% of the time
 global cache busy waits 0.000000% of the time
 ASM PST operation waits 0.000000% of the time
 enq: PT - contention waits 0.000000% of the time
 ASM: MARK subscribe to msg channel waits 0.000000% of the time
 enq: RB - contention waits 0.000000% of the time
 rollback operations active waits 0.000000% of the time
 rollback operations block full waits 0.000000% of the time
 enq: FX - issue ACD Xtnt Relocation CIC waits 0.000000% of the time
 enq: FC - recover an ACD thread waits 0.000000% of the time
 enq: FC - open an ACD thread waits 0.000000% of the time
 enq: FT - disable LGWR writes waits 0.000000% of the time
 enq: FT - allow LGWR writes waits 0.000000% of the time
 enq: FG - LGWR redo generation enq race waits 0.000000% of the time
 enq: FG - FG redo generation enq race waits 0.000000% of the time
 enq: FG - serialize ACD relocate waits 0.000000% of the time
 enq: FR - recover the thread waits 0.000000% of the time
 enq: FR - use the thread waits 0.000000% of the time
 enq: FR - contention waits 0.000000% of the time
 checkpoint advanced waits 0.000000% of the time
 log write(even) waits 0.000000% of the time
 log write(odd) waits 0.000000% of the time
 enq: RX - unlock extent waits 0.000000% of the time
 enq: RX - relocate extent waits 0.000000% of the time
 enq: FA - access file waits 0.000000% of the time
 ASM network foreground exits waits 0.000000% of the time
 ASM db client exists waits 0.000000% of the time
 ASM background starting waits 0.000000% of the time
 Cluster Suspension wait waits 0.000000% of the time
 Cluster stabilization wait waits 0.000000% of the time
 enq: DN - contention waits 0.000000% of the time
 enq: HD - contention waits 0.000000% of the time
 enq: DD - contention waits 0.000000% of the time
 enq: DG - contention waits 0.000000% of the time
 Sync ASM rebalance waits 0.000000% of the time
 enq: XL - fault extent map waits 0.000000% of the time
 extent map load/unlock waits 0.000000% of the time
 enq: DO - disk online operation waits 0.000000% of the time
 enq: DO - startup of MARK process waits 0.000000% of the time
 enq: DO - Staleness Registry create waits 0.000000% of the time
 enq: DO - disk online recovery waits 0.000000% of the time
 enq: DO - disk online waits 0.000000% of the time
 enq: AD - relocate AU waits 0.000000% of the time
 enq: AD - deallocate AU waits 0.000000% of the time
 enq: AD - allocate AU waits 0.000000% of the time
 enq: XQ - purification waits 0.000000% of the time
 enq: XQ - relocation waits 0.000000% of the time
 enq: XQ - recovery waits 0.000000% of the time
 enq: CM - diskgroup dismount waits 0.000000% of the time
 enq: CM - instance waits 0.000000% of the time
 enq: CM - gate waits 0.000000% of the time
 ASM metadata cache frozen waits 0.000000% of the time
 buffer dirty disabled waits 0.000000% of the time
 buffer invalidation wait waits 0.000000% of the time
 buffer write wait waits 0.000000% of the time
 no free buffers waits 0.000000% of the time
 buffer writeList full waits 0.000000% of the time
 buffer rememberlist busy waits 0.000000% of the time
 buffer freelistbusy waits 0.000000% of the time
 buffer busy waits 0.000000% of the time
 ASM Instance startup waits 0.000000% of the time
 ASM internal hang test waits 0.000000% of the time
 enq: AM - ASM Grow ACD waits 0.000000% of the time
 enq: AM - ASM file relocation waits 0.000000% of the time
 enq: AM - ASM file descriptor waits 0.000000% of the time
 enq: AM - ASM disk based alloc/dealloc waits 0.000000% of the time
 enq: AM - block repair waits 0.000000% of the time
 enq: AM - ASM reserved waits 0.000000% of the time
 enq: AM - disk offline waits 0.000000% of the time
 enq: AM - ASM Amdu Dump waits 0.000000% of the time
 enq: AM - ASM Password File Update waits 0.000000% of the time
 enq: AM - ASM User waits 0.000000% of the time
 enq: AM - ASM File Destroy waits 0.000000% of the time
 enq: AM - group block waits 0.000000% of the time
 enq: AM - group use waits 0.000000% of the time
 enq: AM - ASM ACD Relocation waits 0.000000% of the time
 enq: AM - ASM cache freeze waits 0.000000% of the time
 enq: AM - background COD reservation waits 0.000000% of the time
 enq: AM - rollback COD reservation waits 0.000000% of the time
 enq: AM - shutdown waits 0.000000% of the time
 enq: AM - client registration waits 0.000000% of the time
 enq: OQ - xsoqhistrecb waits 0.000000% of the time
 enq: OQ - xsoqhiClose waits 0.000000% of the time
 enq: OQ - xsoq*histrecb waits 0.000000% of the time
 enq: OQ - xsoqhiFlush waits 0.000000% of the time
 enq: OQ - xsoqhiAlloc waits 0.000000% of the time
 enq: AO - contention waits 0.000000% of the time
 enq: AG - contention waits 0.000000% of the time
 enq: AW - AW generation lock waits 0.000000% of the time
 enq: AW - user access for AW waits 0.000000% of the time
 enq: AW - AW state lock waits 0.000000% of the time
 enq: AW - AW$ table lock waits 0.000000% of the time
 OLAP Aggregate Client Deq waits 0.000000% of the time
 OLAP Aggregate Master Deq waits 0.000000% of the time
 OLAP Aggregate Client Enq waits 0.000000% of the time
 OLAP Aggregate Master Enq waits 0.000000% of the time
 OLAP Null PQ Reason waits 0.000000% of the time
 OLAP Parallel Temp Grew waits 0.000000% of the time
 OLAP Parallel Temp Grow Wait waits 0.000000% of the time
 OLAP Parallel Temp Grow Request waits 0.000000% of the time
 OLAP Parallel Type Deq waits 0.000000% of the time
 PX Deq: OLAP Update Close waits 0.000000% of the time
 PX Deq: OLAP Update Execute waits 0.000000% of the time
 PX Deq: OLAP Update Reply waits 0.000000% of the time
 enq: RF - FSFO Primary Shutdown suspended waits 0.000000% of the time
 enq: RF - DG Broker Current File ID waits 0.000000% of the time
 enq: RF - FSFO Observer Heartbeat waits 0.000000% of the time
 enq: RF - RF - Database Automatic Disable waits 0.000000% of the time
 enq: RF - synchronization: critical ai waits 0.000000% of the time
 enq: RF - new AI waits 0.000000% of the time
 enq: RF - synchronization: aifo master waits 0.000000% of the time
 enq: RF - atomicity waits 0.000000% of the time
 enq: RF - synch: DG Broker metadata waits 0.000000% of the time
 Data Guard Broker Wait waits 0.000000% of the time
 NFS read delegation outstanding waits 0.000000% of the time
 enq: XC - XDB Configuration waits 0.000000% of the time
 XDB SGA initialization waits 0.000000% of the time
 enq: OT - TEXT: Generic Lock waits 0.000000% of the time
 enq: CX - TEXT: Index Specific Lock waits 0.000000% of the time
 queue slave messages waits 0.000000% of the time
 AQ spill debug idle waits 0.000000% of the time
 Streams AQ: QueueTable kgl locks waits 0.000000% of the time
 enq: WA - contention waits 0.000000% of the time
 enq: XH - contention waits 0.000000% of the time
 enq: SF - contention waits 0.000000% of the time
 enq: PH - contention waits 0.000000% of the time
 enq: ML - contention waits 0.000000% of the time
 enq: MH - contention waits 0.000000% of the time
 enq: DP - contention waits 0.000000% of the time
 AQ propagation connection waits 0.000000% of the time
 enq: TQ - DDL-INI contention waits 0.000000% of the time
 enq: TQ - INI contention waits 0.000000% of the time
 enq: TQ - DDL contention waits 0.000000% of the time
 enq: TQ - TM contention waits 0.000000% of the time
 Streams AQ: waiting for busy instance for instance_name waits 0.000000% of the time
 tsm with timeout waits 0.000000% of the time
 enq: SE - contention waits 0.000000% of the time
 EMON slave messages waits 0.000000% of the time
 EMON termination waits 0.000000% of the time
 wait for EMON to spawn waits 0.000000% of the time
 Streams AQ: emn coordinator waiting for slave to start waits 0.000000% of the time
 enq: CQ - contention waits 0.000000% of the time
 kpodplck wait before retrying ORA-54 waits 0.000000% of the time
 opishd waits 0.000000% of the time
 enq: AT - contention waits 0.000000% of the time
 qerex_gdml waits 0.000000% of the time
 enq: JI - contention waits 0.000000% of the time
 enq: IA - contention waits 0.000000% of the time
 Streams: RAC waiting for inter instance ack waits 0.000000% of the time
 enq: SI - contention waits 0.000000% of the time
 Streams miscellaneous event waits 0.000000% of the time
 Streams capture: waiting for database startup waits 0.000000% of the time
 enq: SR - contention waits 0.000000% of the time
 knpsmai waits 0.000000% of the time
 knpc_anq_AwaitNonemptyQueue waits 0.000000% of the time
 knpc_acwm_AwaitChangedWaterMark waits 0.000000% of the time
 Replication Dequeue  waits 0.000000% of the time
 enq: KP - contention waits 0.000000% of the time
 Data Pump slave init waits 0.000000% of the time
 Data Pump slave startup waits 0.000000% of the time
 Kupp process shutdown waits 0.000000% of the time
 kupp process wait waits 0.000000% of the time
 scginq AST call waits 0.000000% of the time
 timer in sksawat waits 0.000000% of the time
 enq: RD - RAC load waits 0.000000% of the time
 enq: BF - PMON Join Filter cleanup waits 0.000000% of the time
 enq: BF - allocation contention waits 0.000000% of the time
 enq: IT - contention waits 0.000000% of the time
 enq: TO - contention waits 0.000000% of the time
 enq: AY - contention waits 0.000000% of the time
 latch: PX hash array latch waits 0.000000% of the time
 PX hash elem being inserted waits 0.000000% of the time
 GV$: slave acquisition retry wait time waits 0.000000% of the time
 PX Deq: Table Q Close waits 0.000000% of the time
 kxfxsp waits 0.000000% of the time
 kxfxse waits 0.000000% of the time
 enq: PI - contention waits 0.000000% of the time
 PX Deq: Slave Join Frag waits 0.000000% of the time
 PX Deq Credit: Session Stats waits 0.000000% of the time
 PX Nsq: PQ load info query waits 0.000000% of the time
 PX Nsq: PQ descriptor query waits 0.000000% of the time
 PX Deq: reap credit waits 0.000000% of the time
 PX Deq: Test for credit waits 0.000000% of the time
 PX Deq: Test for msg waits 0.000000% of the time
 PX signal server waits 0.000000% of the time
 PX create server waits 0.000000% of the time
 PX server shutdown waits 0.000000% of the time
 PX Send Wait waits 0.000000% of the time
 PX slave release waits 0.000000% of the time
 PX slave connection waits 0.000000% of the time
 enq: AP - contention waits 0.000000% of the time
 wait for kkpo ref-partitioning *TEST EVENT* waits 0.000000% of the time
 enq: MS - contention waits 0.000000% of the time
 enq: MD - contention waits 0.000000% of the time
 SUPLOG PL wait for inflight pragma-d PL/SQL waits 0.000000% of the time
 wait active processes waits 0.000000% of the time
 optimizer stats update retry waits 0.000000% of the time
 kkshgnc reloop waits 0.000000% of the time
 enq: OD - Serializing DDLs waits 0.000000% of the time
 enq: JQ - contention waits 0.000000% of the time
 enq: JD - contention waits 0.000000% of the time
 job scheduler coordinator slave wait waits 0.000000% of the time
 jobq slave TJ process wait waits 0.000000% of the time
 jobq slave shutdown wait waits 0.000000% of the time
 select wait waits 0.000000% of the time
 OJVM: Generic waits 0.000000% of the time
 listen endpoint status waits 0.000000% of the time
 latch: virtual circuit queues waits 0.000000% of the time
 listener registration dump waits 0.000000% of the time
 dispatcher shutdown waits 0.000000% of the time
 CRS call completion waits 0.000000% of the time
 GPnP Get Error waits 0.000000% of the time
 GPnP Set Item waits 0.000000% of the time
 GPnP Get Item waits 0.000000% of the time
 GPnP Termination waits 0.000000% of the time
 GPnP Initialization waits 0.000000% of the time
 GIPC operation: dump waits 0.000000% of the time
 CSS operation: diagnostic waits 0.000000% of the time
 CSS operation: action waits 0.000000% of the time
 CSS operation: query waits 0.000000% of the time
 CSS Xgrp shared operation waits 0.000000% of the time
 CSS operation: data update waits 0.000000% of the time
 CSS operation: data query waits 0.000000% of the time
 CSS group membership query waits 0.000000% of the time
 CSS group registration waits 0.000000% of the time
 ASM cluster file access waits 0.000000% of the time
 xdb schema cache initialization waits 0.000000% of the time
 resmgr:internal state cleanup waits 0.000000% of the time
 waiting to get RM CAS latch waits 0.000000% of the time
 waiting to get CAS latch waits 0.000000% of the time
 BFILE internal seek waits 0.000000% of the time
 BFILE open waits 0.000000% of the time
 BFILE get path object waits 0.000000% of the time
 BFILE get name object waits 0.000000% of the time
 BFILE get length waits 0.000000% of the time
 BFILE check if open waits 0.000000% of the time
 BFILE check if exists waits 0.000000% of the time
 BFILE closure waits 0.000000% of the time
 library cache shutdown waits 0.000000% of the time
 library cache revalidation waits 0.000000% of the time
 free global transaction table entry waits 0.000000% of the time
 pending global transaction(s) waits 0.000000% of the time
 enq: DR - contention waits 0.000000% of the time
 enq: DX - contention waits 0.000000% of the time
 enq: ZF - add fga audit table partition waits 0.000000% of the time
 enq: ZA - add std audit table partition waits 0.000000% of the time
 enq: ZZ - update hash tables waits 0.000000% of the time
 enq: RL - RAC wallet lock waits 0.000000% of the time
 enq: RK - set key waits 0.000000% of the time
 enq: OW - termination waits 0.000000% of the time
 enq: OW - initialization waits 0.000000% of the time
 enq: MK - contention waits 0.000000% of the time
 enq: CL - compare labels waits 0.000000% of the time
 enq: CL - drop label waits 0.000000% of the time
 enq: IL - contention waits 0.000000% of the time
 enq: PF - contention waits 0.000000% of the time
 enq: AE - lock waits 0.000000% of the time
 kksscl hash split waits 0.000000% of the time
 kksfbc research waits 0.000000% of the time
 kgltwait waits 0.000000% of the time
 kkdlhpon waits 0.000000% of the time
 kkdlsipon waits 0.000000% of the time
 kkdlgon waits 0.000000% of the time
 enq: OL - contention waits 0.000000% of the time
 enq: OC - contention waits 0.000000% of the time
 enq: RW - MV metadata contention waits 0.000000% of the time
 enq: TP - contention waits 0.000000% of the time
 enq: SO - contention waits 0.000000% of the time
 enq: DV - contention waits 0.000000% of the time
 row cache process waits 0.000000% of the time
 row cache cleanup waits 0.000000% of the time
 Compression analysis waits 0.000000% of the time
 enq: ZH - compression analysis waits 0.000000% of the time
 enq: SL - get lock for undo waits 0.000000% of the time
 enq: SL - escalate lock waits 0.000000% of the time
 enq: SL - get lock waits 0.000000% of the time
 enq: WG - delete fso waits 0.000000% of the time
 enq: HP - contention waits 0.000000% of the time
 enq: HQ - contention waits 0.000000% of the time
 enq: DL - contention waits 0.000000% of the time
 kdic_do_merge waits 0.000000% of the time
 dupl. cluster key waits 0.000000% of the time
 kdblil wait before retrying ORA-54 waits 0.000000% of the time
 index block split waits 0.000000% of the time
 Space Manager: slave messages waits 0.000000% of the time
 enq: SJ - Slave Task Cancel waits 0.000000% of the time
 ktm: instance recovery waits 0.000000% of the time
 enq: TT - contention waits 0.000000% of the time
 enq: SU - contention waits 0.000000% of the time
 enq: DW - contention waits 0.000000% of the time
 enq: SK - contention waits 0.000000% of the time
 enq: TS - contention waits 0.000000% of the time
 enq: DT - contention waits 0.000000% of the time
 ktfbtgex waits 0.000000% of the time
 ktsambl waits 0.000000% of the time
 kttm2d waits 0.000000% of the time
 Wait for TT enqueue waits 0.000000% of the time
 Wait for shrink lock waits 0.000000% of the time
 Wait for shrink lock2 waits 0.000000% of the time
 latch: lob segment dispenser latch waits 0.000000% of the time
 latch: lob segment query latch waits 0.000000% of the time
 latch: lob segment hash table latch waits 0.000000% of the time
 enq: TF - contention waits 0.000000% of the time
 latch: change notification client cache latch waits 0.000000% of the time
 enq: FE - contention waits 0.000000% of the time
 enq: CO - master slave det waits 0.000000% of the time
 latch: Change Notification Hash table latch waits 0.000000% of the time
 enq: CN - race with init waits 0.000000% of the time
 enq: CN - race with reg waits 0.000000% of the time
 enq: CN - race with txn waits 0.000000% of the time
 enq: TE - KTF broadcast waits 0.000000% of the time
 enq: TD - KTF dump entries waits 0.000000% of the time
 enq: IM - contention for blr waits 0.000000% of the time
 wait for a paralle reco to abort waits 0.000000% of the time
 wait for a undo record waits 0.000000% of the time
 wait for stopper event to be increased waits 0.000000% of the time
 undo_retention publish retry waits 0.000000% of the time
 wait for another txn - rollback to savepoint waits 0.000000% of the time
 wait for another txn - txn abort waits 0.000000% of the time
 wait for another txn - undo rcv abort waits 0.000000% of the time
 wait for change waits 0.000000% of the time
 unbound tx waits 0.000000% of the time
 undo segment recovery waits 0.000000% of the time
 test long ops waits 0.000000% of the time
 PMON to cleanup detached branches at shutdown waits 0.000000% of the time
 PMON to cleanup pseudo-branches at svc stop time waits 0.000000% of the time
 txn to complete waits 0.000000% of the time
 inactive transaction branch waits 0.000000% of the time
 transaction waits 0.000000% of the time
 blocking txn id for DDL waits 0.000000% of the time
 datafile move cleanup during resize waits 0.000000% of the time
 cell smart flash unkeep waits 0.000000% of the time
 parallel recovery change buffer free waits 0.000000% of the time
 parallel recovery read buffer free waits 0.000000% of the time
 RVWR wait for flashback copy waits 0.000000% of the time
 flashback log switch waits 0.000000% of the time
 flashback free VI log waits 0.000000% of the time
 enq: FD - Flashback logical operations waits 0.000000% of the time
 enq: FD - Restore point create/drop waits 0.000000% of the time
 enq: FD - Flashback on/off waits 0.000000% of the time
 enq: FD - Flashback coordinator waits 0.000000% of the time
 enq: FD - Tablespace flashback on/off waits 0.000000% of the time
 enq: FD - Marker generation waits 0.000000% of the time
 enq: FL - Flashback db command waits 0.000000% of the time
 enq: FL - Flashback database log waits 0.000000% of the time
 enq: RS - write alert level waits 0.000000% of the time
 enq: RS - read alert level waits 0.000000% of the time
 enq: RS - persist alert level waits 0.000000% of the time
 enq: RS - prevent aging list update waits 0.000000% of the time
 enq: RS - prevent file delete waits 0.000000% of the time
 enq: RS - record reuse waits 0.000000% of the time
 enq: RS - file delete waits 0.000000% of the time
 recovery area: computing applied logs waits 0.000000% of the time
 recovery area: computing backed up files waits 0.000000% of the time
 recovery area: computing obsolete files waits 0.000000% of the time
 recovery area: computing dropped files waits 0.000000% of the time
 enq: CT - reading waits 0.000000% of the time
 enq: CT - CTWR process start/stop waits 0.000000% of the time
 enq: CT - state change gate 2 waits 0.000000% of the time
 enq: CT - state change gate 1 waits 0.000000% of the time
 enq: CT - state waits 0.000000% of the time
 enq: CT - change stream ownership waits 0.000000% of the time
 enq: CT - local space management waits 0.000000% of the time
 enq: CT - global space management waits 0.000000% of the time
 CTWR media recovery checkpoint request waits 0.000000% of the time
 block change tracking buffer space waits 0.000000% of the time
 change tracking file parallel write waits 0.000000% of the time
 change tracking file synchronous write waits 0.000000% of the time
 change tracking file synchronous read waits 0.000000% of the time
 standby query scn advance waits 0.000000% of the time
 enq: XR - database force logging waits 0.000000% of the time
 enq: XR - quiesce database waits 0.000000% of the time
 Resolution of in-doubt txns waits 0.000000% of the time
 Logical Standby Debug waits 0.000000% of the time
 Logical Standby Terminal Apply waits 0.000000% of the time
 Logical Standby dictionary build waits 0.000000% of the time
 Logical Standby pin transaction waits 0.000000% of the time
 Logical Standby Apply shutdown waits 0.000000% of the time
 enq: SB - table instantiation waits 0.000000% of the time
 enq: SB - logical standby metadata waits 0.000000% of the time
 enq: PL - contention waits 0.000000% of the time
 enq: MN - contention waits 0.000000% of the time
 Auto BMR RPC standby catchup waits 0.000000% of the time
 Auto BMR completion waits 0.000000% of the time
 enq: AB - ABMR process initialized waits 0.000000% of the time
 enq: AB - ABMR process start/stop waits 0.000000% of the time
 Backup Restore Event 19778 sleep waits 0.000000% of the time
 Backup Restore Switch Bitmap sleep waits 0.000000% of the time
 Backup Restore Throttle sleep waits 0.000000% of the time
 enq: ID - contention waits 0.000000% of the time
 enq: BR - perform autobackup waits 0.000000% of the time
 enq: BR - request autobackup waits 0.000000% of the time
 enq: BR - space info datafile hdr update waits 0.000000% of the time
 enq: BR - multi-section restore section waits 0.000000% of the time
 enq: BR - multi-section restore header waits 0.000000% of the time
 enq: BR - proxy-copy waits 0.000000% of the time
 enq: BR - file shrink waits 0.000000% of the time
 parallel recovery slave wait for change waits 0.000000% of the time
 parallel recovery coord send blocked waits 0.000000% of the time
 parallel recovery coord wait for reply waits 0.000000% of the time
 shutdown after switchover to standby waits 0.000000% of the time
 enq: MR - standby role transition waits 0.000000% of the time
 enq: MR - contention waits 0.000000% of the time
 enq: IR - contention2 waits 0.000000% of the time
 enq: IR - contention waits 0.000000% of the time
 enq: RT - thread internal enable/disable waits 0.000000% of the time
 enq: RT - contention waits 0.000000% of the time
 latch: gc element waits 0.000000% of the time
 enq: RP - contention waits 0.000000% of the time
 enq: DM - contention waits 0.000000% of the time
 enq: FS - contention waits 0.000000% of the time
 enq: IS - contention waits 0.000000% of the time
 enq: DF - contention waits 0.000000% of the time
 DFS db file lock waits 0.000000% of the time
 enq: RN - contention waits 0.000000% of the time
 enq: WL - contention waits 0.000000% of the time
 log file switch (clearing log file) waits 0.000000% of the time
 enq: WR - contention waits 0.000000% of the time
 LGWR-LNS wait on channel waits 0.000000% of the time
 Data Guard: process clean up waits 0.000000% of the time
 Data Guard: process exit waits 0.000000% of the time
 ARCH wait for process death 1 waits 0.000000% of the time
 ARCH wait for process start 1 waits 0.000000% of the time
 Data Guard: RFS disk I/O waits 0.000000% of the time
 LNS simulation latency wait waits 0.000000% of the time
 LGWR simulation latency wait waits 0.000000% of the time
 enq: WL - RFS global state contention waits 0.000000% of the time
 RFS dispatch waits 0.000000% of the time
 RFS ping waits 0.000000% of the time
 RFS detach waits 0.000000% of the time
 RFS register waits 0.000000% of the time
 RFS announce waits 0.000000% of the time
 RFS close waits 0.000000% of the time
 RFS create waits 0.000000% of the time
 RFS attach waits 0.000000% of the time
 ARCH wait on c/f tx acquire 1 waits 0.000000% of the time
 log switch/archive waits 0.000000% of the time
 MRP wait on archivelog archival waits 0.000000% of the time
 MRP wait on archivelog arrival waits 0.000000% of the time
 MRP state inspection waits 0.000000% of the time
 MRP termination waits 0.000000% of the time
 MRP inactivation waits 0.000000% of the time
 MRP wait on startup clear waits 0.000000% of the time
 MRP wait on process restart waits 0.000000% of the time
 MRP wait on process start waits 0.000000% of the time
 LGWR ORL/NoExp FAL archival waits 0.000000% of the time
 enq: WL - RAC-wide SGA contention waits 0.000000% of the time
 RSGA: RAC reconfiguration waits 0.000000% of the time
 TEST: action hang waits 0.000000% of the time
 TEST: action sync waits 0.000000% of the time
 FAL archive wait 1 sec for REOPEN minimum waits 0.000000% of the time
 enq: WL - Test access/locking waits 0.000000% of the time
 Wait on stby instance close waits 0.000000% of the time
 wait for MTTR advisory state object waits 0.000000% of the time
 TSE SSO wallet reopen waits 0.000000% of the time
 TSE master key rekey waits 0.000000% of the time
 recovery buffer pinned waits 0.000000% of the time
 prewarm transfer retry waits 0.000000% of the time
 lock deadlock retry waits 0.000000% of the time
 lock escalate retry waits 0.000000% of the time
 writes stopped by instance recovery or database suspension waits 0.000000% of the time
 cr request retry waits 0.000000% of the time
 buffer latch waits 0.000000% of the time
 kcbzps waits 0.000000% of the time
 enq: PW - perwarm status in dbw0 waits 0.000000% of the time
 buffer resize waits 0.000000% of the time
 buffer exterminate waits 0.000000% of the time
 enq: TC - contention2 waits 0.000000% of the time
 enq: DS - contention waits 0.000000% of the time
 enq: SW - contention waits 0.000000% of the time
 control file diagnostic dump waits 0.000000% of the time
 SGA: sga_target resize waits 0.000000% of the time
 SGA: allocation forcing component growth waits 0.000000% of the time
 GCR member Data from CSS  waits 0.000000% of the time
 GCR CSS join retry waits 0.000000% of the time
 GCR lock acquisition waits 0.000000% of the time
 GCR ctx lock acquisition waits 0.000000% of the time
 gcs log flush sync waits 0.000000% of the time
 gcs to be enabled waits 0.000000% of the time
 CGS skgxn join retry waits 0.000000% of the time
 IMR CSS join retry waits 0.000000% of the time
 IMR membership resolution waits 0.000000% of the time
 IMR rr update waits 0.000000% of the time
 IMR net-check message ack waits 0.000000% of the time
 IMR rr lock release waits 0.000000% of the time
 IMR disk votes waits 0.000000% of the time
 IMR mount phase II completion waits 0.000000% of the time
 kjxgrtest waits 0.000000% of the time
 CGS wait for IPC msg waits 0.000000% of the time
 name-service call wait waits 0.000000% of the time
 lms flush message acks waits 0.000000% of the time
 wait for scn ack waits 0.000000% of the time
 wait for assert messages to be sent waits 0.000000% of the time
 wait for verification ack waits 0.000000% of the time
 wait for sync ack waits 0.000000% of the time
 affinity expansion in replay waits 0.000000% of the time
 latch: gcs resource hash waits 0.000000% of the time
 gcs domain validation waits 0.000000% of the time
 kjctcisnd: Queue/Send client message waits 0.000000% of the time
 kjctssqmg: quick message send wait waits 0.000000% of the time
 ges message buffer allocation waits 0.000000% of the time
 KJC: Wait for msg sends to complete waits 0.000000% of the time
 ack for a broadcasted res from a remote instance waits 0.000000% of the time
 recovery instance recovery completion  waits 0.000000% of the time
 gcs lmon dirtydetach step completion waits 0.000000% of the time
 global plug and play automatic resource creation waits 0.000000% of the time
 ges/gcs diag dump waits 0.000000% of the time
 ges DFS hang analysis phase 2 acks waits 0.000000% of the time
 readable standby redo apply remastering waits 0.000000% of the time
 ges RMS0 retry add redo log waits 0.000000% of the time
 kjbdrmcvtq lmon drm quiesce: ping completion waits 0.000000% of the time
 kjbdomalc allocate recovery domain - retry waits 0.000000% of the time
 kjudomdet wait for recovery domain detach waits 0.000000% of the time
 kjudomatt wait for recovery domain attach waits 0.000000% of the time
 kjbopen wait for recovery domain attach waits 0.000000% of the time
 ges performance test completion waits 0.000000% of the time
 ges LMD suspend for testing event waits 0.000000% of the time
 ges LMON for send queues waits 0.000000% of the time
 ges reusing os pid waits 0.000000% of the time
 ges inquiry response waits 0.000000% of the time
 ges2 LMON to wake up lms - mrcvr 3 waits 0.000000% of the time
 ges2 LMON to wake up lms - mrcvr 2 waits 0.000000% of the time
 ges2 LMON to wake up LMD - mrcvr waits 0.000000% of the time
 ges1 LMON to wake up LMD - mrcvr waits 0.000000% of the time
 ges LMON to get to FTDONE  waits 0.000000% of the time
 ges master to get established for SCN op waits 0.000000% of the time
 ges resource cleanout during enqueue open-cvt waits 0.000000% of the time
 ges resource cleanout during enqueue open waits 0.000000% of the time
 ges cancel waits 0.000000% of the time
 gcs ddet enter server mode waits 0.000000% of the time
 gcs drm freeze in enter server mode waits 0.000000% of the time
 gcs enter server mode waits 0.000000% of the time
 ges enter server mode waits 0.000000% of the time
 ges user error waits 0.000000% of the time
 ges process with outstanding i/o waits 0.000000% of the time
 ges retry query node waits 0.000000% of the time
 ges generic event waits 0.000000% of the time
 ges cached resource cleanup waits 0.000000% of the time
 gcs remastering wait for read latch waits 0.000000% of the time
 gcs retry nowait latch get waits 0.000000% of the time
 gcs drm freeze begin waits 0.000000% of the time
 ges lmd and pmon to attach waits 0.000000% of the time
 ges pmon to exit waits 0.000000% of the time
 ges LMON to join CGS group waits 0.000000% of the time
 ges lms sync during dynamic remastering and reconfig waits 0.000000% of the time
 ges lmd/lmses to unfreeze in rcfg waits 0.000000% of the time
 ges lmd/lmses to freeze in rcfg waits 0.000000% of the time
 ges2 proc latch in rm latch get 2 waits 0.000000% of the time
 ges2 proc latch in rm latch get 1 waits 0.000000% of the time
 ges yield cpu in reconfig waits 0.000000% of the time
 wait for master scn waits 0.000000% of the time
 ges cgs registration waits 0.000000% of the time
 ges wait for lmon to be ready waits 0.000000% of the time
 ges lmd sync during reconfig waits 0.000000% of the time
 ges LMD to inherit communication channels waits 0.000000% of the time
 gcs resource directory to be unfrozen waits 0.000000% of the time
 ges resource directory to be unfrozen waits 0.000000% of the time
 ges global resource directory to be frozen waits 0.000000% of the time
 ges client process to exit waits 0.000000% of the time
 ges LMD to shutdown waits 0.000000% of the time
 DFS lock handle waits 0.000000% of the time
 latch: ges resource hash list waits 0.000000% of the time
 pmon dblkr tst event waits 0.000000% of the time
 DSKM to complete cell health check waits 0.000000% of the time
 master diskmon read waits 0.000000% of the time
 master diskmon startup waits 0.000000% of the time
 enq: MX - sync storage server info waits 0.000000% of the time
 process diagnostic dump waits 0.000000% of the time
 LMON global data update waits 0.000000% of the time
 rolling migration: cluster quiesce waits 0.000000% of the time
 enq: RU - waiting waits 0.000000% of the time
 enq: RU - contention waits 0.000000% of the time
 cleanup of aborted process waits 0.000000% of the time
 enq: PD - contention waits 0.000000% of the time
 enq: AS - service activation waits 0.000000% of the time
 set director factor wait waits 0.000000% of the time
 enq: XY - contention waits 0.000000% of the time
 enq: FM - contention waits 0.000000% of the time
 wait for FMON to come up waits 0.000000% of the time
 DBMS_LDAP: LDAP operation  waits 0.000000% of the time
 ksxr wait for mount shared waits 0.000000% of the time
 ksxr poll remote instances waits 0.000000% of the time
 IPC waiting for OSD resources waits 0.000000% of the time
 IPC busy async request waits 0.000000% of the time
 IPC wait for name service busy waits 0.000000% of the time
 OSD IPC library waits 0.000000% of the time
 IPC send completion sync waits 0.000000% of the time
 enq: SP - contention 4 waits 0.000000% of the time
 enq: SP - contention 3 waits 0.000000% of the time
 enq: SP - contention 2 waits 0.000000% of the time
 enq: SP - contention 1 waits 0.000000% of the time
 enq: PV - syncshut waits 0.000000% of the time
 ksv slave avail wait waits 0.000000% of the time
 master exit waits 0.000000% of the time
 broadcast mesg recovery queue transition waits 0.000000% of the time
 broadcast mesg queue transition waits 0.000000% of the time
 enq: KD - determine DBRM master waits 0.000000% of the time
 enq: CA - contention waits 0.000000% of the time
 enq: KT - contention waits 0.000000% of the time
 enq: KM - contention waits 0.000000% of the time
 slave exit waits 0.000000% of the time
 imm op waits 0.000000% of the time
 enq: BM - clonedb bitmap file write waits 0.000000% of the time
 enq: RE - block repair contention waits 0.000000% of the time
 enq: FP - global fob contention waits 0.000000% of the time
 rdbms ipc message block waits 0.000000% of the time
 prior spawner clean up waits 0.000000% of the time
 process shutdown waits 0.000000% of the time
 process startup waits 0.000000% of the time
 ksbcic waits 0.000000% of the time
 ksbsrv waits 0.000000% of the time
 enq: PG - contention waits 0.000000% of the time
 enq: PE - contention waits 0.000000% of the time
 ksdxexeotherwait waits 0.000000% of the time
 ksdxexeother waits 0.000000% of the time
 debugger command waits 0.000000% of the time
 ksim generic wait event waits 0.000000% of the time
 enq: PR - contention waits 0.000000% of the time
 enq: CI - contention waits 0.000000% of the time
 check CPU wait times waits 0.000000% of the time
 process terminate waits 0.000000% of the time
 inactive session waits 0.000000% of the time
 free process state object waits 0.000000% of the time
 global enqueue expand wait waits 0.000000% of the time
 wait list latch activity waits 0.000000% of the time
 latch activity waits 0.000000% of the time
 unspecified wait event waits 0.000000% of the time
 kslwait unit test event 3 waits 0.000000% of the time
 kslwait unit test event 2 waits 0.000000% of the time
 kslwait unit test event 1 waits 0.000000% of the time
 enq: WM - WLM Plan activation waits 0.000000% of the time
 events in waitclass Other waits 0.000000% of the time
 cell worker idle waits 0.000000% of the time
 cell manager discovering disks waits 0.000000% of the time
 cell manager closing cell waits 0.000000% of the time
 cell manager opening cell waits 0.000000% of the time
 cell list of blocks physical read waits 0.000000% of the time
 cell multiblock physical read waits 0.000000% of the time
 cell single block physical read waits 0.000000% of the time
 JS kill job wait waits 0.000000% of the time
 JS external job waits 0.000000% of the time
 JS kgl get object wait waits 0.000000% of the time
 WCR: replay paused waits 0.000000% of the time
 WCR: replay lock order waits 0.000000% of the time
 WCR: replay clock waits 0.000000% of the time
 WCR: replay client notify waits 0.000000% of the time
 auto-sqltune: wait graph update waits 0.000000% of the time
 ASM Staleness File I/O waits 0.000000% of the time
 lock remastering waits 0.000000% of the time
 ASM PST query : wait for [PM][grp][0] grant waits 0.000000% of the time
 ASM mount : wait for heartbeat waits 0.000000% of the time
 ASM Fixed Package I/O waits 0.000000% of the time
 ASM background timer waits 0.000000% of the time
 kfk: async disk IO waits 0.000000% of the time
 ASM COD rollback operation completion waits 0.000000% of the time
 OLAP DML Sleep waits 0.000000% of the time
 TEXT: File System I/O waits 0.000000% of the time
 TEXT: URL_DATASTORE network wait waits 0.000000% of the time
 HS message to agent waits 0.000000% of the time
 Streams AQ: RAC qmn coordinator idle wait waits 0.000000% of the time
 Streams AQ: deallocate messages from Streams Pool waits 0.000000% of the time
 Streams AQ: delete acknowledged messages waits 0.000000% of the time
 Streams AQ: enqueue blocked due to flow control waits 0.000000% of the time
 Streams AQ: enqueue blocked on low memory waits 0.000000% of the time
 EMON slave idle wait waits 0.000000% of the time
 Streams AQ: emn coordinator idle wait waits 0.000000% of the time
 wait for EMON to process ntfns waits 0.000000% of the time
 enq: UL - contention waits 0.000000% of the time
 External Procedure call waits 0.000000% of the time
 External Procedure initial connection waits 0.000000% of the time
 SQL*Net vector data from dblink waits 0.000000% of the time
 SQL*Net vector data to dblink waits 0.000000% of the time
 SQL*Net vector data from client waits 0.000000% of the time
 SQL*Net vector data to client waits 0.000000% of the time
 SQL*Net vector message from dblink waits 0.000000% of the time
 SQL*Net vector message from client waits 0.000000% of the time
 SQL*Net more data to dblink waits 0.000000% of the time
 enq: ZG - contention waits 0.000000% of the time
 Streams: apply reader waiting for DDL to apply waits 0.000000% of the time
 Streams capture: filter callback waiting for ruleset waits 0.000000% of the time
 Streams capture: waiting for archive log waits 0.000000% of the time
 Streams: waiting for messages waits 0.000000% of the time
 Streams: flow control waits 0.000000% of the time
 Streams apply: waiting for dependency waits 0.000000% of the time
 Streams apply: waiting to commit waits 0.000000% of the time
 Streams: resolve low memory condition waits 0.000000% of the time
 Streams capture: waiting for subscribers to catch up waits 0.000000% of the time
 Streams fetch slave: waiting for txns waits 0.000000% of the time
 dbverify reads waits 0.000000% of the time
 PX Queuing: statement queue waits 0.000000% of the time
 enq: JX - cleanup of  queue waits 0.000000% of the time
 enq: JX - SQL statement queue waits 0.000000% of the time
 enq: RC - Result Cache: Contention waits 0.000000% of the time
 external table misc IO waits 0.000000% of the time
 external table seek waits 0.000000% of the time
 external table open waits 0.000000% of the time
 external table write waits 0.000000% of the time
 external table read waits 0.000000% of the time
 PX Deque wait waits 0.000000% of the time
 enq: DB - contention waits 0.000000% of the time
 pipe put waits 0.000000% of the time
 pipe get waits 0.000000% of the time
 Wait for Table Lock waits 0.000000% of the time
 JOX Jit Process Sleep waits 0.000000% of the time
 connection pool wait waits 0.000000% of the time
 pool server timer waits 0.000000% of the time
 cmon timer waits 0.000000% of the time
 dedicated server timer waits 0.000000% of the time
 dispatcher listen timer waits 0.000000% of the time
 dispatcher timer waits 0.000000% of the time
 shared server idle wait waits 0.000000% of the time
 virtual circuit wait waits 0.000000% of the time
 alter system set dispatcher waits 0.000000% of the time
 utl_file I/O waits 0.000000% of the time
 TCP Socket (KGAS) waits 0.000000% of the time
 resmgr:pq queued waits 0.000000% of the time
 resmgr:become active waits 0.000000% of the time
 resmgr:sessions to exit waits 0.000000% of the time
 resmgr:small I/O queued waits 0.000000% of the time
 resmgr:large I/O queued waits 0.000000% of the time
 BFILE read waits 0.000000% of the time
 library cache: mutex S waits 0.000000% of the time
 library cache pin waits 0.000000% of the time
 enq: BB - 2PC across RAC instances waits 0.000000% of the time
 Global transaction acquire instance locks waits 0.000000% of the time
 cursor: pin X waits 0.000000% of the time
 libcache interrupt action by LCK waits 0.000000% of the time
 row cache read waits 0.000000% of the time
 enq: WG - lock fso waits 0.000000% of the time
 SecureFile mutex waits 0.000000% of the time
 securefile direct-write completion waits 0.000000% of the time
 securefile direct-read completion waits 0.000000% of the time
 index (re)build online merge waits 0.000000% of the time
 index (re)build online cleanup waits 0.000000% of the time
 index (re)build online start waits 0.000000% of the time
 PX Deq: kdcphc_ack waits 0.000000% of the time
 PX Deq: kdcph_mai waits 0.000000% of the time
 enq: HV - contention waits 0.000000% of the time
 PX Deq: Index Merge Close waits 0.000000% of the time
 PX Deq: Index Merge Execute waits 0.000000% of the time
 PX Deq: Index Merge Reply waits 0.000000% of the time
 PX Deq: Metadata Update waits 0.000000% of the time
 sort segment request waits 0.000000% of the time
 enq: SS - contention waits 0.000000% of the time
 securefile chain update waits 0.000000% of the time
 Archive Manager file transfer I/O waits 0.000000% of the time
 fbar timer waits 0.000000% of the time
 latch: MQL Tracking Latch waits 0.000000% of the time
 statement suspended
 wait error to be cleared waits 0.000000% of the time
 latch: Undo Hint Latch waits 0.000000% of the time
 PX Deq: Txn Recovery Reply waits 0.000000% of the time
 PX Deq: Txn Recovery Start waits 0.000000% of the time
 enq: TW - contention waits 0.000000% of the time
 alter rbs offline waits 0.000000% of the time
 switch undo - offline waits 0.000000% of the time
 undo segment tx slot waits 0.000000% of the time
 enq: ST - contention waits 0.000000% of the time
 datafile copy range completion waits 0.000000% of the time
 concurrent I/O completion waits 0.000000% of the time
 parallel recovery slave next change waits 0.000000% of the time
 cell smart restore from backup waits 0.000000% of the time
 cell smart file creation waits 0.000000% of the time
 cell smart incremental backup waits 0.000000% of the time
 cell statistics gather waits 0.000000% of the time
 cell smart index scan waits 0.000000% of the time
 cell smart table scan waits 0.000000% of the time
 parallel recovery control message reply waits 0.000000% of the time
 flashback log file sync waits 0.000000% of the time
 flashback buf free by RVWR waits 0.000000% of the time
 flashback log file read waits 0.000000% of the time
 flashback log file write waits 0.000000% of the time
 parallel recovery coordinator waits for slave cleanup waits 0.000000% of the time
 wait for possible quiesce finish waits 0.000000% of the time
 Logical Standby Apply Delay waits 0.000000% of the time
 LogMiner: internal waits 0.000000% of the time
 LogMiner: find session waits 0.000000% of the time
 LogMiner: reset waits 0.000000% of the time
 LogMiner: activate waits 0.000000% of the time
 LogMiner: other waits 0.000000% of the time
 LogMiner client: transaction waits 0.000000% of the time
 LogMiner reader: redo (idle) waits 0.000000% of the time
 LogMiner reader: log (idle) waits 0.000000% of the time
 LogMiner reader: buffer waits 0.000000% of the time
 LogMiner preparer: memory waits 0.000000% of the time
 LogMiner preparer: idle waits 0.000000% of the time
 LogMiner builder: branch waits 0.000000% of the time
 LogMiner builder: DDL waits 0.000000% of the time
 LogMiner builder: memory waits 0.000000% of the time
 LogMiner builder: idle waits 0.000000% of the time
 parallel recovery slave idle wait waits 0.000000% of the time
 gc cancel retry waits 0.000000% of the time
 gc claim waits 0.000000% of the time
 gc recovery quiesce waits 0.000000% of the time
 gc recovery free waits 0.000000% of the time
 gc domain validation waits 0.000000% of the time
 gc assume waits 0.000000% of the time
 gc cr cancel waits 0.000000% of the time
 gc current cancel waits 0.000000% of the time
 gc flushed buffer waits 0.000000% of the time
 gc recovery waits 0.000000% of the time
 gc object scan waits 0.000000% of the time
 gc quiesce waits 0.000000% of the time
 gc remaster waits 0.000000% of the time
 gc freelist waits 0.000000% of the time
 gc current grant unknown waits 0.000000% of the time
 gc current grant congested waits 0.000000% of the time
 gc current grant busy waits 0.000000% of the time
 gc current grant 2-way waits 0.000000% of the time
 gc cr disk read waits 0.000000% of the time
 gc cr grant unknown waits 0.000000% of the time
 gc cr grant congested waits 0.000000% of the time
 gc cr grant busy waits 0.000000% of the time
 gc cr grant 2-way waits 0.000000% of the time
 gc current block unknown waits 0.000000% of the time
 gc current split waits 0.000000% of the time
 gc current block lost waits 0.000000% of the time
 gc current retry waits 0.000000% of the time
 gc current block congested waits 0.000000% of the time
 gc current block busy waits 0.000000% of the time
 gc current block 3-way waits 0.000000% of the time
 gc current block 2-way waits 0.000000% of the time
 gc cr block unknown waits 0.000000% of the time
 gc cr block lost waits 0.000000% of the time
 gc cr failure waits 0.000000% of the time
 gc cr block congested waits 0.000000% of the time
 gc cr block busy waits 0.000000% of the time
 gc cr block 3-way waits 0.000000% of the time
 gc cr block 2-way waits 0.000000% of the time
 gc block recovery request waits 0.000000% of the time
 gc current multi block request waits 0.000000% of the time
 gc cr multi block request waits 0.000000% of the time
 gc cr disk request waits 0.000000% of the time
 gc cr request waits 0.000000% of the time
 gc current request waits 0.000000% of the time
 enq: MV - datafile move waits 0.000000% of the time
 LGWR real time apply sync waits 0.000000% of the time
 simulated log write delay waits 0.000000% of the time
 log file switch (archiving needed) waits 0.000000% of the time
 log file switch (checkpoint incomplete) waits 0.000000% of the time
 latch: redo copy waits 0.000000% of the time
 LNS ASYNC end of log waits 0.000000% of the time
 LNS ASYNC dest activation waits 0.000000% of the time
 LNS ASYNC archive log waits 0.000000% of the time
 ARCH wait for netserver detach waits 0.000000% of the time
 ARCH wait for flow-control waits 0.000000% of the time
 LGWR wait on LNS waits 0.000000% of the time
 LNS wait on LGWR waits 0.000000% of the time
 ARCH wait for netserver init 2 waits 0.000000% of the time
 ARCH wait on DETACH waits 0.000000% of the time
 ARCH wait on SENDREQ waits 0.000000% of the time
 LGWR wait on DETACH waits 0.000000% of the time
 LGWR wait on SENDREQ waits 0.000000% of the time
 LNS wait on DETACH waits 0.000000% of the time
 LNS wait on SENDREQ waits 0.000000% of the time
 LNS wait on ATTACH waits 0.000000% of the time
 ARCH wait for netserver start waits 0.000000% of the time
 ARCH wait on ATTACH waits 0.000000% of the time
 LGWR wait on ATTACH waits 0.000000% of the time
 ARCH wait for net re-connect waits 0.000000% of the time
 RFS write waits 0.000000% of the time
 RFS random i/o waits 0.000000% of the time
 RFS sequential i/o waits 0.000000% of the time
 MRP redo arrival waits 0.000000% of the time
 enq: RO - contention waits 0.000000% of the time
 db flash cache dynamic disabling wait waits 0.000000% of the time
 db flash cache invalidate wait waits 0.000000% of the time
 db flash cache write waits 0.000000% of the time
 db flash cache multiblock physical read waits 0.000000% of the time
 db flash cache single block physical read waits 0.000000% of the time
 pi renounce write complete waits 0.000000% of the time
 recovery read waits 0.000000% of the time
 multiple dbwriter suspend/resume for file offline waits 0.000000% of the time
 gc buffer busy release waits 0.000000% of the time
 gc buffer busy acquire waits 0.000000% of the time
 buffer read retry waits 0.000000% of the time
 write complete waits: flash cache waits 0.000000% of the time
 checkpoint completed waits 0.000000% of the time
 free buffer waits waits 0.000000% of the time
 enq: PW - flush prewarm buffers waits 0.000000% of the time
 Shared IO Pool IO Completion waits 0.000000% of the time
 Shared IO Pool Memory waits 0.000000% of the time
 control file backup creation waits 0.000000% of the time
 retry contact SCN lock master waits 0.000000% of the time
 SGA: MMAN sleep for component shrink waits 0.000000% of the time
 GCR sleep waits 0.000000% of the time
 heartbeat monitor sleep waits 0.000000% of the time
 gcs remote message waits 0.000000% of the time
 ges remote message waits 0.000000% of the time
 watchdog main loop waits 0.000000% of the time
 PING waits 0.000000% of the time
 KSV master wait waits 0.000000% of the time
 wait for unread message on multiple broadcast channels waits 0.000000% of the time
 wait for unread message on broadcast channel waits 0.000000% of the time
 Archiver slave I/O waits 0.000000% of the time
 LGWR slave I/O waits 0.000000% of the time
 DBWR slave I/O waits 0.000000% of the time
 RMAN Tape slave I/O waits 0.000000% of the time
 RMAN Disk slave I/O waits 0.000000% of the time
 i/o slave wait waits 0.000000% of the time
 io done waits 0.000000% of the time
 Backup: MML query for direct buffers waits 0.000000% of the time
 Backup: MML get base address waits 0.000000% of the time
 Backup: MML release a direct buffer waits 0.000000% of the time
 Backup: MML obtain a direct buffer waits 0.000000% of the time
 Backup: MML proxy prepare to start waits 0.000000% of the time
 Backup: MML data movement done? waits 0.000000% of the time
 Backup: MML proxy start data movement waits 0.000000% of the time
 Backup: MML proxy initialize restore waits 0.000000% of the time
 Backup: MML datafile proxy restore? waits 0.000000% of the time
 Backup: MML datafile proxy backup? waits 0.000000% of the time
 Backup: MML proxy session end waits 0.000000% of the time
 Backup: MML proxy commit backup piece waits 0.000000% of the time
 Backup: MML proxy cancel waits 0.000000% of the time
 Backup: MML proxy initialize backup waits 0.000000% of the time
 Backup: MML write backup piece waits 0.000000% of the time
 Backup: MML restore backup piece waits 0.000000% of the time
 Backup: MML delete backup piece waits 0.000000% of the time
 Backup: MML read backup piece waits 0.000000% of the time
 Backup: MML extended initialization waits 0.000000% of the time
 Backup: MML query backup piece waits 0.000000% of the time
 Backup: MML obtain textual error waits 0.000000% of the time
 Backup: MML shutdown waits 0.000000% of the time
 Backup: MML command to channel waits 0.000000% of the time
 Backup: MML commit backup piece waits 0.000000% of the time
 Backup: MML create a backup piece waits 0.000000% of the time
 Backup: MML v1 delete backup piece waits 0.000000% of the time
 Backup: MML v1 query backup piece waits 0.000000% of the time
 Backup: MML v1 close backup piece waits 0.000000% of the time
 Backup: MML v1 write backup piece waits 0.000000% of the time
 Backup: MML v1 read backup piece waits 0.000000% of the time
 Backup: MML v1 open backup piece waits 0.000000% of the time
 Backup: MML initialization waits 0.000000% of the time
 Network file transfer waits 0.000000% of the time
 Standby redo I/O waits 0.000000% of the time
 RMAN backup & recovery I/O waits 0.000000% of the time
 Log file init write waits 0.000000% of the time
 DG Broker configuration file I/O waits 0.000000% of the time
 dbms_file_transfer I/O waits 0.000000% of the time
 Datapump dump file I/O waits 0.000000% of the time
 Clonedb bitmap file write waits 0.000000% of the time
 direct path sync waits 0.000000% of the time
 Disk file Mirror/Media Repair Write waits 0.000000% of the time
 Disk file Mirror Read waits 0.000000% of the time
 remote db file write waits 0.000000% of the time
 remote db file read waits 0.000000% of the time
 remote db operation waits 0.000000% of the time
 IORM Scheduler Slave Idle Wait waits 0.000000% of the time
 VKTM Init Wait for GSGA waits 0.000000% of the time
 VKTM Logical Idle Wait waits 0.000000% of the time
 logout restrictor waits 0.000000% of the time
 null event waits 0.000000% of the time no wait problems
```

Errores de serialización, incluyendo bloqueos frecuentes innecesarios o que consumen demasiado tiempo, cerrojos (latching) (4), o actividades del buffer de memoria

```
000 PC and Classifier lists for WLM
001 post/wait queue
002 hot latch diags
003 test excl. non-parent l0
004 test excl. parent l0
005 test excl. parent2 l0
006 test shared non-parent l0
007 test excl. non-parent lmax
008 process allocation
009 call allocation
010 interrupt manipulation
011 session allocation
012 session switching
013 process group creation
014 session idle bit
015 client/application info
016 longop free list parent
017 ksuosstats global area
018 ksupkttest latch
019 cached attr list
020 ksim membership request latch
021 ksim group membership cache
022 object stats modification
023 kss move lock
024 parameter table management
025 ksbxic instance latch
026 kse signature
027 messages
028 enqueues
029 enqueue hash chains
030 instance enqueue
031 enqueue freelist latch
032 trace latch
033 FOB s.o list latch
034 FIB s.o chain latch
035 SGA IO buffer pool latch
036 File IO Stats segmented array latch
037 KSFQ
038 X$KSFQP
039 i/o slave adaptor
040 ksfv messages
041 msg queue latch
042 done queue latch
043 session queue latch
044 direct msg latch
045 vecio buf des
046 ksfv subheap
047 resmgr:free threads list
048 resmgr group change latch
049 channel handle pool latch
050 channel operations parent latch
051 message pool operations parent latch
052 channel anchor
053 dynamic channels
054 ksv instance latch
055 ksv class latch
056 ksv msg queue latch
057 ksv allocation latch
058 ksv remote inst ops
059 first spare latch
060 second spare latch
061 third spare latch
062 fourth spare latch
063 fifth spare latch
064 ksxp shared latch
065 IPC stats buffer allocation latch
066 IPC other latch
067 ksxp so latch
068 segmented array pool
069 granule operation
070 KSXR large replies
071 SGA mapping latch
072 active service list
073 database property service latch
074 OS process allocation
075 OS process
076 OS process: request allocation
077 ksir sga latch
078 kspoltest latch
079 ksz_so allocation latch
080 reid allocation latch
081 storage server table manipulation latch
082 pmon tst latch
083 pmon tst latch 2
084 queued dump request
085 global hanganlyze operation
086 ges process table freelist
087 ges process parent latch
088 ges process hash list
089 ges resource table freelist
090 ges caches resource lists
091 ges resource hash list
092 ges resource scan list
093 ges s-lock bitvec freelist
094 ges enqueue table freelist
095 ges DFS wait callback info
096 ges timeout list
097 ges deadlock list
098 ges statistic table
099 ges synchronous data
100 KJC message pool free list
101 KJC receiver ctx free list
102 KJC snd proxy ctx free list
103 KJC destination ctx free list
104 KJC receiver queue access list
105 KJC snd proxy queue access list
106 KJC global resend message queue
107 KJCT receiver queue access
108 KJCT flow control latch
109 KJC global post event buffer
110 ges domain table
111 ges group table
112 gcs resource hash
113 gcs opaque info freelist
114 gcs resource freelist
115 gcs resource scan list
116 gcs resource validate list
117 gcs domain validate latch
118 gcs shadows freelist
119 gcs commit scn state
120 name-service entry
121 name-service request queue
122 name-service pending queue
123 name-service namespace bucket
124 name-service memory objects
125 name-service namespace objects
126 name-service request
127 name-service memory recovery
128 gcs remastering latch
129 gcs partitioned table hash
130 gcs pcm hashed value bucket hash
131 gcs remaster request queue
132 recovery domain freelist
133 recovery domain hash list
134 ges value block free list
135 Testing
136 heartbeat check
137 heartbeat structure management
138 gcr global ctx
139 gcr structure management
140 KMG MMAN ready and startup request latch
141 KMG resize request state object freelist
142 Memory Management Latch
143 file number translation table
144 mostly latch-free SCN
145 lgwr LWN SCN
146 redo on-disk SCN
147 ping redo on-disk SCN
148 Consistent RBA
149 kcbtsemkid latch
150 cache buffers lru chain
151 buffer pool
152 multiple dbwriter suspend
153 active checkpoint queue latch
154 checkpoint queue latch
155 cache buffers chains
156 cache buffer handles
157 multiblock read objects
158 cache protection latch
159 block corruption recovery state
160 parallel ibr array
161 tablespace key chain
162 cache table scan latch
163 simulator lru latch
164 simulator hash latch
165 sim partition latch
166 state object free list
167 object queue header operation
168 object queue header heap
169 archive control
170 FAL Queue
171 kcrrgapb latch
172 Recovery Ping control
173 RSGA Queue
174 krsh_sga_uet latch
175 Managed Standby Recovery State
176 SGA kcrrlac latch
177 SGA kcrrgap latch
178 Real time apply boundary
179 SGA kcrrpinfo latch
180 SGA kcrrlatmscnl latch
181 archive process latch
182 LGWR NS Write
183 managed standby latch
184 alert log latch
185 redo writing
186 redo copy
187 redo allocation
188 real redo SCN
189 readredo stats and histogram
190 OS file lock latch
191 Online Datafile Move latch
192 gc element
193 gc checkpoint
194 gc persistent rm
195 loader state object freelist
196 begin backup scn array
197 krbmrosl
198 logminer work area
199 logminer context allocation
200 logical standby cache
201 logical standby view
202 media recovery process out of buffers
203 read-only violation array modification
204 mapped buffers lru chain
205 Media rcv so alloc latch
206 parallel recoverable recovery
207 block media rcv so alloc latch
208 readable standby influx scn
209 readable standby metadata redo cache
210 readable standby lamport table
211 rsb inst ckpt scn
212 change tracking state change latch
213 change tracking optimization SCN
214 change tracking consistent SCN
215 lock DBA buffer during media recovery
216 lock new checkpoint scn during media recovery
217 reservation so alloc latch
218 Reserved Space Latch
219 flashback marker cache
220 flashback FBA barrier
221 flashback SCN barrier
222 hint flashback FBA barrier
223 flashback hint SCN barrier
224 flashback allocation
225 flashback mapping
226 flashback copy
227 flashback sync request
228 flashback prepare log info
229 Minimum flashback SCN latch
230 Block new check invariant rollback SCN latch
231 Flashback coordinator context latch
232 file deallocation SCN cache
233 Transportable DB Context Latch
234 cvmap freelist lock
235 kcfis latch
236 latch for sabmr requests triggered by queries
237 io pool granule metadata list
238 io pool granule list
239 dml lock allocation
240 DML lock allocation
241 list of block allocation
242 transaction allocation
243 dummy allocation
244 transaction branch allocation
245 undo global data
246 MinActiveScn Latch
247 parallel txn reco latch
248 intra txn parallel recovery
249 Undo Hint Latch
250 resumable state object
251 In memory undo latch
252 KTF sga latch
253 MQL Tracking Latch
254 Change Notification Hash table latch
255 Change Notification Latch
256 flashback archiver latch
257 change notification client cache latch
258 corrupted undo seg latch
259 sort extent pool
260 lob segment hash table latch
261 lob segment query latch
262 lob segment dispenser latch
263 fast space usage latch
264 shrink stat allocation latch
265 file cache latch
266 ktm global data
267 space background SGA latch
268 space background task latch
269 space background state object latch
270 sequence cache
271 temp lob duration state obj allocation
272 kssmov protection latch
273 File State Object Pool Parent Latch
274 Write State Object Pool Parent Latch
275 deferred cleanup latch
276 domain validation update latch
277 kdlx hb parent latch
278 Locator state objects pool parent latch
279 Dedup Write Append Table Latch
280 row cache objects
281 KQF runtime table column alloc
282 KQF runtime purge top lvl latch
283 QOL Name Generation Latch
284 kkae edition name cache
285 KKCN reg stat latch
286 KKCN grp reg latch
287 KKCN grp data latch
288 global KZLD latch for mem in SGA
289 Read Only Database Account Status
290 cost function
291 Policy Refresh Latch
292 Policy Hash Table Latch
293 OLS label cache
294 instance information
295 policy information
296 global ctx hash table latch
297 Role grants to users
298 Role graph
299 Security Class Hashtable
300 first Audit Vault latch
301 second Audit Vault latch
302 third Audit Vault latch
303 fourth Audit Vault latch
304 global tx hash mapping
305 k2q lock allocation
306 k2q global data latch
307 shared pool
308 library cache load lock
309 Token Manager
310 cas latch
311 rm cas latch
312 resmgr:actses change state
313 resmgr:actses change group
314 resmgr:session queuing
315 resmgr:active threads
316 resmgr:schema config
317 resmgr:vc list latch
318 resmgr:incr/decr stats
319 resmgr:method mem alloc latch
320 resmgr:plan CPU method
321 resmgr:resource group CPU method
322 QMT
323 shared pool simulator
324 shared pool sim alloc
325 Streams Generic
326 Shared B-Tree
327 Memory Queue
328 Memory Queue Subscriber
329 Memory Queue Message Subscriber #1
330 Memory Queue Message Subscriber #2
331 Memory Queue Message Subscriber #3
332 Memory Queue Message Subscriber #4
333 pesom_hash_node
334 pesom_free_list
335 pesom_heap_alloc
336 peshm
337 Mutex
338 Mutex Stats
339 pebof_rrv
340 ODM-NFS:Global file structure
341 KGNFS-NFS:SHM structure
342 kgnfs mount latch
343 KGNFS-NFS:SVR LIST
344 SGA heap creation lock
345 SGA heap locks
346 SGA pool creation lock
347 SGA pool locks
348 SGA bucket locks
349 SGA blob lock
350 SGA blob parent
351 kgb latch
352 kgb parent
353 SGA table lock
354 Event Group Locks
355 SGA slab metadata lock
356 Sage HT Latch
357 shared server configuration
358 session timer
359 parameter list
360 presentation list
361 address list
362 end-point list
363 shared server info
364 dispatcher info
365 virtual circuit buffers
366 virtual circuit queues
367 virtual circuits
368 virtual circuit holder
369 kmcptab latch
370 kmcpvec latch
371 cp pool array latch
372 cp cmon array latch
373 cp server array latch
374 cp server hash latch
375 cp cso latch
376 cp pool latch
377 cp cmon/server latch
378 cp holder latch
379 cp sga latch
380 JOX SGA heap latch
381 JOX JIT latch
382 job_queue_processes parameter latch
383 job workq parent latch
384 job_queue_processes free list latch
385 query server process
386 query server freelists
387 error message lists
388 process queue
389 process queue reference
390 parallel query stats
391 business card
392 queuing load statistics
393 parallel query alloc buffer
394 PX hash array latch
395 hash table modification latch
396 hash table dml freq tracking latch
397 hash table column usage latch
398 constraint object allocation
399 device information
400 temporary table state object allocation
401 internal temp table object number allocation latch
402 SQL memory manager latch
403 SQL memory manager workarea list latch
404 compile environment latch
405 Bloom filter list latch
406 Bloom Filter SGA latch
407 Result Cache: RC Latch
408 Result Cache: SO Latch
409 Result Cache: MB Latch
410 Statement queuing latch
411 Statement Queuing SGA latch
412 kupp process latch
413 pass worker exception to master
414 datapump job fixed tables latch
415 datapump attach fixed tables latch
416 process
417 VPSO SGA
418 STREAMS LCR
419 STREAMS: txn btree
420 STREAMS Pool Advisor
421 kokc descriptor allocation latch
422 ncodef allocation latch
423 NLS data objects
424 kpon job info latch
425 kpon sga structure
426 reg$ timeout service time
427 KPON ksr channel latch
428 EMON slave state object latch
429 session state list latch
430 kpplsSyncStateListSga: lock
431 connection pool sga data lock
432 message enqueue sync latch
433 image handles of buffered messages latch
434 kwqi:kchunk latch
435 KWQP Prop Status
436 KWQS pqueue ctx latch
437 KWQS pqsubs latch
438 AQ Propagation Scheduling Proc Table
439 AQ Propagation Scheduling System Load
440 job queue sob latch
441 rules engine aggregate statistics
442 rules engine rule set statistics
443 rules engine rule statistics
444 rules engine evaluation context statistics
445 enqueue sob latch
446 kwqbsgn:msghdr
447 kwqbsn:qxl
448 kwqbsn:qsga
449 kwqbcco:cco
450 bufq statistics
451 spilled messages latch
452 queue sender's info. latch
453 bq:time manger info latch
454 qmn task queue latch
455 qmn task context latch
456 qmn state object latch
457 KWQMN job cache list latch
458 KWQMN to-be-Stopped Buffer list Latch
459 AQ deq hash table latch
460 AQ dequeue txn counter latch
461 AQ disk delete txn counter latch
462 AQ small txn context latch
463 AQ deq log statistics latch
464 AQ deq log cmt cbk chunk latch
465 AQ ht cmt cbk chunk latch
466 fixed table rows for x$hs_session
467 qm_init_sga
468 XDB unused session pool
469 XDB used session pool
470 XDB Config-1
471 XDB Config-2
472 XDB Config-3
473 qmtmrcsg_init
474 XML DB Events
475 XDB NFS Stateful SGA Latch
476 qmne Export Table Latch
477 XDB NFS Security Latch
478 XDB Byte Lock SGA Latch
479 XDB Mcache SGA Latch
480 XDB PL/SQL Support
481 DMON Work Queues Latch
482 DMON Network Error List Latch
483 RSM process latch
484 NSV command ID generation latch
485 NSV creation/termination latch
486 Request id generation latch
487 Fast-Start Failover State Latch
488 xscalc freelist
489 xssinfo freelist
490 AW SGA latch
491 ASM allocation
492 KFA SGA latch
493 buffer pin latch
494 KFC SGA latch
495 KFC LRU latch
496 KFC Hash Latch
497 KFC FX Hash Latch
498 ASM map headers
499 ASM map operation freelist
500 ASM map operation hash table
501 ASM map load waiting list
502 KFK SGA Libload latch
503 ASM Keyed state latch
504 Lsod array latch
505 I/O Staticstics latch
506 KFM allocation
507 KFMD SGA
508 ASM network background latch
509 ASM network SGA latch
510 ASM network state latch
511 ASM db client latch
512 ASM file locked extent latch
513 ASM scan context latch
514 ASM file allocation latch
515 KFR redo allocation latch
516 ASM rollback operations
517 KFCL LE Freelist
518 KFCL Instance Latch
519 KFCL BX Freelist
520 ASM attribute latch
521 ASM Volume process latch
522 ASM Volume SGA latch
523 OFS SGA Latch
524 ASM user latch
525 ASM KFFD SO Latch
526 server alert latch
527 generalized trace enabling latch
528 statistics aggregation
529 AWR Alerted Metric Element list
530 threshold alerts latch
531 WCR: kecu cas mem
532 WCR: kecr File Count
533 WCR: MMON Create dir
534 Real-time plan statistics latch
535 WCR: ticker cache
536 WCR: sync
537 WCR: processes HT
538 WCR: MTS VC queue
539 JS broadcast add buf latch
540 JS broadcast drop buf latch
541 JS broadcast kill buf latch
542 JS broadcast load blnc latch
543 JS broadcast autostart latch
544 JS broadcast LW Job latch
545 JS mem alloc latch
546 JS slv state obj latch
547 JS queue state obj latch
548 JS queue access latch
549 JS Sh mem access
550 PL/SQL warning settings
551 dbkea msgq latch
552 KXDAM sga state latch
```
