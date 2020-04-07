---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/30/2020
ms.author: glenga
ms.openlocfilehash: 44969063765099d350e21abfbd07792891443911
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673159"
---
::: zone pivot="programming-language-python"  
## <a name="create-and-activate-a-virtual-environment"></a><a name="create-venv"></a>Een virtuele omgeving maken en activeren

Voer in een geschikte map de volgende opdrachten uit `.venv`om een virtuele omgeving met de naam te maken en te activeren. Zorg ervoor dat u Python 3.8, 3.7 of 3.6 gebruikt, die worden ondersteund door Azure-functies.

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Als Python het venv-pakket niet op uw Linux-distributie heeft geïnstalleerd, voert u de volgende opdracht uit:

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
py -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

U voert alle volgende opdrachten uit in deze geactiveerde virtuele omgeving. (Voer de virtuele omgeving `deactivate`uit om de virtuele omgeving af te sluiten.
::: zone-end