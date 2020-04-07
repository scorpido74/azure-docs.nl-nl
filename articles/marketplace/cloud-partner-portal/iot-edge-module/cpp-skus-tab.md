---
title: Azure IoT Edge-module SKU's | Azure Marketplace
description: SKU's maken voor een IoT Edge-module.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: a989113c80cd8189cce9da959fe27509297f677a
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745685"
---
# <a name="iot-edge-module-skus-tab"></a>Tabblad IoT Edge-module SKU's

>[!Important]
>Vanaf 30 maart 2020 beginnen we met het verplaatsen van het beheer van uw IoT Edge-moduleaanbiedingen naar Partner Center. Na de migratie maak en beheer je je aanbiedingen in partnercentrum. Volg de instructies in [Een IoT Edge-moduleaanbieding maken](https://aka.ms/AzureCreateIoT) om uw gemigreerde aanbiedingen te beheren.

Op het tabblad **SKU's** van de pagina **Nieuwe aanbieding** u een of meer SKU's maken en deze koppelen aan uw nieuwe aanbieding.  U verschillende SKU's gebruiken om een oplossing te onderscheiden op functiesets, factureringsmodellen of een ander kenmerk.


## <a name="sku-settings"></a>SKU-instellingen

Wanneer u een nieuwe aanbieding maakt, zijn er geen SKU's gekoppeld aan de aanbieding. Voer de volgende stappen uit om een nieuwe SKU te maken:

- Selecteer op de pagina **IoT Edge Modules > Nieuwe aanbieding** het tabblad **SKU's.**
- Selecteer onder SKU's **+ Nieuwe SKU** om een dialoogvenster te openen.

  ![Nieuwe SKU-knop op het tabblad Nieuwe aanbieding voor IoT Edge-modules](./media/iot-edge-module-skus-tab-new-sku.png)

- Voer in het dialoogvenster **Nieuwe SKU** een id voor de SKU in en selecteer **OK**.
(In de volgende tabel worden de id-naamgevingsconventies aangeeft.)

Het tabblad **SKU's** wordt vernieuwd en geeft de velden weer die u bewerkt om de SKU te configureren. Een sterretje (*) dat aan de veldnaam is toegevoegd, geeft aan dat dit vereist is.

|  **Veld**       |     **Beschrijving**                                                          |
|  ---------       |     ---------------                                                          |
| **SKU-id\***       | Id voor deze SKU. Deze naam heeft een maximum van 50 tekens, bestaande uit alfanumerieke letters of streepjes (-), maar kan niet eindigen met een streepje. **Let op:** U deze naam niet wijzigen nadat de aanbieding is gepubliceerd. De naam is openbaar zichtbaar in product-URL's. |
|  |  |


## <a name="sku-details"></a>SKU-gegevens

Configureer de **SKU-gegevens** om te bepalen hoe uw SKU wordt weergegeven op de websites azure Marketplace en Azure Portal.

![IoT Edge-module sku-metagegevens](media/iot-edge-module-skus-tab-metadata.png)

In de volgende tabel worden het doel, de inhoud en de opmaak voor velden onder **SKU-details**beschreven. Vereiste velden worden aangeklaagd door een sterretje (*).

|  **Veld**       |     **Beschrijving**                                                          |
|  ---------       |     ---------------                                                          |
| **Titel\***        | Titel voor deze SKU. Maximale lengte van 50 tekens. <br/> Het wordt weergegeven in de Azure Portal en wordt gebruikt als standaardmodulenaam (zonder spaties en speciale tekens) wanneer het wordt geïmplementeerd. Zie de foto's hieronder om precies te zien waar dit veld wordt weergegeven.|
| **Samenvatting\***      | Korte samenvatting van deze SKU. Maximale lengte van 100 tekens. Niet **NOT** samenvatten van het aanbod, alleen de SKU.  Dit overzicht wordt weergegeven in de Azure Marketplace. Zie de foto's hieronder om precies te zien waar dit veld wordt weergegeven.|
| **Beschrijving\***  | Korte beschrijving van deze SKU. Maximale lengte van 3000 tekens. Beschrijf het aanbod niet, maar alleen deze SKU. Het wordt weergegeven in de azure-marktplaats en in de Azure-portal. In de Azure-portal wordt deze toegevoegd aan de Marketplace-beschrijving waarin de aanbieding wordt beschreven die is gedefinieerd op het tabblad Marketplace.  Het kan hetzelfde zijn als de SKU Samenvatting. Zie de foto's hieronder om precies te zien waar dit veld wordt weergegeven.|
| **Verberg deze SKU\*** | Houd de standaardinstelling, die **nee**is . |
|  |  |


### <a name="sku-example"></a>Voorbeeld van SKU

 In de volgende voorbeelden ziet u hoe de velden **SKU-titel,** **Samenvatting**en **Beschrijving** in verschillende weergaven worden weergegeven.
 

#### <a name="on-the-azure-marketplace-website"></a>Op de Azure Marketplace-website:

- Bij het bekijken van SKU details:

    ![Hoe SKU's worden weergegeven op de Azure Marketplace-website](media/iot-edge-module-ampdotcom-pdp-plans.png)


#### <a name="on-the-azure-portal-website"></a>Op de website van Azure Portal:

- Bij het browsen SKU's:

    ![Hoe de IoT Edge-module wordt weergegeven tijdens het browsen op de Azure-portal #1](media/iot-edge-module-portal-browse.png)

    ![Hoe de IoT Edge-module wordt weergegeven tijdens het browsen op de Azure-portal #2](media/iot-edge-module-portal-product-picker.png)

- Bij het zoeken naar SKU's:

    ![Hoe de IoT Edge-module wordt weergegeven bij het zoeken in de Azure-portal](media/iot-edge-module-portal-search.png)

- Bij het bekijken van SKU details:

    ![Hoe de IoT Edge-module wordt weergegeven wanneer u de productdetails in de portal bekijkt](./media/iot-edge-module-portal-pdp.png)

- Bij de implementatie van de module:
    
    ![Hoe de IoT Edge-module wordt weergegeven wanneer deze wordt geïmplementeerd](./media/iot-edge-module-deployment.png)


## <a name="sku-content"></a>SKU-inhoud

Geef **onder Edge Module Images**de informatie die we nodig hebben om uw IoT Edge-module te uploaden.

Geef ons toegang tot uw [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) (ACR) dat uw IoT Edge-moduleafbeelding bevat, zodat we deze kunnen uploaden en certificeren. Nadat deze is gepubliceerd, wordt uw IoT Edge-module gekopieerd en gedistribueerd met behulp van een openbaar containerregister dat wordt gehost door de Azure Marketplace.

U meerdere platforms targeten en meerdere versies bieden via tags. Meer informatie over [tags en versiebeheer vindt u in 'Technische elementen van uw IoT Edge-module voorbereiden'.](./cpp-create-technical-assets.md)

![Afbeeldingen van IoT Edge-module](./media/iot-edge-module-skus-tab-acr.png)

In de volgende tabel worden het doel, de inhoud en de opmaak van de velden voor de secties **Image Repository Details** en **Image Version**beschreven.  Vereiste velden worden aangeklaagd door een sterretje (*).


|  **Veld**       |     **Beschrijving**                                                          |
|  ---------       |     ---------------                                                          |
|  |  ***Details van de afbeeldingsopslagplaats***    |
| **Abonnements-id\***        | De Azure-abonnements-ID van uw ACR.|
| **Naam van de resourcegroep\***      | De naam van de resourcegroep van uw ACR.|
| **Registernaam\***  | Uw ACR-registernaam. Kopieer alleen de registernaam, NIET de naam van `azurecr.io`de loginserver (bijvoorbeeld zonder .) |
| **Naam van opslagplaats\***  | De repository-naam van uw ACR die uw IoT Edge-module bevat. **Let op:** Nadat de naam is ingesteld, kan deze later niet meer worden gewijzigd. Gebruik een unieke naam om ervoor te zorgen dat geen enkel ander aanbod in uw account dezelfde naam heeft. |
| **Gebruikersnaam\*** | De gebruikersnaam die is gekoppeld aan uw ACR (gebruikersnaam van de beheerder). |
| **Wachtwoord\*** | Het wachtwoord dat aan uw ACR is gekoppeld. |
|    |  ***Afbeeldingsversie***   |
| **Afbeeldingstag of digest\*** | Het moet ten `latest` minste een tag en een versietag bevatten (bijvoorbeeld te beginnen met `xx.xx.xx-` waar xx een getal is). Ze moeten [manifesttags](https://github.com/estesp/manifest-tool) zijn om meerdere platforms te targeten. Alle tags waarnaar wordt verwezen door een manifesttag moeten ook worden toegevoegd, zodat we ze kunnen uploaden. U verschillende versies van een IoT Edge-module toevoegen met behulp van tags. Alle manifesttags `latest`(behalve) moeten `X.Y-` `X.Y.Z-` beginnen met een of wanneer X, Y, Z gehele getallen zijn. Meer informatie over [tags en versiebeheer vindt u in 'Technische elementen van uw IoT Edge-module voorbereiden'.](./cpp-create-technical-assets.md) <br/> Als `latest` een tag daar bijvoorbeeld naar `1.0.1-linux-x64` `1.0.1-linux-arm32`verwijst, `1.0.1-windows-arm32`en deze 6 tags hier moet worden toegevoegd. |
|  |  |


### <a name="help-your-customers-launch-by-using-default-settings"></a>Help uw klanten starten met standaardinstellingen

Definieer de meest voorkomende instellingen om uw IoT Edge-module te implementeren. Optimaliseer klantimplementaties door ze uw IoT Edge-module out-of-the-box te laten starten met deze standaardinstellingen.

![Standaardinstellingen voor IoT Edge-modules bij implementatie](./media/iot-edge-module-skus-tab-iot-edge-defaults.png)

In de volgende tabel worden het doel, de inhoud en de opmaak van de velden voor **Standaardroutes,** **Standaardgewenste eigenschappen,** **Standaardomgevingsvariabelen**en **Standaardopties voor createopties beschreven**.

|  **Veld**       |     **Beschrijving**                                                          |
|  ---------       |     ---------------                                                          |
| **Standaardroutes**        | Elke standaardroutenaam en -waarde moeten minder dan 512 tekens bevatten. U maximaal 5 standaardroutes definiëren. Zorg ervoor dat u een juiste [routesyntaxis](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes) in uw routewaarde gebruikt. Als u naar uw module wilt verwijzen, gebruikt u de standaardmodulenaam, die uw **SKU-titel** is zonder spaties en speciale tekens. Als u wilt verwijzen naar andere `<FROM_MODULE_NAME>` modules die nog niet bekend zijn, gebruikt u de conventie om uw klanten te laten weten dat ze deze informatie moeten bijwerken. Meer informatie over [IoT Edge-routes](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes). <br/> Als module `ContosoModule` bijvoorbeeld luistert naar `ContosoInput` ingangen op `ContosoOutput`en uitvoergegevens op , is het zinvol om de volgende 2 standaardroutes te definiëren:<br/>- Naam #1:`ToContosoModule`<br/>- waarde #1:`FROM /messages/modules/<FROM_MODULE_NAME>/outputs/* INTO BrokeredEndpoint("/modules/ContosoModule/inputs/ContosoInput")`<br/>- Naam #2:`FromContosoModuleToCloud`<br/>- waarde #2:`FROM /messages/modules/ContonsoModule/outputs/ContosoOutput INTO $upstream`<br/>  |
| **Standaard dubbele gewenste eigenschappen**      | Elke standaard dubbele gewenste eigenschappen naam en waarde moet minder dan 512 tekens. U maximaal 5 naam/waarde twin gewenste eigenschappen definiëren. Waarden van dubbele gewenste eigenschappen moeten geldig JSON, niet-ontsnapt, zonder arrays en met een maximale geneste hiërarchie van 4. Meer informatie over [dubbele gewenste eigenschappen.](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties) <br/> Als een module bijvoorbeeld een dynamisch configureerbare verversingsfrequentie ondersteunt via dubbele gewenste eigenschappen, is het zinvol om de volgende standaardgewenste eigenschap te definiëren:<br/> - Naam #1:`RefreshRate`<br/>- waarde #1:`60`|
| **Standaardomgevingsvariabelen**  | Elke standaardomgevingvariabelen naam en waarde moeten minder dan 512 tekens bevatten. U maximaal 5 naam-/waardeomgevingsvariabelen definiëren. <br/>Als een module bijvoorbeeld gebruiksvoorwaarden moet accepteren voordat deze wordt gestart, u de volgende omgevingsvariabele definiëren:<br/> - Naam #1:`ACCEPT_EULA`<br/>- waarde #1:`Y`|
| **Standaardopties voor maken**  | De createOptions moeten minder dan 512 tekens bevatten. Het moet geldig JSON, niet-ontsnapt. Meer informatie over [createOptions](https://docs.microsoft.com/azure/iot-edge/module-composition#configure-modules). <br/> Als een module bijvoorbeeld een poort moet binden, u de volgende createOptions definiëren:<br/>  `"HostConfig":{"PortBindings":{"5012/tcp":[{"HostPort":"5012"}]}`|
|   |   |

Selecteer **Opslaan** om uw SKU-instellingen op te slaan. 


## <a name="next-steps"></a>Volgende stappen

Gebruik het [tabblad Marketplace](./cpp-marketplace-tab.md) om een marktplaatsbeschrijving voor uw aanbieding te maken.
