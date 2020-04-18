---
title: Azure Backup-rapporten configureren
description: Rapporten voor Azure Backup configureren en weergeven met behulp van Logboekanalyse en Azure-werkmappen
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: bcc87deb19190149329369ca58f54b45b62b41fe
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617821"
---
# <a name="configure-azure-backup-reports"></a>Azure Backup-rapporten configureren

Een veelvoorkomende vereiste voor back-upbeheerders is het verkrijgen van inzichten over back-ups op basis van gegevens die een lange periode omspant. Use cases voor een dergelijke oplossing zijn:

 - Toewijzing en prognose van verbruikte cloudopslag.
 - Controle van back-ups en herstel.
 - Het identificeren van belangrijke trends op verschillende granulaire niveaus.

Vandaag biedt Azure Backup een rapportageoplossing die [azure-monitorlogboeken](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) en [Azure-werkmappen](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)gebruikt. Met deze bronnen krijgt u uitgebreide inzichten over uw back-ups in uw hele back-updomein. In dit artikel wordt uitgelegd hoe u Azure Backup-rapporten configureert en bekijkt.

## <a name="supported-scenarios"></a>Ondersteunde scenario's

* Back-uprapporten worden ondersteund voor Azure VM's, SQL in Azure VM's, SAP HANA/ASE in Azure VM's, Microsoft Azure Recovery Services (MARS)-agent, Microsoft Azure Backup Server (MABS) en System Center Data Protection Manager (DPM).
* Voor DPM-workloads worden back-uprapporten ondersteund voor DPM-versie 5.1.363.0 en hoger en Agent Versie 2.0.9127.0 en hoger.
* Voor MABS-workloads worden back-uprapporten ondersteund voor MABS-versie 13.0.415.0 en hoger en Agent Versie 2.0.9170.0 en hoger.
* Back-uprapporten kunnen worden bekeken voor alle back-upitems, kluizen, abonnementen en regio's, zolang hun gegevens worden verzonden naar een Log Analytics-werkruimte waartoe de gebruiker toegang heeft. Als u rapporten voor een set kluizen wilt weergeven, hoeft u alleen toegang te hebben tot de Log Analytics-werkruimte waarnaar de kluizen hun gegevens verzenden. U hoeft geen toegang te hebben tot de individuele kluizen.
* Als u een [Azure Lighthouse-gebruiker](https://docs.microsoft.com/azure/lighthouse/) bent met gedelegeerde toegang tot de abonnementen van uw klanten, u deze rapporten met Azure Lighthouse gebruiken om rapporten voor al uw tenants weer te geven.
* Gegevens voor logboekback-uptaken worden momenteel niet weergegeven in de rapporten.

## <a name="get-started"></a>Aan de slag

Volg deze stappen om de rapporten te gebruiken.

#### <a name="1-create-a-log-analytics-workspace-or-use-an-existing-one"></a>1. Een Log Analytics-werkruimte maken of een bestaande werkruimte gebruiken

Stel een of meer Log Analytics-werkruimten in om uw back-uprapportagegegevens op te slaan. De locatie en het abonnement waar deze Log Analytics-werkruimte kan worden gemaakt, is onafhankelijk van de locatie en het abonnement waar uw kluizen bestaan. 

Zie [Een werkruimte voor Logboekanalyse maken in de Azure-portal](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)als u een werkruimte voor Logboekanalyse wilt instellen.

Standaard worden de gegevens in een Log Analytics-werkruimte 30 dagen bewaard. Als u gegevens voor een langere tijdshorizon wilt bekijken, wijzigt u de bewaarperiode van de werkruimte Log Analytics. Zie [Gebruik en kosten beheren met Azure Monitor-logboeken](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage)als u de bewaarperiode wilt wijzigen.

#### <a name="2-configure-diagnostics-settings-for-your-vaults"></a>2. Diagnostische instellingen configureren voor uw kluizen

Azure Resource Manager-bronnen, zoals vaults van Recovery Services, registreren informatie over geplande bewerkingen en door gebruikers geactiveerde bewerkingen als diagnostische gegevens. 

Selecteer in het controlegedeelte van uw vault Recovery Services **de instellingen voor diagnostische gegevens** en geef het doel op voor de diagnostische gegevens van de vault Recovery Services. Zie [Diagnostische instellingen voor vaults van Recovery Services gebruiken voor](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events)meer informatie over het gebruik van diagnostische gebeurtenissen.

![Het deelvenster Diagnostische instellingen](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

Azure Backup biedt ook een ingebouwd Azure-beleid, dat de configuratie van diagnostische instellingen automatiseert voor alle kluizen in een bepaald bereik. Zie [Instellingen voor kluisdiagnose op schaal configureren voor](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics)meer informatie over het gebruik van dit beleid.

> [!NOTE]
> Nadat u diagnoses hebt geconfigureerd, kan het tot 24 uur duren voordat de eerste gegevenspush is voltooid. Nadat gegevens naar de werkruimte Log Analytics zijn gestroomd, ziet u mogelijk niet onmiddellijk gegevens in de rapporten omdat gegevens voor de huidige gedeeltelijke dag niet worden weergegeven in de rapporten. Zie [Conventies die worden gebruikt in back-uprapporten](https://docs.microsoft.com/azure/backup/configure-reports#conventions-used-in-backup-reports)voor meer informatie . We raden u aan de rapporten twee dagen nadat u uw kluizen hebt geconfigureerd, te bekijken om gegevens naar Log Analytics te verzenden.

#### <a name="3-view-reports-in-the-azure-portal"></a>3. Rapporten bekijken in de Azure-portal

Nadat u uw kluizen hebt geconfigureerd om gegevens naar Log Analytics te verzenden, bekijkt u uw back-uprapporten door naar het deelvenster van een kluis te gaan en **Back-uprapporten te**selecteren.

![Vault-dashboard](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

Selecteer deze koppeling om de werkmap Back-uprapport te openen.

> [!NOTE]
> * Momenteel kan de eerste belasting van het rapport tot 1 minuut duren.
> * De kluis Recovery Services is slechts een toegangspunt voor back-uprapporten. Nadat de werkmap Back-uprapport is geopend vanuit het deelvenster van een kluis, selecteert u de juiste set Logboekanalyse-werkruimten om gegevens te bekijken die zijn samengevoegd in al uw kluizen.

Het rapport bevat verschillende tabbladen:

* **Samenvatting**: Gebruik dit tabblad om een overzicht op hoog niveau van uw back-up landgoed te krijgen. U een snelle blik werpen op het totale aantal back-upitems, de totale verbruikte cloudopslag, het aantal beveiligde exemplaren en het slagingspercentage per werkbelastingtype. Ga voor meer gedetailleerde informatie over een specifiek type back-upartefact naar de desbetreffende tabbladen.

   ![Tabblad Samenvatting](./media/backup-azure-configure-backup-reports/summary.png)

* **Back-upitems:** gebruik dit tabblad om informatie en trends te zien over cloudopslag die wordt verbruikt op back-upniveau. Als u bijvoorbeeld SQL gebruikt in een Azure VM-back-up, u de cloudopslag zien die wordt verbruikt voor elke SQL-database waarvan een back-up wordt gemaakt. U er ook voor kiezen om gegevens te zien voor back-upitems met een bepaalde beveiligingsstatus. Als u bijvoorbeeld de tegel **Beveiliging gestopt** boven aan het tabblad selecteert, worden alle widgets eronder gefilterd om alleen gegevens weer te geven voor back-upitems in de status Beveiliging gestopt.

   ![Tabblad Back-upitems](./media/backup-azure-configure-backup-reports/backup-items.png)

* **Gebruik:** gebruik dit tabblad om de belangrijkste factureringsparameters voor uw back-ups weer te geven. De informatie op dit tabblad bevindt zich op het niveau van de factureringsentiteit (beveiligde container). Als er bijvoorbeeld een back-up van een DPM-server wordt gemaakt naar Azure, u de trend van beveiligde exemplaren en cloudopslag die voor de DPM-server wordt verbruikt, bekijken. Als u SQL gebruikt in Azure Backup of SAP HANA in Azure Backup, geeft dit tabblad u gebruiksgerelateerde informatie op het niveau van de virtuele machine waarin deze databases zijn opgenomen.

   ![Tabblad Gebruik](./media/backup-azure-configure-backup-reports/usage.png)

* **Taken:** gebruik dit tabblad om langlopende trends op taken weer te geven, zoals het aantal mislukte taken per dag en de belangrijkste oorzaken van taakuitval. U deze informatie zowel op geaggregeerd niveau als op back-upniveau bekijken. Selecteer een bepaald back-upitem in een raster om gedetailleerde informatie weer te geven over elke taak die is geactiveerd op dat back-upitem in het geselecteerde tijdsbereik.

   ![Tabblad Vacatures](./media/backup-azure-configure-backup-reports/jobs.png)

* **Beleid:** gebruik dit tabblad om informatie weer te geven over al uw actieve beleidsregels, zoals het aantal gekoppelde items en de totale cloudopslag die wordt verbruikt door items waarvan een back-up is gemaakt onder een bepaald beleid. Selecteer een bepaald beleid om informatie over elk van de bijbehorende back-upitems weer te geven.

   ![Tabblad Beleid](./media/backup-azure-configure-backup-reports/policies.png)

## <a name="export-to-excel"></a>Exporteren naar Excel

Selecteer de pijl-omlaag-knop rechtsboven van een widget, zoals een tabel of grafiek, om de inhoud van dat object te exporteren als een Excel-blad als bij bestaande filters. Als u meer rijen van een tabel naar Excel wilt exporteren, u het aantal rijen op de pagina verhogen met de vervolgpijl **Rijen per pagina** boven aan elk raster.

## <a name="pin-to-dashboard"></a>Vastmaken aan dashboard

Selecteer de pinknop boven aan elke widget om de widget vast te maken aan het Azure-portaldashboard. Met deze functie u aangepaste dashboards maken die zijn afgestemd op de belangrijkste informatie die u nodig hebt.

## <a name="cross-tenant-reports"></a>Rapporten voor cross-tenant

Als u [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/) gebruikt met gedelegeerde toegang tot abonnementen in meerdere tenantomgevingen, u het standaardabonnementsfilter gebruiken. Selecteer de filterknop in de rechterbovenhoek van de Azure-portal om alle abonnementen te kiezen waarvoor u gegevens wilt zien. Met behulp van dit alles u Log Analytics-werkruimten selecteren voor uw tenants om rapporten met meerdere tenants weer te geven.

## <a name="conventions-used-in-backup-reports"></a>Conventies die worden gebruikt in back-uprapporten

* Filters werken van links naar rechts en van boven naar beneden op elk tabblad. Dat wil zeggen, elk filter is alleen van toepassing op al die widgets die zijn gepositioneerd aan de rechterkant van dat filter of onder dat filter. 
* Als u een gekleurde tegel selecteert, worden de widgets onder de tegel gefilterd voor records die betrekking hebben op de waarde van die tegel. Als u bijvoorbeeld de tegel **Beveiliging gestopt selecteert** op het tabblad **Back-upitems,** worden de rasters en grafieken hieronder gefilterd om gegevens weer te geven voor back-upitems in de status Beveiliging gestopt.
* Tegels die niet gekleurd zijn, zijn niet klikbaar.
* Gegevens voor de huidige gedeeltelijke dag worden niet weergegeven in de rapporten. Dus, wanneer de geselecteerde waarde van **tijdbereik** **Laatste 7 dagen**is, wordt in het rapport records weergegeven voor de laatste zeven voltooide dagen. De huidige dag is niet inbegrepen.
* Het rapport bevat details van taken (met uitzondering van logboektaken) die zijn *geactiveerd* in het geselecteerde tijdsbereik. 
* De waarden die worden weergegeven voor **cloudopslag** en **beveiligde exemplaren** bevinden zich aan het *einde* van het geselecteerde tijdsbereik.
* De back-upitems die in de rapporten worden weergegeven, zijn de items die aan het *einde* van het geselecteerde tijdsbereik bestaan. Back-upitems die zijn verwijderd in het midden van het geselecteerde tijdsbereik, worden niet weergegeven. Dezelfde conventie geldt ook voor back-upbeleid.

## <a name="query-load-times"></a>Laadtijden van query's

De widgets in het back-uprapport worden aangedreven door Kusto-query's, die worden uitgevoerd op de Log Analytics-werkruimten van de gebruiker. Deze query's omvatten meestal de verwerking van grote hoeveelheden gegevens, met meerdere joins om rijkere inzichten mogelijk te maken. Als gevolg hiervan kunnen de widgets niet onmiddellijk worden geladen wanneer de gebruiker rapporten bekijkt over een grote back-up-domein. Deze tabel geeft een ruwe schatting van de tijd die verschillende widgets kunnen nemen om te laden, op basis van het aantal back-upitems en het tijdsbereik waarvoor het rapport wordt bekeken.

| **# Gegevensbronnen**                         | **Tijdshorizon** | **Geschatte laadtijden**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ~5 K                       | 1 maand          | Tegels: 5-10 seconden <br> Roosters: 5-10 seconden <br> Grafieken: 5-10 seconden <br> Filters op rapportniveau: 5-10 seconden|
| ~5 K                       | 3 maanden          | Tegels: 5-10 seconden <br> Roosters: 5-10 seconden <br> Grafieken: 5-10 seconden <br> Filters op rapportniveau: 5-10 seconden|
| ~10 K                       | 3 maanden          | Tegels: 15-20 seconden <br> Roosters: 15-20 seconden <br> Grafieken: 1-2 minuten <br> Filters op rapportniveau: 25-30 seconden|
| ~15 K                       | 1 maand          | Tegels: 15-20 seconden <br> Roosters: 15-20 seconden <br> Grafieken: 50-60 seconden <br> Filters op rapportniveau: 20-25 seconden|
| ~15 K                       | 3 maanden          | Tegels: 20-30 seconden <br> Roosters: 20-30 seconden <br> Grafieken: 2-3 minuten <br> Filters op rapportniveau: 50-60 seconden |

## <a name="what-happened-to-the-power-bi-reports"></a> Wat is er gebeurd met de Power BI-rapporten? 
* De eerdere Power BI-sjabloon-app voor rapportage, die gegevens uit een Azure-opslagaccount heeft verkregen, bevindt zich op een afschrijvingspad. We raden u aan diagnostische gegevens van kluizen naar Log Analytics te verzenden om rapporten te bekijken.

* Daarnaast bevindt het V1-schema voor het verzenden van diagnostische gegevens naar een opslagaccount of een Log Analytics-werkruimte zich ook op een afschrijvingspad. Als u aangepaste query's of automatiseringen hebt geschreven op basis van het V1-schema, wordt u geadviseerd deze query's bij te werken om het momenteel ondersteunde V2-schema te gebruiken.

## <a name="next-steps"></a>Volgende stappen
[Meer informatie over monitoring en rapportage met Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-monitor-alert-faq)
