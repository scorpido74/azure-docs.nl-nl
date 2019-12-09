---
title: Veelgestelde vragen over Azure Monitor voor VM's (GA) | Microsoft Docs
description: Azure Monitor voor VM's is een oplossing in azure met een combi natie van de status-en prestatie bewaking van het Azure VM-besturings systeem, evenals het automatisch detecteren van toepassings onderdelen en afhankelijkheden met andere resources en het toewijzen van de communicatie tussen ermee. In dit artikel vindt u antwoorden op veelgestelde vragen over de GA-versie.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 12/05/2019
ms.openlocfilehash: ec0580d3360a56fc6b779b8c0a69eb2a09b007db
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928930"
---
# <a name="azure-monitor-for-vms-generally-available-ga-frequently-asked-questions"></a>Veelgestelde vragen over Azure Monitor voor VM's algemeen beschikbaar (GA)

Deze veelgestelde vragen over algemene Beschik baarheid omvatten wijzigingen die zich voordoen in Azure Monitor voor VM's tijdens de voor bereiding op onze GA-release. 

## <a name="updates-for-azure-monitor-for-vms"></a>Updates voor Azure Monitor voor VM's

Er wordt een nieuwe versie van Azure Monitor voor VM's uitgebracht in januari 2020. Klanten die Azure-monitors voor Vm's inschakelen nadat deze release de nieuwe versie automatisch heeft ontvangen, maar bestaande klanten die al gebruikmaken van Azure Monitor voor VM's, wordt gevraagd om te upgraden. Deze veelgestelde vragen en onze documentatie bieden richt lijnen voor het uitvoeren van een upgrade op schaal als u grote implementaties in meerdere werk ruimten hebt.

Bij deze upgrade worden Azure Monitor voor VM's prestatie gegevens opgeslagen in dezelfde `InsightsMetrics`-tabel als [Azure monitor voor containers](container-insights-overview.md), en kunt u gemakkelijker de twee gegevens sets doorzoeken. U kunt ook meer diverse gegevens sets opslaan die niet kunnen worden opgeslagen in de tabel die eerder is gebruikt. De prestatieweergaven worden ook bijgewerkt, zodat hiervoor deze nieuwe tabel kan worden gebruikt.

We verplaatsen naar nieuwe gegevens typen voor de gegevens sets van de verbinding. Deze wijziging vindt plaats in december 2019 en wordt aangekondigd in een Azure update-blog. Gegevens die momenteel zijn opgeslagen in `ServiceMapComputer_CL` en `ServiceMapProcess_CL`(aangepaste logboek tabellen), worden verplaatst naar specifieke gegevens typen met de naam `VMComputer` en `VMProcess`. Door over te stappen op specifieke gegevens typen, krijgen ze prioriteit voor het opnemen van gegevens en wordt het tabel schema gestandaardiseerd voor alle klanten.

We realiseren dat bestaande klanten die een upgrade uitvoeren, een onderbreking van de werk stroom veroorzaken. Daarom hebben we ervoor gekozen om dit nu uit te voeren in een open bare preview, in plaats van later na GA.

## <a name="what-is-changing"></a>Wat wordt er gewijzigd?

Wanneer u het voorbereidings proces voor Azure Monitor voor VM's voltooit, schakelt u de Servicetoewijzing oplossing in de werk ruimte die u hebt geselecteerd om uw bewakings gegevens op te slaan en configureert u vervolgens de prestatie meters voor de gegevens die we verzamelen van uw Vm's. We zullen een nieuwe oplossing met de naam **VMInsights**, die aanvullende mogelijkheden bevat voor het verzamelen van gegevens, samen met een nieuwe locatie voor het opslaan van deze gegevens in uw log Analytics-werk ruimte.

Met het huidige proces van het gebruik van prestatie meter items in uw Log Analytics-werk ruimte worden de gegevens naar de tabel `Perf` verzonden. Deze nieuwe oplossing verzendt de gegevens naar een tabel met de naam `InsightsMetrics` die ook wordt gebruikt door Azure Monitor voor containers. Met dit tabel schema kunnen we aanvullende metrische gegevens en servicegegevens sets opslaan die niet compatibel zijn met de indeling van de prestatie tabel.

## <a name="what-should-i-do-about-the-performance-counters-in-my-workspace-if-i-install-the-vminsights-solution"></a>Wat moet ik doen met de prestatie meter items in mijn werk ruimte als ik de VMInsights-oplossing Installeer?

Met de huidige methode voor het inschakelen van Azure Monitor voor VM's worden prestatie meter items in uw werk ruimte gebruikt. Met de nieuwe methode worden deze gegevens opgeslagen in een nieuwe tabel met de naam `InsightsMetrics`.

Zodra we onze gebruikers interface hebben bijgewerkt voor het gebruik van de gegevens in de tabel `InsightsMetrics`, zullen we onze documentatie bijwerken en deze aankondiging via meerdere kanalen communiceren, met inbegrip van het weer geven van een banner in de Azure Portal. Op dat moment kunt u ervoor kiezen om deze [prestatie meter items](vminsights-enable-overview.md#performance-counters-enabled) in uw werk ruimte uit te scha kelen als u ze niet meer nodig hebt. 

>[!NOTE]
>Als u waarschuwings regels hebt die naar deze prestatie meter items in de `Perf` tabel verwijzen, moet u deze bijwerken om te verwijzen naar nieuwe gegevens die zijn opgeslagen in de tabel `InsightsMetrics`. Raadpleeg de documentatie voor voorbeeld logboek query's die u kunt gebruiken om deze tabel te raadplegen.
>

Als u ervoor kiest om de prestatie meter items ingeschakeld te laten, wordt u gefactureerd voor de gegevens die zijn opgenomen en opgeslagen in de tabel `Perf` op basis van [Log Analytics prijzen [(https://azure.microsoft.com/pricing/details/monitor/).

## <a name="how-will-this-change-affect-my-alert-rules"></a>Wat is de invloed van deze wijziging op mijn waarschuwings regels?

Als u [logboek waarschuwingen](../platform/alerts-unified-log.md) hebt gemaakt die query's uitvoeren op de `Perf` tabel met doel prestatie meter items die zijn ingeschakeld in de werk ruimte, moet u deze regels bijwerken om te verwijzen naar de `InsightsMetrics`-tabel. Deze richt lijnen zijn ook van toepassing op alle regels voor logboek zoeken die gebruikmaken van `ServiceMapComputer_CL` en `ServiceMapProcess_CL`, omdat deze gegevens sets worden verplaatst naar `VMComputer` en `VMProcess` tabellen.

Deze veelgestelde vragen worden bijgewerkt en onze documentatie bevat voor beelden van waarschuwings regels voor logboek zoeken in de gegevens sets die worden verzameld.

## <a name="how-will-this-affect-my-bill"></a>Wat is van invloed op mijn factuur?

Facturering is nog steeds gebaseerd op gegevens die zijn opgenomen en bewaard in uw Log Analytics-werk ruimte.

De prestatie gegevens voor het computer niveau die we verzamelen, zijn hetzelfde als die van de gegevens die we hebben opgeslagen in de tabel `Perf` en kosten ongeveer hetzelfde bedrag.

## <a name="what-if-i-only-want-to-use-service-map"></a>Wat moet ik doen als ik alleen Servicetoewijzing wil gebruiken?

Dat is prima. U ziet de prompts in de Azure Portal wanneer u Azure Monitor voor VM's over de aanstaande update bekijkt. Zodra de release is uitgebracht, wordt u gevraagd om u bij te werken naar de nieuwe versie. Als u liever alleen de [Maps](vminsights-maps.md) -functie gebruikt, kunt u ervoor kiezen om niet te upgraden en door te gaan met het gebruik van de Maps-functie in azure monitor voor VM's en de servicetoewijzing-oplossing die wordt geopend vanuit uw werk ruimte of dashboard tegel.

Als u ervoor hebt gekozen om de prestatie meter items in uw werk ruimte hand matig in te scha kelen, kunt u de gegevens in een aantal van de prestatie diagrammen weer geven vanuit Azure Monitor. Zodra de nieuwe oplossing is uitgebracht, worden de prestatie grafieken bijgewerkt om de gegevens op te vragen die zijn opgeslagen in de `InsightsMetrics` tabel. Als u gegevens uit die tabel in deze grafieken wilt zien, moet u een upgrade uitvoeren naar de nieuwe versie van Azure Monitor voor VM's.

De wijzigingen voor het verplaatsen van gegevens van `ServiceMapComputer_CL` en `ServiceMapProcess_CL` beïnvloeden zowel Servicetoewijzing als Azure Monitor voor VM's, dus u moet deze update nog steeds plannen.

Als u ervoor hebt gekozen om geen upgrade uit te voeren naar de **VMInsights** -oplossing, zullen we oudere versies van onze prestatie werkmappen blijven leveren die verwijzen naar gegevens in de tabel `Perf`.  

## <a name="will-the-service-map-data-sets-also-be-stored-in-insightsmetrics"></a>Worden de Servicetoewijzing gegevens sets ook opgeslagen in InsightsMetrics?

De gegevens sets worden niet gedupliceerd als u beide oplossingen gebruikt. Beide aanbiedingen delen de gegevens sets die worden opgeslagen in `VMComputer` (voorheen ServiceMapComputer_CL), `VMProcess` (voorheen ServiceMapProcess_CL), `VMConnection`en `VMBoundPort` tabellen om de kaart gegevens sets op te slaan die we verzamelen.  

In de tabel `InsightsMetrics` worden de gegevens sets van de VM, het proces en de service opgeslagen die we verzamelen en alleen worden ingevuld als u Azure Monitor voor VM's en de VM Insights-oplossing gebruikt. Met de Servicetoewijzing oplossing worden geen gegevens verzameld of opgeslagen in de `InsightsMetrics` tabel.

## <a name="will-i-be-double-charged-if-i-have-the-service-map-and-vminsights-solutions-in-my-workspace"></a>Worden er dubbele kosten in rekening gebracht als ik de Servicetoewijzing-en VMInsights-oplossingen in mijn werk ruimte heb?

Nee, de twee oplossingen delen de kaart gegevens sets die we opslaan in `VMComputer` (voorheen ServiceMapComputer_CL), `VMProcess` (voorheen ServiceMapProcess_CL), `VMConnection`en `VMBoundPort`. Als u beide oplossingen in uw werk ruimte hebt, worden deze gegevens niet dubbel in rekening gebracht.

## <a name="if-i-remove-either-the-service-map-or-vminsights-solution-will-it-remove-my-data"></a>Als ik de Servicetoewijzing-of VMInsights-oplossing Verwijder, worden mijn gegevens verwijderd?

Nee, de twee oplossingen delen de kaart gegevens sets die we opslaan in `VMComputer` (voorheen ServiceMapComputer_CL), `VMProcess` (voorheen ServiceMapProcess_CL), `VMConnection`en `VMBoundPort`. Als u een van de oplossingen verwijdert, ziet u in deze gegevens sets dat er nog steeds een oplossing aanwezig is die gebruikmaakt van de gegevens en deze blijft in de werk ruimte Log Analytics. U moet beide oplossingen uit uw werk ruimte verwijderen om ervoor te zorgen dat de gegevens erin worden verwijderd.

## <a name="when-will-this-update-be-released"></a>Wanneer wordt deze update vrijgegeven?

We verwachten dat de update voor Azure Monitor voor VM's in eerste januari 2020 wordt uitgebracht. Zodra de release datum in januari wordt ontvangen, worden hier updates in de Azure Portal geplaatst wanneer u Azure Monitor opent.

## <a name="health-feature-is-in-limited-public-preview"></a>Health-functie heeft een beperkte open bare preview

We hebben een groot aantal fantastische feedback ontvangen van klanten over onze VM-status functie ingesteld. Er is zeer veel interesse in deze functie, met name vanwege de mogelijkheid om bewakingswerkstromen te ondersteunen. We zijn van plan een reeks wijzigingen door te voeren om functionaliteit toe te voegen en hierin de ontvangen feedback te verwerken. 

Om de gevolgen van deze wijzigingen aan nieuwe klanten te beperken, hebben we deze functie verplaatst naar een **beperkte open bare preview**. Deze update is opgetreden in oktober 2019.

We zijn van plan deze status functie opnieuw te starten in 2020, nadat Azure Monitor voor VM's in GA is.

## <a name="how-do-existing-customers-access-the-health-feature"></a>Hoe krijgen bestaande klanten toegang tot de status functie?

Bestaande klanten die gebruikmaken van het Health-onderdeel, hebben nog steeds toegang tot het, maar ze worden niet aangeboden aan nieuwe klanten.  

Als u de functie wilt openen, kunt u de volgende functie vlag `feature.vmhealth=true` toevoegen aan de Azure Portal-URL [https://portal.azure.com](https://portal.azure.com). Voor beeld `https://portal.azure.com/?feature.vmhealth=true`.

U kunt ook deze korte URL gebruiken, waarmee de functie vlag automatisch wordt ingesteld: [https://aka.ms/vmhealthpreview](https://aka.ms/vmhealthpreview).

Als een bestaande klant kunt u de status functie blijven gebruiken op virtuele machines die zijn verbonden met een bestaande installatie van een werk ruimte met de status functionaliteit.  

## <a name="i-use-vm-health-now-with-one-environment-and-would-like-to-deploy-it-to-a-new-one"></a>Ik gebruik nu VM-status met één omgeving en wil deze implementeren in een nieuw abonnement

Als u een bestaande klant bent die gebruikmaakt van de Health-functie en deze wilt gebruiken voor een nieuwe implementatie, neemt u contact met ons op vminsights@microsoft.com om instructies aan te vragen.

## <a name="next-steps"></a>Volgende stappen

Bekijk voor meer informatie over de vereisten en de methoden die u helpen bij uw virtuele machines controleren, [implementeert Azure Monitor voor virtuele machines](vminsights-enable-overview.md).
