---
title: Veelgestelde vragen over Azure Monitor voor VM's (GA) | Microsoft Docs
description: Azure Monitor voor VM's is een oplossing in azure met een combi natie van de status-en prestatie bewaking van het Azure VM-besturings systeem, evenals het automatisch detecteren van toepassings onderdelen en afhankelijkheden met andere resources en het toewijzen van de communicatie tussen ermee. In dit artikel vindt u antwoorden op veelgestelde vragen over de GA-versie.
services: azure-monitor
author: mgoedtel
manager: carmonm
editor: ''
ms.service: azure-monitor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/07/2019
ms.author: magoedte
ms.openlocfilehash: cb21d3bed1efc8f6ee7e16a0976ce46d03404983
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72275963"
---
# <a name="azure-monitor-for-vms-generally-available-ga-frequently-asked-questions"></a>Veelgestelde vragen over Azure Monitor voor VM's algemeen beschikbaar (GA)

Onlangs aangekondigd op de [Azure update](https://azure.microsoft.com/blog/) -blog enkele geplande wijzigingen die in oktober en november 2019 zullen plaatsvinden. Meer informatie over deze wijzigingen vindt u in deze veelgestelde vragen over algemene Beschik baarheid.

## <a name="updates-for-azure-monitor-for-vms"></a>Updates voor Azure Monitor voor VM's

In november wordt een nieuwe versie van Azure Monitor voor VM's uitgebracht. Klanten die Azure-monitors voor virtuele machines inschakelen nadat deze release de nieuwe versie automatisch hebben ontvangen, maar bestaande klanten die al gebruikmaken van Azure Monitor voor VM's, wordt gevraagd om bij te werken.  Deze veelgestelde vragen en onze documentatie bieden richt lijnen voor het uitvoeren van een bulk upgrade als u grote implementaties in meerdere werk ruimten hebt.

Bij deze upgrade worden Azure Monitor voor VM's prestatie gegevens sets nu opgeslagen in dezelfde `InsightsMetrics`-tabel als [Azure monitor voor containers](container-insights-overview.md), en kunt u gemakkelijker de twee gegevens sets doorzoeken. U kunt ook meer diverse gegevens sets opslaan die we niet kunnen opslaan in de tabel die we eerder hebben gebruikt.  Onze prestatie weergaven worden ook bijgewerkt om deze nieuwe tabel te gebruiken.

We verplaatsen naar nieuwe gegevens typen voor de gegevens sets van de verbinding. Gegevens die momenteel zijn opgeslagen in `ServiceMapComputer_CL` en `ServiceMapProcess_CL`, die gebruikmaken van aangepaste logboek tabellen, worden verplaatst naar specifieke gegevens typen met de naam `VMComputer` en `VMProcess`.  Door over te stappen op specifieke gegevens typen, kunnen we deze prioriteit voor gegevens opname opgeven en wordt het tabel schema gestandaardiseerd voor alle klanten.

We realiseren dat bestaande klanten die een upgrade uitvoeren, een onderbreking van de werk stroom veroorzaken. Daarom hebben we ervoor gekozen om dit nu uit te voeren in een open bare preview, in plaats van later zodra we in GA zijn.

## <a name="what-will-change"></a>Wat verandert er?

Wanneer u het voorbereidings proces voor Azure Monitor voor VM's voltooit, schakelt u de Servicetoewijzing-oplossing in op de werk ruimte die u hebt geselecteerd om uw bewakings gegevens op te slaan en configureert u vervolgens prestatie meter items voor de gegevens die we verzamelen van uw Vm's. In de komende weken zullen we een nieuwe oplossing met de naam **VMInsights**, die aanvullende mogelijkheden bevat voor het verzamelen van gegevens, samen met een nieuwe locatie voor het opslaan van deze gegevens in log Analytics.

Het huidige proces van het gebruik van prestatie meter items in uw werk ruimte verzendt de gegevens naar de prestatie tabel in Log Analytics.  Deze nieuwe oplossing verzendt de gegevens naar een tabel met de naam `InsightsMetrics`, die ook wordt gebruikt door Azure Monitor voor containers. Met dit tabel schema kunnen we aanvullende metrische gegevens en servicegegevens sets opslaan die niet compatibel zijn met de indeling van de prestatie tabel.

## <a name="what-should-i-do-about-the-performance-counters-on-my-workspace-if-i-install-the-vminsights-solution"></a>Wat moet ik doen met de prestatie meter items in mijn werk ruimte als ik de VMInsights-oplossing Installeer?

Met de huidige methode voor het inschakelen van Azure Monitor voor VM's worden prestatie meter items in uw werk ruimte gebruikt. Met de nieuwe methode worden deze gegevens opgeslagen in een nieuwe tabel met de naam `InsightsMetrics`.

Zodra we onze gebruikers interface hebben bijgewerkt voor het gebruik van de gegevens in InsightsMetrics, zullen we onze documentatie bijwerken en deze aankondiging via meerdere kanalen communiceren, met inbegrip van het weer geven van een banner in de Azure Portal. Op dat moment kunt u ervoor kiezen om deze [prestatie meter items](vminsights-enable-overview.md#performance-counters-enabled) in uw werk ruimte uit te scha kelen als u ze niet meer wilt gebruiken. 

[!NOTE]
>Als u waarschuwings regels hebt die naar deze prestatie meter items in de prestatie tabel verwijzen, moet u deze bijwerken om te verwijzen naar de nieuwe gegevens in de tabel `InsightsMetrics`.  Raadpleeg de documentatie voor voorbeeld logboek query's die u kunt gebruiken om deze tabel te raadplegen.

Als u ervoor kiest om de prestatie meter items ingeschakeld te houden, wordt u gefactureerd voor de gegevens die worden opgenomen en bewaard in de prestatie tabel op basis van [Log Analytics prijzen [(https://azure.microsoft.com/pricing/details/monitor/).

## <a name="how-will-this-change-affect-my-alert-rules"></a>Wat is de invloed van deze wijziging op mijn waarschuwings regels?

Als u [logboek waarschuwingen](../platform/alerts-unified-log.md) hebt gemaakt die query's uitvoeren op de tabel `Perf` die is gericht op de prestatie meter items die zijn ingeschakeld op de werk ruimte, moet u deze regels bijwerken om te verwijzen naar de tabel `InsightsMetrics`. Deze richt lijnen zijn ook van toepassing op alle regels voor logboek zoeken die gebruikmaken van `ServiceMapComputer_CL` en `ServiceMapProcess_CL`, omdat deze gegevens sets worden verplaatst naar `VMComputer`-en `VMProcess`-tabellen.

Deze veelgestelde vragen worden bijgewerkt en onze documentatie bevat voor beelden van waarschuwings regels voor logboek zoeken in de gegevens sets die worden verzameld.

## <a name="will-there-be-any-changes-to-billing"></a>Zijn er wijzigingen in de facturering?

De facturering is nog steeds gebaseerd op gegevens die zijn opgenomen en bewaard in uw Log Analytics-werk ruimte.

De prestatie gegevens voor het computer niveau die we verzamelen, zijn hetzelfde als die van de gegevens die we hebben opgeslagen in de prestatie tabel en kosten ongeveer hetzelfde bedrag.

## <a name="what-if-i-only-want-to-use-service-map"></a>Wat moet ik doen als ik alleen Servicetoewijzing wil gebruiken?

Dat is prima.  U ziet de prompts in de Azure Portal wanneer u Azure Monitor voor VM's over de aanstaande update bekijkt. Zodra de release is uitgebracht, ontvangt u een melding dat u wordt gevraagd om bij te werken naar de nieuwe versie. Als u liever de [Maps](vminsights-maps.md) -functie gebruikt, kunt u ervoor kiezen om niet te upgraden en door te gaan met het gebruik van de Maps-functie Azure monitor voor VM's en de servicetoewijzing-oplossing die toegankelijk is vanuit uw werk ruimte of dashboard tegel.

Als u ervoor hebt gekozen om de prestatie meter items in uw werk ruimte hand matig in te scha kelen, kunt u de gegevens in een aantal van de prestatie diagrammen weer geven vanuit Azure Monitor. Zodra de nieuwe oplossing is uitgebracht, worden de prestatie grafieken bijgewerkt om de gegevens op te vragen die zijn opgeslagen in de tabel `InsightsMetrics`. Als u gegevens uit die tabel in deze grafieken wilt zien, moet u een upgrade uitvoeren naar de nieuwe versie van Azure Monitor voor VM's.

De wijzigingen voor het verplaatsen van gegevens van `ServiceMapComputer_CL` en `ServiceMapProcess_CL` zijn van invloed op zowel Servicetoewijzing als Azure Monitor voor VM's, dus u moet nog steeds plannen voor deze update.

Als u ervoor hebt gekozen om geen upgrade uit te voeren naar de **VMInsights** -oplossing, zullen we oudere versies van onze prestatie werkmappen blijven leveren die verwijzen naar gegevens in de tabel `Perf`.  

## <a name="will-the-service-map-data-sets-also-be-stored-in-insightsmetrics"></a>Worden de Servicetoewijzing gegevens sets ook opgeslagen in InsightsMetrics?

De gegevens sets worden niet gedupliceerd als u beide oplossingen gebruikt. Beide aanbiedingen delen de gegevens sets die worden opgeslagen in `VMComputer` (voorheen ServiceMapComputer_CL), `VMProcess` (voorheen ServiceMapProcess_CL), `VMConnection` en `VMBoundPort` tabellen om de kaart gegevens sets op te slaan die we verzamelen.  

De tabel `InsightsMetrics` wordt gebruikt voor het opslaan van virtuele machines, processen en service gegevens sets die we verzamelen en alleen worden ingevuld als u Azure Monitor voor VM's gebruikt.

## <a name="will-i-be-double-charged-if-i-have-the-service-map-and-vminsights-solutions-on-my-workspace"></a>Worden er dubbele kosten in rekening gebracht als ik de Servicetoewijzing-en VMInsights-oplossingen in mijn werk ruimte heb?

Nee, de twee oplossingen delen de kaart gegevens sets die we opslaan in `VMComputer` (voorheen ServiceMapComputer_CL), `VMProcess` (voorheen ServiceMapProcess_CL), `VMConnection` en `VMBoundPort`.  Als u beide oplossingen in uw werk ruimte hebt, worden deze gegevens niet dubbel in rekening gebracht.

## <a name="if-i-remove-either-the-service-map-or-vminsights-solution-will-it-remove-my-data-in-log-analytics"></a>Als ik de Servicetoewijzing-of VMInsights-oplossing Verwijder, worden mijn gegevens in Log Analytics verwijderd?

Nee, de twee oplossingen delen de kaart gegevens sets die we opslaan in `VMComputer` (voorheen ServiceMapComputer_CL), `VMProcess` (voorheen ServiceMapProcess_CL), `VMConnection` en `VMBoundPort`.  Als u een van de oplossingen verwijdert, ziet u in deze gegevens sets dat er nog steeds een oplossing aanwezig is die gebruikmaakt van de gegevens en blijft deze in Log Analytics.  U moet beide oplossingen uit uw werk ruimte verwijderen, zodat de gegevens uit uw Log Analytics-werk ruimte worden verwijderd.

## <a name="when-will-this-update-be-released"></a>Wanneer wordt deze update vrijgegeven?

We verwachten dat de update voor Azure Monitor voor VM's halverwege november wordt uitgebracht. Als we dichter bij de release datum komen, worden hier updates in de Azure Portal geplaatst wanneer u naar Azure Monitor navigeert.

## <a name="health-feature-to-enter-limited-public-preview"></a>Status functie voor het invoeren van beperkte open bare preview-versie

We hebben een groot aantal fantastische feedback ontvangen van klanten over onze VM-status functie ingesteld.  Er is veel belang aan deze functie en de kans op het ondersteunen van bewakings werk stromen. We zijn van plan een reeks wijzigingen aan te brengen om functionaliteit toe te voegen en de ontvangen feedback te verhelpen. Om de gevolgen van deze wijzigingen aan nieuwe klanten te beperken, verplaatsen we deze functie naar een beperkte open bare preview.

Deze overgang wordt in eerste oktober gestart en moet aan het eind van de maand worden voltooid.

Enkele van de gebieden waarop we zich richten:

- Meer controle over het status model en de drempel waarden
- Ontwerpen op basis van schaal status modellen die van toepassing zijn op een bereik van Vm's
- Systeem eigen gebruik van het waarschuwings platform voor het beheren van waarschuwings regels op basis van status
- De mogelijkheid om de status van een groter bereik te bekijken, zoals een of meer abonnementen
- Beperkte latentie in status overgangen en waarschuwings meldingen

## <a name="how-do-existing-customers-access-the-health-feature"></a>Hoe krijgen bestaande klanten toegang tot de status functie?

Bestaande klanten die gebruikmaken van het Health-onderdeel, hebben nog steeds toegang tot het, maar ze worden niet aangeboden aan nieuwe klanten.  

Als u de functie wilt openen, kunt u de volgende functie vlag `feature.vmhealth=true` toevoegen aan de Portal-URL [https://portal.azure.com](https://portal.azure.com). Voor beeld `https://portal.azure.com/?feature.vmhealth=true`.

U kunt ook deze korte URL gebruiken, waarmee de functie vlag automatisch wordt ingesteld: [https://aka.ms/vmhealthpreview](https://aka.ms/vmhealthpreview).

Als een bestaande klant kunt u de status functie blijven gebruiken op virtuele machines die zijn verbonden met een bestaande installatie van een werk ruimte met de status functionaliteit.  

## <a name="i-use-vm-health-now-with-one-environment-and-would-like-to-deploy-it-for-a-new-environment"></a>Ik gebruik nu VM Health met één omgeving en wil deze implementeren voor een nieuwe omgeving

Als u een bestaande klant bent die gebruikmaakt van de Health-functie en deze wilt gebruiken voor een nieuwe implementatie, kunt u contact met ons opnemen via vminsights@microsoft.com om instructies aan te vragen.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg [Deploy Azure monitor voor VM's](vminsights-enable-overview.md)voor meer informatie over de vereisten en methoden die u helpen bij het bewaken van uw virtuele machines.
