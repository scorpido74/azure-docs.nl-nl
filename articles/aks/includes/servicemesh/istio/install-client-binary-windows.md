---
author: paulbouwer
ms.service: container-service
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: a1a608b4f4e1afe4a3cf99229561a91cc3f9fa96
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74170820"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Down load en installeer het binaire bestand voor de Istio istioctl-client

Gebruik in een Power shell-shell in Windows `Invoke-WebRequest` om de Istio-release te downloaden en vervolgens als volgt met `Expand-Archive` op te halen:

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.4.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

De `istioctl` binaire-client wordt uitgevoerd op uw client computer en maakt het mogelijk om te communiceren met het Istio service-net. Gebruik de volgende opdrachten om de Istio `istioctl`-client-binary te installeren in een Power shell-shell op Windows. Met deze opdrachten kopieert u de `istioctl` binaire client naar een Istio-map en maakt u deze zowel onmiddellijk beschikbaar (in de huidige shell) als permanent (tijdens het opnieuw opstarten van de shell) via uw `PATH`. U hebt geen verhoogde beheerders bevoegdheden nodig om deze opdrachten uit te voeren en u hoeft de shell niet opnieuw op te starten.

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