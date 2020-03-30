---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: b7d832ba375925d30a976dfde63a776b5d0742bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593709"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Download en installeer de Linkerd linkerd client binaire

Gebruik in `Invoke-WebRequest` een PowerShell-gebaseerde shell op Windows de Linkerd-release als volgt:

```powershell
# Specify the Linkerd version that will be leveraged throughout these instructions
$LINKERD_VERSION="stable-2.6.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-windows.exe" -OutFile "linkerd2-cli-$LINKERD_VERSION-windows.exe"
```

De `linkerd` client binaire draait op uw client machine en stelt u in staat om te communiceren met de Linkerd service mesh. Gebruik de volgende opdrachten om `linkerd` de linkerd-clientbinaire te installeren in een PowerShell-gebaseerde shell op Windows. Deze opdrachten kopiëren `linkerd` de client binaire naar een Linkerd map en vervolgens beschikbaar zowel onmiddellijk (in de `PATH`huidige shell) en permanent (over shell herstart) via uw . U hebt geen verhoogde bevoegdheden (admin) nodig om deze opdrachten uit te voeren en u hoeft uw shell niet opnieuw op te starten.

```powershell
# Copy linkerd.exe to C:\Linkerd
New-Item -ItemType Directory -Force -Path "C:\Linkerd"
Copy-Item -Path ".\linkerd2-cli-$LINKERD_VERSION-windows.exe" -Destination "C:\Linkerd\linkerd.exe"

# Add C:\Linkerd to PATH. 
# Make the new PATH permanently available for the current User, and also immediately available in the current shell.
$PATH = [environment]::GetEnvironmentVariable("PATH", "User") + "; C:\Linkerd\"
[environment]::SetEnvironmentVariable("PATH", $PATH, "User") 
[environment]::SetEnvironmentVariable("PATH", $PATH)
```