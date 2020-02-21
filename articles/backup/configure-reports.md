---
title: Azure Backup-rapporten configureren
description: Rapporten voor Azure Backup configureren en weer geven met behulp van Log Analytics en Azure-werkmappen
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 5c43efc8d61d7aa6c8fc94c6c39294d744cd6b87
ms.sourcegitcommit: 0a9419aeba64170c302f7201acdd513bb4b346c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77501119"
---
# <a name="configure-azure-backup-reports"></a>Azure Backup-rapporten configureren

Een veelvoorkomende vereiste voor back-upbeheerders is het verkrijgen van inzichten op back-ups, op basis van gegevens die een lange periode lang zijn. Er kunnen meerdere use cases zijn voor een dergelijke oplossing: toewijzing en prognose van het verbruik van Cloud opslag, het controleren van back-ups en herstel bewerkingen en het identificeren van de belangrijkste trends op verschillende niveaus.

Vandaag biedt Azure Backup een rapportage oplossing die gebruikmaakt van [Azure monitor-logboeken](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) en [Azure-werkmappen](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks), zodat u uw back-ups op uw hele back-up kunt maken. In dit artikel wordt uitgelegd hoe u back-uprapporten kunt configureren en weer geven.

## <a name="supported-scenarios"></a>Ondersteunde scenario's

* Back-uprapporten worden ondersteund voor virtuele Azure-machines, SQL in azure vm's, SAP HANA-ASE in azure Vm's, Azure Backup Agent (MARS), Azure Backup Server (MABS) en System Center DPM.
* Voor DPM-workloads worden back-uprapporten ondersteund voor de DPM-versie 5.1.363.0 en hoger en de agent versie 2.0.9127.0 en hoger.
* Voor MABS-workloads worden back-uprapporten ondersteund voor de MABS-versie 13.0.415.0 en hoger en de agent versie 2.0.9170.0 en hoger.
* Back-uprapporten kunnen worden weer gegeven in alle back-upitems, kluizen, abonnementen en regio's, zolang hun gegevens worden verzonden naar een Log Analytics (LA)-werk ruimte waartoe de gebruiker toegang heeft. 
* Als u een [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/) -gebruiker bent met gedelegeerde toegang tot de abonnementen van uw klanten, kunt u deze rapporten gebruiken met Azure Lighthouse om rapporten weer te geven over al uw tenants.
* Gegevens voor logboek back-uptaken worden momenteel niet weer gegeven in de rapporten.

## <a name="getting-started"></a>Aan de slag

Volg de drie stappen die hieronder worden beschreven om aan de slag te gaan met het gebruik van de rapporten:

1. **Een Log Analytics (LA)-werk ruimte maken (of een bestaande gebruiken):**

U moet een of meer LA-werk ruimten instellen om de gegevens van uw back-uprapport op te slaan. De locatie en het abonnement waar deze LA-werk ruimte kan worden gemaakt, zijn onafhankelijk van de locatie en het abonnement waar uw kluizen zich bevinden. 

Raadpleeg het volgende artikel: [een log Analytics-werk ruimte maken in de Azure Portal](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) om een werk ruimte van La in te stellen.

De gegevens in een werk ruimte van LA worden standaard 30 dagen bewaard. Als u gegevens wilt weer geven voor een langere periode, wijzigt u de Bewaar periode van de werk ruimte LA. Als u de retentie periode wilt wijzigen, raadpleegt u het volgende artikel: [gebruik en kosten beheren met Azure monitor-logboeken](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage).

2. **Diagnostische instellingen voor uw kluizen configureren:**

Azure Resource Manager resources, zoals Recovery Services kluizen, registreert u informatie over geplande bewerkingen en door de gebruiker geactiveerde bewerkingen als diagnostische gegevens. 

Selecteer in de sectie bewaking van uw Recovery Services kluis **Diagnostische instellingen** en geef het doel op voor de diagnostische gegevens van de Recovery Services kluis. Meer [informatie over het gebruik van diagnostische gebeurtenissen](https://aka.ms/AzureBackupDiagnosticDocs).

![Blade instellingen voor diagnostische gegevens](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

Azure Backup biedt ook een ingebouwde Azure Policy, waarmee de configuratie van diagnostische instellingen voor alle kluizen in een bepaald bereik wordt geautomatiseerd. Raadpleeg het volgende artikel voor meer informatie over het gebruik van dit beleid: [instellingen voor diagnostische gegevens van de kluis op schaal configureren](https://aka.ms/AzureBackupDiagnosticsPolicyDocs)

3. **Rapporten weer geven op de Azure Portal:**

Wanneer u uw kluizen zo hebt geconfigureerd dat gegevens naar LA worden verzonden, kunt u de back-uprapporten bekijken door te navigeren naar de Blade van de kluis en te klikken op het menu **-item back-uprapporten** . 

![Kluis dashboard](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

Als u op deze koppeling klikt, wordt de werkmap van het back-uprapport geopend. 

> [!NOTE]
> De aanvankelijke belasting van het rapport kan tot wel 1 minuut duren.

Hieronder ziet u een beschrijving van de verschillende tabbladen die het rapport bevat:

* **Samen vatting** : op het tabblad samen vatting vindt u een overzicht van uw back-ups op hoog niveau. Op het tabblad samen vatting ziet u een kort overzicht van het totale aantal back-upitems, het totale verbruik van de Cloud opslag, het aantal beveiligde instanties en het succes percentage van de taak per type werk belasting. Voor meer informatie over een specifiek type back-upartefact gaat u naar de desbetreffende tabbladen.

![Tabblad samen vatting](./media/backup-azure-configure-backup-reports/summary.png)

* **Back-** upitems: op het tabblad Back-upitems kunt u informatie en trends bekijken over Cloud opslag die wordt verbruikt op het niveau van een back-upartikel. Als u bijvoorbeeld SQL in azure VM Backup gebruikt, ziet u dat de gebruikte Cloud opslag voor elke SQL database een back-up wordt gemaakt. U kunt er ook voor kiezen om gegevens te bekijken voor back-upitems van een bepaalde beveiligings status. Als u bijvoorbeeld op de tegel **beveiliging gestopt** klikt boven aan het tabblad, worden alle onderstaande widgets gefilterd om alleen gegevens weer te geven voor back-upitems met de status gestopt voor beveiliging.

![Tabblad Back-upitems](./media/backup-azure-configure-backup-reports/backup-items.png)

* **Gebruik** : het tabblad gebruik helpt u bij het weer geven van de para meters voor de sleutel facturering voor uw back-ups. De informatie die op dit tabblad wordt weer gegeven, bevindt zich op het niveau van een facturerings entiteit (beveiligde container). Als er bijvoorbeeld een back-up van een DPM-server wordt gemaakt naar Azure, kunt u de trend van beveiligde exemplaren en Cloud opslag die is verbruikt voor de DPM-server weer geven. Op dezelfde manier kunt u, als u SQL gebruikt in Azure Backup of SAP HANA in Azure Backup, op dit tabblad informatie over het gebruik weer geven op het niveau van de virtuele machine waarop deze data bases zijn opgenomen.

![Tabblad gebruik](./media/backup-azure-configure-backup-reports/usage.png)

* **Taken** : met het tabblad taken kunt u langlopende trends op taken weer geven, zoals het aantal mislukte taken per dag en de belangrijkste oorzaken van een mislukte taak. U kunt deze informatie op zowel een totaal niveau als een niveau van een back-upitem weer geven. Als u op een bepaald back-upitem in een raster klikt, kunt u gedetailleerde informatie over elke taak weer geven die is geactiveerd op het back-upitem in het geselecteerde tijds bereik.

![Tabblad Taken](./media/backup-azure-configure-backup-reports/jobs.png)

* **Beleid** : met het tabblad beleid kunt u informatie weer geven over al uw actieve beleids regels, zoals het aantal gekoppelde items en de totale Cloud opslag die wordt gebruikt door items waarvan een back-up is gemaakt onder een bepaald beleid. Als u op een bepaald beleid klikt, kunt u informatie weer geven over elk van de bijbehorende back-upitems.

![Tabblad beleid](./media/backup-azure-configure-backup-reports/policies.png)

## <a name="exporting-to-excel"></a>Exporteren naar Excel

Als u op de pijl-omlaag in de rechter bovenhoek van een wille keurige widget (tabel/grafiek) klikt, wordt de inhoud van die widget geëxporteerd als een Excel-werk blad, net als bij bestaande filters die zijn toegepast. Als u meer rijen van een tabel wilt exporteren naar Excel, kunt u het aantal rijen dat wordt weer gegeven op de pagina verhogen met behulp van de vervolg keuzelijst **rijen per pagina** boven aan elk raster.

## <a name="pinning-to-dashboard"></a>Vastmaken aan dash board

Klik op het speld pictogram boven aan elke widget om de widget aan uw Azure Portal dash board vast te maken. Dit helpt u bij het maken van aangepaste Dash boards die zijn aangepast om de belangrijkste informatie die u nodig hebt, weer te geven.

## <a name="cross-tenant-reports"></a>Rapporten over meerdere tenants

Als u een [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/) -gebruiker bent met gedelegeerde toegang tot abonnementen in meerdere Tenant omgevingen, kunt u het standaard abonnements filter gebruiken (door te klikken op het filter pictogram in de rechter bovenhoek van de Azure Portal) om alle abonnementen te kiezen waarvoor u gegevens wilt weer geven. Als u dit doet, kunt u LA-werk ruimten in uw tenants selecteren om multi-tenant rapporten weer te geven.

## <a name="conventions-used-in-backup-reports"></a>Conventies die in back-uprapporten worden gebruikt

* Filters werken van links naar rechts en van boven naar beneden op elk tabblad, dat wil zeggen dat alle filters alleen van toepassing zijn op alle widgets die aan de rechter kant van het filter of onder dat filter worden geplaatst. 
* Als u op een gekleurde tegel klikt, worden de widgets onder de tegel gefilterd op records die betrekking hebben op de waarde van die tegel. Als u bijvoorbeeld op de tegel *beveiliging gestopt* klikt op het tabblad Back-upitems, worden de rasters en de onderstaande grafieken gefilterd om gegevens weer te geven voor back-upitems in de status ' beveiliging gestopt '.
* Tegels die niet zijn gekleurd, kunnen niet worden geklikt.
* Gegevens voor het huidige dagdeel worden niet weergegeven in de rapporten. Als de geselecteerde waarde van het **tijds bereik** bijvoorbeeld de ***laatste 7 dagen***is, worden in het rapport records weer gegeven voor de afgelopen 7 voltooide dagen (die de huidige dag niet bevat).
* Het rapport bevat details van taken (naast logboek taken) die zijn **geactiveerd** in het geselecteerde tijds bereik. 
* De waarden die worden weer gegeven voor Cloud opslag en beveiligde instanties, bevinden zich aan het **einde** van het geselecteerde tijds bereik.
* De back-upitems die in de rapporten worden weer gegeven, zijn de items die aan het **einde** van het geselecteerde tijds bereik bestaan. Back-upitems die in het midden van het geselecteerde tijds bereik zijn verwijderd, worden niet weer gegeven. Dezelfde Conventie geldt ook voor back-upbeleid.

## <a name="query-load-times"></a>Laad tijden query

De widgets in het back-uprapport worden aangedreven door Kusto-query's, die worden uitgevoerd op de LA-werk ruimten van de gebruiker. Aangezien deze query's doorgaans de verwerking van grote hoeveel heden gegevens omvatten, met meerdere samen voegingen om uitgebreid inzicht mogelijk te maken, kunnen de widgets niet direct worden geladen wanneer de gebruiker rapporten weergeeft over een grote back-up. De onderstaande tabel bevat een ruwe schatting van de tijd die verschillende widgets kunnen laden, op basis van het aantal back-upitems en het tijds bereik waarvoor het rapport wordt weer gegeven:

| **Aantal gegevens bronnen**                         | **Tijd horizon** | **Laad tijden (ong.)**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ~ 5 K                       | 1 maand          | Tegels: 5-10 seconden <br> Rasters: 5-10 seconden <br> Grafieken: 5-10 seconden <br> Filters op rapport niveau: 5-10 seconden|
| ~ 5 K                       | 3 maanden          | Tegels: 5-10 seconden <br> Rasters: 5-10 seconden <br> Grafieken: 5-10 seconden <br> Filters op rapport niveau: 5-10 seconden|
| ~ 10 K                       | 3 maanden          | Tegels: 15-20 seconden <br> Rasters: 15-20 seconden <br> Grafieken: 1-2 minuten <br> Filters op rapport niveau: 25-30 seconden|
| ~ 15 K                       | 1 maand          | Tegels: 15-20 seconden <br> Rasters: 15-20 seconden <br> Grafieken: 50-60 seconden <br> Filters op rapport niveau: 20-25 seconden|
| ~ 15 K                       | 3 maanden          | Tegels: 20-30 seconden <br> Rasters: 20-30 seconden <br> Grafieken: 2-3 minuten <br> Filters op rapport niveau: 50-60 seconden |

## <a name="what-happened-to-the-power-bi-reports"></a>Wat is er gebeurd met de Power BI-rapporten?
* Onze eerder Power BI sjabloon-app voor rapportage (waarvan de bron gegevens van een Azure Storage account) zich op een afschaffing-pad bevindt. Het wordt aanbevolen om te beginnen met het verzenden van diagnostische gegevens van de kluis naar Log Analytics, zoals hierboven wordt beschreven, om rapporten weer te geven.

* Daarnaast bevindt het v1-schema voor het verzenden van diagnostische gegevens naar een opslag account of een LA-werk ruimte zich ook op een afschaffing pad. Dit betekent dat als u aangepaste query's of Automatiseringen hebt geschreven op basis van het v1-schema, u wordt geadviseerd om deze query's bij te werken om het momenteel ondersteunde v2-schema te gebruiken.

## <a name="next-steps"></a>Volgende stappen
[Meer informatie over bewaking en rapportage met Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-monitor-alert-faq)