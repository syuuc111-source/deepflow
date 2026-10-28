# DeepFlow 中文入口

需求探索、规格、执行和验收的 Codex 中文适配入口。

这是独立公开仓库，只包含 **deepflow**。完整技能说明见 [skills/deepflow/SKILL.md](skills/deepflow/SKILL.md)。

## 获取仓库

需要 Git 和 PowerShell。

```powershell
git clone https://github.com/syuuc111-source/deepflow.git
cd deepflow
```

## 安装技能

以下命令在仓库根目录执行，只复制技能文件，已有安装会拒绝覆盖。

```powershell
$codexRoot = if ($env:CODEX_HOME) { $env:CODEX_HOME } else { Join-Path $env:USERPROFILE '.codex' }
$skillRoot = Join-Path $codexRoot 'skills'
New-Item -ItemType Directory -Force -Path $skillRoot | Out-Null
# 仅安装本仓库的技能。
$selected = @('deepflow')
# 先检查所有目标，避免覆盖已有安装。
foreach ($name in $selected) {
    if (Test-Path -LiteralPath (Join-Path $skillRoot $name)) { throw "技能已存在，请先备份并移走旧目录：$name" }
}
foreach ($name in $selected) {
    Copy-Item -LiteralPath (Join-Path '.\skills' $name) -Destination (Join-Path $skillRoot $name) -Recurse
}
```

## 获取上游资料

本技能是适配入口，需要另外获取固定版本上游资料。以下操作不会安装上游运行依赖、API 或 hooks。也可在调用时指定已有源码检出目录。

```powershell
$codexRoot = if ($env:CODEX_HOME) { $env:CODEX_HOME } else { Join-Path $env:USERPROFILE '.codex' }
$sourceRoot = Join-Path $codexRoot 'skill-sources'
New-Item -ItemType Directory -Force -Path $sourceRoot | Out-Null
$deps = Get-Content -LiteralPath '.\dependencies.json' -Raw | ConvertFrom-Json
$selected = @('deepflow')
foreach ($name in $selected) {
    if (Test-Path -LiteralPath (Join-Path $sourceRoot $name)) { throw "依赖目录已存在，请检查或备份：$name" }
}
foreach ($name in $selected) {
    $spec = $deps.$name
    $target = Join-Path $sourceRoot $name
    git clone --filter=blob:none --no-checkout $spec.url $target
    if ($LASTEXITCODE -ne 0) { throw "clone 失败：$name" }
    git -C $target checkout --detach $spec.commit
    if ($LASTEXITCODE -ne 0) { throw "checkout 失败：$name" }
}
```

## 使用

客户端重新加载技能或新建会话后：

```text
$deepflow 根据这个功能需求编写有可验证验收标准的规格。
```

## 检查

需要 Python 3.9+。

```powershell
python scripts/validate.py
```

校验覆盖文件头、来源清单、文档链接和机器专用路径；实际领域运行需对应的软件与数据。本次拆分没有执行建模、渲染、皮肤应用或收费 API。

## 许可与来源

[MIT](LICENSE) · [第三方声明](THIRD_PARTY.md)。来源及安装依赖独立保留，不把上游项目称作原创。
