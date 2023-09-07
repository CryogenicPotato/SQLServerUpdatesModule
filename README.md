# SqlServerUpdatesModule
The module downloads information about the newest available updates for SQL Server instances.

The module SqlServerUpdatesModule now works on PowerShell 6 (and PowerShell 7-preview).

- 1.1.6.1 - Added support for Sql Server 2022

## Installing
It is not possible to install this forked module using the ```Install-Module``` cmdlet; this is because the OP rightfully hasn't provided the NuGetApiKey along with the source code for publishing on PowerShellGallery.

Currently, it is possible to use the module by cloning it into a local folder and performing an ```Import-Module``` by doing like this:

```
Set-StrictMode -Version Latest

# Import the appropriate nested binary module
$PSModuleRoot = ".\SQLServerUpdatesModule"

$binaryModuleRoot = Join-Path -Path $PSModuleRoot -ChildPath 'net45'
if (($PSVersionTable.Keys -contains "PSEdition") -and ($PSVersionTable.PSEdition -ne 'Desktop')) {
    $binaryModuleRoot = Join-Path -Path $PSModuleRoot -ChildPath 'netstandard2.0'
}

# Load Angle Sharp
$a = Get-ChildItem -Path (Join-Path -Path $binaryModuleRoot -ChildPath 'anglesharp') -Filter *.dll | ForEach-Object {
    [System.Reflection.Assembly]::LoadFile($_.FullName)
}

# Add types to load SMO Assemblies only:
$b = Get-ChildItem -Path (Join-Path -Path $binaryModuleRoot -ChildPath 'smo') -Filter *.dll | ForEach-Object {
    if (($PSVersionTable.Keys -contains "PSEdition") -and ($PSVersionTable.PSEdition -ne 'Desktop')) {
        Add-Type -Path $_.FullName -ErrorAction SilentlyContinue
    }
    else {
        [System.Reflection.Assembly]::LoadFile($_.FullName)
    }
}
# Load function
$c = Get-ChildItem -Path (Join-Path $PSModuleRoot -ChildPath 'function') | ForEach-Object {
    . $_.FullName
}

# CSS file
$script:CSSPath = Join-Path (Join-Path -Path $PSModuleRoot -ChildPath 'css') -ChildPath 'style.css'


# Import-Module -Name SQLServerUpdatesModule
Import-Module ".\SQLServerUpdatesModule"
```

## Usage

Update list for all SQL Server from version 2008 to 2019.
```
Get-SQLServerUpdateList
```
