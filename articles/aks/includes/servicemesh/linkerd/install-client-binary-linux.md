---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 7a22256c88f9cee3ce62c68a2de4a5974d76f026
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593708"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Download en installeer de Linkerd linkerd client binaire

In een bash-gebaseerde shell op Linux `curl` of Windows Subsystem voor [Linux,][install-wsl]gebruiken om de Linkerd release te downloaden als volgt:

```bash
# Specify the Linkerd version that will be leveraged throughout these instructions
LINKERD_VERSION=stable-2.6.0

curl -sLO "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-linux"
```

De `linkerd` client binaire draait op uw client machine en stelt u in staat om te communiceren met de Linkerd service mesh. Gebruik de volgende opdrachten om `linkerd` de Linkerd client binary te installeren in een bash-gebaseerde shell op Linux of [Windows Subsystem voor Linux][install-wsl]. Deze opdrachten kopiëren `linkerd` de client binair naar `PATH`de standaard locatie van het gebruikersprogramma in uw .

```bash
sudo cp ./linkerd2-cli-$LINKERD_VERSION-linux /usr/local/bin/linkerd
sudo chmod +x /usr/local/bin/linkerd
```

Als u opdrachtregelvoltooiing wilt voor de `linkerd` binaire linkerd-client, stelt u deze als volgt in:

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