---
author: paulbouwer
ms.service: container-service
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: d812baa15380b7824deaed081d2857fbc6e0b1b6
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74170834"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Down load en installeer het binaire bestand voor de Istio istioctl-client

In een bash-shell op MacOS, gebruikt u `curl` om de Istio-release te downloaden en gaat u als volgt te extra heren met `tar`:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.4.0

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

De `istioctl` binaire-client wordt uitgevoerd op uw client computer en maakt het mogelijk om te communiceren met het Istio service-net. Gebruik de volgende opdrachten om de Istio `istioctl`-client-binary te installeren in een op bash gebaseerde shell op MacOS. Met deze opdrachten wordt het `istioctl` binaire client bestand naar de standaard locatie van het gebruikers programma in uw `PATH`gekopieerd.

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