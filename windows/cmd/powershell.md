---
title: Powershell
---

POWERSHELL
==================


* Aktuelle Version: 7.0.3 (16. Juli 2020) 
* verbindet die aus Unix-Shells bekannte Philosophie von Pipes und Filtern mit dem Paradigma der objektorientierten Programmierung
* plattformübergreifendes Framework von Microsoft zur Automatisierung, Konfiguration und Verwaltung von Systemen, bestehend aus einem Kommandozeileninterpreter sowie einer Skriptsprache


Privileges
=========================

* you have exactly the same permissions in PowerShell as you do in the GUI

#### -Credential parameter
elevate privileges on a per command basis
nice if a command has this parameter


BASICS
=========================

`ctrl + space` # suggests parameters: Simply type the command you want. Then type `–` and then press `ctrl + space`. 

* run local command: `./<command>`
* run your own scripts:
  * `Set-ExecutionPolicy RemoteSigned`
  * `& <path-to-script>`

### cmdlets

In sich geschlossene Programmierungsobjekte, mit denen man auf die zugrundeliegenden Administrationsoptionen in Windows zugreifen kann. Vor PowerShell navigierten Systeadministratoren durch die grafischen Oberflächen, um diese Optionen zu finden,m und es gab keine Möglichkeit, den Ablauf des Durchklickens durch die Menüs erneut zu verwenden, um Optionen in großem Umfang zu ändern.

* perform an action and typically return a Microsoft .NET object to the next command in the pipeline.
* Cmdlets # Kompilierte Befehle
* Nomenklatur: *Verb-Nomen* im Singular

#### cmdlets vs. commands
Cmdlets differ from commands in other command-shell environments in the following ways:
* Cmdlets are instances of .NET classes; they are not stand-alone executables.
* Cmdlets process input objects from the pipeline rather than from streams of text, and cmdlets typically deliver objects as output to the pipeline.


how to
----------

* example: `Get-Service`
  * sort: `Get-Service | Sort-Object Status` 
  * filter: 
    * Where-Object: 
      * `Get-Service | Where-Object Status -eq "running"`
      * `Get-Service | Where-Object DisplayName -Match Firewall`
      * `Get-Service | Where-Object DisplayName -Match "^Windows.*"`
      * `Get-Service | Where-Object DisplayName -NotLike "*Windows*"`
  * show properties:
    * `Get-NetFirewallRule | Select-Object * -First 1`


SYNTAX
=========================


Operators
---------------

The call operator `&` allows you to execute a command, script or function. 
`& "[path] command" [arguments]`

### comparison operators

* `-eq` #
* `-ne` #
* `-gt` #
* `-ge` #
* `-lt` #
* `-le` #

#### matching operators

These PowerShell operators are capable of finding elements with specific patterns using wild card expressions.

* `-match`    # Matches a string with a specified regular expression
* `-notmatch` # Does not match a string with a specified regular expression
* `-replace`  # Check for the given string and replace with specified string

#### containment operators

* `-contains`     # Checks for the existence of a specified element in an array
* `-notcontains`  # Checks for the non-existence of specified element in an array
* `-in`           # Checks for the existence of a specified element in an array
* `-notin`        # Checks for the non-existence of specified element in an array

### assignment operators

* `=`
* `+=`
* `-=`
* `*=`
* `/=`
* `%=`


Variables
----------------------

`$a = (Get-Process | Sort-Object ID)`
`$a | Where-Object ProcessName -like "firefox"`

### special variables

* **`$_`** # *this* token. Contains the current object in the pipeline object. 
  * Alias: `$PSItem`.
  * Examples:
    * `... | foreach { Write-Host $_ }`
    * `Get-ChildItem *docx | Rename-Item -NewName {$_.name -replace " ","_"}`
    * `$_.name` # the *name* property of the current object
* **`$?`** # Represents the execution status of the last operation. 
  * *TRUE* if the last operation succeeded
  * *FALSE* if it failed
* **`$(...)`** # *Sub-expression*
  * Example: 
    * `"Hello, $name, there are $($name.length) characters in your name"`
* **`$INPUT`** # Represents an enumerator that enumerates all input that is passed to a function.
* **`&`** # Execute *string as command*
  * Example:
    * `& "Get-Process"`
* **`@(...)`** # Declare *arrays*.
  * Note: comma is used as a separator, in contrast to hash table declaration.
  * Example:
    * `$a = @("One", "Two", "Three")`
* **`@{...}`** # Declare *hash tables*.
  * Examples:
    * `$a = @{"1" = "one"; "2" = "two"; "3" = "three"}`
* **`backtick`**
  *  Continue command on the next line:
  *  Include a special symbol into a string. Available options:
     *  `<backtick>$` # include a dollar symbol in your string. If you don't escape it, Powershell will assume you are trying to embed a variable.
     *  `<backtick>0` # Null. My preference is using `$null` instead.
     *  `<backtick>a` # Alert. Yes, it does make sound, and you can use multiple for multiple beeps.
     *  `<backtick>b` # Backspace
     *  `<backtick>n` # New line
     *  `<backtick>r` # Carriage return
     *  `<backtick>t` # Horizontal tab
* **`<# ... #>`** # Multi-line comment.
*  **`..`** # Specify a range.
   *  `1..5`
* **`.`**
  * *dot sourcing*: Include external powershell file as part of your script. Any functions or variables will become available in the current scope.
* **`::`** # Reference static member of a class. The class name must be enclosed in square brackets.
  * `[string]::Equals("a", "b")`

#### shortcuts

* **`%`** # Shortcut to `foreach`
  * Examples:
    * `... | % { Write-Host $_ }`
* **`?`** # Shortcut to `where`
  * Examples:  
    * `1..5 | ? { $_ % 2 }`
* **`!`** # Shortcut to `-not`
  * Examples:
    * `if(!$a) { Write-Host '$a is null' }`

#### environment variables 
`$env:computername` #  
`$PSVersionTable` #  
`$FormatEnumerationLimit` # 


Control Flow
--------------------

* `Break`   # Exit the current control block


### loops


#### for

```powershell
$array = @("item1", "item2", "item3")
for($i = 0; $i -lt $array.length; $i++){ $array[$i] }
```
#### foreach
`foreach ( <variable> in <collection> )`

```powershell
foreach($line in $content) { 
  Write-Host $line 
}
```

#### while

```powershell
$n = 0
while ($n -lt 5) {
  Write-Host "n = $n"
  $n+=1
}
```

### conditions

#### if

`If ($variable )`

```powershell
if(Boolean_expression) {
   // Statements will execute if the Boolean expression is true
}elseif(Boolean_expression 3) {
   // Executes when the Boolean expression 3 is true
}
else {
   // Executes when the Boolean expression is false
}
```

#### switch

```powershell
switch(<test-value>) {
   <condition> {<action>} 
      break;
   <condition> {<action>} 
      break;
   <condition> {<action>} 
      break;
}
```

Hashtables
--------------------

also known as a *dictionary* or *associative array*  
a compact data structure that stores one or more *key/value pairs*  

* The syntax of a hash table:  
  * `@{ name=value}`  
  * `@{ name=value; name=value}`  
* The syntax of an ordered dictionary:
  * `[ordered]@{ <name> = <value>}`  
  * `[ordered]@{ <name> = <value>; <name> = <value>}`
* create
  * `$hash = @{}` # creates an empty hash table  
  * `$hash = @{Number=1; Shape="Square"; Color="Blue"}` # creates a prefilled hash table  
* properties
  * `$hash.keys`    # access the keys  
  * `$hash.values`  # access the values  
  * `$hash.count`
* methods
  * `$hash.Add("key", "value")` # add a key/value pair
  * `$hash.Remove("key")`
* tricks
  * `$hash = $hash + @{Time="Now"}` # adds a "Time" key with a value of "Now" to the hash table in the `$hash` variable.


Arrays
-------------------------------------------------------------------------------------------------------

#### create
* `$data = @()`
* `$data = @('Zero','One','Two','Three')`
* `$data = 'Zero','One','Two','Three'`

#### access

`$data[0,2,3]`
Zero
Two
Three


Objects
-----------------------------------------------------------------------------------

```powershell
$newObject = New-Object -TypeName PSObject -Property @{
    Name = $env:username
    ID = 12
    Address = $null
}
```

Strings
---------------------------------------------------------------------------------------------------

### formatting

`Write-Host ("Hello " + $env:USERNAME)`
`Write-Output ("Hello " + $env:USERNAME)`


Collections
-------------------------------------------------------------------

`$newCollection = @()`
`$newCollection += <anything>`


Advanced Functions
--------------

The `CmdletBinding` attribute is an attribute of functions that makes them operate like compiled cmdlets
When you call them via `[CmdletBinding()]` you initialize a new instance of the class.

### begin {}

* specify variables and arrays
* optional
* Everything in the `begin` block will only run once per call of your function.

### process {}

* this is where the work is done in a PowerShell advanced function. 
* runs multiple times based on the array list we pipe into our function
There are two different approaches we need to keep in mind and account for:  
The first is how you handle parameters and the second is pipeline input into your function.

* can be used without the BEGIN or END blocks

Some questions you should ask yourself:
* Does my function take a single value for a parameter or an array of values?
* Does my function accept pipeline input?



```powershell
# This process block is called once for every piped parameter:
function ShowBeginProcessEnd {
  [CmdletBinding()]
  Param (
    [Parameter(ValueFromPipeline=$true)]
    [string] # only one argument acceped
    $arg
  )
  begin {
    Write-Host 'Calling Begin Block'
  }
  process {
      Write-Host "Calling Process Block with $arg variable"
  }
  end {
    Write-Host 'Calling End Block'
  }
}
'first' | ShowBeginProcessEnd
# Calling Begin Block    
# Calling Process Block with first variable
# Calling End Block
'first', 'second', 'third' | ShowBeginProcessEnd
# Calling Begin Block
# Calling Process Block with first variable
# Calling Process Block with second variable
# Calling Process Block with third variable
# Calling End Block
```

```powershell
# This process block is called once for every parameter passed
# (pipe or argument)

function ShowBeginProcessEnd {
  [CmdletBinding()]
  Param (
    [Parameter(ValueFromPipeline=$true)]
    [string[]] # multiple arguments accepted
    $args
  )
  begin {
    Write-Host 'Calling Begin Block'
  }
  process {
    foreach ($item in $args){
      Write-Host "Calling Process Block with $args variable"
    }
  }
  end {
    Write-Host 'Calling End Block'
  }
}
```

```powershell
function ShowBeginProcessEnd {
  [CmdletBinding()]
  Param (
    [Parameter(ValueFromPipeline=$true)]
    [string[]] # arrays are accepted
    $args
  )
  begin {
    Write-Host 'Calling Begin Block'
  }
  process {
    foreach($item in $args){
      switch ($item) {
        meeting { Start-Meeting $item }
        ticket  { Start-Ticket $item  }
        support { Start-Support $item }
      }
    }
  }
  end {
    Write-Host 'Calling End Block'
  }
}
```

### end {}

* dispose or cleanup (if needed):  If we created objects, variables, files,... in our BEGIN or PROCESS blocks then we will use the END block to either clean these objects up or return any complex data structures.
* if you created a database connection in your BEGIN block, processed or updated a database in the PROCESS block then you would use the END block to disconnect/destroy our database connection.
* if you have created an PSObject in the BEGIN block, added data to the object in the PROCESS block and then use the END block to return the final object.



Misc
----------------------------------------------------------------------------------------------------

* `Break`   # Exit the current control block
* `Return`  # Exit the current scope, which can be a function, script, or script block. 
* `Exit`    # Exit the current PowerShell session
* `n # new line
* `r # carriage return
* `r`n

#### properties

* `array.count`

### pipes and redirects
* `Get-Service | ConvertTo-HTML -Property Name, Status > C:\services.htm`

### subshell.property
`(<commands in a subshell>).<property>`
* `(Get-NetIPAddress -InterfaceAlias "ethernet").IPAddress`
* `(Get-ComputerInfo).LogonServer`

### brackets

* Parenthesis brackets ()
  * pass arguments: `foreach ($element in $array) { $element }`
  * enclose multiple set of instructions
  * resolve ambiguity
  * create array: `$array = @("item1", "item2", "item3")`
* Square brackets []
  * access to array
  * access to hashtables
  * filter using regular expression


OS/SYSTEM INFO
=========================

* Microsoft.PowerShell.Management
  * `Get-ComputerInfo` # gets all system and operating system properties from the computer

### WMI

* Microsoft.PowerShell.Management
  * `Get-WmiObject` # has been superseded by `Get-CimInstance`
    * `Get-WMIObject -Class Win32_DiskDrive | Select Interfacetype, Caption`
  * `Get-CimInstance`
* CimCmdlets
  * `Get-CimInstance` # returns one or more CIM instance objects representing a snapshot of the CIM instances present on the CIM server
    * `-Query '<wql-expression>` # using WMI Query Language
      * `Get-CimInstance -Query 'Select * from win32_bios'` # 
    * `-ClassName <class>` # the Powershell way to do it
    * `-Property <property>` # limits the information that's retrieved; this makes the query to WMI more efficient.
    * `-ComputerName <computer>` # 
      * `Get-CimInstance -ClassName Win32_BIOS -Property SerialNumber | Select-Object SerialNumber`
      * `Get-CimInstance -ClassName Win32_BIOS | Select-Object SerialNumber` # 
      * `Get-CimInstance -ClassName Win32_BIOS -Property SerialNumber | Select-Object -ExpandProperty SerialNumber` #  returns a string
      * `(Get-CimInstance -ClassName Win32_BIOS -Property SerialNumber).SerialNumber` # eliminates the need to pipe to *Select-Object*
  * `Get-CimClass <class|*keyword*>` #
    * `Get-CimClass *disk*`

Get-CimInstance doesn't have a *-Credential* parameter so the solution in this scenario is to create a CimSession first.
Then I use the CimSession instead of a computer name to query WMI on the remote computer.

1. `$CimSession = New-CimSession -ComputerName dc01 -Credential (Get-Credential)`
   The CIM session is stored in a variable named *$CimSession*  
   *Get-Credential* is put in parentheses so that it executes first, prompting for alternate credentials, before creating the new session.
2. `Get-CimInstance -CimSession $CimSession -ClassName Win32_BIOS`  
   The CIM session created in the previous example can now be used with the Get-CimInstance cmdlet to query the BIOS information from WMI on the remote computer.


### PowerShell
* Microsoft.PowerShell.Utility
  * `Get-Host` # 
    * Gets an object that represents the current host program (powershell)
    * includes the Windows PowerShell version number and the current region and language settings
    * Alias: `host`

### Logs

* Microsoft.PowerShell.Management
  * `Get-EventLog` # gets events and event logs from local and remote computers
    * `-LogName HardwareEvents|Security|System|Applikation` #
    * `-ComputerName <computer>` #   
    * `-Newest 10 -LogName "System"`
      * `Get-EventLog System Newest 3` # Aufruf der drei neuesten Einträge im System-Ereignisprotokoll
    * `-UserName <user>` # Specifies, as a string array, user names that are associated with events.
    * `-Message <message>` # Specifies a string in the event message. You can use this parameter to search for messages that contain certain words or phrases.
    * `Get-EventLog System | Where Source -Match "netlogon" | where EntryType -Match "Error"`
* Microsoft.PowerShell.Diagnostics
  * `Get-WinEvent` # gets events from event logs, including classic logs, such as the *System* and *Application* logs, and the event logs that are generated by the *Windows Event Log* technology introduced in Windows Vista.
    * Parameters:
      * `-ListLog <string>` # specifies the event logs. Enter the event log names in a comma-separated list. Wildcards are permitted.
      * `-MaxEvents <n>` # the maximum number of events to be returned
      * `-ListProvider <string>` # specifies the event log providers. An event log provider is a program or service that writes events to the event log.
        * `(Get-WinEvent -ListProvider Microsoft-Windows-GroupPolicy).Events | Format-Table Id, Description`
      * `-ProviderName <string>` # Gets events written by the specified event log providers. Enter the provider names in a comma-separated list, or use wildcard characters to create provider name patterns.
        * `Get-WinEvent -ProviderName *evo* -MaxEvents 1 | ft TimeCreated, Id, Level, ProviderName, LogName, LevelDisplayName, Message`
      * `-LogName <string>` # Gets events from the specified event logs. Enter the event log names in a comma-separated list. Wildcards are permitted.
    * Usage:
      * get logs by keyword:
        * `Get-WinEvent -ListLog *<keyword>*` # get the *LogName* to *keyword*
        * `Get-WinEvent -LogName "<LogName>"` # Das Log auslesen
      * get information about a log:
        * `Get-WinEvent -ListLog <log> | Format-List -Property *`
      * get all providers that write to a log:
        * `(Get-WinEvent -ListLog <LogName>).ProviderNames` # displays the providers that write to the Application log
      * get all logs with more than one record:
        * `Get-WinEvent -ListLog * | Where-Object RecordCount -GT 1` # 
    * FilterHastable:
      * parameters:
        * `LogName=<value>`
        * `ProviderName=<value>`
        * `Path=<value>`
        * `Keywords=<value>`
        * `ID=<value>`
        * `Level=<value>`
        * `StartTime=<value>`
        * `EndTime=<value>`
        * `UserID=<value>`
        * `Data=<value>`
        * `<named-data>=<value>`
      * examples:
        * `Get-WinEvent -FilterHashtable @{ LogName='Application'; ProviderName='.NET Runtime' }`
      * Wann wurde der Rechner neu gestartet?
        * `Get-WinEvent -FilterHashtable @{logname='system'; id=6005}` # Mit dem selben Benutzer auf dem angemeldeten Rechner
        * `Get-WinEvent -FilterHashtable @{logname='system'; id=6005} -ComputerName HOSTNAME` # Mit dem angemeldeten Benutzer auf einen anderen Rechner
        * `Get-WinEvent -FilterHashtable @{logname='system'; id=6005} -ComputerName HOSTNAME -credentials get-credential` # ...mit einem anderen Benutzer
      

#### Get log information from event object properties
`$Event = Get-WinEvent -LogName 'Windows PowerShell'`
`$Event.Count` # get the number of records
`$Event | Group-Object -Property Id -NoElement | Sort-Object -Property Count -Descending`
`$Event | Group-Object -Property LevelDisplayName -NoElement`

#### filter has tables
When you work with large event logs, it's not efficient to send objects down the pipeline to a Where-Object command.
`Get-EventLog -LogName Application | Where-Object Source -Match defrag`
`Get-WinEvent -FilterHashtable @{LogName='Application'; ProviderName='*defrag'}`

#### log providers

* Microsoft-Windows-GroupPolicy

### DISM
* Dism
  * `Get-WindowsOptionalFeature -online` #
  * `Get-WindowsEdition -online` # prints Windows edition 


PROCESSES / SERVICES
=========================

* Microsoft.PowerShell.Management
  * `Get-Process`
    * lists all processes   
  * `Get-Process <process>`
    * equal to `get-process -name <process>`
    * `-IncludeUserName` #
      * `Get-Process -IncludeUserName *evo*` 
  * `Stop-Process <id>` # stops process by id 
    * `-Name <process>` # stops process by name
    * `Get-Process | where { $_.WS -gt 10MB } | stop-process` # Find the processes that use more than 10 MB of memory and kill them
  * `Get-Service` #  
  * `Get-Service <service>` #  
    * `Get-Service -ComputerName <computer>`
    * `Get-Service | Out-String -Stream | Select-String "running"` # lists all running services
    * `Get-Service | Where-Object {$_.status -eq "running"}` # lists all running services
  * `Stop-Service <service>` # 


FILESYSTEM
=========================


* Microsoft.PowerShell.Management
  * `Get-ChildItem` # 
    * Alias: `ls`
  * `Get-PSDrive` # gets the drives in the current session. The following types of drives are included:
    * Windows logical drives on the computer, including drives mapped to network shares.
    * Drives exposed by PowerShell providers (such as the *Certificate:*, *Function:*, and *Alias:* drives) and the *HKLM:* and *HKCU:*
  * `Get-Location` # Gets information about the current working location or a location stack
    * Alias: `pwd`
  * `Rename-Item`
    * `Get-ChildItem *docx | Rename-Item -NewName {$_.name -replace " ","_"}`
  * `New-Item` # Creates a new item.
    * file system: creates files and folders.
    * registry: creates registry keys and entries.
      * `New-Item -Path '<path>' -ItemType File`
  * **`Test-Path`**
    * Parameters:
      * `-PathType` # Specifies the type of the final element in the path. This cmdlet returns $True if the element is of the specified type and $False if it is not. The acceptable values for this parameter are:
        * `Container`   # An element that contains other elements, such as a directory or registry key.
        * `Leaf`        # An element that does not contain other elements, such as a file.
        * `Any`         # Either a container or a leaf.
    * Usage:
        * `Test-Path -Path E:\reports\processes.txt`
      * Check whether there are any files besides a specified type:
        * `Test-Path -Path "C:\CAD\Commercial Buildings\*" -Exclude *.dwg`
      * Test if a file is newer than a specified date:
        * `Test-Path $pshome\PowerShell.exe -NewerThan "July 13, 2009"`


### Input / Output

* Microsoft.PowerShell.Management
  * `Get-Content <path>` # gets the content of the item at *path*, such as the text in a file or the content of a function.
    * Alias: `cat`, `gc`
    * Files: the content is read one line at a time and returns an array of string-objects, each of which represents a line of content.
    * `-TotalCount <n>` # the number of lines from the beginning of a file or other item
    * `-Tail` # the number of lines from the end of a file or other item
  * `Set-Content` # Writes new content or replaces existing content in a file.
    * Alias: `sc`
  * `Add-Content <path> <string>` # appends content to a specified item or file
* Microsoft.PowerShell.Utility
  * **`Write-Output`**
    * `Write-Output "<text>" > <file>`  # (over)write
    * `Write-Output "<text>" >> <file>` # append
    * Alias: `echo` 
  * `Write-Host "<text>"`
    * Writes directly to the host and sends nothing to the PowerShell engine to be forwarded to commands later in the pipeline.
    * Richer output for a user interface
    * Parameters:
      * `-ForegroundColor <color>` 
      * `-BackgroundColor <color>`
      * `-NoNewline`
  * `Read-Host`
    * Parameters:
      * `-AsSecureString` # allows the user to not only store the output as a secure string but also to show asterisk while typing
    * Usage:
      * `$variable = Read-Host -Prompt '<text>'`
      * `$serverName = Read-Host -Prompt 'Server name to process'`

COMMANDS / MODULES
---------------------

#### Get-Command / Invoke-Command / Modules

* Microsoft.PowerShell.Core
  * `Get-Command <cmd>` #
    * Alias: `gcm`
    * `Get-Command -Module <module>` # prints all commands contained in \<module\>   
    * `Get-Command -Module Microsoft.PowerShell.Utility` # 
  * `Get-Help <cmd>` #  
    * `get-help -examples`
    * `get-help <cmd> -Parameter <parameter>`   
    * Alias: `man`, `help` #
  * `Invoke-Command` # 
  * `Invoke-Command -ComputerName <computer>` # 
  * `Invoke-Command -ComputerName <computer> -ScriptBlock { <cmd> }` # Specifies the commands to run. Enclose the commands in curly braces `{ }` to create a script block.
  * `Invoke-Command -ComputerName <computer> -FilePath <path>` # Specifies a local script that this cmdlet runs on one or more remote computers
  * `Get-Module -ListAvailable` # print all modules installed on the system

#### Alias

* Microsoft.PowerShell.Utility
  * `Get-Alias` # prints all aliases
    * `Get-Alias <alias>` # get cmdlet for alias
    * `get-alias | Out-String -Stream | Select-String <cmdlet>` # get aliases for *cmdlet*
    * `Get-Alias -Definition <cmdlet>` # get aliases for *cmdlet*

#### Add new modules
1) `$Env:PSModulePath` # Determine the install Path  
2) Copy new module to path
3) `Get-Module -ListAvailable` # verify the new module is visible to PowerShell
4) `Import-module -name <module>` #  


UTILITY
=========================

* `more` # seitenweise durchblättern
  * `Get-Services | more` 
* Microsoft.PowerShell.Core
  * `Out-Host` # Sends output to the command line.
    * automatically appended to every command that is executed.
    * `-Paging` # Indicates that it's  displayed just one page of output at a time; waits for user input before the remaining pages are displayed.


#### Select / Format / Sort / Filter / Group / Convert

* Microsoft.PowerShell.Utility
  * **`Select-Object <property>`** # selects specified properties of an object; useful to narrow things down by including only the properties you are really interested in
    * parameters
      * `-Last <n>` # like tail  
      * `-Property <property>` # separated by comma  
      * `-ExpandProperty <property>` # expands the given property (returns a string)
        * `Get-Process | Select-Object -Property ProcessName, Id, WS`
  * **`Select-String`** 
    * description
      * searches for text and text patterns in input strings and files
      * based on lines of text.
    * parameters
      * `-Pattern <regex>` # Specifies the text to find on each line. The pattern value is treated as a regular expression.
      * `-SimpleMatch <string>` # use a simple match rather than a regular expression match.
      * `-Context <n,n>` # Captures the specified number of lines before and after the line that matches the pattern.
  * `Format-List` # Formats the output of a command as a list of properties in which each property is displayed on a separate line. 
    * Alias: `fl`
    * examples
      * get all properties of an object:
      * `<object> | Format-List -Property *`
  * `Format-Table` # Formattiert die Ausgabe eines Befehls als Tabelle, wobei die ausgewählten Eigenschaften des Objekts in jeweils einer Spalte dargestellt werden. Der Objekttyp bestimmt das Standardlayout und die Standardeigenschaften, die in den einzelnen Spalten angezeigt werden. Sie können jedoch mit dem Property-Parameter die Eigenschaften auswählen, die angezeigt werden sollen.
    * `-AutoSize` # Indicates that the cmdlet adjusts the column size and number of columns based on the width of the data. By default, the column size and number are determined by the view.
  * **`Out-String`** # cmdlet converts the objects that PowerShell manages into an array of strings
    * parameters
      * `-Stream` # sends a separate string for each object. By default, the strings for each object are accumulated and sent as a single string.
        * `Get-Service | Out-String -Stream | Select-String "running"`
  * `Sort-Object <property>` # Sorts objects by property values.
  * `Get-Member` # Gets the properties and methods of objects. To specify the object, use the InputObject parameter or pipe an object to `Get-Member`.
  * `Group-Object` # 
    * description
      * displays objects in groups based on the value of a specified property.
      * returns a table with one row for each property value and a column that displays the number of items with that value.
      * If you specify more than one property, `Group-Object` first groups them by the values of the first property, and then, within each property group, it groups by the value of the next property.
  * **`Get-Date`**
    * methods
      * `<date-object>.addDays(<int>)`
        * `$sevenDaysAgo = (Get-Date).AddDays(-7)`
  * **`ConvertTo-Html`** # converts .NET objects into HTML
    * `Get-Service | ConvertTo-HTML -Property Name, Status > C:\services.htm`
* Microsoft.PowerShell.Core
  * `Where-Object <property> <operator> <value>` # Selects objects from a collection based on their property values
    * Alias: `where`
    * script block
      * You can use a script block to specify the property name, a comparison operator, and a property value. *Where-Object* returns all objects for which the script block statement is true.
      * `Get-Process | Where-Object {$_.PriorityClass -eq "Normal"}` # gets processes where the value of the PriorityClass property equals Normal.
    * comparison statement
      * Logical operators, such as `And` and `Or`, are valid only in script blocks. You cannot use them in the comparison statement format of a Where-Object command.
      * These commands are equivalent and can be used interchangeably:
      * `Get-Process | Where-Object -Property PriorityClass -eq -Value "Normal"`
      * `Get-Process | Where-Object PriorityClass -eq "Normal"`
    * operators
      * `Where-Object <property> -Eq "<string>"` # equals value
      * `Where-Object <property> -Match "<regex>"` # match regex
      * `Where-Object <property> -Like "<*wildcards*>"` # match string with wildcards
      * `Where-Object <property> -NotLike "<*wildcards*>"` # match string with wildcards


USER/GROUPS: LocalAccounts
=========================

* Microsoft.PowerShell.LocalAccounts
  * `Get-LocalGroup`
    * Gets the local security groups
    * `Get-LocalGroup -SID <sid>`            
  * `Get-LocalUser`
    * Gets local user accounts.
  * `Get-LocalGroupMember -Group <group>`
    * Gets members from the given local group.
  * `Disable-LocalUser` #
    *  `Disable-LocalUser -Name <name>` # 


NETWORK
=========================

* DnsClient
  * `Resolve-DnsName` 
* `Invoke-WebRequest` # sends HTTP, HTTPS, FTP, and FILE requests to a web page or web service. It parses the response and returns collections of forms, links, images, and other significant HTML elements.
  * `-OutFile <path>` # Specifies the output file for which this cmdlet saves the response body.
  * `-UseBasicParsing` # By default, script code in the web page may be run when the page is being parsed to populate the *ParsedHtml* property. Use this parameter to suppress this.
    * `Invoke-WebRequest https://www.evo-solutions.com/de/ -UseBasicParsing -OutFile evo-solutions.html` #
* Microsoft.PowerShell.Management
  * `Test-Connection` # sends Internet Control Message Protocol (ICMP) echo request packets (pings)

### TCP/IP

* NetTCPIP
  * `Test-NetConnection <ip|dns> -p <port>` # supports ping test, TCP test, route tracing, and route selection diagnostics
    * `Test-NetConnection 192.168.178.35 -p 389`
    * `Test-NetConnection tomeelive -port 8080`
    * `Test-NetConnection <destination> -TraceRoute`
    * `Invoke-Command -ComputerName client001 {Test-NetConnection dc01 -Port 53 -InformationLevel Quiet}` # Can client001 establish a connection to dc01 via DNS?
    * `1..99 | % { Test-NetConnection -ComputerName x.x.x.$_ } | FT -AutoSize`
  * `Get-NetIPAddress` # Gets the IP address configuration.
    * `Get-NetIPAddress -InterfaceAlias <interface>` # Gets the IP address for the given interface
    * `Get-NetIPAddress -AddressFamily ipv4` # Get only IPv4 addresses
  * `Get-NetIPConfiguration` # Gets IP network configuration
  * `Get-NetRoute` # Gets the IP route information from the IP routing table
    * *ActiveStore* # Aktiv bis zum nächsten Neustart
    * *Persistantstore* # Dauerhaft aktiv – die PersistanStore Regeln werden nach einem Neustart in den ActiveStore kopiert
    * `Get-NetRoute | ft -a` # 
    * `Get-NetRoute -InterfaceIndex 47 | ft -AutoSize`
  * **`New-NetRoute`**
    * `New-NetRoute –DestinationPrefix „192.168.222.111/32“ -InterfaceIndex 67 –NextHop 192.168.0.1 –RouteMetric 10 –PolicyStore ActiveStore | ft –a`
  * **`Get-NetTCPConnection`** # Gets TCP connections; replaces Netstat
    * Parameters:
      * `-State listen|established|closed`
      * `-RemoteAddress <ip>`
    * Usage:
      * `Get-NetTCPConnection -State Listen | Sort-Object -Property OwningProcess | Format-Table LocalAddress,LocalPort,RemoteAddress,RemotePort,State,OwningProcess`
      * figure out which process is behind a specific port:
        * `Get-NetTCPConnection -LocalPort <port> | Format-Table OwningProcess`
        *  `Get-Process -Id <id>`
      * Which process is behind which port?
        * `$ports = Get-NetTCPConnection -State listen | Select-Object -ExpandProperty LocalPort` # stores the numbers of all listening ports as a string in a variable
        * `Get-Process -Id (Get-NetTCPConnection -LocalPort $ports).OwningProcess | Sort-Object Id`
* NetAdapter
  * **`Get-NetAdapter`** # Gets the basic network adapter properties.
    * `Get-NetAdapter | ? Status -eq Up`
    * `Get-NetAdapter | ft Name, DriverName, DriverVersion, DriverInformation, DriverFileName` # Get driver information

#### VPN connection

* Check a VPN connection
  * `Get-NetAdapter` # --> TAP-Windows Adapter V9 --> ifIndex 3
  * `Get-NetRoute -InterfaceIndex 3 | ft -AutoSize`

#### Which process is behind which port?

1. `$ports = Get-NetTCPConnection -State listen | Select-Object -ExpandProperty LocalPort` # stores the numbers of all listening ports as a string in a variable
2. `Get-Process -Id (Get-NetTCPConnection -LocalPort $ports).OwningProcess`

### Domain

* Microsoft.PowerShell.Management
  * `Add-Computer` # adds the local computer or remote computers to a domain or workgroup, or moves them from one domain to another

### SMB

`Get-WindowsOptionalFeature -Online -FeatureName smb1protocol`
`Disable-WindowsOptionalFeature -Online -FeatureName smb1protocol`  
`Set-SmbServerConfiguration -EnableSMB2Protocol $false` # disable 

* SmbShare
  * `Get-SmbShare` # Retrieves the SMB shares on the computer.
    * Usage:
      * View shares on a remote server using a *CIM-Session*
        * `$cim = New-CimSession -ComputerName <name>`
        * `Get-SmbShare -CimSession $cim`
      * View shares on a remote server using *Invoke-Command*
        * `Invoke-Command -ComputerName "FILE01","FILE02" -ScriptBlock {Get-SmbShare} | Format-Table -Property Name,Path,Description,PSComputerName`
  * `Get-SmbShareAccess` # Retrieves the ACL of the SMB share
  * `Get-SmbConnection` # Retrieves the connections established from the SMB client to the SMB servers
  * `Get-SmbClientNetworkInterface` # Retrieves the network interfaces used by the SMB client.
  * `Get-SmbServerConfiguration` # Retrieves the SMB server configuration.
  * `Get-SmbClientConfiguration` # Retrieves the SMB client configuration.  


```powershell
# get a list of all servers in the File Servers organizational unit
# get their smb shares

$FileServAD = Get-ADComputer -SearchBase "OU=File Servers,OU=Servers,DC=corp,dc=ad" -Filter * | Select-Object -ExpandProperty Name
Invoke-Command -ComputerName $FileServAD -ScriptBlock { Get-SmbShare -Special $false } | Format-Table -Property Name,Path,Description,PSComputerName
```

### Remote Powershell sessions
* Microsoft.PowerShell.Core
  * **`Enter-PSSession`** # starts an interactive session with a single remote computer. 
  * Parameters:
    * `-ComputerName <name>`

### GPO

install: *Remote Server Administration Tools* (RSAT)


### Active Directory

* `Get-Command *gpo*` #  
* `Import-Module ActiveDirectory` #   

* Applikation
  * `gpresult /V` #  

#### accounts
`New-ADUser <user>` #   
`Get-ADUser -Filter * -Properties samAccountName | select samAccountName` # lists all Active Directory users


#### certificates
`sigcheck -u -e c:\windows\system32` #

### e-mails

```batch
  $EmailFrom = “yourgmailadress@gmail.com”
  $EmailTo = “destination@somedomain.com”
  $Subject = “The subject of your email”
  $Body = “What do you want your email to say”
  $SMTPServer = “smtp.gmail.com”
  $SMTPClient = New-Object Net.Mail.SmtpClient($SmtpServer, 587)
  $SMTPClient.EnableSsl = $true
  $SMTPClient.Credentials = New-Object System.Net.NetworkCredential(“usr”, “pass”);
  $SMTPClient.Send($EmailFrom, $EmailTo, $Subject, $Body)
```
```
powershell.exe -command send-mailmessage -to to@domain.com -from from@domain.com -subject '"My Testmail"' -smtpserver 10.10.10.10 -body '"This is a test with spaces."'
```

SECURITY
=========================

* Microsoft.PowerShell.Security
  * `Get-ExecutionPolicy`
    * see the effective execution policy for your PowerShell session
  * `-List`
    * Gets all execution policy values for the session listed in precedence order
  * `Set-ExecutionPolicy`
    * Set-ExecutionPolicy RemoteSigned
  * `Get-Credential`
    * `$credential = Get-Credential`
* NetSecurity
  * Retrieves firewall rules from the target computer
  * **`Get-NetFirewallRule`**
    * `Get-NetFirewallRule | Select-Object DisplayName, Enabled, Direction, Action -First 10`
  * `Get-NetFirewallPortFilter` # returns the port filter objects associated with the input rules.
    * Usage:
      * get the rules corresponding to a specific port:
        * `Get-NetFirewallPortFilter | Where LocalPort -eq 162 | Get-NetFirewallRule`
        * `Get-NetFirewallPortFilter | Where LocalPort -eq 80 | Get-NetFirewallRule | Where Enabled -eq true`
      * gets the rules 
        * `$rules = (Get-NetFirewallRule | Where {$_.Enabled -eq "true" -and $_.Action -eq "Allow" -and $_.Direction -eq "Inbound"})`
        * `$rules = Get-NetFirewallRule -Action Block -Enabled True -Direction Inbound`

### Checks

`$ports = Get-NetTCPConnection -State listen | Select-Object -ExpandProperty LocalPort`
`Get-Process -Id (Get-NetTCPConnection -LocalPort $ports).OwningProcess | Sort-Object Id`
`Get-NetFirewallPortFilter | Where-Object LocalPort -eq <port> | Get-NetFirewallRule | Where-Object Enabled -eq true`

### Windows Defender

`Get-Command -Module Defender` # Prompts the user for a password or a user name and password. Returns a credential object based on a user name and password. You can use the credential object in security operations.

#### ExecutionPolicies

The execution policy isn't a security system that restricts user actions. For example, users can easily bypass a policy by typing the script contents at the command line when they cannot run a script. Instead, the execution policy helps users to set basic rules and prevents them from violating them unintentionally.

* *Restricted* — Stops any script from running.
* *RemoteSigned* — Allows scripts created on the device, but scripts created on another computer won't run unless they include a trusted publisher's signature.
* *AllSigned* — All the scripts will run, but only if a trusted publisher has signed them.
* *Unrestricted* — Runs any script without any restrictions.

STORAGE
============

* Storage
  * `Get-FileShare` # gets objects that correspond to the file shares on the specified server. You must have local administrator credentials on a server to retrieve the objects.
    * Parameters:
      * `-Protocol SMB|NFS`
      * `-FileServer` # Specifies the file server on which the file share is hosted. To obtain a FileServer, use `Get-StorageFileServer`
    * Usage:
      * `Get-FileShare -Protocol NFS -FileServer (Get-StorageFileServer -FriendlyName "FileServer01")`
  * `Get-StorageFileServer` # gets the specified storage file server or all discovered storage file servers.

PRINTER
===========

* PrintManagement
  * **`Get-Printer`**
    * `Get-Printer | Where-Object Type -eq "Connection" | ft -AutoSize`