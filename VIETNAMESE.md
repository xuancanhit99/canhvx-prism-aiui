# Vietnamese UI on Rokid RV101

This local extension adds all 116 Vietnamese UI strings, Vietnamese widget
metadata, and an app-specific language preference. Other languages remain
available. The glasses' system language and conversation contents are unchanged.

## Build and select a language

Use the upstream development prerequisites, sign in to Prism Hub, and connect
the glasses through ADB. In PowerShell, inside this repository:

```powershell
$prismSerial = Read-Host 'ADB connection address or USB serial'
$env:ANDROID_SERIAL = $prismSerial
npm.cmd test
npm.cmd run debug
```

The debug package is written outside the repository, under
`../.aiui-build/<checkout-name>/package.aix`. It includes private sign-in material:
keep it local and do not upload it, screenshots, or the Hub config to a fork.
The checked-in `lib/prism-local-token.js` must always contain empty credentials.

Exit the active Prism page fully before changing language. Back from a
conversation returns to the inbox; back twice from the inbox exits. A launch
request can otherwise leave an old page visible.

```powershell
$prismBuild = Join-Path (Split-Path -Parent (Get-Location).Path) ('.aiui-build/' + (Split-Path -Leaf (Get-Location).Path))
$prismLocale = Join-Path $prismBuild 'language-vi.json'
'{"locale":"vi-VN"}' | Set-Content -LiteralPath $prismLocale -Encoding utf8
aix.cmd launch-page --serial $prismSerial --params-file $prismLocale "$prismBuild/package.aix" pages/index/index
```

Use `en-US` for English or `auto` for system detection. The preference is saved
as `prism.locale` within this agent. Reopening the same installed agent without
parameters preserves it. The pinned packer generates a new UUID on each build,
so rebuilding can create a separate agent with separate settings. Record the
installed ID and package and distinguish old/new entries before removing any.

Device CLI 0.10.1 accepts `--serial` after a subcommand. The debug helper uses
that ordering. The pack wrapper independently pins CLI 0.8.2; these two versions
serve different roles and must not be conflated.

## Windows connector

The companion `codex-windows` plugin 0.1.37-windows.4 sends generated progress,
task, and action labels in English. Actual user/assistant text and labels read
from Codex retain their original language. The UI extension does not replace
Codex or change its account/model settings.

## Validation and limits

Observed on RV101 / Android API 32 on 2026-10-16: Vietnamese accents rendered in
the inbox and menu, saved locale survived reopening, and conversation history
and live responses arrived through Prism Hub. The UI suite passed, including
locale key/placeholder parity and preference switching. Vietnamese speech input
requests `vi-VN`; recognition and a full wearer-initiated voice round trip were
not separately established by these checks.

The upstream [LICENSE](LICENSE) is PolyForm Noncommercial 1.0.0 with additional
notices about Rokid cloud endpoints and Agent Store publication. Retain it;
this fork does not grant rights to those services or change the source license.
