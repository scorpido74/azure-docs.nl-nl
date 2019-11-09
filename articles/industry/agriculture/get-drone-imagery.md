---
title: Drone-installatie kopie ophalen
description: Hierin wordt beschreven hoe u de drone-installatie kopie van de partners kunt ophalen
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 6ee4391369744f966ce273697e5ba9e7f0590edd
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890982"
---
# <a name="get-drone-imagery-from-drone-partners"></a>Drone-installatie kopie van drone-partners ophalen

In dit artikel wordt beschreven hoe u orthomosaic-gegevens uit uw drone-installatie kopie partners in naar Azure FarmBeats data hub kunt ophalen. Een orthomosaic is een afbeelding van een lucht foto die geometrisch is gecorrigeerd en bijgesteld van de gegevens die zijn verzameld door drone.

Momenteel worden de volgende installatie kopieën van images ondersteund.

  ![Maten van project-Farm](./media/get-drone-imagery-from-drone-partner/drone-partner-1.png)

Het integreren van drone-afbeeldings gegevens met Azure FarmBeats helpt u bij het ophalen van orthomosaic gegevens van de drone-vluchten die u in uw farm uitvoert in de data hub. Nadat de gegevens beschikbaar zijn, kunt u deze weer geven in de FarmBeats-Accelerator en kunt u deze gebruiken voor het bouwen van gegevens fusie en het maken van AI/ML-modellen.

## <a name="before-you-begin"></a>Voordat u begint

  - Zorg ervoor dat u Azure FarmBeats hebt geïmplementeerd. Ga naar [Deploy FarmBeats](prepare-for-deployment.md)om het te implementeren.
  - Zorg ervoor dat u beschikt over de farm (waarvoor u drone installatie kopieën wilt) die zijn gedefinieerd in uw FarmBeats-systeem.

## <a name="enable-drone-imagery-integration-with-farmbeats"></a>Drone image-integratie met FarmBeats inschakelen   

U moet de volgende informatie opgeven voor de provider van uw apparaat om de integratie met FarmBeats in te scha kelen:  
 - API-eindpunt  
 - Tenant-id  
 - Client-id  
 - Client geheim  

Voer de volgende stappen uit:

1. Down load dit [script](https://aka.ms/farmbeatspartnerscript) en pak het uit op uw lokale station. U vindt er twee bestanden in dit ZIP-bestand.  
2. Meld u aan bij [Azure Portal](https://portal.azure.com/) en open Cloud shell (deze optie is beschikbaar in de rechter balk van de portal).   

    ![Maten van project-Farm](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

3. Zorg ervoor dat de omgeving is ingesteld op **Power shell**

    ![Maten van project-Farm](./media/get-drone-imagery-from-drone-partner/power-shell-new-1.png)

4. Upload de twee bestanden die u hebt gedownload (uit stap 1 hierboven) in uw Cloud Shell.  

    ![Maten van project-Farm](./media/get-drone-imagery-from-drone-partner/power-shell-two-1.png)

5. Ga naar de map waarin de bestanden zijn geüpload. (Standaard wordt deze geüpload naar de basismap > gebruikers naam.)  
6. Voer het volgende script uit:

    ```azurepowershell-interactive 

    ./generateCredentials.ps1   

    ```

7. Volg de instructies op het scherm voor het vastleggen van de waarden van API-eind punt, Tenant-ID, client-ID, client geheim en EventHub-verbindings reeks.

    Zodra u de vereiste referenties hebt ingevoerd in het drone-software systeem van de partner, kunt u alle farms importeren uit het FarmBeats-systeem en de farm gegevens gebruiken om uw vlucht paden te plannen en te drone.

    Nadat de onbewerkte installatie kopieën door de software van de drone-providers zijn verwerkt, laadt het drone-software systeem de niet-geplaatste orthomosaic en andere verwerkte installatie kopieën in de data hub.

## <a name="view-drone-imagery"></a>Drone-installatie kopie weer geven

Zodra de gegevens naar de FarmBeats-data hub zijn verzonden, moet u een query kunnen uitvoeren op het scène archief met behulp van de FarmBeats data hub-Api's.

U kunt ook de meest recente drone-installatie kopie bekijken op de pagina met **Farm Details** . Volg de stappen om weer te geven:  

1. Selecteer de farm waarnaar uw installatie kopie is geüpload. De pagina Details van **Farm** wordt weer gegeven.
2. Schuif omlaag naar het gedeelte meest recente **precisie kaarten** .
3. U zou de installatie kopie moeten kunnen bekijken in de sectie **drone imagey** .

    ![Maten van project-Farm](./media/get-drone-imagery-from-drone-partner/drone-imagery-1.png)

## <a name="download-drone-imagery"></a>Drone-installatie kopie downloaden

Wanneer u de sectie drone installatie kopie selecteert, wordt er een pop-up geopend met een afbeelding met een hoge resolutie van de drone-orthomosaic.

![Maten van project-Farm](./media/get-drone-imagery-from-drone-partner/download-drone-imagery-1.png)

## <a name="view-all-drone-maps"></a>Alle drone-kaarten weer geven

Bestanden en afbeeldingen die zijn geüpload door de drone-provider, worden weer gegeven in de sectie kaarten. Selecteer de sectie **kaarten** , filter op **Farm** en kies de gewenste bestanden om weer te geven en te downloaden:

  ![Maten van project-Farm](./media/get-drone-imagery-from-drone-partner/view-drone-maps-1.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het gebruik van de FarmBeats data hub- [api's](references-for-farmbeats.md#rest-api) om uw drone-installatie kopie op te halen.
