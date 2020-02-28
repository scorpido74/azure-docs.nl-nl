---
title: Toepassings wijzigings analyse in Azure Monitor gebruiken om problemen met web-apps op te sporen | Microsoft Docs
description: Toepassings wijzigingen in Azure Monitor gebruiken om problemen met toepassingen op Live sites in Azure App Service op te lossen.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/07/2019
ms.openlocfilehash: 143f55a02a856b536172bd5fc2bac15903a228b9
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77655681"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>Toepassings wijzigings analyse (preview) gebruiken in Azure Monitor

Wanneer er een probleem is met de live site of de storing optreedt, is het snel bepalen van de hoofd oorzaak van cruciaal belang. Met oplossingen voor standaard bewaking wordt u mogelijk op een probleem gewaarschuwd. Ze kunnen zelfs aangeven welk onderdeel mislukt. Deze waarschuwing legt echter niet altijd onmiddellijk de oorzaak van de fout uit. U weet dat uw site vijf minuten geleden heeft gewerkt en nu is verbroken. Wat is er in de afgelopen vijf minuten gewijzigd? Dit is de vraag of de analyse van toepassings wijzigingen is ontworpen om in Azure Monitor te beantwoorden.

Wijzigingen die zijn gebaseerd op de kracht van de [Azure-resource grafiek](https://docs.microsoft.com/azure/governance/resource-graph/overview), bieden inzicht in de veranderingen in de Azure-toepassing om de waarneembaarheid te verg Roten en MTTR te verminderen (gemiddelde tijd om te herstellen).

> [!IMPORTANT]
> De analyse van wijzigingen is momenteel beschikbaar als preview-versie. Deze preview-versie is beschikbaar zonder een service overeenkomst. Deze versie wordt niet aanbevolen voor productie werkbelastingen. Sommige functies worden mogelijk niet ondersteund of hebben mogelijk beperkte mogelijkheden. Zie voor meer informatie [aanvullende gebruiks voorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview"></a>Overzicht

Met wijzigings analyse worden verschillende soorten wijzigingen gedetecteerd, van de laag van de infra structuur voor de implementatie van toepassingen. Het is een Azure-resource provider op abonnements niveau waarmee wijzigingen in de resource in het abonnement worden gecontroleerd. Wijzigings analyse biedt gegevens voor verschillende diagnostische hulpprogram ma's waarmee gebruikers kunnen begrijpen welke wijzigingen problemen hebben veroorzaakt.

In het volgende diagram ziet u de architectuur van de wijzigings analyse:

![Architectuur diagram van de manier waarop wijzigingen in de analyse wijzigings gegevens worden opgehaald en biedt client hulpprogramma's](./media/change-analysis/overview.png)

De huidige wijzigings analyse is geïntegreerd in de ervaring voor het **opsporen en oplossen van problemen** in de app service Web-app, en ook beschikbaar als een zelfstandig tabblad in azure Portal.
Zie de sectie *wijzigingen weer geven voor alle resources in azure* voor toegang tot de Blade wijzigings analyse en de *wijzigings analyse voor de functie van web apps voor het* gebruik van de web-app-Portal verderop in dit artikel.

### <a name="azure-resource-manager-tracked-properties-changes"></a>Wijzigingen in bijgehouden eigenschappen Azure Resource Manager

Met behulp van de [resource grafiek van Azure](https://docs.microsoft.com/azure/governance/resource-graph/overview)kunt u een historisch overzicht krijgen van de manier waarop de Azure-resources die als host voor uw toepassing fungeren, na verloop van tijd worden gewijzigd. Bijgehouden instellingen, zoals beheerde identiteiten, upgrade van platform besturingssysteem en hostnamen, kunnen worden gedetecteerd.

### <a name="azure-resource-manager-proxied-setting-changes"></a>Wijzigingen in de instellingen van de proxy Azure Resource Manager
Instellingen, zoals de IP-configuratie regel, SSL-instellingen en extensie versies zijn nog niet beschikbaar in ARG, dus Wijzig analyse query's en berekent deze wijzigingen veilig om meer informatie te geven over wat er in de app is gewijzigd. Deze gegevens zijn nog niet beschikbaar in de Azure-resource grafiek, maar zijn binnenkort beschikbaar.

### <a name="changes-in-web-app-deployment-and-configuration-in-guest-changes"></a>Wijzigingen in de implementatie en configuratie van de web-app (in-gast wijzigingen)

Met de wijzigings analyse worden de implementatie-en configuratie status van een toepassing elke 4 uur vastgelegd. Het kan bijvoorbeeld wijzigingen in de omgevings variabelen van de toepassing detecteren. Het hulp programma berekent de verschillen en geeft aan wat er is gewijzigd. In tegens telling tot Resource Manager-wijzigingen is de wijzigings informatie voor code-implementatie mogelijk niet onmiddellijk beschikbaar in het hulp programma. Als u de meest recente wijzigingen in de analyse van wijzigingen wilt weer geven, selecteert u **wijzigingen nu scannen**.

![Scherm opname van de knop "wijzigingen nu scannen"](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Wijzigingen van afhankelijkheden

Wijzigingen in bron afhankelijkheden kunnen ook problemen veroorzaken in een web-app. Als een web-app bijvoorbeeld aanroept in een redis-cache, kan de redis-cache-SKU invloed hebben op de prestaties van de web-app. Als u wijzigingen in afhankelijkheden wilt detecteren, controleert u of de DNS-record van de web-app is gewijzigd. Op deze manier worden wijzigingen in alle app-onderdelen geïdentificeerd die problemen kunnen veroorzaken.
Momenteel worden de volgende afhankelijkheden ondersteund:
- Web Apps
- Azure Storage
- Azure SQL

### <a name="enablement"></a>Modules
De resource provider micro soft. ChangeAnalysis moet worden geregistreerd met een abonnement voor de Azure Resource Manager bijgehouden eigenschappen en de instellingen voor proxy Change data beschikbaar zijn. Wanneer u het hulp programma voor het vaststellen en oplossen van problemen met de web-app invoert of het zelfstandige tabblad wijzigings analyse weer geven, wordt deze resource provider automatisch geregistreerd. Er zijn geen prestatie-en kosten implementaties voor uw abonnement. Wanneer u de functie voor het wijzigen van de analyse voor web-apps inschakelt (of inschakelt in het hulp programma problemen vaststellen en oplossen), heeft dit een negatieve invloed op de prestaties van de web-app en geen facturerings kosten.
Voor wijzigingen in de gast van een web-app is afzonderlijke activering vereist voor het scannen van code bestanden in een web-app. Zie voor meer informatie [analyse van wijzigingen inschakelen in de sectie problemen vaststellen en oplossen](https://docs.microsoft.com/azure/azure-monitor/app/change-analysis#enable-change-analysis-in-the-diagnose-and-solve-problems-tool) , verderop in dit artikel voor meer informatie.


## <a name="viewing-changes-for-all-resources-in-azure"></a>Wijzigingen voor alle resources in azure weer geven
In Azure Monitor is er een zelfstandige Blade voor het wijzigen van de analyse om alle wijzigingen met inzichten en toepassings afhankelijkheden weer te geven.

Zoek naar veranderingen analyse in de zoek balk op Azure Portal om de Blade te starten.

![Scherm opname van het zoeken van wijzigingen in Azure Portal](./media/change-analysis/search-change-analysis.png)

Selecteer de resource groep en de resources om de wijzigingen weer te geven.

![Scherm opname van de Blade voor het wijzigen van de analyse in Azure Portal](./media/change-analysis/change-analysis-standalone-blade.png)

U kunt inzichten en gerelateerde afhankelijkheids resources zien die uw toepassing hosten. Deze weer gave is ontworpen om toepassingen gericht te zijn voor ontwikkel aars om problemen op te lossen.

De resources die momenteel worden ondersteund zijn:
- Virtuele machines
- Schaalset voor virtuele machines
- Azure-netwerk bronnen
- Een web-app met wijzigingen in de gast bestands tracering en omgevings variabelen

Gebruik de knop feedback verzenden in de Blade of het e-mail changeanalysisteam@microsoft.comvoor elke feedback.

![Scherm afbeelding van de knop feedback op de Blade wijzigings analyse](./media/change-analysis/change-analysis-feedback.png)

## <a name="change-analysis-for-the-web-apps-feature"></a>De analyse van de functie Web Apps wijzigen

In Azure Monitor is wijzigings analyse ook ingebouwd in de ervaring van de self-service om **problemen op te lossen** . Toegang tot deze ervaring via de pagina **overzicht** van uw app service-toepassing.

![Scherm afbeelding van de knop overzicht en de knop problemen vaststellen en oplossen](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>Wijzigingen in de analyse inschakelen in het hulp programma problemen vaststellen en oplossen

1. Selecteer **Beschik baarheid en prestaties**.

    ![Scherm afbeelding van de opties voor het oplossen van problemen met Beschik baarheid en prestaties](./media/change-analysis/availability-and-performance.png)

1. Selecteer **toepassings wijzigingen**. Het is niet mogelijk dat de functie ook beschikbaar is in **toepassings crashes**.

   ![Scherm opname van de knop ' toepassings crashes '](./media/change-analysis/application-changes.png)

1. Selecteer **nu inschakelen**om de analyse van wijzigingen in te scha kelen.

   ![Scherm opname van de opties ' toepassings crashes '](./media/change-analysis/enable-changeanalysis.png)

1. Schakel de **analyse van wijzigingen** in en selecteer **Opslaan**. Het hulp programma geeft alle web-apps weer onder een App Service-abonnement. U kunt de schakel optie plan niveau gebruiken om wijzigings analyse in te scha kelen voor alle web-apps onder een abonnement.

    ![Scherm opname van de gebruikers interface voor het inschakelen van een analyse](./media/change-analysis/change-analysis-on.png)


1. Als u de wijzigings analyse wilt openen, selecteert u **problemen vaststellen en oplossen** > **Beschik baarheid en prestaties** > **toepassing Crashes**. U ziet een grafiek met een overzicht van het type wijzigingen in de loop van de tijd, samen met details over deze wijzigingen. Standaard worden wijzigingen in de afgelopen 24 uur weer gegeven om onmiddellijke problemen op te lossen.

     ![Scherm opname van de weer gave diff wijzigen](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-at-scale"></a>Wijzigings analyse op schaal inschakelen

Als uw abonnement een groot aantal web-apps bevat, kan het inschakelen van de service op het niveau van de web-app inefficiënt zijn. Voer het volgende script uit om alle web-apps in uw abonnement in te scha kelen.

Vereisten:
* Power shell AZ-module. Volg de instructies op [de Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.6.0)

Voer het volgende script uit:

```PowerShell
# Log in to your Azure subscription
Connect-AzAccount

# Get subscription Id
$SubscriptionId = Read-Host -Prompt 'Input your subscription Id'

# Make Feature Flag visible to the subscription
Set-AzContext -SubscriptionId $SubscriptionId

# Register resource provider
Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"


# Enable each web app
$webapp_list = Get-AzWebApp | Where-Object {$_.kind -eq 'app'}
foreach ($webapp in $webapp_list)
{
    $tags = $webapp.Tags
    $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
    Set-AzResource -ResourceId $webapp.Id -Tag $tags -Force
}

```



## <a name="next-steps"></a>Volgende stappen

- Schakel Application Insights in voor [Azure-app Services-apps](azure-web-apps.md).
- Schakel Application Insights in voor [Azure VM-en Azure virtual machine Scale set door IIS gehoste apps](azure-vm-vmss-apps.md).
- Meer informatie over [Azure resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview), waarmee u de analyse van energie wijzigingen kunt aanbrengen.
