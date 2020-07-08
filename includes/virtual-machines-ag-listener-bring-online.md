---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 760bb5b62e9bba9b7a83f99760f7fe5d8c399dfb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "67175986"
---
1. In Failoverclusterbeheer vouwt u **rollen**uit en markeert u vervolgens uw beschikbaarheids groep.  

2. Klik op het tabblad **resources** met de rechter muisknop op de naam van de listener en klik vervolgens op **Eigenschappen**.

3. Klik op het tabblad **afhankelijkheden** . Als er meerdere bronnen worden weer gegeven, controleert u of de IP-adressen of, niet en, afhankelijkheden hebben.  

4. Klik op **OK**.

5. Klik met de rechter muisknop op de naam van de listener en klik vervolgens op **online brengen**.

6. Nadat de listener online is, klikt u op het tabblad **resources** met de rechter muisknop op de beschikbaarheids groep en klikt u vervolgens op **Eigenschappen**.
   
    ![De resource voor de beschikbaarheids groep configureren](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678772.gif)

7. Maak een afhankelijkheid van de naam van de listener-resource (niet de naam van de IP-adres resources) en klik vervolgens op **OK**.
   
    ![Afhankelijkheid toevoegen aan de naam van de listener](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678773.gif)

8. Start SQL Server Management Studio en maak vervolgens verbinding met de primaire replica.

9. Ga naar AlwaysOn-beschikbaarheids groepen voor de beschikbaarheids groep met **hoge Beschik baarheid**  >  **Availability Groups**  >  **\<AvailabilityGroupName\>**  >  **Availability Group Listeners**.  
    De naam van de listener die u hebt gemaakt in Failoverclusterbeheer moet worden weer gegeven.

10. Klik met de rechter muisknop op de naam van de listener en klik vervolgens op **Eigenschappen**.

11. Geef in het vak **poort** het poort nummer voor de beschikbaarheids groep-listener op met behulp van de $EndpointPort die u eerder hebt gebruikt (in deze zelf studie is 1433 de standaard instelling) en klik vervolgens op **OK**.

