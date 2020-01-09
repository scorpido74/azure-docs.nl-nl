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
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/07/2020
ms.locfileid: "75692525"
---
### <a name="create-a-storage-account-for-event-processor-host"></a>Een opslagaccount voor Event Processor Host maken
De Event Processor Host is een intelligente agent die het ontvangen van gebeurtenissen van Event Hubs vereenvoudigt door permanente controlepunten en parallelle ontvangsten te beheren. Voor controlepunten heeft de Event Processor Host een opslagaccount nodig. In het volgende voorbeeld ziet u hoe u een opslagaccount maakt en hoe u de sleutels krijgt voor toegang:

1. Selecteer in het menu Azure Portal de optie **een resource maken**.

    ![Een menu opdracht voor een resource maken, Microsoft Azure-portal](./media/event-hubs-create-storage/create-resource.png)

2. Selecteer **Opslag** > **Opslagaccount**.
   
    ![Selecteer een opslag account, Microsoft Azure-portal](./media/event-hubs-create-storage/select-storage-account.png)

3. Voer op de pagina **Opslagaccount maken** de volgende stappen uit: 

   1. Voer de **naam van het opslag account**in.
   2. Kies een Azure- **abonnement** dat de Event hub bevat.
   3. Kies of maak de **resource groep** die de Event hub heeft.
   4. Kies een **locatie** om de resource te maken. 
   5. Selecteer **Controleren + maken**.
   
        ![Controleren + maken, opslag account maken, Microsoft Azure-portal](./media/event-hubs-create-storage/review-create.png)

4. Op de pagina **Controleren + maken** controleert u de waarden en selecteert u **Maken**. 

    ![Controleer de instellingen voor het opslag account en maak, Microsoft Azure-portal](./media/event-hubs-create-storage/create-storage-account.png)
5. Nadat u het bericht **implementaties voltooid** in uw meldingen hebt weer gegeven, selecteert u **naar resource gaan** om de pagina opslag account te openen. U kunt ook **implementatie gegevens** uitvouwen en vervolgens uw nieuwe resource selecteren in de lijst met resources.  

    ![Ga naar resource, implementatie van het opslag account, Microsoft Azure-portal](./media/event-hubs-create-storage/go-to-resource.png) 
6. Selecteer **containers**.

    ![Selecteer de blobs-container service, opslag accounts, Microsoft Azure-portal](./media/event-hubs-create-storage/select-blob-container-service.png)
7. Selecteer **+ container** bovenaan, voer een **naam** in voor de container en selecteer **OK**. 

    ![Een nieuwe BLOB-container maken, opslag accounts Microsoft Azure-portal](./media/event-hubs-create-storage/create-new-blob-container.png)
8. Kies **toegangs sleutels** in het menu van het pagina voor het **opslag account** en kopieer de waarde van **key1**.

    Sla de volgende waarden op in Klad blok of een andere tijdelijke locatie.
    - De naam van het opslag account
    - Toegangs sleutel voor het opslag account
    - Naam van de container
