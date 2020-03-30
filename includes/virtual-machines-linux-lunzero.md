---
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 09fa612e7e5c681da16bf19e94c626ae14a3b8a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77590828"
---
Wanneer u gegevensschijven toevoegt aan een Linux-vm, u fouten tegenkomen als een schijf niet bestaat bij LUN 0. Als u een schijf handmatig `az vm disk attach -new` toevoegt met de opdracht`--lun`en u een LUN ( ) opgeeft in plaats van het Azure-platform toe te staan de juiste LUN te bepalen, moet u ervoor zorgen dat er al een schijf bestaat / bestaat bij LUN 0. 

Neem het volgende voorbeeld met een `lsscsi`fragment van de uitvoer uit :

```bash
[5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc 
[5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd 
```

De twee gegevensschijven bestaan bij LUN 0 en `lsscsi` LUN `[host:channel:target:lun]`1 (de eerste kolom in de uitvoerdetails). Beide schijven moeten toegankelijk zijn vanuit de VM. Als u handmatig had opgegeven dat de eerste schijf bij LUN 1 en de tweede schijf op LUN 2 moet worden toegevoegd, ziet u de schijven mogelijk niet correct vanuit uw VM.

> [!NOTE]
> De `host` Azure-waarde is 5 in deze voorbeelden, maar dit kan variëren afhankelijk van het type opslag dat u selecteert.
> 
> 

Dit schijfgedrag is geen Azure-probleem, maar de manier waarop de Linux-kernel de SCSI-specificaties volgt. Wanneer de Linux-kernel de SCSI-bus scant op aangesloten apparaten, moet er een apparaat worden gevonden op LUN 0 om ervoor te kunnen weten dat het systeem kan blijven scannen op extra apparaten. Als zodanig:

* Controleer de `lsscsi` uitvoer van na het toevoegen van een gegevensschijf om te controleren of u een schijf op LUN 0 hebt.
* Als uw schijf niet correct wordt weergegeven in uw vm, controleert u of er een schijf bestaat op LUN 0.

