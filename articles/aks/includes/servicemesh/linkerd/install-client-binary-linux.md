---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 7a22256c88f9cee3ce62c68a2de4a5974d76f026
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77593708"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Down load en installeer het binaire bestand voor de Linkerd Linkerd-client

In een bash shell op Linux of Windows- [subsysteem voor Linux][install-wsl]gebruikt u `curl` om de Linkerd-release als volgt te downloaden:

```bash
# Specify the Linkerd version that will be leveraged throughout these instructions
LINKERD_VERSION=stable-2.6.0

curl -sLO "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-linux"
```

De `linkerd` binaire-client wordt uitgevoerd op uw client computer en maakt het mogelijk om te communiceren met het Linkerd service-net. Gebruik de volgende opdrachten om de Linkerd `linkerd`-client-binary te installeren in een bash shell op een Linux-of [Windows-subsysteem voor Linux][install-wsl]. Met deze opdrachten wordt het `linkerd` binaire client bestand naar de standaard locatie van het gebruikers programma in uw `PATH`gekopieerd.

```bash
sudo cp ./linkerd2-cli-$LINKERD_VERSION-linux /usr/local/bin/linkerd
sudo chmod +x /usr/local/bin/linkerd
```

Als u de Linkerd van de opdracht regel voor het binaire `linkerd` van de client wilt laten volt ooien, moet u deze als volgt instellen:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && linkerd completion bash > ~/completions/linkerd.bash
source ~/completions/linkerd.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/linkerd.bash" >> ~/.bashrc
```

<!-- LINKS - external -->
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10