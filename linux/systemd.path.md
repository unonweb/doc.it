encodes information about a path monitored by systemd, for path-based activation

* for each path file, a matching unit file must exist, describing the unit to activate when the path changes

  by default, a service by the same name as the path (except for the suffix) is activated

* just make sure to *enable* **and** *start* the **path** file when you’re done otherwise nothing will happen when the file changes 😅


```ini
# Watch a file or directory and activate the configured unit whenever it changes:

PathExists = 
# may be used to watch the mere existence of a file or directory. If the file specified exists, the configured unit is activated.
PathChanged = some/path 
# activated if the file which was open for writing gets closed (not activated on every write)
PathModified = some/path 
# similar, but additionally it is activated also on simple writes to the watched file


## Configure a limit on how often this path unit may be activated within a specific time interval:
TriggerLimitIntervalSec = 2s
# configure the length of the time interval in the usual time units
TriggerLimitBurst = 200
# takes a positive integer value and specifies the number of permitted activations per time interval, and defaults to 200. Set either to 0 to disable any form of trigger rate limiting. If the limit is hit, the unit is placed into a failure mode, and will not watch the paths anymore until restarted. Note that this limit is enforced before the service activation is enqueued.
```
