# Mini scripts bat file

Mini scripts to create and open folders in a bat file.

## 1. Create folders 

Create folders with folder names in seprate text file ( folder names can have spaces ).

create.bat

```MS-DOS
for /F "usebackq delims=" %%i in (FolderName.txt) do md "%%i"
```

FolderName.txt

```
Folder Name 1
Folder Name 2
```

Ref : [Stackoverflow : Create many folders ..](https://superuser.com/questions/175383/how-can-i-create-many-folders-on-windows-7-from-a-text-file-txt/175389)

## 2 Open multiple folders with single bat file

open.bat

```MS-DOS
@ECHO OFF
ECHO Opening Folders....
start /max %SystemRoot%\explorer.exe "C:\Users\User1\Downloads" 
start /max %SystemRoot%\explorer.exe "D:\Data\Other pgms"
ECHO Finished Opening Folders....
```