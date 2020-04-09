---
title: Toewijzing van Azure-partners en klantgebruik | Azure Marketplace
description: Overzicht van het bijhouden van klantgebruik voor Azure Marketplace-oplossingen
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 9/23/2019
ms.author: dsindona
ms.openlocfilehash: 2895944dea6417949488076186135680523e19db
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80874940"
---
# <a name="azure-partner-customer-usage-attribution"></a>Gebruikskenmerken van klanten van Azure-partners

Als softwarepartner voor Azure vereisen uw oplossingen Azure-componenten of moeten ze rechtstreeks op de Azure-infrastructuur worden geïmplementeerd. Klanten die een partneroplossing implementeren en hun eigen Azure-resources inrichten, kunnen het moeilijk vinden om inzicht te krijgen in de status van de implementatie en optica te krijgen in de impact op azure-groei. Wanneer u een hoger zichtbaarheidsniveau toevoegt, sluit u zich aan bij de Microsoft-verkoopteams en krijgt u krediet voor Microsoft-partnerprogramma's.

Microsoft biedt nu een methode om partners te helpen het Azure-gebruik van klantimplementaties van hun software op Azure beter te volgen. De nieuwe methode maakt gebruik van Azure Resource Manager om de implementatie van Azure-services te orkestreren.

Als Microsoft-partner u Azure-gebruik koppelen aan azure-resources die u namens een klant indient. U de koppeling vormen via de Azure Marketplace, de Quickstart-opslagplaats, privé-GitHub-repositories en één-op-één klantbetrokkenheid. Toeschrijving van klantgebruik ondersteunt drie implementatieopties:

- Azure Resource Manager-sjablonen: Partners kunnen Resource Manager-sjablonen gebruiken om de Azure-services te implementeren om de software van de partner uit te voeren. Partners kunnen een Resource Manager-sjabloon maken om de infrastructuur en configuratie van hun Azure-oplossing te definiëren. Met een Resource Manager-sjabloon kunnen u en uw klanten uw oplossing gedurende de gehele levenscyclus implementeren. U erop vertrouwen dat uw resources consistent zijn geïmplementeerd.
- Azure Resource Manager API's: Partners kunnen de API's voor Resource Beheer rechtstreeks aanroepen om een Resource Manager-sjabloon te implementeren of om de API-aanroepen te genereren om Azure-services rechtstreeks in te richten.
- Terraform: Partners kunnen cloudorchestrator zoals Terraform gebruiken om een Resource Manager-sjabloon te implementeren of Azure-services rechtstreeks te implementeren.

Toeschrijving van klantgebruik is voor nieuwe implementatie en ondersteunt GEEN het taggen van bestaande resources die al zijn geïmplementeerd.

Toeschrijving van klantgebruik is vereist voor [Azure Application:](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/azure-applications/cpp-azure-app-offer)oplossingssjabloonaanbieding gepubliceerd op Azure Marketplace.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-resource-manager-templates"></a>Resource Manager-sjablonen gebruiken
Veel partneroplossingen worden geïmplementeerd op het abonnement van een klant met behulp van Resource Manager-sjablonen. Als u een Resource Manager-sjabloon hebt die beschikbaar is in de Azure Marketplace, op GitHub of als Quickstart, moet het proces om uw sjabloon te wijzigen om toeschrijving van klantgebruik in te schakelen, eenvoudig zijn.

Zie voor meer informatie over het maken en publiceren van oplossingssjablonen

* [Maak en implementeer uw eerste Resource Manager-sjabloon](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).
* [Azure Application-aanbieding](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/azure-applications/cpp-azure-app-offer).
* Video: [Oplossingssjablonen en beheerde toepassingen voor de Azure Marketplace bouwen.](https://channel9.msdn.com/Events/Build/2018/BRK3603)


## <a name="add-a-guid-to-your-template"></a>Een GUID toevoegen aan uw sjabloon

Als u een guid (globally unique identifier) wilt toevoegen, brengt u één wijziging aan in het hoofdsjabloonbestand:

1. [Maak een GUID](#create-guids) met de voorgestelde methode en [registreer de GUID](#register-guids-and-offers).

1. Open de sjabloon Resourcemanager.

1. Voeg een nieuwe bron toe aan het hoofdsjabloonbestand. De bron moet alleen in het bestand **mainTemplate.json** of **azuredeploy.json** staan en niet in geneste of gekoppelde sjablonen.

1. Voer de GUID-waarde in na het **pid-voorvoegsel** (bijvoorbeeld pid-eb7927c8-dd66-43e1-b0cf-c346a422063).

1. Controleer de sjabloon op eventuele fouten.

1. Publiceer de sjabloon opnieuw in de juiste opslagplaatsen.

1. [Controleer het succes van GUID in de sjabloonimplementatie](#verify-the-guid-deployment).

### <a name="sample-resource-manager-template-code"></a>Voorbeeld van sjablooncode voor resourcebeheer

Als u trackingresources voor uw sjabloon wilt inschakelen, moet u de volgende extra resource toevoegen onder de sectie Resources. Zorg ervoor dat u de onderstaande voorbeeldcode wijzigt met uw eigen ingangen wanneer u deze toevoegt aan het hoofdsjabloonbestand.
De bron moet alleen worden toegevoegd in het bestand **mainTemplate.json** of **azuredeploy.json** en niet in geneste of gekoppelde sjablonen.

```
// Make sure to modify this sample code with your own inputs where applicable

{ // add this resource to the resources section in the mainTemplate.json (do not add the entire file)
    "apiVersion": "2018-02-01",
    "name": "pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", // use your generated GUID here
    "type": "Microsoft.Resources/deployments",
    "properties": {
        "mode": "Incremental",
        "template": {
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "resources": []
        }
    }
} // remove all comments from the file when complete
```

## <a name="use-the-resource-manager-apis"></a>De API's voor resourcebeheer gebruiken

In sommige gevallen u de voorkeur geven om rechtstreeks te bellen tegen de API's van Resource Manager REST om Azure-services te implementeren. [Azure ondersteunt meerdere SDK's](https://docs.microsoft.com/azure/?pivot=sdkstools) om deze oproepen in te schakelen. U een van de SDK's gebruiken of de REST API's rechtstreeks bellen om resources te implementeren.

Als u een resourcemanagersjabloon gebruikt, moet u uw oplossing taggen door de eerder beschreven instructies te volgen. Als u geen Resource Manager-sjabloon gebruikt en geen directe API-aanroepen doet, u uw implementatie nog steeds taggen om het gebruik van Azure-resources te koppelen.

### <a name="tag-a-deployment-with-the-resource-manager-apis"></a>Een implementatie taggen met de API's voor ResourceManager

Als u toeschrijving van klantgebruik wilt inschakelen, neemt u bij het ontwerpen van uw API-aanroepen een GUID op in de header van de gebruikersagent in de aanvraag. Voeg de GUID toe voor elke aanbieding of SKU. Maak de tekenreeks op met het **pid-voorvoegsel** en voeg de door partners gegenereerde GUID toe. Hier is een voorbeeld van de GUID-indeling voor het invoegen in de gebruikersagent:

![Voorbeeld-GUID-indeling](media/marketplace-publishers-guide/tracking-sample-guid-for-lu-2.PNG)

> [!Note]
> Het formaat van de tekenreeks is belangrijk. Als het **pid-voorvoegsel** niet is opgenomen, is het niet mogelijk om de gegevens op te vragen. Verschillende SDK's volgen anders. Als u deze methode wilt implementeren, controleert u de ondersteunings- en trackingbenadering voor uw favoriete Azure SDK.

#### <a name="example-the-python-sdk"></a>Voorbeeld: De Python SDK

Gebruik voor Python het **kenmerk config.** U het kenmerk alleen toevoegen aan een UserAgent. Hier volgt een voorbeeld:

![Het kenmerk toevoegen aan een gebruikersagent](media/marketplace-publishers-guide/python-for-lu.PNG)

> [!Note]
> Voeg het kenmerk voor elke client toe. Er is geen globale statische configuratie. U een clientfabriek taggen om er zeker van te zijn dat elke client wordt gevolgd. Zie voor meer informatie dit [voorbeeld van de clientfabriek op GitHub.](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79)

#### <a name="tag-a-deployment-by-using-the-azure-powershell"></a>Een implementatie taggen met de Azure PowerShell

Als u resources implementeert via Azure PowerShell, sluit u uw GUID toe met de volgende methode:

```powershell
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

#### <a name="tag-a-deployment-by-using-the-azure-cli"></a>Een implementatie taggen met de Azure CLI

Wanneer u de Azure CLI gebruikt om uw GUID toe te toevoegen, stelt u de **AZURE_HTTP_USER_AGENT** omgevingsvariabele in. U deze variabele instellen binnen het bereik van een script. U de variabele ook globaal instellen voor shell-scope:

```
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```
Zie [Azure SDK for Go voor](https://docs.microsoft.com/azure/go/)meer informatie .

## <a name="use-terraform"></a>Terraform gebruiken

De ondersteuning voor Terraform is beschikbaar via de 1.21.0-versie van Azure Provider: [https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019).  Deze ondersteuning is van toepassing op alle partners die hun oplossing implementeren via Terraform en alle resources die zijn geïmplementeerd en gemeten door de Azure Provider (versie 1.21.0 of hoger).

Azure-provider voor Terraform heeft een nieuw optioneel veld toegevoegd, [*partner_id,*](https://www.terraform.io/docs/providers/azurerm/#partner_id) waarmee u de tracking-GUID opgeeft die u voor uw oplossing gebruikt. De waarde van dit veld kan ook worden ingekocht uit de *ARM_PARTNER_ID* Environment Variable.

```
provider "azurerm" {
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          ……
          # new stuff for ISV attribution
          partner_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
Partners die hun implementatie via Terraform willen laten bijhouden op naamvan klanten, moeten het volgende doen:

* Een GUID maken (de GUID moet worden toegevoegd voor elke aanbieding of SKU)
* Werk hun Azure Provider bij om de waarde van *partner_id* in te stellen op de GUID (FIX ER NIET vooraf de GUID met "pid-", stel deze gewoon in op de werkelijke GUID)

## <a name="create-guids"></a>GUID's maken

Een GUID is een uniek referentienummer met 32 hexadecimale cijfers. Als u GUID's wilt maken voor tracking, moet u een GUID-generator gebruiken. Het Azure Storage-team heeft een [GUID-generatorformulier](https://aka.ms/StoragePartners) gemaakt waarmee u een GUID van de juiste indeling wordt gemaild en kan worden hergebruikt voor de verschillende trackingsystemen.

> [!Note]
> Het wordt ten zeerste aanbevolen om het [GUID-generatorformulier van Azure Storage](https://aka.ms/StoragePartners) te gebruiken om uw GUID te maken. Zie voor meer informatie onze [FAQ.](#faq)

Wij raden u aan voor elk aanbiedings- en distributiekanaal voor elk product een unieke GUID te maken. U ervoor kiezen om één GUID te gebruiken voor de meerdere distributiekanalen van het product als u niet wilt dat de rapportage wordt gesplitst.

Als u een product implementeert met behulp van een sjabloon en het is beschikbaar op zowel de Azure Marketplace als op GitHub, u 2 verschillende GUIDS maken en registreren:

*   Product A in Azure Marketplace
*   Product A op GitHub

Rapportage gebeurt op basis van de partnerwaarde (Microsoft Partner ID) en de GUIDs.

U GUID's ook volgen op een gedetailleerder niveau, zoals de SKU, waar SKU's varianten van een aanbieding zijn.

## <a name="register-guids-and-offers"></a>GUID's en aanbiedingen registreren

De GUID's moeten worden geregistreerd om toeschrijving van klantgebruik mogelijk te maken.

Alle registraties voor sjabloon-GUIDs worden uitgevoerd in het Partnercentrum.

Nadat u de GUID hebt toegevoegd aan uw sjabloon of in de gebruikersagent en de GUID hebt geregistreerd in het partnercentrum, worden alle implementaties bijgehouden.

1. Meld u aan als [uitgever van een commerciële marktplaats.](https://aka.ms/JoinMarketplace)

   * Partners zijn verplicht om een profiel te [hebben in partnercentrum.](https://docs.microsoft.com/azure/marketplace/become-publisher) U wordt aangemoedigd om de aanbieding in Azure Marketplace of AppSource aan te bieden.
   * Partners kunnen meerdere GUID's registreren.
   * Partners kunnen een GUID registreren voor de niet-Marketplace-oplossingssjablonen en -aanbiedingen.

1. Meld u aan bij [Partner Center](https://partner.microsoft.com/dashboard).

1. Selecteer in de rechterbovenhoek het pictogram versnelling instellingen en selecteer **Vervolgens Ontwikkelaarsinstellingen**.

1. Selecteer op de **pagina Accountinstellingen**de optie **Tracking GUID toevoegen.**

1. Voer in het **vak GUID** uw tracking-GUID in. Voer alleen de GUID in zonder het **pid-voorvoegsel.** Voer **in het** vak Beschrijving de naam of beschrijving van uw aanbieding in.

1. Als u meer dan één GUID wilt registreren, selecteert u **GUID toevoegen** opnieuw. Er worden extra vakken op de pagina weergegeven.

1. Selecteer **Opslaan**.


## <a name="verify-the-guid-deployment"></a>De GUID-implementatie verifiëren

Nadat u uw sjabloon hebt gewijzigd en een testimplementatie hebt uitgevoerd, gebruikt u het volgende PowerShell-script om de resources op te halen die u hebt geïmplementeerd en getagd.

U het script gebruiken om te controleren of de GUID is toegevoegd aan de sjabloon ResourceManager. Het script is niet van toepassing op Api-bronbeheer of Terraform-implementaties.

Meld u aan bij Azure. Selecteer het abonnement met de implementatie die u wilt verifiëren voordat u het script uitvoert. Voer het script uit binnen de abonnementscontext van de implementatie.

De **GUID-** en **resourceGroepnaam** van de implementatie zijn vereiste parameters.

U [het originele script](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1#file-verify-deploymentguid-ps1) op GitHub krijgen.

```powershell
Param(
    [GUID][Parameter(Mandatory=$true)]$guid,
    [string][Parameter(Mandatory=$true)]$resourceGroupName
)

# Get the correlationId of the pid deployment

$correlationId = (Get-AzResourceGroupDeployment -ResourceGroupName
$resourceGroupName -Name "pid-$guid").correlationId

# Find all deployments with that correlationId

$deployments = Get-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

# Find all deploymentOperations in a deployment by name
# PowerShell doesn't surface outputResources on the deployment
# or correlationId on the deploymentOperation

foreach ($deployment in $deployments){

# Get deploymentOperations by deploymentName
# then the resourceId for any create operation

($deployment | Get-AzResourceGroupDeploymentOperation | Where-Object{$_.properties.provisioningOperation -eq "Create" -and $_.properties.targetResource.resourceType -ne "Microsoft.Resources/deployments"}).properties.targetResource.id

}
```

## <a name="report"></a>Rapport

U het rapport voor toeschrijving van[https://partner.microsoft.com/dashboard/mpn/analytics/CPP/MicrosoftAzure](https://partner.microsoft.com/dashboard/mpn/analytics/CPP/MicrosoftAzure)klantgebruik vinden in uw dashboard van het Partnercentrum ( ). Als u het rapport wilt zien, moet u uw partnercentrumreferenties gebruiken om u aan te melden. Als u problemen ondervindt bij het melden of aanmelden, maakt u een ondersteuningsverzoek naar aanleiding van de instructie in de sectie Ondersteuning krijgen.

Kies Bijgehouden sjabloon in de vervolgkeuzelijst Partnerkoppelingstype om het rapport te bekijken.

![Rapport voor toeschrijving van klantgebruik](media/marketplace-publishers-guide/customer-usage-attribution-report.png)

## <a name="notify-your-customers"></a>Uw klanten hiervan op de hoogte stellen

Partners moeten hun klanten informeren over implementaties die gebruikmaken van toeschrijving van klantgebruik. Microsoft rapporteert het Azure-gebruik dat aan deze implementaties is gekoppeld aan de partner. De volgende voorbeelden zijn inhoud die u gebruiken om uw klanten op de hoogte te stellen van deze implementaties. Vervang in de \<voorbeelden PARTNER> door uw bedrijfsnaam. Partners moeten ervoor zorgen dat de melding in overeenstemming is met hun beleid voor gegevensprivacy en -verzameling, inclusief opties voor klanten die kunnen worden uitgesloten van tracking.

### <a name="notification-for-resource-manager-template-deployments"></a>Melding voor implementaties van resourcebeheersjablonen

Wanneer u deze sjabloon implementeert, kan \<Microsoft de installatie van PARTNER-> software identificeren met de Azure-resources die zijn geïmplementeerd. Microsoft is in staat om de Azure-bronnen die worden gebruikt om de software te ondersteunen correleren. Microsoft verzamelt deze informatie om de beste ervaringen met hun producten te bieden en om hun bedrijf te runnen. De gegevens worden verzameld en beheerst door het privacybeleid van https://www.microsoft.com/trustcenterMicrosoft, dat kan worden gevonden op .

### <a name="notification-for-sdk-or-api-deployments"></a>Melding voor SDK- of API-implementaties

Wanneer u \<PARTNER->-software implementeert, kan \<Microsoft de installatie van PARTNER->-software identificeren met de Azure-resources die worden geïmplementeerd. Microsoft is in staat om de Azure-bronnen die worden gebruikt om de software te ondersteunen correleren. Microsoft verzamelt deze informatie om de beste ervaringen met hun producten te bieden en om hun bedrijf te runnen. De gegevens worden verzameld en beheerst door het privacybeleid van https://www.microsoft.com/trustcenterMicrosoft, dat kan worden gevonden op .

## <a name="get-support"></a>Ondersteuning krijgen

Er zijn twee ondersteuningskanalen, afhankelijk van de problemen waarmee u wordt geconfronteerd.

Als u problemen ondervindt in het Partnercentrum, zoals het zien van het attributierapport voor klantgebruik of aanmelden, maakt u hier een ondersteuningsverzoek met het ondersteuningsteam van het Partnercenter:[https://partner.microsoft.com/support](https://partner.microsoft.com/support)

![](./media/marketplace-publishers-guide/partner-center-log-in-support.png)

Als u hulp nodig hebt voor Marketplace Onboarding en/of toeschrijving van het klantgebruik in het algemeen, zoals het instellen van de toeschrijving van het klantgebruik, volgt u de onderstaande stappen:

1. Ga naar de [ondersteuningspagina.](https://go.microsoft.com/fwlink/?linkid=844975)

1. Selecteer Marketplace **Onboarding** **onder Probleemtype**.

1. Kies de **rubriek** voor uw probleem:

   - Selecteer **Andere**voor problemen met de gebruikskoppeling .
   - Selecteer **Access-probleem**voor toegangsproblemen met de Azure Marketplace .

     ![De uitgiftecategorie kiezen](media/marketplace-publishers-guide/lu-article-incident.png)

1. Selecteer **Beginaanvraag**.

1. Voer op de volgende pagina de vereiste waarden in. Selecteer **Doorgaan**.

1. Voer op de volgende pagina de vereiste waarden in.

   > [!Important]
   > Voer in het vak **Incidenttitel** **ISV Usage Tracking in**. Beschrijf uw probleem in detail.

   ![Voer ISV-gebruikstracking in voor de incidenttitel](media/marketplace-publishers-guide/guid-dev-center-help-hd%201.png)

1. Vul het formulier in en selecteer **Verzenden**.

U ook technische richtlijnen ontvangen van een technische adviseur van Microsoft Partner voor technische voorverkoop, implementatie- en app-ontwikkelingsscenario's om de toeschrijving van het klantgebruik te begrijpen en op te nemen.

### <a name="how-to-submit-a-technical-consultation-request"></a>Hoe dient u een verzoek tot technisch overleg in te dienen

1. Bezoek [https://aka.ms/TechnicalJourney](https://aka.ms/TechnicalJourney).
1. Selecteer Cloud-infrastructuur en -beheer en er wordt een nieuwe pagina geopend om de technische reis te bekijken.
1. Klik onder Deployment Services op de knop Een aanvraag verzenden
1. Meld u aan met uw MSA -account (MPN-account) of uw AAD (Partner Dashboard-account); op basis van uw aanmeldingsgegevens wordt een online aanvraagformulier geopend:
    * Vul/bekijk de contactgegevens.
    * De raadplegingsgegevens kunnen vooraf worden ingevuld of uit de vervolgkeuzeklassen worden geselecteerd.
    * Voer een titel en de beschrijving van het probleem in (geef zoveel mogelijk details).
1. Klik op Submit

Bekijk stapsgewijze instructies met screenshots [https://aka.ms/TechConsultInstructions](https://aka.ms/TechConsultInstructions)op .

### <a name="whats-next"></a>Volgend onderwerp

Er wordt contact met u opgenomen met een technische adviseur van Microsoft Partner om een oproep in te stellen om aan uw behoeften te voldoen.

## <a name="faq"></a>Veelgestelde vragen

**Wat is het voordeel van het toevoegen van de GUID aan de sjabloon?**

Microsoft biedt partners een overzicht van de implementaties van klanten van hun oplossingen en inzichten over hun beïnvloed gebruik. Zowel Microsoft als de partner kunnen deze informatie gebruiken om de betrokkenheid van verkoopteams te vergroten. Zowel Microsoft als de partner kunnen de gegevens gebruiken om een consistenter beeld te krijgen van de impact van een individuele partner op azure-groei.

**Kan een GUID worden gewijzigd nadat een GUID is toegevoegd?**

Ja, een klant of implementatiepartner kan de sjabloon aanpassen en de GUID wijzigen of verwijderen. We raden partners aan om proactief de rol van de resource en GUID te beschrijven aan hun klanten en partners om verwijdering of bewerkingen van de GUID te voorkomen. Het wijzigen van de GUID heeft alleen gevolgen voor nieuwe, niet bestaande implementaties en resources.

**Kan ik sjablonen bijhouden die zijn geïmplementeerd vanuit een niet-Microsoft-opslagplaats zoals GitHub?**

Ja, zolang de GUID aanwezig is wanneer de sjabloon wordt geïmplementeerd, wordt het gebruik bijgehouden. Partners moeten een profiel hebben in de inschrijving op de commerciële marktplaats in het Partnercentrum om GUID's te registreren die worden gebruikt voor de implementatie buiten de Azure Marketplace.

**Ontvangt de klant ook rapportage?**

Klanten kunnen hun gebruik van afzonderlijke resources of door de klant gedefinieerde brongroepen binnen de Azure-portal bijhouden.

**Is deze methodologie vergelijkbaar met de Digital Partner of Record (DPOR)?**

Deze nieuwe methode om de implementatie en het gebruik te koppelen aan de oplossing van een partner biedt een mechanisme om een partneroplossing te koppelen aan Azure-gebruik. DPOR is bedoeld om een consulting (Systems Integrator) of managementpartner (Managed Service Provider) te koppelen aan het Azure-abonnement van een klant.

**Wat is het voordeel van het gebruik van het GUID-generatorformulier van Azure Storage?**

Het GUID-generatorformulier van Azure Storage genereert gegarandeerd een GUID met de vereiste indeling. Als u een van de methoden voor het bijhouden van gegevensvlakvan Azure Storage gebruikt, u bovendien dezelfde GUID gebruiken voor het bijhouden van het beheer van marketplace-besturingsvlak. Hiermee u gebruikmaken van een uniforme GUID voor partnertoewijzing zonder afzonderlijke GUIDS te hoeven onderhouden.

**Kan ik een privé, aangepaste VHD gebruiken voor een oplossingssjabloonaanbieding in de Azure Marketplace?**

Nee, dat kan je niet. De virtuele machine afbeelding moet afkomstig zijn [https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines)van de Azure Marketplace, zie: .

U een VM-aanbieding in marketplace maken met behulp van uw aangepaste VHD en deze markeren als Privé, zodat niemand het kan zien. Verwijs vervolgens naar deze VM in uw oplossingssjabloon.

**Kan de eigenschap *contentVersion* voor de hoofdsjabloon niet worden bijgewerkt?**

Waarschijnlijk een bug in sommige gevallen wanneer de sjabloon wordt geïmplementeerd met behulp van een TemplateLink van een andere sjabloon die oudere contentVersion verwachten om wat voor reden dan ook. De tijdelijke oplossing is het gebruik van de eigenschap metagegevens:

```
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "metadata": {
        "contentVersion": "1.0.1.0"
    },
    "parameters": {
```
