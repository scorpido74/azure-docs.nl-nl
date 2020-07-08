---
author: paulbouwer
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: b310de560f9791e1fc49d54dfbf0789c38d37f57
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77593945"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Down load en installeer het binaire bestand voor de Istio istioctl-client

In een bash shell op Linux of Windows- [subsysteem voor Linux][install-wsl]gebruikt `curl` u om de Istio-release te downloaden en vervolgens `tar` als volgt op te halen:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.4.0

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-linux.tar.gz" | tar xz
```

De `istioctl` binaire client wordt uitgevoerd op uw client computer en maakt het mogelijk om te communiceren met het Istio-service-net. Gebruik de volgende opdrachten om de Istio- `istioctl` client binaire bestanden te installeren in een op bash gebaseerde shell op Linux of [Windows-subsysteem voor Linux][install-wsl]. Met deze opdrachten `istioctl` wordt de binaire client gekopieerd naar de standaard gebruikers programma locatie in uw `PATH` .

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Als u de opdracht regel voor het binaire bestand van de Istio-client wilt laten volt ooien, moet u `istioctl` deze als volgt instellen:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

<!-- LINKS - external -->
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10