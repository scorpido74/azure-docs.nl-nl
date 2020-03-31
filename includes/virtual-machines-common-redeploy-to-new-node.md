---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 1c3996c3f40da496af0cd795d0873864667a1f19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67176040"
---
## <a name="use-the-azure-portal"></a>De Azure-portal gebruiken
1. Selecteer de VM die u opnieuw wilt implementeren en selecteer vervolgens de knop *Opnieuw implementeren* in het *mes Instellingen.* Mogelijk moet u omlaag scrollen om de sectie **Ondersteuning en probleemoplossing** te zien die de knop 'Opnieuw implementeren' bevat, zoals in het volgende voorbeeld:
   
    ![Azure VM-blad](./media/virtual-machines-common-redeploy-to-new-node/vmoverview.png)
2. Als u de bewerking wilt bevestigen, selecteert u de knop *Opnieuw implementeren:*
   
    ![Een VM-blad opnieuw implementeren](./media/virtual-machines-common-redeploy-to-new-node/redeployvm.png)
3. De **status** van de VM verandert in *bijwerken* terwijl de VM zich voorbereidt op herimplementatie, zoals in het volgende voorbeeld wordt weergegeven:
   
    ![VM-updates](./media/virtual-machines-common-redeploy-to-new-node/vmupdating.png)
4. De **status** wordt vervolgens gewijzigd in *Starten* terwijl de VM wordt opgestart op een nieuwe Azure-host, zoals in het volgende voorbeeld wordt weergegeven:
   
    ![VM starten](./media/virtual-machines-common-redeploy-to-new-node/vmstarting.png)
5. Nadat de VM het opstartproces heeft voltooid, wordt de **status** vervolgens geretourneerd naar *Uitvoeren,* wat aangeeft dat de VM opnieuw is geïmplementeerd:
   
    ![VM-uitgevoerd](./media/virtual-machines-common-redeploy-to-new-node/vmrunning.png)

