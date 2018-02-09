# PowerShellProjects
$needtowrite= 0
Write-Host "Num Args:" $args.Length;
foreach ($arg in $args)
{
    if($arg -eq "-w"){
      $needtowrite= 1
      Write-Host "Arg: $arg";
     }
}
  
$batnames = Get-Content upgrade.bat
$fileNames = Get-ChildItem "*.sql" | select-object name
$i = 0
$ErrorText = ""
$Testing = "ERRORS.txt"
$linestart = "*"+"rem -- S_POS --"+"*"
for ($i=$batnames.Length; $i -gt 0 ; $i--){
  
        $line =$batnames[$i]
        if($line -like $linestart){
           $lineNumber = $i
           break
          }
}
if (Test-Path $Testing){
 Remove-Item $Testing}
for ($i=0; $i  -le $fileNames.Length; $i++){
 $searchForThis = "*" +  $fileNames[$i].Name + "*"
    
    if(!($batnames -like $searchForThis )) {
        Write-Host "not-found" + $searchForThis 
        $ErrorText =  $ErrorText + $fileNames[$i].Name +"`r`n"
    if ($needtowrite -eq 1){
        $indexofdot = $fileNames[$i].Name.IndexOf(".")
        $substring = $fileNames[$i].Name.Substring(0,$indexofdot)
  $batnames[$lineNumber] =$batnames[$lineNumber] + "`r`n`r`nsqlcmd -S %1 -d %4 -E -I -i " + $substring + ".sql >> Logs\" + $substring + ".txt 
    sqlcmd -S %2 -d %4 -E -I -i "+ $substring + ".sql >> Logs\"+ $substring +".txt 
    sqlcmd -S %3 -d %4 -E -I -i " + $substring + ".sql >> Logs\" + $substring + ".txt"}
    }
}
if ($needtowrite -eq 1){
    $batnames | out-file upgrade.bat}
If (!(($ErrorText) -eq "")) {
    $ErrorText  | out-file "ERRORS.txt"}
    cls
    
    
    
