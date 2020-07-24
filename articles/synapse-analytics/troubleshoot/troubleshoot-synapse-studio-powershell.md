---
title: Problemen met de connectiviteit van Synapse Studio (preview) oplossen met Power shell
description: Problemen met Azure Synapse Studio-connectiviteit oplossen met Power shell
author: julieMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: f4afaf536a9c65758ad030e5cdeeee5fb97074d7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87046673"
---
# <a name="diagnose-azure-synapse-studio-preview-connectivity-issues-with-powershell-script"></a>Problemen met de connectiviteit van Azure Synapse Studio (preview) vaststellen met Power shell-script

Azure Synapse Studio (preview) is afhankelijk van een aantal web-API-eind punten om goed te werken. Deze hand leiding helpt u bij het identificeren van de oorzaken van verbindings problemen wanneer u het volgende bent:
- het configureren van uw lokale netwerk (zoals netwerk achter een bedrijfs firewall) voor toegang tot Azure Synapse Studio.
- Er zijn verbindings problemen met Azure Synapse Studio.

## <a name="prerequisite"></a>Vereiste

* Power shell 5,0 of hoger in Windows, of
* Power shell Core 6,0 of hoger in Windows of Linux.

## <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing

Klik met de rechter muisknop op de volgende koppeling en klik op "doel opslaan als":

- [Test-AzureSynapse.ps1](https://go.microsoft.com/fwlink/?linkid=2119734)

U kunt de koppeling ook rechtstreeks openen en het geopende script bestand opslaan. Sla het adres van de bovenstaande koppeling niet op, omdat deze in de toekomst kan worden gewijzigd.

Klik in Verkenner met de rechter muisknop op het gedownloade script bestand en op uitvoeren met Power shell.

![Gedownload script bestand uitvoeren met Power shell](media/troubleshooting-synapse-studio-powershell/run-with-powershell.png)

Wanneer u hierom wordt gevraagd, voert u de naam van de Azure Synapse-werk ruimte in die momenteel een probleem ondervindt of die u wilt testen voor de connectiviteit. Druk op ENTER.

![Naam van de werk ruimte invoeren](media/troubleshooting-synapse-studio-powershell/enter-workspace-name.png)

De diagnostische sessie wordt gestart. Wacht totdat de bewerking is voltooid.

![Wachten tot de diagnose is voltooid](media/troubleshooting-synapse-studio-powershell/wait-for-diagnosis.png)

In het eind wordt een samen vatting van de diagnose weer gegeven. Als uw PC geen verbinding kan maken met een of meer van de eind punten, worden enkele suggesties weer gegeven in de sectie samen vatting.

![Diagnostische samen vatting controleren](media/troubleshooting-synapse-studio-powershell/diagnosis-summary.png)

Daarnaast wordt een diagnostisch logboek bestand voor deze sessie gegenereerd in dezelfde map als het script voor het oplossen van problemen. De locatie wordt weer gegeven in de sectie ' algemene tips ' ( `D:\TestAzureSynapse_2020....log` ). U kunt dit bestand zo nodig naar technische ondersteuning verzenden.

Als u een netwerk beheerder bent en uw firewall configuratie afstemt voor Azure Synapse Studio, kunnen de technische details die worden weer gegeven boven de sectie samen vatting mogelijk helpen.

* Alle test items (aanvragen) die zijn gemarkeerd met ' door gegeven ' hebben een geslaagde connectiviteits test, ongeacht de HTTP-status code.
 De reden voor de mislukte aanvragen wordt weer gegeven in het geel, zoals `NamedResolutionFailure` of `ConnectFailure` . Deze redenen kunnen u helpen bij het bepalen of er fouten zijn met uw netwerk omgeving.


## <a name="next-steps"></a>Volgende stappen
Als de vorige stappen niet helpen bij het oplossen van het probleem, [maakt u een ondersteunings ticket](../../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md).
