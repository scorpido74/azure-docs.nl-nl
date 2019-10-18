---
author: paulbouwer
ms.service: container-service
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 06479b4396ab26c280cc6246d774bc30b5ea1c76
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530392"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Down load en installeer het binaire bestand voor de Istio istioctl-client

In een bash-shell op MacOS, gebruikt u `curl` om de Istio-release te downloaden en gaat u als volgt te extra heren met `tar`:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.3.2

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

De `istioctl` binaire-client wordt uitgevoerd op uw client computer en maakt het mogelijk om te communiceren met het Istio service-net. Gebruik de volgende opdrachten om de Istio `istioctl`-client-binary te installeren in een op bash gebaseerde shell op MacOS. Met deze opdrachten wordt het `istioctl` binaire client bestand naar de standaard locatie van het gebruikers programma in uw `PATH` gekopieerd.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Als u de Istio van de opdracht regel voor het binaire `istioctl` van de client wilt laten volt ooien, moet u deze als volgt instellen:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```