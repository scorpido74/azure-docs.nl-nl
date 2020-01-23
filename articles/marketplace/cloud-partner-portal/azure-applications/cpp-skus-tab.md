---
title: Sku's voor een Azure-toepassings aanbieding configureren | Azure Marketplace
description: De Sku's configureren voor een door Azure beheerde toepassing en een Azure-oplossings sjabloon.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: 2430d7e6fa74438c148d3cb849510be06243faa0
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76543151"
---
# <a name="azure-application-skus-tab"></a>Tabblad Azure Application Sku's

In dit artikel wordt beschreven hoe u het tabblad Sku's gebruikt om Sku's te maken voor uw Azure-toepassing. 

> [!IMPORTANT]
> De stappen voor het configureren van een SKU verschillen voor een beheerde toepassings aanbieding en een oplossings sjabloon. Deze verschillen worden beschreven in dit artikel. 

## <a name="configure-azure-application-skus"></a>Azure Application Sku's configureren

### <a name="create-a-new-sku"></a>Een nieuwe SKU maken

Gebruik deze stappen om een nieuwe SKU te maken:

1. Selecteer het tabblad **sku's** .
2. Onder Sku's selecteert u **+ nieuwe SKU**.

    ![Nieuwe SKU-prompt](./media/azureapp-plus-sku.png)

3. Typ een **SKU-id**in het pop-upvenster nieuwe SKU. Deze id is beperkt tot 50 tekens en mag alleen bestaan uit kleine letters, alfanumerieke tekens, streepjes of onderstrepingen. De SKU-ID kan niet eindigen op een streepje.
4. De SKU-ID is zichtbaar voor klanten in product-Url's, Resource Manager-sjablonen (indien van toepassing) en facturerings rapporten. U kunt deze id niet wijzigen na de publicatie van de aanbieding.

### <a name="sku-details-for-a-solution-template"></a>SKU-gegevens voor een oplossings sjabloon

In de volgende scherm opname wordt het formulier SKU-details weer gegeven voor een oplossings sjabloon.

![Formulier SKU-Details voor oplossings sjabloon](./media/azureapp-sku-details-solutiontemplate.png)

Geef de volgende SKU-waarden op.  De velden die zijn toegevoegd met een sterretje zijn vereist.

|    Veld         |       Beschrijving                                                            |
|  ---------       |     ---------------                                                          |
|  **Titel\***     | Een titel voor de SKU. Deze titel wordt weer gegeven in de galerie voor dit item.   |
| **Samenvatting\***    | Een korte beschrijving van de SKU. (De maximale lengte is 100 tekens.)  |
| **Beschrijving\*** | Een gedetailleerde beschrijving van de SKU. Eenvoudige HTML wordt ondersteund.                 | 
| **SKU-type\***   | Type Azure-toepassings oplossing, selecteer ***oplossings sjabloon** voor dit scenario. |
| **Beschik baarheid van Cloud\*** | De locatie van de SKU. De standaard waarde is **open bare Azure**.  <b/>**open bare Azure** -app kan worden geïmplementeerd voor klanten in alle open bare Azure-regio's die Marketplace-integratie hebben.  <b/>**Azure Government Cloud** -app wordt geïmplementeerd in de Azure Government Cloud. Voordat u naar [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)publiceert, raadt micro soft uitgevers aan om te testen en de oplossing te valideren werkt zoals verwacht in deze omgeving. Vraag een [proef account](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/)aan om het te kunnen klaarzetten en testen.  |
| **Is dit een privé-SKU?\*** | Selecteer **Ja** als deze SKU alleen beschikbaar is voor een geselecteerde groep klanten. |
|   |   |

  > [!NOTE] 
  > Microsoft Azure Government is een Cloud-community met gecontroleerde toegang voor klanten van de Amerikaanse federale, staats-, lokale of tribale en partners die in aanmerking komen voor deze entiteiten.


### <a name="sku-details-for-managed-application"></a>SKU-gegevens voor de beheerde toepassing

In de volgende scherm opname wordt het formulier SKU-details weer gegeven voor een beheerde toepassing.

   ![Formulier SKU-Details voor beheerde toepassing](./media/azureapp-sku-details-managedapplication.png)

Configureer de volgende SKU-instellingen. De velden die zijn toegevoegd met een sterretje zijn vereist.

|    Veld         |       Beschrijving                                                            |
|  ---------       |     ---------------                                                          |
|  **Titel\***     | Een titel voor de SKU. Deze titel wordt weer gegeven in de galerie voor dit item.   |
| **Samenvatting\***    | Een korte beschrijving van de SKU. (De maximale lengte is 100 tekens.)  |
| **Beschrijving\*** | Een gedetailleerde beschrijving van de SKU. Eenvoudige HTML wordt ondersteund.                 | 
| **SKU-type\***   | Type Azure-toepassings oplossing: Selecteer ***beheerde toepassing** voor dit scenario. 
| **Beschik baarheid van Cloud\*** | De locatie van de SKU. De standaard waarde is **open bare Azure**.  <b/>**open bare Azure** -app kan worden geïmplementeerd voor klanten in alle open bare Azure-regio's die Marketplace-integratie hebben.  <b/>**Azure Government Cloud** -app wordt geïmplementeerd in de Azure Government Cloud. Voordat u naar [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)publiceert, raadt micro soft uitgevers aan om te testen en de oplossing te valideren werkt zoals verwacht in deze omgeving. Vraag een [proef account](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/)aan om het te kunnen klaarzetten en testen.   Microsoft Azure Government is een Cloud-community met gecontroleerde toegang voor klanten van de Amerikaanse federale, staats-, lokale of tribale en partners die in aanmerking komen voor deze entiteiten. |
| **Is dit een privé-SKU?\*** | Selecteer **Ja** als deze SKU alleen beschikbaar is voor een geselecteerde groep klanten. |
| **Beschik baarheid van land/regio\*** | Gebruik **regio's selecteren** om de lijst weer te geven met landen/regio's die beschikbaar zijn. Controleer elk land/elke regio en selecteer **OK** om uw Picks op te slaan.  ](./media/azure-app-select-country-region.png) voor de lijst met Beschik baarheid van ![land en regio <b/>  |
| **Oude prijzen\*** | De prijs voor de SKU, in USD per maand. Prijzen worden in de lokale valuta ingesteld met behulp van de huidige wissel koersen bij de configuratie. Valideer deze omdat u deze instellingen uiteindelijk bezit. Om de prijs van elk land/elke regio afzonderlijk in te stellen of weer te geven, exporteert u het prijs werk blad en importeert u dit met de aangepaste prijzen.  U moet uw prijs wijzigingen opslaan om het exporteren/importeren van prijs gegevens in te scha kelen.  |
| **Prijs\* voor vereenvoudigd valuta** | De prijs voor de SKU, in USD per maand. Dit moet hetzelfde zijn als de oude prijzen. Zie voor meer informatie [vereenvoudigde valuta prijzen](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-update-existing-offer). |
|  |  |


### <a name="package-details-for-solution-template"></a>Pakket Details voor oplossings sjabloon

![Pakket Details voor oplossings sjabloon](./media/azureapp-sku-pkgdetails-solutiontemplate.png)

Geef de volgende waarden voor **pakket Details** op.  De velden die zijn toegevoegd met een sterretje zijn vereist.

- **Versie\*** : de versie van het pakket dat u wilt uploaden. Versie Tags moeten de indeling X. Y. Z hebben, waarbij X, Y en Z gehele getallen zijn.
- **Pakket bestand (. zip)\*** : dit pakket bevat de volgende bestanden, opgeslagen in een zip-bestand.
  - **mainTemplate. json\*** : het implementatie sjabloon bestand dat wordt gebruikt voor het implementeren van de oplossing/toepassing en het maken van de resources die zijn gedefinieerd voor de oplossing. Zie de [sjabloon bestanden](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)van de implementatie maken voor meer informatie.
  - **createUIDefinition. json\*** : dit bestand wordt door de Azure Portal gebruikt voor het genereren van de gebruikers interface voor het inrichten van deze oplossing/toepassing. Zie [Azure Portal gebruikers interface voor uw beheerde toepassing maken](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)voor meer informatie.
  - Scripts (indien vereist): extra scripts die mogelijk vereist zijn voor het uitvoeren van de sjabloon, bijvoorbeeld `Microsoft.Compute/virtualMachines/extensions`.
  - Geneste sjablonen (indien nodig): extra geneste sjablonen.

  > [!IMPORTANT] 
  > Dit pakket moet geneste sjablonen of scripts bevatten die nodig zijn om deze toepassing in te richten. Het bestand mainTemplate. json en createUIDefinition. json moeten zich in de hoofdmap bevindt. Zie [Azure Resource Manager sjablonen-best practices Guide (Engelstalig](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md#deployment-artifacts-nested-templates-scripts)) voor meer informatie over implementatie artefacten.


### <a name="package-details-for-managed-application"></a>Pakket Details voor de beheerde toepassing

   ![Pakket Details voor de beheerde toepassing](./media/azureapp-sku-pkgdetails-managedapplication.png)

Geef de volgende pakket Details op.  De velden die zijn toegevoegd met een sterretje zijn vereist.

- **Versie\*** : de versie van het pakket dat u wilt uploaden. Versie Tags moeten de indeling X. Y. Z hebben, waarbij X, Y en Z gehele getallen zijn.
- **Pakket bestand (. zip)\*** : dit pakket bevat de volgende bestanden, opgeslagen in een zip-bestand.
  - applianceMainTemplate. json: het implementatie sjabloon bestand dat wordt gebruikt voor het implementeren van de oplossing/toepassing en het maken van de gedefinieerde resources. Zie voor meer informatie [Quick Start: Azure Resource Manager sjablonen maken en implementeren met behulp van de Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal). 
  - applianceCreateUIDefinition. json: dit bestand wordt door de Azure Portal gebruikt voor het genereren van de gebruikers interface voor het inrichten van deze oplossing/toepassing. Zie [Azure Portal gebruikers interface voor uw beheerde toepassing maken](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)voor meer informatie.
  - mainTemplate. json: het sjabloon bestand dat alleen de resource micro soft. Solution/Appliances bevat. Zie [inzicht krijgen in de structuur en de syntaxis van Azure Resource Manager-sjablonen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)voor meer informatie. <br>
Let op de volgende belang rijke eigenschappen van deze resource:
    - ' kind ': de waarde moet ' Marketplace ' zijn in het geval van door Marketplace beheerde toepassing.
    - "ManagedResourceGroupId": de resource groep in het abonnement van de klant waar alle resources die zijn gedefinieerd in de applianceMainTemplate. json, worden geïmplementeerd.
    - "PublisherPackageId": de teken reeks waarmee het pakket op unieke wijze wordt geïdentificeerd. Deze waarde moet als volgt worden samengesteld: het is een samen voeging van [publisherId]. [OfferId]-preview [SKUID]. [PackageVersion].

  >[!IMPORTANT] 
  >Dit pakket moet geneste sjablonen of scripts bevatten die nodig zijn om deze toepassing in te richten. Deze bestanden moeten zich in de hoofdmap: MainTemplate. json, applianceMainTemplate. json en applianceCreateUIDefinition. json bevindt.

- **Tenant-id\*** : de Azure Active Directory Tenant-id van uw organisatie.
- **JIT-toegang inschakelen?\*** : Selecteer **Ja** om de just-in-time-beheer toegang voor klant implementaties in te scha kelen met behulp van deze aanbieding.

  >[!NOTE] 
  >Als u JIT inschakelt, moet u het bestand CreateUiDefinition. json bijwerken ter ondersteuning van JIT-toegang.

Voor een beheerde toepassing moet u autorisatie en beleids instellingen configureren.


#### <a name="authorization"></a>Autorisatie

Voeg de Azure Active Directory id toe van de gebruiker, groep of toepassing waaraan u de machtiging wilt verlenen voor de beheerde resource groep. De machtiging die wordt verleend, wordt aangegeven door de roldefinitie-id. Dit kan een eigenaar, bijdrager of een aangepaste rol zijn.


#### <a name="policy-settings"></a>Beleidsinstellingen

Voeg het beleid toe waaraan de beheerde app voldoet. Meer informatie over Azure resource policies, Zie [Wat is Azure Policy?](../../../governance/policy/overview.md)

   ![Autorisatie en beleids instellingen voor een beheerde toepassing](./media/azureapp-sku-details-managedapp-auth-policy.png)

**Een nieuwe autorisatie maken:**

1. Onder **autorisatie**selecteert u **+ nieuwe autorisatie**.
2. Typ voor **Principal-id**de Azure Active Directory-id van de gebruiker, groep of toepassing waaraan u de machtiging wilt verlenen voor de beheerde resource groep. De machtiging die wordt verleend, wordt aangegeven door de functie definitie.
3. Selecteer voor **roldefinitie**een van de volgende opties in de vervolg keuzelijst: eigenaar of Inzender. Zie [Ingebouwde rollen voor Azure-resources](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) voor meer informatie.

>[!NOTE] 
>Er kunnen meerdere autorisaties worden toegevoegd. Het is echter raadzaam om een Active Directory-gebruikers groep te maken en de ID op te geven in ' PrincipalId '. Hiermee kunnen meer gebruikers aan de gebruikers groep worden toegevoegd zonder dat de SKU hoeft te worden bijgewerkt.

**Een nieuw beleid maken:**

1. Onder **beleids instellingen**selecteert u **+ Nieuw beleid**.
2. Voer bij **beleids naam**een naam voor het beleid in. De naam mag Maxi maal 50 tekens lang zijn.
3. Selecteer een van de opties in de vervolg keuzelijst voor **beleid**. Kies het beleid dat de gegevens provider wil inschakelen wanneer de toepassing de gegevens gebruikt. Zie de Azure Policy-voor [beelden](https://docs.microsoft.com/azure/governance/policy/samples/index)voor meer informatie.

    ![Beleids instellingen voor een beheerde toepassing](./media/azureapp-sku-policy-settings.png)

4. Voor de **beleids-SKU**selecteert u gratis of standaard als SKU-type beleid. De standaard-SKU is vereist voor controle beleid.


## <a name="next-steps"></a>Volgende stappen

U beschrijft uw aanbieding en levert ook marketing assets op het [tabblad Marketplace](./cpp-marketplace-tab.md). 
