# Storage-Health

Function Storage-Health{


<# These are your target servers #>
$servers = @("$server1")

foreach($server in $servers){

<# This is where you clear out some files that are taking up space #>
Get-ChildItem -Path "\\$server\REMOTE\FILE\LOCATION\" | Remove-Item -Recurse -Force

Get-ChildItem -Path "\\$server\REMOTE\FILE\LOCATION\" | Remove-Item -Recurse -Force

<# Varaible to target a specific content in the file location #>
$files = get-childitem "\\$server\REMOTE\FILE\LOCATION\" -Exclude "*.zip"

Foreach($file in $files.name){

<# Varaible for the location #>
$targetpath = "\\$server\REMOTE\FILE\LOCATION\$file.zip"

<# Going to loop a compression command pointing to a folder thats eating up storage #>
$compress = @{
  Path = "\\$server\REMOTE\FILE\LOCATION\$file"
  CompressionLevel = "Fastest"
  DestinationPath = $targetpath 
}

<# Compressing using the specifics with in $compress #>
Compress-Archive @compress

<# Using rmdir to remove the folder after compressing to prevent storage lock up #>
rmdir "\\$server\REMOTE\FILE\LOCATION\$file" -Force -recurse

}
Foreach($file in $files.name){


<# We dont want to keep unessisary compressed files so this helps manage that #>
Get-ChildItem –Path "\\$server\REMOTE\FILE\LOCATION\" -Recurse | Where-Object {($_.LastWriteTime -lt (Get-Date).AddDays(-365))} | Remove-Item

}

}

}

Storage-Health
