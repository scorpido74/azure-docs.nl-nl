---
title: bestand opnemen
description: bestand opnemen
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 10/16/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: bf107da82fb3f772a341e70ce472f08ea674a450
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75692525"
---
### <a name="create-a-storage-account-for-event-processor-host"></a>Een opslagaccount voor Event Processor Host maken
De Event Processor Host is een intelligente agent die het ontvangen van gebeurtenissen van Event Hubs vereenvoudigt door permanente controlepunten en parallelle ontvangsten te beheren. Voor controlepunten heeft de Event Processor Host een opslagaccount nodig. In het volgende voorbeeld ziet u hoe u een opslagaccount maakt en hoe u de sleutels krijgt voor toegang:

1. Selecteer in het menu azure portal de optie **Een resource maken**.

    ![Een resourcemenu-item maken, Microsoft Azure-portal](./media/event-hubs-create-storage/create-resource.png)

2. Selecteer > **Opslagopslagaccount**. **Storage**
   
    ![Selecteer Opslagaccount, Microsoft Azure-portal](./media/event-hubs-create-storage/select-storage-account.png)

3. Voer op de pagina **Opslagaccount maken** de volgende stappen uit: 

   1. Voer de naam van het **opslagaccount**in .
   2. Kies een **Azure-abonnement** dat de gebeurtenishub bevat.
   3. Kies of maak de **resourcegroep** met de gebeurtenishub.
   4. Kies een **locatie** waar u de resource wilt maken. 
   5. Selecteer **Controleren + maken**.
   
        ![Controleren + maken, Opslagaccount maken, Microsoft Azure-portal](./media/event-hubs-create-storage/review-create.png)

4. Op de pagina **Controleren + maken** controleert u de waarden en selecteert u **Maken**. 

    ![Instellingen voor opslagaccount controleren en Microsoft Azure-portal maken](./media/event-hubs-create-storage/create-storage-account.png)
5. Nadat u het bericht **Geslaagde implementaties** in uw meldingen hebt weergegeven, selecteert u **Ga naar bron om** de pagina Opslagaccount te openen. U ook **implementatiegegevens** uitbreiden en vervolgens uw nieuwe resource selecteren in de bronlijst.  

    ![Ga naar bron, implementatie van opslagaccount, Microsoft Azure-portal](./media/event-hubs-create-storage/go-to-resource.png) 
6. Selecteer **Containers**.

    ![De containerservice Blobs, opslagaccounts, Microsoft Azure-portal selecteren](./media/event-hubs-create-storage/select-blob-container-service.png)
7. Selecteer **+ Container** bovenaan, voer een **naam** voor de container in en selecteer **OK**. 

    ![Een nieuwe blobcontainer, opslagaccounts, Microsoft Azure-portal maken](./media/event-hubs-create-storage/create-new-blob-container.png)
8. Kies **Toegangssleutels** in het menu van de pagina **Opslagaccount** en kopieer de waarde van **toets1**.

    Sla de volgende waarden op in Kladblok of een andere tijdelijke locatie.
    - Naam van het opslagaccount
    - Toegangssleutel voor het opslagaccount
    - Naam van de container
