---
title: Analyse van toepassingswijzigingen gebruiken in Azure Monitor om problemen met de web-app te vinden | Microsoft Documenten
description: Gebruik Application Change Analysis in Azure Monitor om toepassingsproblemen op te lossen op live sites in Azure App Service.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/07/2019
ms.openlocfilehash: 036b8c084bdfdc11c02274758c550c76bdc7b1e7
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348737"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>Analyse van toepassingswijziging (voorbeeld) gebruiken in Azure Monitor

Wanneer een probleem met een live-site of storing optreedt, is het snel bepalen van de hoofdoorzaak van cruciaal belang. Standaardbewakingsoplossingen kunnen u waarschuwen voor een probleem. Ze kunnen zelfs aangeven welk onderdeel niet werkt. Maar deze waarschuwing zal niet altijd onmiddellijk verklaren de oorzaak van de storing. Je weet dat je site vijf minuten geleden werkte, en nu is het kapot. Wat is er veranderd in de laatste vijf minuten? Dit is de vraag die Application Change Analysis is ontworpen om te beantwoorden in Azure Monitor.

Voortduren op de kracht van [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview)biedt Change Analysis inzicht in wijzigingen in uw Azure-toepassing om de waarneembaarheid te vergroten en MTTR te verminderen (gemiddelde reparatietijd).

> [!IMPORTANT]
> Change Analysis is momenteel in preview. Deze preview-versie wordt geleverd zonder een service-level overeenkomst. Deze versie wordt niet aanbevolen voor productieworkloads. Sommige functies worden mogelijk niet ondersteund of hebben mogelijk beperkte mogelijkheden. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-voorvertoningen voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.

## <a name="overview"></a>Overzicht

Change Analysis detecteert verschillende soorten wijzigingen, van de infrastructuurlaag tot de implementatie van toepassingen. Het is een Azure-bronprovider op abonnementsniveau die resourcewijzigingen in het abonnement controleert. Change Analysis biedt gegevens voor verschillende diagnostische tools om gebruikers te helpen begrijpen welke wijzigingen problemen kunnen hebben veroorzaakt.

Het volgende diagram illustreert de architectuur van Change Analysis:

![Architectuurdiagram van hoe change analysis wijzigingsgegevens krijgt en deze biedt aan clienthulpprogramma's](./media/change-analysis/overview.png)

Momenteel is Change Analysis geïntegreerd in de **diagnose en problemen op te lossen** in de App Service web-app, evenals beschikbaar als een standalone tabblad in Azure portal.
Zie de *sectie Wijzigingen weergeven voor alle resources in Azure* om toegang te krijgen tot het wijzigingsblad voor wijziging en de sectie Analyse wijzigen voor de functie Web *Apps* voor het gebruik ervan in de webapp-portal later in dit artikel.

### <a name="azure-resource-manager-tracked-properties-changes"></a>Wijzigingen in Azure Resource Manager bijgehouden eigenschappen

Met [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview)biedt Change Analysis een historisch overzicht van hoe de Azure-resources die uw toepassing hosten, in de loop van de tijd zijn gewijzigd. Bijgehouden instellingen zoals beheerde identiteiten, platform-os-upgrade en hostnamen kunnen worden gedetecteerd.

### <a name="azure-resource-manager-proxied-setting-changes"></a>Azure Resource Manager proxied-instellingswijzigingen
Instellingen zoals IP-configuratieregel, TLS-instellingen en extensieversies zijn nog niet beschikbaar in ARG, dus beschrijft deze wijzigingen en berekent deze wijzigingen veilig om meer details te geven over wat er in de app is gewijzigd. Deze informatie is nog niet beschikbaar in Azure Resource Graph, maar is binnenkort beschikbaar.

### <a name="changes-in-web-app-deployment-and-configuration-in-guest-changes"></a>Wijzigingen in de implementatie en configuratie van webapps (wijzigingen in de gast)

Change Analysis legt elke 4 uur de implementatie- en configuratiestatus van een toepassing vast. Het kan bijvoorbeeld veranderingen in de variabelen van de toepassingsomgeving detecteren. De tool berekent de verschillen en presenteert wat er is veranderd. In tegenstelling tot wijzigingen in Resource Manager zijn gegevens over codeimplementatiewijzigingen mogelijk niet onmiddellijk beschikbaar in het hulpprogramma. Als u de laatste wijzigingen in Wijzigingsanalyse wilt weergeven, selecteert u **Nu wijzigingen scannen**.

![Schermafbeelding van de knop 'Nu scannen verandert nu'](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Afhankelijkheidswijzigingen

Wijzigingen in resourceafhankelijkheden kunnen ook problemen veroorzaken in een web-app. Als een web-app bijvoorbeeld een Redis-cache aanroept, kan de SKU van de Redis-cache de prestaties van de web-app beïnvloeden. Als u wijzigingen in afhankelijkheden wilt detecteren, controleert Change Analysis de DNS-record van de web-app. Op deze manier identificeert het wijzigingen in alle app-onderdelen die problemen kunnen veroorzaken.
Momenteel worden de volgende afhankelijkheden ondersteund:
- Web-apps
- Azure Storage
- Azure SQL

### <a name="enablement"></a>Activering
De resourceprovider 'Microsoft.ChangeAnalysis' moet worden geregistreerd met een abonnement op de bijgehouden eigenschappen van Azure Resource Manager en proxied settings change data to be available. Als u de diagnose van web-app invoert en problemen oplost of het tabblad Zelfstandige wijzigingsanalyse weerbrengt, wordt deze resourceprovider automatisch geregistreerd. Het heeft geen prestaties en kosten implementaties voor uw abonnement. Wanneer u Wijzigingsanalyse inschakelt voor web-apps (of inschakelt in het hulpprogramma Problemen diagnosticeren en oplossen), heeft dit een verwaarloosbare impact op de prestaties van de web-app en hebben er geen factureringskosten.
Voor wijzigingen in de web-app in de gast is een aparte enablement vereist voor het scannen van codebestanden in een web-app. Zie [Wijzigingsanalyse inschakelen in de](https://docs.microsoft.com/azure/azure-monitor/app/change-analysis#enable-change-analysis-in-the-diagnose-and-solve-problems-tool) sectie Problemen oplossen later in dit artikel voor meer informatie.


## <a name="viewing-changes-for-all-resources-in-azure"></a>Wijzigingen weergeven voor alle bronnen in Azure
In Azure Monitor is er een standalone blade voor Change Analysis om alle wijzigingen weer te geven met inzichten en toepassingsafhankelijkheden resources.

Zoek naar wijzigingsanalyse in de zoekbalk op azure-portal om het blad te starten.

![Schermafbeelding van het zoeken naar wijzigingsanalyse in Azure-portal](./media/change-analysis/search-change-analysis.png)

Selecteer Resourcegroep en resources om wijzigingen weer te geven.

![Schermafbeelding van het mes Wijzigingsanalyse in Azure-portal](./media/change-analysis/change-analysis-standalone-blade.png)

U bronnen voor inzichten en gerelateerde afhankelijkheden zien die uw toepassing hosten. Deze weergave is ontworpen om toepassingsgericht te zijn voor ontwikkelaars om problemen op te lossen.

Momenteel ondersteunde bronnen zijn onder andere:
- Virtuele machines
- Virtuele machineschaalset
- Azure-netwerkbronnen
- Web-app met wijzigingen in het bijhouden van in-guest-bestanden en omgevingsvariabelen

Voor feedback gebruikt u de knop Feedback changeanalysisteam@microsoft.comverzenden in het blad of de e-mail.

![Schermafbeelding van de feedbackknop in het blad Analyse wijzigen](./media/change-analysis/change-analysis-feedback.png)

## <a name="change-analysis-for-the-web-apps-feature"></a>Analyse wijzigen voor de functie Web Apps

In Azure Monitor is Change Analysis ook ingebouwd in de selfservice **Diagnose en het oplossen van problemen.** Toegang tot deze ervaring via de **overzichtspagina** van uw App Service-toepassing.

![Schermafbeelding van de knop 'Overzicht' en de knop 'Problemen diagnosticeren en oplossen'](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>Change Analysis inschakelen in het hulpprogramma Problemen diagnosticeren en oplossen

1. Selecteer **Beschikbaarheid en prestaties**.

    ![Schermafbeelding van de probleemoplossingsopties 'Beschikbaarheid en prestaties'.](./media/change-analysis/availability-and-performance.png)

1. Selecteer **Toepassingswijzigingen**. Niet dat de functie ook beschikbaar is in **Application Crashes.**

   ![Schermafbeelding van de knop 'Toepassing vastloopt'](./media/change-analysis/application-changes.png)

1. Als u Wijzigingsanalyse wilt inschakelen, selecteert u **Nu inschakelen**.

   ![Schermafbeelding van opties 'Toepassingscrashes'](./media/change-analysis/enable-changeanalysis.png)

1. Schakel **Wijzigingsanalyse** in en selecteer **Opslaan**. De tool geeft alle web-apps weer onder een App Service-abonnement. U de schakelaar op planniveau gebruiken om Wijzigingsanalyse in te schakelen voor alle web-apps onder een abonnement.

    ![Schermafbeelding van de gebruikersinterface 'Wijzigingsanalyse inschakelen'](./media/change-analysis/change-analysis-on.png)


1. Als u toegang wilt krijgen tot wijzigingsanalyse, selecteert u **Diagnose en problemen oplossen** > **Beschikbaarheid en Crashen van prestatietoepassingen** > **Application Crashes**. U ziet een grafiek die het type wijzigingen in de loop van de tijd samenvat, samen met details over deze wijzigingen. Standaard worden wijzigingen in de afgelopen 24 uur weergegeven om te helpen bij onmiddellijke problemen.

     ![Schermafbeelding van de weergave Diff wijzigen](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-at-scale"></a>Change Analysis op schaal inschakelen

Als uw abonnement tal van web-apps bevat, is het inefficiënt om de service op het niveau van de web-app in te schakelen. Voer het volgende script uit om alle web-apps in uw abonnement in te schakelen.

Vereisten:
* PowerShell Az Module. Instructies volgen bij [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.6.0)

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

- Toepassingsinzichten inschakelen voor [Azure App Services-apps](azure-web-apps.md).
- Toepassingsinzichten inschakelen voor [Azure VM- en Azure-apps voor virtuele machineschaal instellen IIS-gehoste apps](azure-vm-vmss-apps.md).
- Meer informatie over [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview), waarmee u de analyse van de wijziging wijzigen.
