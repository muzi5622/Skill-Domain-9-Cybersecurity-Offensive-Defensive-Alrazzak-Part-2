# Lab 2: Windows PowerShell Fundamentals

### What I Can Do Now
- I can launch and use PowerShell on Linux (`pwsh`)
- I manage files/folders faster than with bash (thanks to aliases like `ls`, `cp`, `mv`, `rm`, `mkdir`)
- I write simple scripts with variables, if-else, loops
- I automate boring tasks in seconds
- I understand the same commands used by red teams and blue teams

### How I Start
```bash
pwsh
```

### Commands I Use Every Day
| What I want          | Command I type                        | Alias I actually use |
|----------------------|---------------------------------------|----------------------|
| List files           | `Get-ChildItem`                       | `ls`                 |
| See hidden files     | `ls -Force`                           |                      |
| Make folder          | `New-Item -ItemType Directory X`      | `mkdir X`            |
| Touch/create file    | `"" > file.txt`   or   `ni file.txt`  |                      |
| Copy                 | `Copy-Item a.txt b/`                  | `cp a.txt b/`        |
| Copy whole folder    | `cp Folder Backup -Recurse`           |                      |
| Move or rename       | `Move-Item old.txt new.txt`           | `mv old.txt new.txt` |
| Delete               | `Remove-Item file.txt`                | `rm file.txt`        |
| Get help + examples  | `Get-Help ls -Examples`               |                      |

### Scripts I Wrote & Use

**Project starter (I run this all the time)**
```powershell
$p = Read-Host "Project name"
mkdir $p; cd $p
mkdir src, docs, tests
"" > src/main.ps1
"# $p" > README.md
Write-Host "$p ready!" -ForegroundColor Green
```

**Auto-organize files by extension (saves me hours)**
```powershell
ls -File | Group Extension | ForEach-Object {
  $folder = $_.Name.TrimStart('.') + "s"
  mkdir $folder -Force
  mv "*$($_.Name)" $folder
}
```

### Quick Practice I Did
```powershell
mkdir -Force Lab/A/B/C; "" > Lab/A/B/C/test.txt
1..20 | ForEach-Object { "data" > "file$_.txt" }
mkdir Backup; cp file*.txt Backup/
```

I now feel confident using PowerShell on Linux for sysadmin, scripting, and cybersecurity tasks!  
