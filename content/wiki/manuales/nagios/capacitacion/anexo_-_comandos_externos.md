---
title: "Comandos externos"
date: 2015-10-16T12:05:54-03:00
---

### ACKNOWLEDGE_HOST_PROBLEM

Command Format: ACKNOWLEDGE_HOST_PROBLEM;\<host_name\>;\<sticky\>;\<notify\>;\<persistent\>;\<author\>;\<comment\>

Description: Allows you to acknowledge the current problem for the specified host. By acknowledging the current problem, future notifications (for the same host state) are disabled. If the “sticky” option is set to two (2), the acknowledgement will remain until the host returns to an UP state. Otherwise the acknowledgement will automatically be removed when the host changes state. If the “notify” option is set to one (1), a notification will be sent out to contacts indicating that the current host problem has been acknowledged. If the “persistent” option is set to one (1), the comment associated with the acknowledgement will survive across restarts of the Nagios process. If not, the comment will be deleted the next time Nagios restarts.

Shell Script Usage Example:

```bash>
 $commandfile
```

### ACKNOWLEDGE_SVC_PROBLEM

Command Format: ACKNOWLEDGE_SVC_PROBLEM;\<host_name\>;\<service_description\>;\<sticky\>;\<notify\>;\<persistent\>;\<author\>;\<comment\>

Description: Allows you to acknowledge the current problem for the specified service. By acknowledging the current problem, future notifications (for the same servicestate) are disabled. If the “sticky” option is set to two (2), the acknowledgement will remain until the service returns to an OK state. Otherwise the acknowledgement will automatically be removed when the service changes state. If the “notify” option is set to one (1), a notification will be sent out to contacts indicating that the current service problem has been acknowledged. If the “persistent” option is set to one (1), the comment associated with the acknowledgement will survive across restarts of the Nagios process. If not, the comment will be deleted the next time Nagios restarts.

Shell Script Usage Example:

```bash>
 $commandfile
```

### ADD_HOST_COMMENT

Command Format: ADD_HOST_COMMENT;\<host_name\>;\<persistent\>;\<author\>;\<comment\>

Description: Adds a comment to a particular host. If the “persistent” field is set to zero (0), the comment will be deleted the next time Nagios is restarted. Otherwise, the comment will persist across program restarts until it is deleted manually.

Shell Script Usage Example:

```bash>
 $commandfile
```

### ADD_SVC_COMMENT

Command Format: ADD_SVC_COMMENT;\<host_name\>;\<service_description\>;\<persistent\>;\<author\>;\<comment\>

Description: Adds a comment to a particular service. If the “persistent” field is set to zero (0), the comment will be deleted the next time Nagios is restarted. Otherwise, the comment will persist across program restarts until it is deleted manually.

Shell Script Usage Example:

```bash>
 $commandfile
```

## Referencias

<https://old.nagios.org/developerinfo/externalcommands/commandlist.php>
