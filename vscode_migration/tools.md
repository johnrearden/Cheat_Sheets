# Tools

### Delete a directory on WIN (and all contents) in Powershell

```Remove-Item -Path "C:\Temp\OldFolder" -Recurse -Force```

### venv/Scripts/activate won't run (permissions probem)

- Run PowerShell as administrator
    - Run this : ```Get-ExecutionPolicy```. If it's set to Restricted, try setting it 
    first to RemoteSigned : ```Set-ExecutionPolicy RemoteSigned```

### Check all versions of python on $PATH in powershell

```$env:Path -split ';' | ForEach-Object { Get-ChildItem -Path $_ -Filter "python*.exe" -ErrorAction SilentlyContinue }```

    

