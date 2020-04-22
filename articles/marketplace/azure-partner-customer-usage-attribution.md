---
title: Commerciële Marketplace-partner en toeschrijving van klantgebruik
description: Krijg een overzicht van het bijhouden van klantgebruik voor Azure Marketplace-oplossingen.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: e061baa8e7eb73bae0a78f4215f0d48610e85fcb
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686736"
---
# <a name="commercial-marketplace-partner-and-customer-usage-attribution"></a>Commerciële Marketplace-partner en toeschrijving van klantgebruik

Toeschrijving van klantgebruik is een methode om Azure-resources die worden uitgevoerd in klantabonnementen, die zijn geïmplementeerd om uw oplossing uit te voeren, te koppelen aan u als partner. Het vormen van deze associaties in interne Microsoft-systemen geeft meer zichtbaarheid aan de Azure-footprint waarop uw software wordt uitgevoerd. Wanneer u deze trackingmogelijkheid overneemt, sluit u zich aan bij microsoft-verkoopteams en krijgt u krediet voor Microsoft-partnerprogramma's.

U de koppeling vormen via Azure Marketplace, de Quickstart-repository, private GitHub-repositories en 1:1 klantinteracties die duurzame IP maken (zoals de ontwikkeling van een app).

Toeschrijving van klantgebruik ondersteunt drie implementatieopties:

- Azure Resource Manager-sjablonen: Partners kunnen Resource Manager-sjablonen gebruiken om de Azure-services te implementeren om de software van de partner uit te voeren. Partners kunnen een Resource Manager-sjabloon maken om de infrastructuur en configuratie van hun Azure-oplossing te definiëren. Met een Resource Manager-sjabloon kunnen u en uw klanten uw oplossing gedurende de gehele levenscyclus implementeren. U erop vertrouwen dat uw resources consistent zijn geïmplementeerd.
- Azure Resource Manager API's: Partners kunnen de API's voor Resource Beheer rechtstreeks aanroepen om een Resource Manager-sjabloon te implementeren of om de API-aanroepen te genereren om Azure-services rechtstreeks in te richten.
- Terraform: Partners kunnen Terraform gebruiken om een Resource Manager-sjabloon te implementeren of Azure-services rechtstreeks te implementeren.

>[!IMPORTANT]
>- Toeschrijving van klantgebruik is niet bedoeld om het werk bij te houden van systeemintegratoren, beheerde serviceproviders of hulpprogramma's die zijn ontworpen om software te implementeren en te beheren die op Azure wordt uitgevoerd.
>
>- Toeschrijving van klantgebruik is voor nieuwe implementaties en biedt geen ondersteuning voor het taggen van bestaande resources die al zijn geïmplementeerd.
>
>- Toeschrijving van klantgebruik is vereist voor [Azure Application-aanbiedingen](./partner-center-portal/create-new-azure-apps-offer.md) die zijn gepubliceerd op Azure Marketplace.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-guids"></a>GUID's maken

Een GUID is een unieke referentie-id met 32 hexadecimale cijfers. Als u GUID's wilt maken voor tracking, moet u een GUID-generator gebruiken. Het Azure Storage-team heeft een [GUID-generatorformulier](https://aka.ms/StoragePartners) gemaakt waarmee u een GUID van de juiste indeling wordt gemaild en kan worden hergebruikt voor de verschillende trackingsystemen.

> [!NOTE]
> Het wordt ten zeerste aanbevolen om het [GUID-generatorformulier van Azure Storage](https://aka.ms/StoragePartners) te gebruiken om uw GUID te maken. Zie voor meer informatie onze [FAQ.](#faq)

Wij raden u aan voor elk aanbiedings- en distributiekanaal voor elk product een unieke GUID te maken. U ervoor kiezen om één GUID te gebruiken voor de meerdere distributiekanalen van het product als u niet wilt dat de rapportage wordt gesplitst.

Als u een product implementeert met behulp van een sjabloon en het is beschikbaar op zowel de Azure Marketplace als op GitHub, u twee verschillende GUIDS maken en registreren:

- Product A in Azure Marketplace
- Product A op GitHub

Rapportage wordt gedaan door Microsoft Partner Network ID en GUID.

U het gebruik ook op een gedetailleerder niveau bijhouden door extra GUID's te registreren en GUID's tussen plannen te wijzigen, waarbij plannen varianten van een aanbieding zijn.

## <a name="register-guids"></a>GUID's registreren

De GUID's moeten zijn geregistreerd in het Partnercentrum om toeschrijving van klantgebruik mogelijk te maken.

Nadat u een GUID hebt toegevoegd aan uw sjabloon of in de gebruikersagent en de GUID hebt geregistreerd in partnercentrum, worden toekomstige implementaties bijgehouden.

1. Meld u aan bij [Partner Center](https://partner.microsoft.com/dashboard).

1. Meld u aan als [uitgever van een commerciële marktplaats.](https://aka.ms/JoinMarketplace)

   * Partners zijn verplicht om een profiel te [hebben in partnercentrum.](https://docs.microsoft.com/azure/marketplace/become-publisher) U wordt aangemoedigd om de aanbieding in Azure Marketplace of AppSource aan te bieden.
   * Partners kunnen meerdere GUID's registreren.
   * Partners kunnen GUID's registreren voor niet-marketplace-oplossingssjablonen en -aanbiedingen.

1. Selecteer in de rechterbovenhoek het pictogram versnelling instellingen en selecteer **Vervolgens Ontwikkelaarsinstellingen**.

1. Selecteer op de **pagina Accountinstellingen**de optie **Tracking GUID toevoegen.**

1. Voer in het **vak GUID** uw tracking-GUID in. Voer alleen de GUID in zonder het **pid-voorvoegsel.** Voer **in het** vak Beschrijving de naam of beschrijving van uw aanbieding in.

1. Als u meer dan één GUID wilt registreren, selecteert u **GUID toevoegen** opnieuw. Er worden extra vakken op de pagina weergegeven.

1. Selecteer **Opslaan**.

## <a name="use-resource-manager-templates"></a>Resource Manager-sjablonen gebruiken
Veel partneroplossingen worden geïmplementeerd met Azure Resource Manager-sjablonen. Als u een Resource Manager-sjabloon hebt die beschikbaar is in de Azure Marketplace, op GitHub of als Quickstart, is het proces om uw sjabloon te wijzigen om toeschrijving van klantgebruik in te schakelen, eenvoudig.

> [!NOTE]
> Zie voor meer informatie over het maken en publiceren van oplossingssjablonen
> * [Maak en implementeer uw eerste Resource Manager-sjabloon](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).
>* [Azure Application-aanbieding](./partner-center-portal/create-new-azure-apps-offer.md).
>* Video: [Oplossingssjablonen en beheerde toepassingen voor de Azure Marketplace bouwen.](https://channel9.msdn.com/Events/Build/2018/BRK3603)


Als u een guid (globally unique identifier) wilt toevoegen, brengt u één wijziging aan in het hoofdsjabloonbestand:

1. [Maak een GUID](#create-guids) met de voorgestelde methode en [registreer de GUID](#register-guids).

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

> [!NOTE]
> Het formaat van de tekenreeks is belangrijk. Als het **pid-voorvoegsel** niet is opgenomen, is het niet mogelijk om de gegevens op te vragen. Verschillende SDK's volgen anders. Als u deze methode wilt implementeren, controleert u de ondersteunings- en trackingbenadering voor uw favoriete Azure SDK.

#### <a name="example-the-python-sdk"></a>Voorbeeld: De Python SDK

Gebruik voor Python het **kenmerk config.** U het kenmerk alleen toevoegen aan een UserAgent. Hier volgt een voorbeeld:

![Het kenmerk toevoegen aan een gebruikersagent](media/marketplace-publishers-guide/python-for-lu.PNG)

> [!NOTE]
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

   > [!IMPORTANT]
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

Ja, zolang de GUID aanwezig is wanneer de sjabloon wordt geïmplementeerd, wordt het gebruik bijgehouden. Partners moeten hun GUIDs nog steeds registreren.

**Ontvangt de klant ook rapportage?**

Klanten kunnen hun gebruik van afzonderlijke resources of door de klant gedefinieerde brongroepen binnen de Azure-portal bijhouden. Klanten zien geen gebruik uitgebroken door GUID.

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
