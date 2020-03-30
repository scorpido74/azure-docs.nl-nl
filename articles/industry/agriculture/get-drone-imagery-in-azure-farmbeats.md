---
title: Door drones verzamelde afbeeldingen ophalen
description: In dit artikel wordt beschreven hoe u dronebeelden van partners krijgen.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 3e452cd548738e5f211899d3a6a676f883d800ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132055"
---
# <a name="get-drone-imagery-from-drone-partners"></a>Krijg dronebeelden van dronepartners

In dit artikel wordt beschreven hoe u orthomozaïekgegevens van uw partners voor dronebeelden inbrengen naar Azure FarmBeats Datahub. Een orthomozaïek is een luchtillustratie of afbeelding die geometrisch is gecorrigeerd en gestikt op basis van gegevens die door een drone zijn verzameld.

Momenteel worden de volgende beeldenpartners ondersteund.

  ![FarmBeats drone beelden partners](./media/get-drone-imagery-from-drone-partner/drone-partner-1.png)

Door dronebeelden te integreren met Azure FarmBeats u orthomozaïekgegevens van de dronevluchten die u op uw farm uitvoert, in de datahub krijgen. Nadat de gegevens beschikbaar zijn, u deze bekijken in de FarmBeats Accelerator. De gegevens kunnen worden gebruikt voor datafusion en kunstmatige intelligentie en machine learning modelbuilding.

## <a name="before-you-begin"></a>Voordat u begint

  - Zorg ervoor dat u Azure FarmBeats hebt geïnstalleerd. Zie [Azure FarmBeats](install-azure-farmbeats.md)installeren voor informatie over het installeren van FarmBeats.
  - Zorg ervoor dat je de boerderij hebt waarvoor je dronebeelden wilt definiëren in je FarmBeats-systeem.

## <a name="enable-drone-imagery-integration-with-farmbeats"></a>Integratie van dronebeelden inschakelen met FarmBeats

Geef de volgende informatie aan uw apparaatprovider om integratie met FarmBeats mogelijk te maken:
 - API-eindpunt
 - Tenant-id
 - Client-id
 - Clientgeheim

Volg deze stappen.

1. Download dit [script](https://aka.ms/farmbeatspartnerscript)en haal het naar uw lokale schijf. Er zitten twee bestanden in het zip-bestand.
2. Meld u aan bij [Azure Portal](https://portal.azure.com/) en open Azure Cloud Shell. Deze optie is beschikbaar op de werkbalk in de rechterbovenhoek van de portal.

    ![Azure Cloud Shell openen op de rechterbovenbalk van de portal](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

3. Controleer of de omgeving is ingesteld op **PowerShell.**

    ![PowerShell-instelling](./media/get-drone-imagery-from-drone-partner/power-shell-new-1.png)

4. Upload de twee bestanden die je hebt gedownload van stap 1 in je Cloud Shell-exemplaar.

    ![Bestanden uploaden](./media/get-drone-imagery-from-drone-partner/power-shell-two-1.png)

5. Ga naar de map waar de bestanden zijn geüpload. Standaard worden ze geüpload naar de thuismap onder de gebruikersnaam.
6. Voer het volgende script uit:

    ```azurepowershell-interactive 

    ./generateCredentials.ps1   

    ```

7. Volg de instructies op het scherm om de waarden van API-eindpunt, tenant-id, client-id, clientgeheim en EventHub-verbindingstekenreeks vast te leggen.

    Nadat u de vereiste referenties in het dronesoftwaresysteem van de partner hebt ingevoerd, u alle boerderijen importeren uit het FarmBeats-systeem. Vervolgens u de details van de boerderij gebruiken om uw vliegrouteplanning en het verzamelen van dronebeelden te doen.

    Nadat de ruwe beelden zijn verwerkt door de software van de droneproviders, uploadt het dronesoftwaresysteem het gestikte orthomozaïek en andere verwerkte beelden naar de datahub.

## <a name="view-drone-imagery"></a>Bekijk dronebeelden

Nadat de gegevens naar de FarmBeats-datahub zijn verzonden, u de scènearchief opvragen met FarmBeats Datahub API's.

U ook de nieuwste droneafbeelding bekijken op de pagina **Farm Details.** Volg de stappen om de afbeelding te bekijken.

1. Selecteer de farm waarnaar uw afbeeldingen zijn geüpload. De pagina **Farmdetails** wordt weergegeven.
2. Scroll naar beneden naar de nieuwste **sectie Precision Maps.**
3. Bekijk de afbeelding in de sectie **Drone-beelden.**

    ![Sectie Dronebeelden](./media/get-drone-imagery-from-drone-partner/drone-imagery-1.png)

## <a name="download-drone-imagery"></a>Dronebeelden downloaden

Wanneer u de sectie Drone-beelden selecteert, wordt een pop-up geopend om een afbeelding met hoge resolutie van het droneorthomozaïek weer te geven.

![Orthomozaïek met hoge resolutie](./media/get-drone-imagery-from-drone-partner/download-drone-imagery-1.png)

## <a name="view-all-drone-maps"></a>Bekijk alle dronekaarten

Bestanden en afbeeldingen die door de droneprovider zijn geüpload, worden weergegeven in de sectie **Kaarten.** Selecteer de sectie **Kaarten,** filter op **Farm**en selecteer de juiste bestanden die u wilt bekijken en downloaden.

  ![Sectie Kaarten](./media/get-drone-imagery-from-drone-partner/view-drone-maps-1.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het gebruik van FarmBeats Datahub [API's](rest-api-in-azure-farmbeats.md) om uw dronebeelden te krijgen.
