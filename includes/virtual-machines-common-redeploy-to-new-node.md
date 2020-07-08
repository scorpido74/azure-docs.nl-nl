---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 1c3996c3f40da496af0cd795d0873864667a1f19
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "67176040"
---
## <a name="use-the-azure-portal"></a>Azure Portal gebruiken
1. Selecteer de virtuele machine die u opnieuw wilt implementeren en selecteer vervolgens de knop opnieuw *implementeren* op de Blade *instellingen* . Mogelijk moet u omlaag schuiven om de sectie **ondersteuning en probleem oplossing** weer te geven, zoals in het volgende voor beeld:
   
    ![Blade Azure VM](./media/virtual-machines-common-redeploy-to-new-node/vmoverview.png)
2. Als u de bewerking wilt bevestigen, selecteert u de knop opnieuw *implementeren* :
   
    ![Een VM-Blade opnieuw implementeren](./media/virtual-machines-common-redeploy-to-new-node/redeployvm.png)
3. De **status** van de VM wordt *bijgewerkt* , omdat de VM wordt voor bereid om opnieuw te worden geïmplementeerd, zoals wordt weer gegeven in het volgende voor beeld:
   
    ![VM bijwerken](./media/virtual-machines-common-redeploy-to-new-node/vmupdating.png)
4. De **status** wordt vervolgens gewijzigd in *starten* als de VM wordt opgestart op een nieuwe Azure-host, zoals wordt weer gegeven in het volgende voor beeld:
   
    ![VM starten](./media/virtual-machines-common-redeploy-to-new-node/vmstarting.png)
5. Nadat de virtuele machine het opstart proces heeft voltooid, keert de **status** terug naar *actief*, wat aangeeft dat de virtuele machine is gedistribueerd:
   
    ![VM met](./media/virtual-machines-common-redeploy-to-new-node/vmrunning.png)

