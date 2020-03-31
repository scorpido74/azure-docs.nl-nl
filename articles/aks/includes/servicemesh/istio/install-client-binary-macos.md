---
author: paulbouwer
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: 74f5b22ccc822a188059b29d9c661a15cf8412bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593946"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>De Istio istioctl client binary downloaden en installeren

In een bash-gebaseerde shell `curl` op MacOS, gebruiken om `tar` de Istio release te downloaden en vervolgens te extraheren met als volgt:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.4.0

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

De `istioctl` client binaire draait op uw client machine en stelt u in staat om te communiceren met de Istio service mesh. Gebruik de volgende opdrachten om `istioctl` de Istio-client-binaire te installeren in een op bash gebaseerde shell op MacOS. Deze opdrachten kopiëren `istioctl` de client binair naar `PATH`de standaard locatie van het gebruikersprogramma in uw .

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Als u opdrachtregelvoltooiing wilt voor de `istioctl` istio-client-binaire, stelt u deze als volgt in:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```