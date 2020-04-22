---
author: paulbouwer
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: e26a2c214a03243d6507296c1e981706be8c56db
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81735485"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>De Istio istioctl client binary downloaden en installeren

Gebruik in `Invoke-WebRequest` een PowerShell-gebaseerde shell op Windows de Istio-versie te downloaden en vervolgens als volgt uit `Expand-Archive` te pakken:

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.4.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

De `istioctl` client binaire draait op uw client machine en stelt u in staat om te communiceren met de Istio service mesh. Gebruik de volgende opdrachten om `istioctl` de Istio-client-binaire te installeren in een PowerShell-gebaseerde shell op Windows. Deze opdrachten kopiëren `istioctl` de client binaire naar een Istio map en vervolgens beschikbaar zowel onmiddellijk (in de `PATH`huidige shell) en permanent (over shell herstart) via uw . U hebt geen verhoogde bevoegdheden (admin) nodig om deze opdrachten uit te voeren en u hoeft uw shell niet opnieuw op te starten.

```powershell
# Copy istioctl.exe to C:\Istio
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:\Istio"
Copy-Item -Path .\bin\istioctl.exe -Destination "C:\Istio\"

# Add C:\Istio to PATH. 
# Make the new PATH permanently available for the current User
$USER_PATH = [environment]::GetEnvironmentVariable("PATH", "User") + ";C:\Istio\"
[environment]::SetEnvironmentVariable("PATH", $USER_PATH, "User")
# Make the new PATH immediately available in the current shell
$env:PATH += ";C:\Istio\"
```