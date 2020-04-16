---
title: Problemen met Azure Synapse Studio (preview) connectiviteit PowerShell oplossen
description: Problemen met Azure Synapse Studio-connectiviteit oplossen met PowerShell
author: julieMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: bbc985407a6cb56f4f1b539f514ab092b5f7d0de
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431473"
---
# <a name="diagnose-azure-synapse-studio-preview-connectivity-issues-with-powershell-script"></a>Problemen met Azure Synapse Studio (preview) met PowerShell-script diagnosticeren

Azure Synapse Studio (preview) is afhankelijk van een set Web API-eindpunten om goed te werken. Met deze handleiding u de oorzaken van verbindingsproblemen identificeren wanneer u:
- het configureren van uw lokale netwerk (zoals netwerk achter een bedrijfsfirewall) voor toegang tot Azure Synapse Studio.
- verbindingsproblemen met Azure Synapse Studio.

## <a name="prerequisite"></a>Vereiste

* PowerShell 5.0 of hoger versie op Windows, of
* PowerShell Core 6.0 of hoger versie op Windows of Linux.

## <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing

Klik met de rechtermuisknop op de volgende link en klik op 'Doel opslaan als':

- [Test-AzureSynapse.ps1](https://go.microsoft.com/fwlink/?linkid=2119734)

U de koppeling ook rechtstreeks openen en het geopende scriptbestand opslaan. Sla het adres van de bovenstaande link niet op, omdat deze in de toekomst kan veranderen.

Klik in verkenner met de rechtermuisknop op het gedownloade scriptbestand en klik op 'Uitvoeren met PowerShell'.

![Gedownload scriptbestand uitvoeren met PowerShell](media/troubleshooting-synapse-studio-powershell/run-with-powershell.png)

Wanneer u daarom wordt gevraagd, voert u de naam azure Synapse-werkruimte in die momenteel een probleem ondervindt of die u wilt testen op connectiviteit en drukt u op Enter.

![Werkruimtenaam invoeren](media/troubleshooting-synapse-studio-powershell/enter-workspace-name.png)

De diagnostische sessie wordt gestart. Wacht tot het klaar is.

![Wacht tot de diagnose is voltooid](media/troubleshooting-synapse-studio-powershell/wait-for-diagnosis.png)

Uiteindelijk wordt een diagnosesamenvatting getoond. Als uw pc geen verbinding kan maken met een of meer eindpunten, worden enkele suggesties weergegeven in de sectie Samenvatting.

![Diagnostische samenvatting bekijken](media/troubleshooting-synapse-studio-powershell/diagnosis-summary.png)

Bovendien wordt een diagnostisch logboekbestand voor deze sessie gegenereerd in dezelfde map als het probleemoplossingsscript. De locatie wordt weergegeven in "Algemene tips" sectie (`D:\TestAzureSynapse_2020....log`). U dit bestand indien nodig naar de technische ondersteuning sturen.

Als u een netwerkbeheerder bent en uw firewallconfiguratie voor Azure Synapse Studio afstemt, kunnen de technische details boven de sectie Samenvatting helpen.

* Alle testitems (aanvragen) die zijn gemarkeerd met 'Geslaagd' betekenen dat ze connectiviteitstests hebben doorstaan, ongeacht de HTTP-statuscode.
 Voor de mislukte aanvragen wordt de reden `NamedResolutionFailure` geel `ConnectFailure`weergegeven, zoals of . Met deze redenen u achterhalen of er verkeerde configuraties zijn met uw netwerkomgeving.


## <a name="next-steps"></a>Volgende stappen
Als de vorige stappen niet helpen om het probleem op te lossen [Maak een ondersteuningsticket](../../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md).
