---
title: Inzichten in Azure Monitor Resource Group | Microsoft Documenten
description: Inzicht in de status en prestaties van uw gedistribueerde toepassingen en services op het niveau van de resourcegroep met Azure Monitor
ms.subservice: ''
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 09/19/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 6d97e40bf2bf2298fb53609621db8ff2c6f1038f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663535"
---
# <a name="monitor-resource-groups-with-azure-monitor-preview"></a>Brongroepen controleren met Azure Monitor (voorbeeld)

Moderne toepassingen zijn vaak complex en sterk verdeeld met veel discrete onderdelen die samenwerken om een service te leveren. Azure Monitor herkent deze complexiteit en biedt monitoringinzichten voor resourcegroepen. Dit maakt het gemakkelijk om triage en diagnose van eventuele problemen uw individuele middelen&mdash;tegenkomen,&mdash;terwijl het aanbieden van context met betrekking tot de gezondheid en prestaties van de resource groep en uw toepassing als geheel.

## <a name="access-insights-for-resource-groups"></a>Toegang tot inzichten voor resourcegroepen

1. Selecteer **Resourcegroepen** op de navigatiebalk aan de linkerkant.
2. Kies een van uw resourcegroepen die u wilt verkennen. (Als u een groot aantal brongroepen hebt die filteren op abonnement, kan dat soms nuttig zijn.)
3. Als u toegang wilt krijgen tot inzichten voor een resourcegroep, klikt u op **Inzicht** in het menu aan de linkerkant van een resourcegroep.

![Schermafbeelding van de overzichtspagina resourcegroepinzichten](./media/resource-group-insights/0001-overview.png)

## <a name="resources-with-active-alerts-and-health-issues"></a>Bronnen met actieve waarschuwingen en gezondheidsproblemen

De overzichtspagina geeft aan hoeveel waarschuwingen zijn gestart en nog steeds actief zijn, samen met de huidige Azure Resource Health van elke resource. Samen kunnen deze informatie u helpen snel alle bronnen te herkennen die problemen ondervinden. Waarschuwingen helpen u problemen in uw code en hoe u uw infrastructuur hebt geconfigureerd, op te sporen. Azure Resource Health maakt problemen met het Azure-platform zelf, die niet specifiek zijn voor uw afzonderlijke toepassingen.

![Schermafbeelding van het deelvenster Azure Resource Health](./media/resource-group-insights/0002-overview.png)

### <a name="azure-resource-health"></a>Azure Resource Health

Als u Azure Resource Health wilt weergeven, schakelt u het selectievakje Azure Resource Health boven de tabel **weergeven** in. Deze kolom is standaard verborgen om de pagina snel te kunnen laden.

![Schermafbeelding met resourcestatusgrafiek toegevoegd](./media/resource-group-insights/0003-overview.png)

Standaard worden de resources gegroepeerd op app-laag en resourcetype. **App-laag** is een eenvoudige categorisering van resourcetypen, die alleen bestaat binnen de context van de overzichtspagina resourcegroepinzichten. Er zijn resourcetypen met betrekking tot toepassingscode, compute-infrastructuur, netwerken, opslag + databases. Beheertools krijgen hun eigen app-lagen en elke andere resource is gecategoriseerd als behorend tot de **andere** app-laag. Deze groepering kan u helpen in één oogopslag te zien welke subsystemen van uw toepassing gezond en ongezond zijn.

## <a name="diagnose-issues-in-your-resource-group"></a>Problemen in uw resourcegroep diagnosticeren

De pagina inzichten in resourcegroepen biedt verschillende andere tools die worden gebruikt om problemen te diagnosticeren

   |         |          |
   | ---------------- |:-----|
   | [**Waarschuwingen**](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)      |  Uw waarschuwingen weergeven, maken en beheren. |
   | [**Statistieken**](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) | Visualiseer en verken uw metrische gegevens.    |
   | [**Activiteitenlogboeken**](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) | Gebeurtenissen op abonnementsniveau die in Azure zijn opgetreden.  |
   | [**Toepassingskaart**](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) | Navigeer door de topologie van uw gedistribueerde toepassing om prestatieknelpunten of fouthotspots te identificeren. |

## <a name="failures-and-performance"></a>Storingen en prestaties

Wat als u hebt gemerkt dat uw toepassing langzaam wordt uitgevoerd of gebruikers fouten hebben gemeld? Het is tijdrovend om te zoeken door al uw middelen om problemen te isoleren.

De tabbladen **Prestaties** en **fouten** vereenvoudigen dit proces door diagnostische weergaven voor prestaties en fouten samen te brengen voor veel voorkomende resourcetypen.

De meeste resourcetypen openen een galerie met Azure Monitor-werkmapsjablonen. Elke werkmap die u maakt, kan worden aangepast, opgeslagen, gedeeld met uw team en in de toekomst opnieuw worden gebruikt om vergelijkbare problemen te diagnosticeren.

### <a name="investigate-failures"></a>Fouten onderzoeken

Als u het tabblad Fouten wilt testen, selecteert u **Fouten** onder **Onderzoeken** in het linkermenu.

De menubalk aan de linkerkant verandert nadat uw selectie is gemaakt, waardoor u nieuwe opties krijgt.

![Schermafbeelding van het overzichtsvenster Voor fouten](./media/resource-group-insights/00004-failures.png)

Wanneer App Service is gekozen, krijgt u een galerij met Azure Monitor Werkmapsjablonen te zien.

![Schermafbeelding van de galerie met toepassingswerkmap](./media/resource-group-insights/0005-failure-insights-workbook.png)

Als u de sjabloon voor Foutinzichten kiest, wordt de werkmap geopend.

![Schermafbeelding van het rapport voor een fout](./media/resource-group-insights/0006-failure-visual.png)

U een van de rijen selecteren. De selectie wordt vervolgens weergegeven in een grafische detailweergave.

![Schermafbeelding van foutgegevens](./media/resource-group-insights/0007-failure-details.png)

Werkmappen abstraheren het moeilijke werk van het maken van aangepaste rapporten en visualisaties in een gemakkelijk verbruikbare indeling. Hoewel sommige gebruikers misschien alleen de vooraf gebouwde parameters willen aanpassen, zijn werkmappen volledig aanpasbaar.

Als u een idee wilt krijgen van hoe deze werkmap intern functioneert, selecteert u **Bewerken** in de bovenste balk.

![Schermafbeelding van de optie Extra bewerken](./media/resource-group-insights/0008-failure-edit.png)

Er worden een aantal **bewerkingsvakken** weergegeven in de buurt van de verschillende elementen van de werkmap. Selecteer het vak **Bewerken** onder de tabel met bewerkingen.

![Schermafbeelding van bewerkingsvakken](./media/resource-group-insights/0009-failure-edit-graph.png)

Dit onthult de onderliggende logboekquery die de tabelvisualisatie aandrijft.

 ![Schermafbeelding van het venster logboekquery](./media/resource-group-insights/0010-failure-edit-query.png)

U de query rechtstreeks wijzigen. Of u het gebruiken als referentie en lenen van het bij het ontwerpen van uw eigen aangepaste parameterwerkmap.

### <a name="investigate-performance"></a>Prestaties onderzoeken

Performance biedt een eigen werkgalerij. Voor App Service biedt de vooraf gebouwde werkmap Voor toepassingsprestaties de volgende weergave:

 ![Schermafbeelding van de prestatieweergave](./media/resource-group-insights/0011-performance.png)

Als u bewerken selecteert, ziet u in dit geval dat deze set visualisaties wordt aangedreven door Azure Monitor Metrics.

 ![Schermafbeelding van de prestatieweergave met Azure-statistieken](./media/resource-group-insights/0012-performance-metrics.png)

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="enabling-access-to-alerts"></a>Toegang tot waarschuwingen inschakelen

Als u waarschuwingen wilt zien in Azure Monitor voor Resourcegroepen, moet iemand met een rol eigenaar of inzender voor dit abonnement Azure Monitor voor resourcegroepen openen voor elke brongroep in het abonnement. Hiermee kunnen iedereen met leestoegang waarschuwingen zien in Azure Monitor for Resource Groups voor alle brongroepen in het abonnement. Als u een rol eigenaar of inzender hebt, vernieuwt u deze pagina binnen enkele minuten.

Azure Monitor voor resourcegroepen is afhankelijk van het Azure Monitor Alerts Management-systeem om de waarschuwingsstatus op te halen. Waarschuwingsbeheer is niet standaard geconfigureerd voor elke resourcegroep en -abonnement en kan alleen worden ingeschakeld door iemand met een rol eigenaar of inzender. Het kan worden ingeschakeld door:
* Azure Monitor voor resourcegroepen openen voor elke resourcegroep in het abonnement.
* Of door naar het abonnement te gaan, op **Resourceproviders**te klikken en vervolgens op **Registreren voor Waarschuwingen.Management**te klikken.

## <a name="next-steps"></a>Volgende stappen

- [Azure Monitor-werkmappen](https://docs.microsoft.com/azure/application-insights/app-insights-usage-workbooks)
- [Azure Resource Health](https://docs.microsoft.com/azure/service-health/resource-health-overview)
- [Azure-monitorwaarschuwingen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)
