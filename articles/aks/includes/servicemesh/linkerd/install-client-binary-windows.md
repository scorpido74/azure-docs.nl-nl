---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 1a023475de1ce2891916807632d9ee15e382326c
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81734679"
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
# Make the new PATH permanently available for the current User
$USER_PATH = [environment]::GetEnvironmentVariable("PATH", "User") + ";C:\Linkerd\"
[environment]::SetEnvironmentVariable("PATH", $USER_PATH, "User")
# Make the new PATH immediately available in the current shell
$env:PATH += ";C:\Linkerd\"
```