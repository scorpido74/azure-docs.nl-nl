---
title: Uw back-ups controleren met Backup Explorer
description: In dit artikel wordt beschreven hoe u Backup Explorer gebruiken om realtime controle uit te voeren van back-ups van kluizen, abonnementen, regio's en tenants.
ms.reviewer: dcurwin
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: fa30a061dfe0d9f7721bd2405280f8a01bea87fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131796"
---
# <a name="monitor-your-backups-with-backup-explorer"></a>Uw back-ups controleren met Backup Explorer

Naarmate organisaties steeds meer machines naar de cloud maken, wordt het steeds belangrijker om deze back-ups efficiënt te monitoren. De beste manier om te beginnen is het gebruik van een centrale locatie voor het bekijken van operationele informatie over een groot landgoed.

Backup Explorer is een ingebouwde Azure Monitor-werkmap die Azure Backup-clients deze centrale locatie biedt. Met Backup Explorer u operationele activiteiten in het gehele back-updomein op Azure bewaken, met bestaande uit tenants, locaties, abonnementen, brongroepen en kluizen. Backup Explorer biedt in grote lijnen de volgende mogelijkheden:

* **Perspectief op schaal:** krijg een geaggregeerde weergave van de back-upitems, taken, waarschuwingen, beleidsregels en resources die nog niet zijn geconfigureerd voor back-up over het hele domein. 
* **Analyse van de drill-down:** geef gedetailleerde informatie weer over elk van uw taken, waarschuwingen, beleidsregels en back-upitems, allemaal op één plek.
* **Bruikbare interfaces:** Nadat u een probleem hebt geïdentificeerd, u dit oplossen door naadloos naar het desbetreffende back-upitem of Azure-bron te gaan.

Deze mogelijkheden worden out-of-box geleverd door native integratie met Azure Resource Graph en Azure Monitor-werkmappen.

> [!NOTE]
> * Backup Explorer is momenteel alleen beschikbaar voor Vm's (Vm's) van Azure.
> * Backup Explorer is bedoeld als een operationeel dashboard voor het bekijken van informatie over uw back-ups in de afgelopen 7 dagen (maximaal).
> * Backup Explorer wordt momenteel niet ondersteund in nationale clouds.
> * Momenteel wordt het aanpassen van de sjabloon Back-up explorer niet ondersteund. 
> * We raden u aan aangepaste automatiseringen te schrijven op Azure Resource Graph-gegevens.

## <a name="get-started"></a>Aan de slag

U Backup Explorer openen door naar een van uw Vaults voor Herstelservices te gaan en de koppeling **Back-upverkenner** in het **deelvenster Overzicht** te selecteren.

![Snelkoppeling kluis](media/backup-azure-monitor-with-backup-explorer/vault-quick-link.png)

Als u de koppeling selecteert, wordt Back-upverkenner geopend, die een geaggregeerde weergave biedt over alle kluizen en abonnementen waartoe u toegang hebt. Als u een Azure Lighthouse-account gebruikt, u gegevens bekijken over alle tenants waartoe u toegang hebt. Zie de sectie 'Cross-tenant views' aan het einde van dit artikel voor meer informatie.

![Bestemmingspagina Back-up Explorer](media/backup-azure-monitor-with-backup-explorer/explorer-landing-page.png)

## <a name="backup-explorer-use-cases"></a>Use Cases van Backup Explorer

Back-up Explorer geeft meerdere tabbladen weer, die elk gedetailleerde informatie geven over een specifiek back-upartefact (bijvoorbeeld een back-upitem, taak of beleid). In deze sectie vindt u een kort overzicht van elk van de tabbladen. De video's bieden voorbeeldusecases voor elk back-upartefact, samen met beschrijvingen van de beschikbare besturingselementen.

### <a name="the-summary-tab"></a>Het tabblad Overzicht

Het tabblad **Overzicht** geeft een snelle blik op de algehele staat van uw back-updomein. U bijvoorbeeld het aantal items bekijken dat wordt beschermd, het aantal items waarvoor geen beveiliging is ingeschakeld of het aantal taken dat in de afgelopen 24 uur is gelukt.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYd]

### <a name="the-backup-items-tab"></a>Het tabblad Back-upitems

U elk van uw back-upitems filteren en bekijken op basis van abonnement, kluis en andere kenmerken. Als u de naam van een back-upitem selecteert, u het Azure-deelvenster voor dat item openen. Uit de tabel u bijvoorbeeld zien dat de laatste back-up is mislukt voor item *X.* Door *X*te selecteren, u het **deelvenster Back-up van** het item openen, waar u een on-demand back-upbewerking activeren.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYc]

### <a name="the-jobs-tab"></a>Het tabblad Vacatures

Selecteer het tabblad **Vacatures** om de details weer te geven van alle taken die in de afgelopen zeven dagen zijn geactiveerd. Hier u filteren op *taakbewerking,* *taakstatus*en *foutcode* (voor mislukte taken).


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nOrh]

### <a name="the-alerts-tab"></a>Het tabblad Waarschuwingen

Selecteer het tabblad **Waarschuwingen** om de details weer te geven van alle waarschuwingen die in de afgelopen zeven dagen op uw kluizen zijn gegenereerd. U waarschuwingen filteren op tekst (*Back-upfout* of *herstelfout),* huidige status (*Actief* of *Opgelost*) en ernst *(Kritieke*, *waarschuwing*of *informatie).* U ook een koppeling selecteren om naar de Azure VM te gaan en de nodige actie te ondernemen.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nTxe]

### <a name="the-policies-tab"></a>Het tabblad Beleid

U het tabblad **Beleid** selecteren om belangrijke informatie weer te geven over alle back-upbeleidsregels die in uw back-updomein zijn gemaakt. U het aantal items bekijken dat aan elk beleid is gekoppeld, samen met het bewaarbereik en de back-upfrequentie die door het beleid zijn opgegeven.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nLKV]

### <a name="the-backup-not-enabled-tab"></a>Het tabblad Back-up niet ingeschakeld

Back-up moet worden ingeschakeld voor alle machines die bescherming nodig hebben. Met Backup Explorer kunnen back-upbeheerders snel identificeren welke machines in een organisatie nog niet zijn beveiligd met back-ups. Als u deze informatie wilt weergeven, selecteert u het tabblad **Back-up niet ingeschakeld.**

In het deelvenster **Back-up niet ingeschakeld** wordt een tabel weergegeven met een lijst met onbeveiligde machines. Uw organisatie kan verschillende tags toewijzen aan productiemachines en testmachines, of aan machines die verschillende functies bedienen. Omdat elke klasse machines een apart back-upbeleid nodig heeft, u met filteren op tags informatie weergeven die specifiek is voor elk. Als u de naam van een machine selecteert, wordt u omgeleid naar het **deelvenster Back-up configureren** van die machine, waar u kiezen om een geschikt back-upbeleid toe te passen.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQXZ]

## <a name="export-to-excel"></a>Exporteren naar Excel

U de inhoud van een tabel of grafiek exporteren als een Excel-spreadsheet. De inhoud wordt geëxporteerd zoals het is, met uw bestaande filters toegepast. Als u extra tabelrijen wilt exporteren, u het aantal rijen dat op de pagina moet worden weergegeven, verhogen met de vervolgkeuzelijst **Rijen per pagina** boven aan elk tabblad.

## <a name="pin-to-the-dashboard"></a>Vastmaken aan het dashboard

U het pictogram 'vastmaken' boven aan elke tabel of grafiek selecteren om het vast te maken aan uw Azure-portaldashboard. Door deze informatie vast te maken, u een aangepast dashboard maken dat is aangepast om de informatie weer te geven die voor u het belangrijkst is.

## <a name="cross-tenant-views"></a>Weergaven van meerdere huurders

Als u een Azure Lighthouse-gebruiker bent met gedelegeerde toegang tot abonnementen in meerdere tenantomgevingen, u het standaardabonnementsfilter gebruiken. U geeft de abonnementen weer waarvoor u gegevens wilt zien door het pictogram 'filter' rechtsboven in de Azure-portal te selecteren. Wanneer u deze functie gebruikt, verzamelt Backup Explorer informatie over alle kluizen in uw geselecteerde abonnementen. Zie Wat is Azure Lighthouse voor meer [informatie.](https://docs.microsoft.com/azure/lighthouse/overview)

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over het gebruik van Azure Monitor voor het verkrijgen van inzichten over uw back-upgegevens](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)
