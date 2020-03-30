---
title: SKU's configureren voor een Azure-toepassingsaanbieding | Azure Marketplace
description: De SKU's configureren voor een door Azure beheerde toepassing en een Azure-oplossingssjabloon.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: dsindona
ms.openlocfilehash: 043394a1303456ce5b209bb84b5afaf09f6beba4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289070"
---
# <a name="azure-application-skus-tab"></a>Tabblad SKU's voor Azure-toepassingen

In dit artikel wordt beschreven hoe u het tabblad SKU's gebruiken om SKU's voor uw Azure-toepassing te maken. 

> [!IMPORTANT]
> De stappen voor het configureren van een SKU zijn verschillend voor een managed-toepassingsaanbieding en een sjabloonaanbieding voor oplossingen. Deze verschillen zijn gedocumenteerd in dit artikel. 

## <a name="configure-azure-application-skus"></a>SKU's voor Azure-toepassingen configureren

### <a name="create-a-new-sku"></a>Een nieuwe SKU maken

Gebruik deze stappen om een nieuwe SKU te maken:

1. Selecteer het tabblad **SKU's.**
2. Selecteer onder SKU's de optie **+ Nieuwe SKU**.

    ![Nieuwe SKU-prompt](./media/azureapp-plus-sku.png)

3. Typ in het nieuwe Pop-upvenster van De SKU een **SKU-id**. Deze id is beperkt tot 50 tekens en mag alleen bestaan uit kleine letters, alfanumerieke tekens, streepjes of underscores. De SKU-id kan niet in een streepje eindigen.
4. De SKU-id is zichtbaar voor klanten in product-URL's, Resource Manager-sjablonen (indien van toepassing) en factureringsrapporten. U deze id niet wijzigen nadat de aanbieding is gepubliceerd.

### <a name="sku-details-for-a-solution-template"></a>SKU-gegevens voor een oplossingssjabloon

De volgende schermopname toont het Formulier SKU-details voor een oplossingssjabloon.

![SKU-gegevensformulier voor oplossingssjabloon](./media/azureapp-sku-details-solutiontemplate.png)

Geef de volgende SKU-waarden op.  De velden die zijn toegevoegd met een sterretje zijn vereist.

|    Veld         |       Beschrijving                                                            |
|  ---------       |     ---------------                                                          |
|  **Titel\***     | Een titel voor de SKU. Deze titel wordt weergegeven in de galerie voor dit object.   |
| **Samenvatting\***    | Een korte samenvatting van de SKU. (Maximale lengte is 100 tekens.)  |
| **Beschrijving\*** | Een gedetailleerde beschrijving van de SKU. Basis-HTML wordt ondersteund.                 | 
| **SKU-type\***   | Type Azure-toepassingsoplossing selecteert ***Oplossingssjabloon** voor dit scenario. |
| **Beschikbaarheid in de cloud\*** | De locatie van de SKU. De standaardinstelling is **Public Azure**.  <b/>   **Openbare Azure** - App is beschikbaar voor klanten in alle openbare Azure-regio's met marktintegratie.  <b/>   **Azure Government Cloud** - App wordt geïmplementeerd in de Azure Government Cloud. Voordat microsoft aan [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)publiceert, raadt Microsoft uitgevers aan hun oplossingswerken te testen en te valideren zoals verwacht in deze omgeving. Als u wilt fasen en testen, vraagt u een [proefaccount aan](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/).  |
| **Is dit een privé SKU?\*** | Selecteer **Ja** als deze SKU alleen beschikbaar is voor een selecte groep klanten. |
|   |   |

  > [!NOTE] 
  > Microsoft Azure Government is een cloud die door de overheid wordt beheerd met gecontroleerde toegang voor klanten van de Amerikaanse federale, staats-, lokale of tribale EN-partners die in aanmerking komen om deze entiteiten te bedienen.


### <a name="sku-details-for-managed-application"></a>SKU-gegevens voor beheerde toepassing

De volgende schermopname toont het Formulier SKU-details voor een beheerde toepassing.

   ![SKU-informatieformulier voor beheerde toepassing](./media/azureapp-sku-details-managedapplication.png)

Configureer de volgende SKU-instellingen. De velden die zijn toegevoegd met een sterretje zijn vereist.

|    Veld         |       Beschrijving                                                            |
|  ---------       |     ---------------                                                          |
|  **Titel\***     | Een titel voor de SKU. Deze titel wordt weergegeven in de galerie voor dit object.   |
| **Samenvatting\***    | Een korte samenvatting van de SKU. (Maximale lengte is 100 tekens.)  |
| **Beschrijving\*** | Een gedetailleerde beschrijving van de SKU. Basis-HTML wordt ondersteund.                 | 
| **SKU-type\***   | Type Azure-toepassingsoplossing selecteert ***Beheerde toepassing** voor dit scenario. 
| **Beschikbaarheid in de cloud\*** | De locatie van de SKU. De standaardinstelling is **Public Azure**.  <b/>   **Openbare Azure** - App is beschikbaar voor klanten in alle openbare Azure-regio's met marktintegratie.  <b/>   **Azure Government Cloud** - App wordt geïmplementeerd in de Azure Government Cloud. Voordat microsoft aan [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)publiceert, raadt Microsoft uitgevers aan hun oplossingswerken te testen en te valideren zoals verwacht in deze omgeving. Als u wilt fasen en testen, vraagt u een [proefaccount aan](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/).   Microsoft Azure Government is een cloud die door de overheid wordt beheerd met gecontroleerde toegang voor klanten van de Amerikaanse federale, staats-, lokale of tribale EN-partners die in aanmerking komen om deze entiteiten te bedienen. |
| **Is dit een privé SKU?\*** | Selecteer **Ja** als deze SKU alleen beschikbaar is voor een selecte groep klanten. |
| **Beschikbaarheid van land/regio\*** | Gebruik **Regio's selecteren** om de lijst met landen/regio's weer te geven die beschikbaar zijn. Controleer elk land/regio en selecteer **OK** om uw selecties op te slaan.  <b/>   ![Beschikbaarheidslijst voor land en regio](./media/azure-app-select-country-region.png)  |
| **Oude prijzen\*** | De prijs voor de SKU, in USD per maand. Prijzen worden ingesteld in lokale valuta met behulp van de huidige wisselkoersen bij configuratie. Valideer deze omdat u uiteindelijk eigenaar bent van deze instellingen. Als u de prijs van elk land/regio afzonderlijk wilt instellen of bekijken, exporteert u de prijsspreadsheet en import met aangepaste prijzen.  U moet uw prijswijzigingen opslaan om export-/import-informatie in te schakelen.  |
| **Vereenvoudigde valutaprijzen\*** | De prijs voor de SKU, in USD per maand. Dit moet hetzelfde zijn als oude prijzen. Zie [Vereenvoudigde valutaprijzen](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-update-existing-offer)voor meer informatie . |
|  |  |


### <a name="package-details-for-solution-template"></a>Pakketgegevens voor oplossingssjabloon

![Pakketgegevens voor oplossingssjabloon](./media/azureapp-sku-pkgdetails-solutiontemplate.png)

Geef de volgende **waarden voor pakketdetails op.**  De velden die zijn toegevoegd met een sterretje zijn vereist.

- **Versie\* ** - De versie van het pakket dat u uploadt. Versietags moeten van het formulier X.Y.Z zijn, waarbij X, Y en Z gehele getallen zijn.
- **Pakketbestand (.zip)\* ** - Dit pakket bevat de volgende bestanden, opgeslagen in een .zip-bestand.
  - **mainTemplate.json\* ** - Het implementatiesjabloonbestand dat wordt gebruikt om de oplossing/toepassing te implementeren en de bronnen te maken die voor de oplossing zijn gedefinieerd. Zie Hoe u [implementatiesjabloonbestanden maakt](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)voor meer informatie .
  - **createUIDefinition.json\* ** - Dit bestand wordt gebruikt door de Azure-portal om de gebruikersinterface te genereren voor het inrichten van deze oplossing/toepassing. Zie [Gebruikersinterface van Azure portal maken voor uw beheerde toepassing voor](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)meer informatie.
  - Scripts (indien nodig) - Eventuele aanvullende scripts die nodig kunnen `Microsoft.Compute/virtualMachines/extensions`zijn bij het uitvoeren van de sjabloon, bijvoorbeeld .
  - Geneste sjablonen (indien nodig) - Eventuele extra geneste sjablonen.

  > [!IMPORTANT] 
  > Dit pakket moet geneste sjablonen of scripts bevatten die nodig zijn om deze toepassing in te richten. Het bestand mainTemplate.json en het bestand createUIDefinition.json moeten zich in de hoofdmap bevinden. Zie [Azure Resource Manager Templates - Handleiding voor aanbevolen procedures](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md#deployment-artifacts-nested-templates-scripts)voor meer informatie over implementatieartefacten.


### <a name="package-details-for-managed-application"></a>Pakketgegevens voor beheerde toepassing

   ![Pakketgegevens voor beheerde toepassing](./media/azureapp-sku-pkgdetails-managedapplication.png)

Geef de volgende pakketgegevens op.  De velden die zijn toegevoegd met een sterretje zijn vereist.

- **Versie\* ** - De versie van het pakket dat u uploadt. Versietags moeten van het formulier X.Y.Z zijn, waarbij X, Y en Z gehele getallen zijn.
- **Pakketbestand (.zip)\* ** - Dit pakket bevat de volgende bestanden, opgeslagen in een .zip-bestand.
  - toestelMainTemplate.json - Het implementatiesjabloonbestand dat wordt gebruikt om de oplossing/toepassing te implementeren en de resources te maken die zijn gedefinieerd. Zie [Snelstart: Azure Resource Manager-sjablonen maken en implementeren met behulp van de Azure-portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)voor meer informatie. 
  - toestelCreateUIDefinition.json - Dit bestand wordt door de Azure-portal gebruikt om de gebruikersinterface voor het inrichten van deze oplossing/toepassing te genereren. Zie [Gebruikersinterface van Azure portal maken voor uw beheerde toepassing voor](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)meer informatie.
  - mainTemplate.json - Het sjabloonbestand dat alleen de bron Microsoft.Solution/appliances bevat. Zie [De structuur en syntaxis van Azure Resource Manager Templates begrijpen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)voor meer informatie. <br>
Let op de volgende belangrijke eigenschappen van deze bron:
    - "vriendelijk" - De waarde moet "Marketplace" zijn in het geval van Marketplace-Managed-toepassing.
    - "ManagedResourceGroupId" - De resourcegroep in het abonnement van de klant waarbij alle resources die zijn gedefinieerd in het toestelMainTemplate.json worden geïmplementeerd.
    - "PublisherPackageId"- De string die het pakket uniek identificeert. Deze waarde moet als volgt worden geconstrueerd: het is een samenvoeging van [publisherId]. [OfferId]-preview[SKUID]. [PackageVersion].

  >[!IMPORTANT] 
  >Dit pakket moet geneste sjablonen of scripts bevatten die nodig zijn om deze toepassing in te richten. Deze bestanden moeten zich in de hoofdmap bevinden: MainTemplate.json, applianceMainTemplate.json en applianceCreateUIDefinition.json.

- **Tenant-id\* ** - de Azure Active Directory-tenant-id van uw organisatie.
- **JIT Access inschakelen? \* ** - Selecteer **Ja** om Just-In-Time-beheertoegang in te schakelen voor klantimplementaties met behulp van deze aanbieding.

  >[!NOTE] 
  >Als u JIT inschakelt, moet u het bestand CreateUiDefinition.json bijwerken om JIT-toegang te ondersteunen.

Voor een beheerde toepassing moet u autorisatie- en beleidsinstellingen configureren.


#### <a name="authorization"></a>Autorisatie

Voeg de Azure Active Directory Identifier van gebruiker, groep of toepassing waaraan u de toestemming wilt verlenen aan de beheerde brongroep toe. De machtiging die wordt verleend, wordt aangegeven met de functiedefinitie-id. Het kan een eigenaar, bijdrager of een aangepaste rol.


#### <a name="policy-settings"></a>Beleid Instellingen

Voeg het beleid toe waar de Beheerde App aan voldoet. Meer informatie over Azure Resource-beleidsregels, zie [Wat is Azure-beleid?](../../../governance/policy/overview.md)

   ![Autorisatie- en beleidsinstellingen voor een beheerde toepassing](./media/azureapp-sku-details-managedapp-auth-policy.png)

**Ga als volgende over een nieuwe autorisatie:**

1. Selecteer **onder Autorisatie**de optie + Nieuwe **autorisatie**.
2. Typ **voor Hoofd-id**de Azure Active Directory-id van gebruiker, groep of toepassing waaraan u de toestemming wilt verlenen aan de beheerde brongroep. De verleende toestemming wordt aangegeven in de roldefinitie.
3. Selecteer **voor Roldefinitie**een van deze opties in de vervolgkeuzelijst: Eigenaar of Inzender. Zie [Ingebouwde rollen voor Azure-resources](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) voor meer informatie.

>[!NOTE] 
>Er kunnen meerdere autorisaties worden toegevoegd. Het wordt echter aanbevolen om een Active Directory-gebruikersgroep te maken en de id op te geven in de PrincipalId. Hierdoor kunnen meer gebruikers aan de gebruikersgroep worden toegeten zonder dat u de SKU hoeft bij te werken.

**Ga als lid van het nieuwe beleid:**

1. Selecteer **onder Beleidsinstellingen** **+ Nieuw beleid**.
2. Voer **voor Beleidsnaam**een naam in voor het beleid. De maximale lengte van de naam is 50 tekens.
3. Selecteer **voor Beleid**een van de opties in de vervolgkeuzelijst. Kies het beleid dat de gegevensprovider wil inschakelen wanneer de toepassing de gegevens gebruikt. Zie de Azure [Policy Samples](https://docs.microsoft.com/azure/governance/policy/samples/index)voor meer informatie.

    ![Beleidsinstellingen voor een beheerde toepassing](./media/azureapp-sku-policy-settings.png)

4. Selecteer **Voor Beleids-SKU**de optie Gratis of Standaard als het type Beleids-SKU. De standaard SKU is vereist voor controlebeleid.


## <a name="next-steps"></a>Volgende stappen

U beschrijft uw aanbieding en levert marketingassets verder op het [tabblad Marketplace.](./cpp-marketplace-tab.md) 
