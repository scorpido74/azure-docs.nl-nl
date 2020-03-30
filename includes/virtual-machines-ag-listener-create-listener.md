---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 394b242ab46da7821f77e8d008836753f4e358e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175984"
---
In deze stap maakt u handmatig de listener van de beschikbaarheidsgroep in Failover Cluster Manager en SQL Server Management Studio.

1. Open Failoverclusterbeheer vanaf het knooppunt dat de primaire replica host.

2. Selecteer het knooppunt **Netwerken** en noteer vervolgens de naam van het clusternetwerk. Deze naam wordt gebruikt in de $ClusterNetworkName variabele in het PowerShell-script.

3. Vouw de clusternaam uit en klik op **Rollen**.

4. Klik **in** het deelvenster Rollen met de rechtermuisknop op de naam van de beschikbaarheidsgroep en selecteer vervolgens Het toegangspunt **voor** > **resources**toevoegen .
   
    ![Clienttoegangspunt toevoegen voor beschikbaarheidsgroep](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678769.gif)

5. Maak in het vak **Naam** een naam voor deze nieuwe listener, klik twee maal op **Volgende** en klik vervolgens op **Voltooien**.  
    Breng de luisteraar of bron niet online op dit punt.

6. Klik op het tabblad **Resources** en vouw het clienttoegangspunt uit dat u zojuist hebt gemaakt. 
    De IP-adresbron voor elk clusternetwerk in uw cluster wordt weergegeven. Als dit een azure-only oplossing is, wordt slechts één IP-adresbron weergegeven.

7. Voer een van de volgende bewerkingen uit:
   
   * Ga als lid van het nieuwe bedrijf naar een hybride oplossing:
     
        a. Klik met de rechtermuisknop op de IP-adresbron die overeenkomt met uw on-premises subnet en selecteer **Eigenschappen**. Let op de IP-adresnaam en de netwerknaam.
   
        b. Selecteer **Statisch IP-adres,** wijs een ongebruikt IP-adres toe en klik op **OK**.
 
   * Ga als u een oplossing alleen voor Azure configureren:

        a. Klik met de rechtermuisknop op de IP-adresbron die overeenkomt met uw Azure-subnet en selecteer **Eigenschappen**.
       
       > [!NOTE]
       > Als de listener later niet online komt vanwege een conflicterend IP-adres dat door DHCP is geselecteerd, u een geldig statisch IP-adres configureren in dit eigenschappenvenster.
       > 
       > 

       b. Wijzig in hetzelfde **venster IP-adreseigenschappen** de **IP-adresnaam**.  
        Deze naam wordt gebruikt in de $IPResourceName variabele van het PowerShell-script. Als uw oplossing meerdere Virtuele Azure-netwerken omvat, herhaalt u deze stap voor elke IP-bron.

