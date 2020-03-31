---
author: paulbouwer
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: 562382cc1cfb6adb7e65d76e717df4c4e2962ba7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593943"
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
# Make the new PATH permanently available for the current User, and also immediately available in the current shell.
$PATH = [environment]::GetEnvironmentVariable("PATH", "User") + "; C:\Istio\"
[environment]::SetEnvironmentVariable("PATH", $PATH, "User") 
[environment]::SetEnvironmentVariable("PATH", $PATH)
```