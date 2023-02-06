$domain=“@winitpro.loc”
Import-Module activedirectory
Import-Csv "C:\ps\new_ad_users2.csv" | ForEach-Object {
$userSAM=$_.SamAccountName
if (@(Get-ADUser -Filter "SamAccountName -eq '$($_.SamAccountName)'").Count -ne 0) {
Add-Type -AssemblyName Microsoft.VisualBasic
$userSAM = [Microsoft.VisualBasic.Interaction]::InputBox("Пользователь с именем $_.SamAccountName уже существует", 'Укажите новое имя пользователя', $_.SamAccountName)
}
$upn = $userSAM + $domain
$uname = $_.LastName + " " + $_.FirstName + " " + $_.Initials
#переводим в транслит фамилию, имя и отчество
$transLastName=Translit($_.LastName)
$transFirstName=Translit($_.FirstName)
$transInitials=Translit($_.Initials)
$transuname = $transLastName + " " + $transFirstName + " " + $transInitials
New-ADUser -Name $transuname `
-DisplayName $uname `
-GivenName $_.FirstName `
-Surname $_.LastName `
-Initials $_.Initials `
-OfficePhone $_.Phone `
-Department $_.Department `
-Title $_.JobTitle `
-UserPrincipalName $upn `
-SamAccountName $userSAM `
-Path $_.OU `
-AccountPassword (ConvertTo-SecureString $_.Password -AsPlainText -force) -Enabled $true
}
Ниже представлен код функции транслитерации (вставить выше кода основного скрипта):
#сама функция транслитерации
function global:Translit {
param([string]$inString)
$Translit = @{
[char]'а' = "a"
[char]'А' = "A"
[char]'б' = "b"
[char]'Б' = "B"
[char]'в' = "v"
[char]'В' = "V"
[char]'г' = "g"
[char]'Г' = "G"
[char]'д' = "d"
[char]'Д' = "D"
[char]'е' = "e"
[char]'Е' = "E"
[char]'ё' = "yo"
[char]'Ё' = "Yo"
[char]'ж' = "zh"
[char]'Ж' = "Zh"
[char]'з' = "z"
[char]'З' = "Z"
[char]'и' = "i"
[char]'И' = "I"
[char]'й' = "j"
[char]'Й' = "J"
[char]'к' = "k"
[char]'К' = "K"
[char]'л' = "l"
[char]'Л' = "L"
[char]'м' = "m"
[char]'М' = "M"
[char]'н' = "n"
[char]'Н' = "N"
[char]'о' = "o"
[char]'О' = "O"
[char]'п' = "p"
[char]'П' = "P"
[char]'р' = "r"
[char]'Р' = "R"
[char]'с' = "s"
[char]'С' = "S"
[char]'т' = "t"
[char]'Т' = "T"
[char]'у' = "u"
[char]'У' = "U"
[char]'ф' = "f"
[char]'Ф' = "F"
[char]'х' = "h"
[char]'Х' = "H"
[char]'ц' = "c"
[char]'Ц' = "C"
[char]'ч' = "ch"
[char]'Ч' = "Ch"
[char]'ш' = "sh"
[char]'Ш' = "Sh"
[char]'щ' = "sch"
[char]'Щ' = "Sch"
[char]'ъ' = ""
[char]'Ъ' = ""
[char]'ы' = "y"
[char]'Ы' = "Y"
[char]'ь' = ""
[char]'Ь' = ""
[char]'э' = "e"
[char]'Э' = "E"
[char]'ю' = "yu"
[char]'Ю' = "Yu"
[char]'я' = "ya"
[char]'Я' = "Ya"
}
$outCHR=""
foreach ($CHR in $inCHR = $inString.ToCharArray())
{
if ($Translit[$CHR] -cne $Null )
{$outCHR += $Translit[$CHR]}
else
{$outCHR += $CHR}
}
Write-Output $outCHR
}
