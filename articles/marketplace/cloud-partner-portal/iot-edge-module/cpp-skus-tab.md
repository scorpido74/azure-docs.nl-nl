---
title: Sku's van Azure IoT Edge-module | Azure Marketplace
description: Sku's maken voor een IoT Edge module.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pabutler
ms.openlocfilehash: 230f3d6438d44c4e1e1721c0cb1453c85958e282
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73813850"
---
# <a name="iot-edge-module-skus-tab"></a>Tabblad IoT Edge module Sku's

Op het tabblad **sku's** van de pagina **nieuwe aanbieding** kunt u een of meer sku's maken en deze koppelen aan uw nieuwe aanbieding.  U kunt verschillende Sku's gebruiken om een oplossing te onderscheiden van functie sets, facturerings modellen of een ander kenmerk.


## <a name="sku-settings"></a>SKU-instellingen

Wanneer u begint met het maken van een nieuwe aanbieding, zijn er geen Sku's gekoppeld aan de aanbieding. Voer de volgende stappen uit om een nieuwe SKU te maken:

- Selecteer op de pagina **IOT Edge-Modules > nieuwe aanbieding** het tabblad **sku's** .
- Onder Sku's selecteert u **+ nieuwe SKU** om een dialoog venster te openen.

  ![Nieuwe SKU-knop op het tabblad nieuwe aanbieding voor IoT Edge modules](./media/iot-edge-module-skus-tab-new-sku.png)

- Voer in het dialoog venster **nieuwe SKU** een id in voor de SKU en selecteer **OK**.
(De volgende tabel bevat de naamgevings conventies voor id's.)

Het tabblad **sku's** wordt vernieuwd en de velden die u bewerkt, worden weer gegeven voor het configureren van de SKU. Een asterisk (*) die wordt toegevoegd aan de veld naam geeft aan dat deze vereist is.

|  **Veld**       |     **Beschrijving**                                                          |
|  ---------       |     ---------------                                                          |
| **SKU-ID\***       | Id voor deze SKU. Deze naam mag Maxi maal 50 tekens bevatten, bestaande uit kleine letters of streepjes (-), maar mag niet eindigen met een streepje. **Opmerking:** U kunt deze naam niet wijzigen na de publicatie van de aanbieding. De naam is openbaar zichtbaar in product-Url's. |
|  |  |


## <a name="sku-details"></a>SKU-gegevens

Configureer de **SKU-gegevens** om te bepalen hoe uw SKU wordt weer gegeven op de websites van Azure Marketplace en Azure Portal.

![SKU-meta gegevens van IoT Edge-module](media/iot-edge-module-skus-tab-metadata.png)

De volgende tabel beschrijft het doel, de inhoud en de opmaak voor velden onder **SKU-Details**. Vereiste velden worden gedicteerd door een asterisk (*).

|  **Veld**       |     **Beschrijving**                                                          |
|  ---------       |     ---------------                                                          |
| **Titel\***        | Titel voor deze SKU. Maximale lengte van 50 tekens. <br/> Deze wordt weer gegeven in azure Portal en wordt gebruikt als een standaard module naam (zonder spaties en speciale tekens) wanneer deze wordt geïmplementeerd. Bekijk de onderstaande afbeeldingen om precies te zien waar dit veld wordt weer gegeven.|
| **Overzicht\***      | Korte samen vatting van deze SKU. Maximale lengte van 100 tekens. Vat de aanbieding **niet** samen, alleen de SKU.  Deze samen vatting wordt weer gegeven in de Azure Marketplace. Bekijk de onderstaande afbeeldingen om precies te zien waar dit veld wordt weer gegeven.|
| **Beschrijving\***  | Korte beschrijving van deze SKU. Maximale lengte van 3000 tekens. Beschrijf de aanbieding niet, maar alleen deze SKU. Deze wordt weer gegeven in de Azure Marketplace en in de Azure Portal. In de Azure Portal wordt deze toegevoegd aan de beschrijving van Marketplace, met een beschrijving van de aanbieding die op het tabblad Marketplace is gedefinieerd.  Dit kan hetzelfde zijn als de SKU-samen vatting. Bekijk de onderstaande afbeeldingen om precies te zien waar dit veld wordt weer gegeven.|
| **Deze SKU\* verbergen** | Behoud de standaard instelling. Dit is **Nee**. |
|  |  |


### <a name="sku-example"></a>SKU-voor beeld

 In de volgende voor beelden ziet u hoe de velden SKU- **titel**, **samen vatting**en **Beschrijving** in verschillende weer gaven worden weer gegeven.
 

#### <a name="on-the-azure-marketplace-website"></a>Op de website van Azure Marketplace:

- Bij het bekijken van SKU Details:

    ![Hoe Sku's worden weer gegeven op de website van Azure Marketplace](media/iot-edge-module-ampdotcom-pdp-plans.png)


#### <a name="on-the-azure-portal-website"></a>Op de website van Azure portal:

- Bij het bladeren door Sku's:

    ![Hoe IoT Edge module wordt weer gegeven tijdens het bladeren door de Azure Portal #1](media/iot-edge-module-portal-browse.png)

    ![Hoe IoT Edge module wordt weer gegeven tijdens het bladeren door de Azure Portal #2](media/iot-edge-module-portal-product-picker.png)

- Bij het zoeken naar Sku's:

    ![Hoe IoT Edge module wordt weer gegeven bij het zoeken naar Azure Portal](media/iot-edge-module-portal-search.png)

- Bij het bekijken van SKU Details:

    ![Hoe IoT Edge module wordt weer gegeven wanneer u de product gegevens in de portal bekijkt](./media/iot-edge-module-portal-pdp.png)

- Bij het implementeren van de module:
    
    ![Hoe IoT Edge module wordt weer gegeven wanneer deze wordt geïmplementeerd](./media/iot-edge-module-deployment.png)


## <a name="sku-content"></a>SKU-inhoud

Geef onder **installatie kopieën van Edge-module**de gegevens op die nodig zijn om uw IOT Edge module te uploaden.

Geef ons toegang tot uw [Azure container Registry](https://azure.microsoft.com/services/container-registry/) (ACR) die de installatie kopie van de IOT Edge module bevat zodat we deze kunnen uploaden en certificeren. Nadat deze is gepubliceerd, wordt uw IoT Edge-module gekopieerd en gedistribueerd met behulp van een openbaar container register dat wordt gehost door de Azure Marketplace.

U kunt richten op meerdere platformen en verschillende versies bieden via Tags. Meer informatie over [Tags en versie beheer vindt u in de technische activa van uw IOT Edge-module voorbereiden](./cpp-create-technical-assets.md).

![Installatie kopieën van IoT Edge module](./media/iot-edge-module-skus-tab-acr.png)

De volgende tabel beschrijft het doel, de inhoud en de opmaak van de velden voor de **afbeeldings opslagplaats Details** en de versie van de **installatie kopie**.  Vereiste velden worden gedicteerd door een asterisk (*).


|  **Veld**       |     **Beschrijving**                                                          |
|  ---------       |     ---------------                                                          |
|  |  ***Details van de afbeeldings opslagplaats***    |
| **Abonnements-ID\***        | De ID van het Azure-abonnement van uw ACR.|
| **Naam van resource groep\***      | De naam van de resource groep van uw ACR.|
| **Register naam\***  | De naam van uw ACR-REGI ster. Kopieer alleen de register naam, niet de naam van de aanmeldings server (bijvoorbeeld zonder de `azurecr.io`.) |
| **Naam van opslag plaats\***  | De naam van de opslag plaats van uw ACR die de module IoT Edge bevat. **Opmerking:** Nadat de naam is ingesteld, kunt u deze later niet meer wijzigen. Gebruik een unieke naam om ervoor te zorgen dat geen enkel ander aanbod in uw account dezelfde naam heeft. |
| **Gebruikers naam\*** | De gebruikers naam die is gekoppeld aan uw ACR (gebruikers naam beheerder). |
| **Wachtwoord\*** | Het wacht woord dat is gekoppeld aan uw ACR. |
|    |  ***Versie van installatie kopie***   |
| **Afbeeldings code of Digest-\*** | Het moet mini maal een `latest` tag en een versie label bevatten (bijvoorbeeld vanaf `xx.xx.xx-` waarbij XX een getal is). Ze moeten [manifest Tags](https://github.com/estesp/manifest-tool) zijn voor het richten op meerdere platforms. Alle labels waarnaar wordt verwezen door een manifest-tag, moeten ook worden toegevoegd, zodat ze kunnen worden geüpload. U kunt verschillende versies van een IoT Edge module met behulp van Tags toevoegen. Alle manifest Tags (met uitzonde ring van `latest`) moeten beginnen met `X.Y-` of `X.Y.Z-` waarbij X, Y, Z een geheel getal zijn. Meer informatie over [Tags en versie beheer vindt u in de technische activa van uw IOT Edge-module voorbereiden](./cpp-create-technical-assets.md). <br/> Als bijvoorbeeld een `latest`-tag naar die punten verwijst naar `1.0.1-linux-x64`, `1.0.1-linux-arm32`, en `1.0.1-windows-arm32`, moeten deze zes Tags hier worden toegevoegd. |
|  |  |


### <a name="help-your-customers-launch-by-using-default-settings"></a>Help uw klanten te starten met behulp van de standaard instellingen

Definieer de meest voorkomende instellingen voor het implementeren van uw IoT Edge-module. Optimaliseer klant implementaties door uw IoT Edge module out-of-the-box te laten starten met deze standaard instellingen.

![Standaard instellingen van IoT Edge-module tijdens implementatie](./media/iot-edge-module-skus-tab-iot-edge-defaults.png)

De volgende tabel beschrijft het doel, de inhoud en de opmaak van de velden voor **standaard routes**, **standaard dubbele gewenste eigenschappen**, **standaard omgevings variabelen**en **standaard CreateOptions**.

|  **Veld**       |     **Beschrijving**                                                          |
|  ---------       |     ---------------                                                          |
| **Standaard routes**        | Elke standaard route naam en-waarde moeten minder dan 512 tekens bevatten. U kunt Maxi maal vijf standaard routes definiëren. Zorg ervoor dat u een correcte [route syntaxis](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes) gebruikt in uw route waarde. Als u wilt verwijzen naar uw module, gebruikt u de standaard module naam. deze is uw **SKU-titel** zonder spaties en speciale tekens. Als u wilt verwijzen naar andere modules die nog niet bekend zijn, gebruikt u de `<FROM_MODULE_NAME>`-Conventie om uw klanten te laten weten dat ze deze gegevens moeten bijwerken. Meer informatie over [IOT Edge routes](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes). <br/> Als module `ContosoModule` bijvoorbeeld luistert naar invoer op `ContosoInput` en uitvoer gegevens op `ContosoOutput`, is het zinvol om de volgende twee standaard routes te definiëren:<br/>-Name #1: `ToContosoModule`<br/>-Waarde #1:`FROM /messages/modules/<FROM_MODULE_NAME>/outputs/* INTO BrokeredEndpoint("/modules/ContosoModule/inputs/ContosoInput")`<br/>-Name #2: `FromContosoModuleToCloud`<br/>-Waarde #2: `FROM /messages/modules/ContonsoModule/outputs/ContosoOutput INTO $upstream`<br/>  |
| **Standaard dubbele gewenste eigenschappen**      | Elke standaard dubbele gewenste eigenschappen naam en waarde moet kleiner zijn dan 512 tekens. U kunt Maxi maal vijf naam/waarde dubbele gewenste eigenschappen definiëren. Waarden van dubbele gewenste eigenschappen moeten een geldige JSON, niet-escaped, zonder matrices en een maximum geneste hiërarchie van 4 zijn. Meer informatie over [dubbele gewenste eigenschappen](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties). <br/> Als een module bijvoorbeeld een dynamisch Configureer bare vernieuwings frequentie ondersteunt via dubbele gewenste eigenschappen, is het zinvol om de volgende standaard dubbele gewenste eigenschap te definiëren:<br/> -Name #1: `RefreshRate`<br/>-Waarde #1: `60`|
| **Standaard omgevings variabelen**  | De naam en waarde van elke standaard omgevings variabele moet kleiner zijn dan 512 tekens. U kunt Maxi maal vijf omgevings variabelen voor de naam/waarde opgeven. <br/>Als er bijvoorbeeld een module vereist is om de gebruiksrecht overeenkomst te accepteren voordat deze wordt gestart, kunt u de volgende omgevings variabele definiëren:<br/> -Name #1: `ACCEPT_EULA`<br/>-Waarde #1: `Y`|
| **Standaard createOptions**  | De createOptions moet kleiner zijn dan 512 tekens. Dit moet een geldige JSON zijn, zonder Escape. Meer informatie over [createOptions](https://docs.microsoft.com/azure/iot-edge/module-composition#configure-modules). <br/> Als een module bijvoorbeeld BIND a-poort vereist, kunt u de volgende createOptions definiëren:<br/>  `"HostConfig":{"PortBindings":{"5012/tcp":[{"HostPort":"5012"}]}`|
|   |   |

Selecteer **Opslaan** om uw SKU-instellingen op te slaan. 


## <a name="next-steps"></a>Volgende stappen

Gebruik het [tabblad Marketplace](./cpp-marketplace-tab.md) om een beschrijving voor de Marketplace voor uw aanbieding te maken.
