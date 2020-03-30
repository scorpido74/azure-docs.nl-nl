---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 760bb5b62e9bba9b7a83f99760f7fe5d8c399dfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175986"
---
1. Vouw in Failoverclusterbeheer **rollen**uit en markeer vervolgens uw beschikbaarheidsgroep.  

2. Klik op het tabblad **Resources** met de rechtermuisknop op de naam van de listener en klik vervolgens op **Eigenschappen**.

3. Klik op het tabblad **Afhankelijkheden.** Als er meerdere bronnen worden vermeld, controleert u of de IP-adressen of, niet EN, afhankelijkheden hebben.  

4. Klik op **OK**.

5. Klik met de rechtermuisknop op de naam van de listener en klik vervolgens op **Online brengen**.

6. Nadat de listener online is, klikt u op het tabblad **Resources** met de rechtermuisknop op de beschikbaarheidsgroep en klikt u vervolgens op **Eigenschappen**.
   
    ![De bron van de beschikbaarheidsgroep configureren](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678772.gif)

7. Maak een afhankelijkheid van de listenernaambron (niet de naam van de IP-adresbron) en klik op **OK**.
   
    ![Afhankelijkheid van de naam van de listener toevoegen](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678773.gif)

8. Start SQL Server Management Studio en maak vervolgens verbinding met de primaire replica.

9. Ga naar**Beschikbaarheidsgroepen beschikbaarheidsgroepen** > **\<\>** > beschikbaarheid spaargroepen beschikbaarheid **alwayson beschikbaarheid** > **.**  
    De listenernaam die u hebt gemaakt in Failoverclusterbeheer moet worden weergegeven.

10. Klik met de rechtermuisknop op de naam van de listener en klik vervolgens op **Eigenschappen**.

11. Geef **in** het vak Poort het poortnummer op voor de listener van de beschikbaarheidsgroep met behulp van de $EndpointPort die u eerder hebt gebruikt (in deze zelfstudie was 1433 de standaardinstelling) en klik vervolgens op **OK**.

