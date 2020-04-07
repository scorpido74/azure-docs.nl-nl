---
title: Azure Backup-rapporten configureren
description: Rapporten voor Azure Backup configureren en weergeven met behulp van Logboekanalyses en Azure-werkmappen
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 497d70c1bcc577faa467720b959eb828e785a26a
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80672670"
---
# <a name="configure-azure-backup-reports"></a>Azure Backup-rapporten configureren

Een veelvoorkomende vereiste voor back-upbeheerders is het verkrijgen van inzichten over back-ups, op basis van gegevens die een lange periode beslaat. Er kunnen meerdere use cases zijn voor een dergelijke oplossing: het toewijzen en voorspellen van verbruikte cloudopslag, het controleren van back-ups en herstel, en het identificeren van belangrijke trends op verschillende granulaire niveaus.

Vandaag biedt Azure Backup een rapportageoplossing die gebruikmaakt van [Azure Monitor Logs](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) en Azure [Workbooks,](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)zodat u uitgebreide inzichten krijgt over uw back-ups in uw hele back-updomein. In dit artikel wordt uitgelegd hoe u back-uprapporten configureert en bekijkt.

## <a name="supported-scenarios"></a>Ondersteunde scenario's

* Back-uprapporten worden ondersteund voor Azure VM's, SQL in Azure VM's, SAP HANA/ASE in Azure VM's, Azure Backup Agent (MARS), Azure Backup Server (MABS) en System Center DPM.
* Voor DPM-workloads worden back-uprapporten ondersteund voor DPM-versie 5.1.363.0 en hoger en Agent Versie 2.0.9127.0 en hoger.
* Voor MABS-workloads worden back-uprapporten ondersteund voor MABS-versie 13.0.415.0 en hoger en Agent Versie 2.0.9170.0 en hoger.
* Back-uprapporten kunnen worden bekeken in alle back-upitems, kluizen, abonnementen en regio's, zolang hun gegevens worden verzonden naar een LA-werkruimte (Log Analytics) waartoe de gebruiker toegang heeft. Houd er rekening mee dat u alleen toegang nodig hebt tot **de LA Workspace** waarnaar de kluizen hun gegevens verzenden om rapporten voor een set kluizen te bekijken. U **hoeft geen** toegang te hebben tot de individuele kluizen.
* Als u een [Azure Lighthouse-gebruiker](https://docs.microsoft.com/azure/lighthouse/) bent met gedelegeerde toegang tot de abonnementen van uw klanten, u deze rapporten met Azure Lighthouse gebruiken om rapporten voor al uw tenants weer te geven.
* Gegevens voor logboekback-uptaken worden momenteel niet weergegeven in de rapporten.

## <a name="getting-started"></a>Aan de slag

Volg de drie onderstaande stappen om aan de slag te gaan met het gebruik van de rapporten:

1. **Een LA-werkruimte (Log Analytics) maken (of een bestaande werkruimte gebruiken):**

U moet een of meer LA Workspaces instellen om uw back-uprapportagegegevens op te slaan. De locatie en het abonnement waar deze LA-werkruimte kan worden gemaakt, is onafhankelijk van de locatie en het abonnement waar uw kluizen bestaan. 

Raadpleeg het volgende artikel: [Een Logboekanalysewerkruimte maken in de Azure-portal](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) om een LA Workspace in te stellen.

Standaard worden de gegevens in een LA Workspace 30 dagen bewaard. Als u gegevens voor een langere tijdshorizon wilt bekijken, wijzigt u de bewaarperiode van de LA Workspace. Als u de bewaarperiode wilt wijzigen, raadpleegt u het volgende artikel: [Gebruik en kosten beheren met Azure Monitor-logboeken](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage).

2. **Diagnostische instellingen voor uw kluizen configureren:**

Azure Resource Manager-bronnen, zoals vaults van Recovery Services, registreren informatie over geplande bewerkingen en door gebruikers geactiveerde bewerkingen als diagnostische gegevens. 

Selecteer **diagnostische instellingen** in het controlegedeelte van uw vault Recovery Services en geef het doel op voor de diagnostische gegevens van de vault Recovery Services. [Meer informatie over het gebruik van diagnostische gebeurtenissen](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events).

![Blade diagnostische instellingen](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

Azure Backup biedt ook een ingebouwd Azure-beleid, dat de configuratie van diagnostische instellingen automatiseert voor alle kluizen in een bepaald bereik. Raadpleeg het volgende artikel voor meer informatie over het gebruik van dit beleid: [Vault Diagnostics Settings op schaal configureren](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics)

> [!NOTE]
> Zodra u diagnoses configureert, kan het tot 24 uur duren voordat de eerste gegevenspush is voltooid. Zodra de gegevens naar de LA Workspace stromen, u mogelijk niet onmiddellijk gegevens in de rapporten zien, omdat gegevens voor de huidige gedeeltelijke dag niet worden weergegeven in de rapporten (meer details [hier).](https://docs.microsoft.com/azure/backup/configure-reports#conventions-used-in-backup-reports) Daarom is het raadzaam om de rapporten 2 dagen nadat u uw kluizen configureert om gegevens naar Log Analytics te verzenden, te bekijken.

3. **Rapporten weergeven op de Azure-portal:**

Zodra u uw kluizen hebt geconfigureerd om gegevens naar LA te verzenden, bekijkt u uw back-uprapporten door naar het blad van een kluis te navigeren en te klikken op het **menu-item Back-uprapporten.** 

![Vault-dashboard](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

Als u op deze koppeling klikt, wordt de werkmap voor back-uprapport geopend.

> [!NOTE]
> * Momenteel kan de eerste belasting van het rapport tot 1 minuut duren.
> * De kluis Recovery Services is slechts een toegangspunt voor back-uprapporten. Zodra de werkmap Back-uprapporten wordt geopend vanaf het blad van een kluis, u gegevens zien die zijn samengevoegd in al uw kluizen (door de juiste set LA-werkruimten te selecteren).

Hieronder vindt u een beschrijving van de verschillende tabbladen die het rapport bevat:

* **Samenvatting** - Het tabblad Overzicht biedt een overzicht op hoog niveau van uw back-updomein. Onder het tabblad Overzicht u een snelle blik werpen op het totale aantal back-upitems, de totale verbruikte cloudopslag, het aantal beveiligde exemplaren en het slagingspercentage per werkbelastingtype. Ga naar de desbetreffende tabbladen voor meer gedetailleerde informatie over een specifiek type back-upartefact.

![Tabblad Samenvatting](./media/backup-azure-configure-backup-reports/summary.png)

* **Back-upitems** : met het tabblad Back-upitems u informatie en trends zien over cloudopslag die wordt verbruikt op back-upitemniveau. Als u bijvoorbeeld SQL gebruikt in Azure VM-back-ups, u zien dat er een back-up wordt gemaakt van de cloudopslag die wordt gebruikt voor elke SQL-database. U er ook voor kiezen om gegevens te zien voor back-upitems met een bepaalde beveiligingsstatus. Als u bijvoorbeeld boven aan het tabblad op de tegel **Beveiliging gestopt** klikt, filtert u alle onderstaande widgets om alleen gegevens weer te geven voor Back-upitems in de status Gestopt e-mailbeveiliging.

![Tabblad Back-upitems](./media/backup-azure-configure-backup-reports/backup-items.png)

* **Gebruik** : op het tabblad Gebruik u de belangrijkste factureringsparameters voor uw back-ups weergeven. De informatie op dit tabblad bevindt zich op een niveau van factureringsentiteit (beveiligde container). Als er bijvoorbeeld een back-up van een DPM-server wordt gemaakt naar Azure, u de trend van beveiligde exemplaren en cloudopslag die voor de DPM-server wordt verbruikt, bekijken. Als u SQL gebruikt in Azure Backup of SAP HANA in Azure Backup, geeft dit tabblad u gebruiksgerelateerde informatie op het niveau van de virtuele machine waarin deze databases zich bevinden.

![Tabblad Gebruik](./media/backup-azure-configure-backup-reports/usage.png)

* **Taken** : met het tabblad Vacatures u langlopende trends op taken weergeven, zoals het aantal mislukte taken per dag en de belangrijkste oorzaken van taakuitval. U deze informatie zowel op geaggregeerd niveau als op back-upitemniveau bekijken. Als u op een bepaald back-upitem in een raster klikt, u gedetailleerde informatie weergeven over elke taak die is geactiveerd op dat back-upitem in het geselecteerde tijdsbereik.

![Tabblad Vacatures](./media/backup-azure-configure-backup-reports/jobs.png)

* **Beleid** : op het tabblad Beleid u informatie weergeven over al uw actieve beleidsregels, zoals het aantal gekoppelde items en de totale cloudopslag die wordt verbruikt door items waarvan een back-up is gemaakt onder een bepaald beleid. Als u op een bepaald beleid klikt, u informatie weergeven over elk van de bijbehorende back-upitems.

![Tabblad Beleid](./media/backup-azure-configure-backup-reports/policies.png)

## <a name="exporting-to-excel"></a>Exporteren naar Excel

Als u klikt op de pijl-omlaag rechtsboven in een widget (tabel/grafiek) wordt de inhoud van die widget geëxporteerd als een Excel-blad, zoals deze is toegepast met bestaande filters. Als u meer rijen van een tabel naar Excel wilt exporteren, u het aantal rijen op de pagina verhogen met de vervolgkeuzelijst **Rijen per pagina** boven aan elk raster.

## <a name="pinning-to-dashboard"></a>Vastmaken aan dashboard

Klik op het pictogram Vastmaken boven aan elke widget om de widget vast te maken aan uw Azure-portaldashboard. Zo u aangepaste dashboards maken die zijn afgestemd op de belangrijkste informatie die u nodig hebt.

## <a name="cross-tenant-reports"></a>Rapporten met meerdere huurders

Als u een [Azure Lighthouse-gebruiker](https://docs.microsoft.com/azure/lighthouse/) bent met gedelegeerde toegang tot abonnementen in meerdere tenantomgevingen, u het standaardabonnementsfilter gebruiken (door op het filterpictogram rechtsboven in de Azure-portal te klikken) om alle abonnementen te kiezen waarvoor u gegevens wilt zien. Als u dit doet, u LA Workspaces voor uw huurders selecteren om rapporten met meerdere tenants te bekijken.

## <a name="conventions-used-in-backup-reports"></a>Conventies die worden gebruikt in back-uprapporten

* Filters werken van links naar rechts en van boven naar beneden op elk tabblad, dat wil zeggen, elk filter is alleen van toepassing op al die widgets die zijn gepositioneerd, hetzij aan de rechterkant van dat filter of onder dat filter. 
* Als u op een gekleurde tegel klikt, worden de widgets onder de tegel gefilterd voor records met betrekking tot de waarde van die tegel. Als u bijvoorbeeld op de tegel *Beveiliging gestopt* klikt op het tabblad Back-upitems, worden de rasters en grafieken hieronder gefilterd om gegevens weer te geven voor back-upitems in de status 'Gestopt voor beveiliging'.
* Tegels die niet gekleurd zijn, zijn niet klikbaar.
* Gegevens voor het huidige dagdeel worden niet weergegeven in de rapporten. Wanneer de geselecteerde waarde van **Tijdbereik** dus, ***Laatste 7 dagen,*** wordt in het rapport records weergegeven voor de laatste 7 voltooide dagen (exclusief de huidige dag).
* Het rapport bevat details van taken (met uitzondering van logboektaken) die zijn **geactiveerd** in het geselecteerde tijdsbereik. 
* De waarden die worden weergegeven voor cloudopslag en beveiligde exemplaren, bevinden zich aan het **einde** van het geselecteerde tijdsbereik.
* De back-upitems die in de rapporten worden weergegeven, zijn de items die aan het **einde** van het geselecteerde tijdsbereik bestaan. Back-upitems die in het midden van het geselecteerde tijdsbereik zijn verwijderd, worden niet weergegeven. Dezelfde conventie geldt ook voor back-upbeleid.

## <a name="query-load-times"></a>Laadtijden van query's

De widgets in het back-uprapport worden aangedreven door Kusto-query's, die worden uitgevoerd op de LA Workspaces van de gebruiker. Aangezien deze query's meestal betrekking hebben op de verwerking van grote hoeveelheden gegevens, met meerdere joins om rijkere inzichten mogelijk te maken, kunnen de widgets niet onmiddellijk laden wanneer de gebruiker rapporten bekijkt over een grote back-updomein. De onderstaande tabel geeft een ruwe schatting van de tijd die verschillende widgets kunnen nemen om te laden, op basis van het aantal back-upitems en het tijdsbereik waarvoor het rapport wordt bekeken:

| **# Gegevensbronnen**                         | **Tijdshorizon** | **Laadtijden (ca.)**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ~5 K                       | 1 maand          | Tegels: 5-10 seconden <br> Roosters: 5-10 seconden <br> Grafieken: 5-10 seconden <br> Filters op rapportniveau: 5-10 seconden|
| ~5 K                       | 3 maanden          | Tegels: 5-10 seconden <br> Roosters: 5-10 seconden <br> Grafieken: 5-10 seconden <br> Filters op rapportniveau: 5-10 seconden|
| ~10 K                       | 3 maanden          | Tegels: 15-20 seconden <br> Roosters: 15-20 seconden <br> Grafieken: 1-2 minuten <br> Filters op rapportniveau: 25-30 seconden|
| ~15 K                       | 1 maand          | Tegels: 15-20 seconden <br> Roosters: 15-20 seconden <br> Grafieken: 50-60 seconden <br> Filters op rapportniveau: 20-25 seconden|
| ~15 K                       | 3 maanden          | Tegels: 20-30 seconden <br> Roosters: 20-30 seconden <br> Grafieken: 2-3 minuten <br> Filters op rapportniveau: 50-60 seconden |

## <a name="what-happened-to-the-power-bi-reports"></a>Wat is er gebeurd met de Power BI-rapporten?
* Onze eerdere Power BI-sjabloon-app voor rapportage (die gegevens uit een Azure Storage-account heeft gehaald) bevindt zich op een afschrijvingspad. Het wordt aanbevolen om te beginnen met het verzenden van diagnostische vaultgegevens naar Log Analytics zoals hierboven beschreven, om rapporten te bekijken.

* Daarnaast bevindt het V1-schema voor het verzenden van diagnostische gegevens naar een opslagaccount of een LA Workspace zich ook op een afschrijvingspad. Dit betekent dat als u aangepaste query's of automatiseringen hebt geschreven op basis van het V1-schema, u wordt geadviseerd deze query's bij te werken om het momenteel ondersteunde V2-schema te gebruiken.

## <a name="next-steps"></a>Volgende stappen
[Meer informatie over monitoring en rapportage met Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-monitor-alert-faq)