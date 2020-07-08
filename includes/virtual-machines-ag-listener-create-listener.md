---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 394b242ab46da7821f77e8d008836753f4e358e2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "67175984"
---
In deze stap maakt u hand matig de beschikbaarheids groep-listener in Failoverclusterbeheer en SQL Server Management Studio.

1. Open Failoverclusterbeheer van het knoop punt dat als host fungeert voor de primaire replica.

2. Selecteer het knoop punt **netwerken** en noteer de naam van het cluster netwerk. Deze naam wordt gebruikt in de variabele $ClusterNetworkName in het Power shell-script.

3. Vouw de cluster naam uit en klik vervolgens op **rollen**.

4. Klik in het deel venster **functies** met de rechter muisknop op de naam van de beschikbaarheids groep en selecteer vervolgens **bron**  >  **Client Access Point**toevoegen.
   
    ![Client toegangs punt toevoegen voor beschikbaarheids groep](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678769.gif)

5. Maak in het vak **naam** een naam voor deze nieuwe listener, klik twee keer op **volgende** en klik vervolgens op **volt ooien**.  
    Zet de listener of bron op dit punt niet online.

6. Klik op het tabblad **resources** en vouw vervolgens het client toegangs punt uit dat u zojuist hebt gemaakt. 
    De IP-adres bron voor elk cluster netwerk in uw cluster wordt weer gegeven. Als dit een alleen-Azure-oplossing is, wordt er slechts één IP-adres bron weer gegeven.

7. Gebruik een van de volgende methoden:
   
   * Een hybride oplossing configureren:
     
        a. Klik met de rechter muisknop op de IP-adres bron die overeenkomt met uw on-premises subnet en selecteer vervolgens **Eigenschappen**. Noteer de IP-adres naam en de netwerk naam.
   
        b. Selecteer **statisch IP-adres**, wijs een ongebruikt IP-adres toe en klik vervolgens op **OK**.
 
   * Een alleen-Azure-oplossing configureren:

        a. Klik met de rechter muisknop op de IP-adres bron die overeenkomt met uw Azure-subnet en selecteer vervolgens **Eigenschappen**.
       
       > [!NOTE]
       > Als de listener later niet online kan worden gebracht vanwege een conflicterende IP-adres dat is geselecteerd door DHCP, kunt u een geldig statisch IP-adres configureren in dit eigenschappen venster.
       > 
       > 

       b. Wijzig de **IP-adres naam**in het venster Eigenschappen van **IP-adres** .  
        Deze naam wordt gebruikt in de $IPResourceName variabele van het Power shell-script. Als uw oplossing meerdere virtuele netwerken van Azure omvat, herhaalt u deze stap voor elke IP-bron.

