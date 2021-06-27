# check_aix_paging
nagios check for AIX paging activity and paging space usage

# Requirements
perl, ssh on nagios server

# Configuration

This script is executed remotely on a monitored system by the NRPE or check_by_ssh methods available in nagios.  

If you are using the check_by_ssh method, you will need to add a section similar to the following to the services.cfg file on the nagios server.  
This assumes you already have ssh key pairs configured.
```
    define service {
       use                             generic-service
       hostgroup_name                  all_aix
       service_description             AIX paging
       check_command                   check_by_ssh!/usr/local/nagios/libexec/check_aix_paging
       }
```

Alternatively, if you are using the check_nrpe method, you will need to add a section similar to the following to the services.cfg file on the nagios server.  
This assumes you already have ssh key pairs configured.
```
    define service{
       use                             generic-service
       hostgroup_name                  all_aix
       service_description             AIX paging
       check_command                   check_nrpe!check_aix_paging -t 30
       notification_options            c,r                     ; Send notifications about critical and recovery events
       }
```

If you are using the NRPE method, you will also need a command definition similar to the following on each monitored host in the /usr/local/nagios/nrpe/nrpe.cfg file:
```
    command[check_aix_paging]=/usr/local/nagios/libexec/check_aix_paging
```



Please note that the default thresholds for alerting are as follows:
```
    WARN on page space utilization 50%
    CRIT on page space utilization 70%
    WARN on pagein/pageout activity  50 pages/sec
    CRIT on pagein/pageout activity 100 pages/sec
```

If you want to specify a custom threshold for a particular host, you can pass the values as command line parameters.
For example, if you wanted to warn on 60% space util and critical on 80%, with 75 pageins/sec and critical on 150 pageins/sec,
the section in the services.cfg would look something like this:
```
   define service {
      use                             generic-service
      host_name                       my_aix_server
      service_description             AIX paging
      check_command                   check_by_ssh!/usr/local/nagios/libexec/check_aix_paging!60!80!75!150
      }
```
