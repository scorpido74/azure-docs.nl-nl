---
title: Veelgestelde azure monitor voor VM's (GA) | Microsoft Documenten
description: Azure Monitor voor VM's is een oplossing in Azure die status- en prestatiebewaking van het Azure VM-besturingssysteem combineert, evenals het automatisch ontdekken van toepassingsonderdelen en afhankelijkheden met andere bronnen en de communicatie tussen Hen. Dit artikel beantwoordt veelgestelde vragen over de GA-release.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/31/2020
ms.openlocfilehash: 3d250ef1aba979be04a44acaf31a3d685f162e37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283885"
---
# <a name="azure-monitor-for-vms-generally-available-ga-frequently-asked-questions"></a>Veelgestelde vragen over vraag naar veel gestelde Azure-monitor voor VM's
Deze algemene beschikbaarheid faq heeft betrekking op wijzigingen die zijn aangebracht in Q4 2019 en Q1 2020 zoals we voorbereid voor GA.

## <a name="updates-for-azure-monitor-for-vms"></a>Updates voor Azure Monitor voor VM's
We hebben in januari 2020 een nieuwe versie van Azure Monitor voor VM's uitgebracht voorafgaand aan onze GA-aankondiging. Klanten die Azure Monitor voor VM's inschakelen, ontvangen nu de GA-versie, maar bestaande klanten die de versie van Azure Monitor voor VM's van Q4 2019 en eerder gebruiken, worden gevraagd om te upgraden. Deze veelgestelde vragen biedt richtlijnen om een upgrade op schaal uit te voeren als u grote implementaties in meerdere werkruimten hebt.


Met deze upgrade worden prestatiegegevens van Azure Monitor voor VM's opgeslagen in dezelfde *InsightsMetrics-tabel* als [Azure Monitor voor containers](container-insights-overview.md), waardoor u de twee gegevenssets gemakkelijker opvragen. Ook u meer diverse gegevenssets opslaan die we niet konden opslaan in de eerder gebruikte tabel. 

Onze prestatieweergaven gebruiken nu de gegevens die we opslaan in de tabel *InsightsMetrics.*  Als u nog geen upgrade hebt uitgevoerd om de nieuwste VMInsights-oplossing op uw werkruimte te gebruiken, worden er geen gegevens meer weergegeven in uw grafieken.  U upgraden via onze **pagina Aan de slag,** zoals hieronder beschreven.


## <a name="what-is-changing"></a>Wat verandert er?
We hebben een nieuwe oplossing uitgebracht, vminsights genaamd, met extra mogelijkheden voor het verzamelen van gegevens, samen met een nieuwe locatie voor het opslaan van deze gegevens in uw Log Analytics-werkruimte. 

In het verleden hebben we de ServiceMap-oplossing op uw werkruimte en prestatiemeteritems in uw Log Analytics-werkruimte ingeschakeld om de gegevens naar de *perf-tabel* te verzenden. Deze nieuwe oplossing stuurt de gegevens naar een tabel met de naam *InsightsMetrics* die ook door Azure Monitor voor containers wordt gebruikt. Met dit tabelschema kunnen we aanvullende statistieken en servicegegevenssets opslaan die niet compatibel zijn met de *perf-tabelindeling.*

We hebben onze prestatiegrafieken bijgewerkt om de gegevens te gebruiken die we opslaan in de tabel *InsightsMetrics.* U upgraden naar de tabel *InsightsMetrics* op onze pagina **Aan de slag,** zoals hieronder beschreven.


## <a name="how-do-i-upgrade"></a>Hoe kan ik upgraden?
Wanneer een Log Analytics-werkruimte wordt geüpgraded naar de nieuwste versie van Azure Monitor naar VM's, wordt de afhankelijkheidsagent op elk van de VM's die aan die werkruimte zijn gekoppeld, geüpgraded. Elke vm die een upgrade nodig heeft, wordt geïdentificeerd op het tabblad **Aan de slag** in Azure Monitor voor VM's in de Azure-portal. Wanneer u ervoor kiest een virtuele machine te upgraden, wordt de werkruimte voor die vm geüpuwd, samen met alle andere VM's die aan die werkruimte zijn gekoppeld. U één vm of meerdere VM's, resourcegroepen of abonnementen selecteren. 

Gebruik de volgende opdracht om een werkruimte te upgraden met PowerShell:

```PowerShell
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName <resource-group-name> -WorkspaceName <workspace-name> -IntelligencePackName "VMInsights" -Enabled $True
```

## <a name="what-should-i-do-about-the-performance-counters-in-my-workspace-if-i-install-the-vminsights-solution"></a>Wat moet ik doen met de prestatiemeteritems in mijn werkruimte als ik de VMInsights-oplossing installeer?

De vorige methode voor het inschakelen van Azure Monitor voor VM's gebruikte prestatiemeteritems in uw werkruimte. De huidige versie slaat deze `InsightsMetrics`gegevens op in een tabel met de naam . U ervoor kiezen om deze prestatiemeteritems in uw werkruimte uit te schakelen als u ze niet meer hoeft te gebruiken. 

>[!NOTE]
>Als u waarschuwingsregels hebt die `Perf` verwijzen naar deze tellers in de tabel, `InsightsMetrics` moet u deze bijwerken om te verwijzen naar nieuwe gegevens die in de tabel zijn opgeslagen. Raadpleeg bijvoorbeeld onze documentatie logquery's die u gebruiken die verwijzen naar deze tabel.
>

Als u besluit de prestatiemeteritems ingeschakeld te houden, wordt u gefactureerd `Perf` voor de ingenomen gegevenshttps://azure.microsoft.com/pricing/details/monitor/)en opgeslagen in de tabel op basis van [Log Analytics-prijzen[.

## <a name="how-will-this-change-affect-my-alert-rules"></a>Welke invloed heeft deze wijziging op mijn waarschuwingsregels?

Als u [Logboekwaarschuwingen](../platform/alerts-unified-log.md) hebt `Perf` gemaakt waarin de prestatiemeteritems voor de tabel worden geopperd die in de werkruimte zijn ingeschakeld, moet u deze regels bijwerken om in plaats daarvan naar de `InsightsMetrics` tabel te verwijzen. Deze richtlijnen zijn ook van `ServiceMapComputer_CL` toepassing `ServiceMapProcess_CL`op alle logzoekregels `VMComputer` `VMProcess` die worden gebruikt en omdat deze gegevenssets worden verplaatst naar en tabellen.

We zullen deze faq en onze documentatie bijwerken met voorbeeld regels voor het zoeken naar logboek voor de gegevenssets die we verzamelen.

## <a name="how-will-this-affect-my-bill"></a>Welke invloed heeft dit op mijn factuur?

Facturering is nog steeds gebaseerd op gegevens die zijn ingenomen en bewaard in uw Log Analytics-werkruimte.

De prestatiegegevens op machineniveau die we verzamelen, zijn dezelfde, zijn `Perf` even groot als de gegevens die we in de tabel hebben opgeslagen en kosten ongeveer hetzelfde bedrag.

## <a name="what-if-i-only-want-to-use-service-map"></a>Wat moet ik doen als ik alleen Servicemap wil gebruiken?

Dat is prima. U ziet aanwijzingen in de Azure-portal wanneer u Azure Monitor voor VM's bekijkt over de komende update. Zodra u is vrijgegeven, ontvangt u een prompt met het verzoek om de nieuwe versie bij te werken. Als u liever alleen de functie [Kaarten](vminsights-maps.md) gebruikt, u ervoor kiezen om de functie Kaarten in Azure Monitor voor VM's en de servicekaartoplossing die vanuit uw werkruimte of dashboardtegel wordt geopend, niet te upgraden en te blijven gebruiken.

Als u ervoor hebt gekozen om de prestatiemeteritems in uw werkruimte handmatig in te schakelen, u mogelijk gegevens zien in sommige van onze prestatiegrafieken die worden weergegeven vanuit Azure Monitor. Zodra de nieuwe oplossing is vrijgegeven, werken we onze `InsightsMetrics` prestatiegrafieken bij om de gegevens op te vragen die in de tabel zijn opgeslagen. Als u gegevens uit die tabel in deze grafieken wilt zien, moet u upgraden naar de nieuwe versie van Azure Monitor voor VM's.

De wijzigingen om `ServiceMapComputer_CL` gegevens `ServiceMapProcess_CL` van te verplaatsen en hebben invloed op zowel Service Map als Azure Monitor voor VM's, dus u moet nog steeds plannen voor deze update.

Als u ervoor kiest om niet te upgraden naar de **VMInsights-oplossing,** blijven we `Perf` verouderde versies van onze prestatiewerkmappen leveren die verwijzen naar gegevens in de tabel.  

## <a name="will-the-service-map-data-sets-also-be-stored-in-insightsmetrics"></a>Worden de gegevenssets van de servicekaart ook opgeslagen in InsightsMetrics?

De gegevenssets worden niet gedupliceerd als u beide oplossingen gebruikt. Beide aanbiedingen delen de gegevenssets `VMComputer` die worden opgeslagen `VMProcess` in (voorheen `VMConnection`ServiceMapComputer_CL), (voorheen ServiceMapProcess_CL) en `VMBoundPort` tabellen om de kaartgegevenssets op te slaan die we verzamelen.  

In `InsightsMetrics` de tabel worden VM-, proces- en servicegegevenssets opgeslagen die we verzamelen en worden ze alleen ingevuld als u Azure Monitor voor VM's en de VM Insights-oplossing gebruikt. De servicekaartoplossing verzamelt of slaat geen `InsightsMetrics` gegevens op in de tabel.

## <a name="will-i-be-double-charged-if-i-have-the-service-map-and-vminsights-solutions-in-my-workspace"></a>Krijg ik dubbel kosten in rekening als ik de servicekaart- en VMInsights-oplossingen in mijn werkruimte heb?

Nee, de twee oplossingen delen de kaartgegevenssets waarin `VMComputer` `VMProcess` we opslaan (voorheen ServiceMapComputer_CL), (voorheen ServiceMapProcess_CL), `VMConnection`en `VMBoundPort`. Voor deze gegevens worden geen dubbele kosten in rekening gebracht als u beide oplossingen in uw werkruimte hebt.

## <a name="if-i-remove-either-the-service-map-or-vminsights-solution-will-it-remove-my-data"></a>Als ik de ServiceMap of vmInsights-oplossing verwijder, worden mijn gegevens dan verwijderd?

Nee, de twee oplossingen delen de kaartgegevenssets waarin `VMComputer` `VMProcess` we opslaan (voorheen ServiceMapComputer_CL), (voorheen ServiceMapProcess_CL), `VMConnection`en `VMBoundPort`. Als u een van de oplossingen verwijdert, wordt in deze gegevenssets opgemerkt dat er nog steeds een oplossing is die de gegevens gebruikt en deze in de werkruimte Log Analytics blijft. U moet beide oplossingen uit uw werkruimte verwijderen om de gegevens eruit te kunnen verwijderen.

## <a name="health-feature-is-in-limited-public-preview"></a>Gezondheidsfunctie is in beperkte openbare preview

We hebben veel goede feedback ontvangen van klanten over onze VM Health-functieset. Er is veel belangstelling rond deze functie en opwinding over het potentieel voor het ondersteunen van monitoring workflows. We zijn van plan om een reeks wijzigingen aan te brengen om functionaliteit toe te voegen en de feedback die we hebben ontvangen aan te pakken. 

Om de impact van deze wijzigingen op nieuwe klanten te minimaliseren, hebben we deze functie verplaatst naar een **beperkte openbare preview.** Deze update vond plaats in oktober 2019.

We zijn van plan om deze statusfunctie opnieuw te starten in 2020, nadat Azure Monitor for VM's in GA is.

## <a name="how-do-existing-customers-access-the-health-feature"></a>Hoe krijgen bestaande klanten toegang tot de functie Gezondheid?

Bestaande klanten die de statusfunctie gebruiken, blijven toegang tot de functie hebben, maar worden niet aangeboden aan nieuwe klanten.  

Als u toegang wilt krijgen tot `feature.vmhealth=true` de functie, [https://portal.azure.com](https://portal.azure.com)kunt u de volgende functievlag toevoegen aan de URL van de Azure-portal. Voorbeeld `https://portal.azure.com/?feature.vmhealth=true`.

U deze korte url ook gebruiken, die [https://aka.ms/vmhealthpreview](https://aka.ms/vmhealthpreview)de functievlag automatisch instelt:.

Als bestaande klant u de functie Status blijven gebruiken op VM's die zijn verbonden met een bestaande werkruimte-instelling met de statusfunctionaliteit.  

## <a name="i-use-vm-health-now-with-one-environment-and-would-like-to-deploy-it-to-a-new-one"></a>Ik gebruik VM Health nu met één omgeving en wil deze graag implementeren in een nieuwe omgeving

Als u een bestaande klant bent die de functie Gezondheid gebruikt en deze wilt vminsights@microsoft.com gebruiken voor een nieuwe uitrol, neem dan contact met ons op om instructies te vragen.

## <a name="next-steps"></a>Volgende stappen

Als u inzicht wilt krijgen in de vereisten en methoden waarmee u uw virtuele machines controleren, controleert u [Azure-monitor voor VM's implementeren.](vminsights-enable-overview.md)
