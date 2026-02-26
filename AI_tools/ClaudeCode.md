### Claude Code 

```powershell
& ([scriptblock]::Create((irm https://claude.ai/install.ps1))) stable
```

在“用户变量”的 `Path` 中添加：`%USERPROFILE%\.local\bin`

```
claude
```

