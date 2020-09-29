---
title: Problemen oplossen bij het gebruik van de Azure Cosmos-emulator
description: Meer informatie over het troubleshot van de service, het oplossen van problemen met certificaten, versleuteling en versie beheer bij het gebruik van de Azure Cosmos-emulator.
ms.service: cosmos-db
ms.topic: troubleshooting
author: markjbrown
ms.author: mjbrown
ms.date: 09/17/2020
ms.custom: contperfq1
ms.openlocfilehash: af9122aaa0233fe5248f31ffe805e01a98831eae
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91447431"
---
# <a name="troubleshoot-issues-when-using-the-azure-cosmos-emulator"></a>Problemen oplossen bij het gebruik van de Azure Cosmos-emulator

De Azure Cosmos-emulator biedt een lokale omgeving die de Azure Cosmos DB-Service voor ontwikkelings doeleinden emuleert. Gebruik de tips in dit artikel voor hulp bij het oplossen van problemen die optreden bij het installeren of gebruiken van de Azure Cosmos-emulator. 

Als u een nieuwe versie van de emulator hebt geïnstalleerd en er fouten optreden, zorg dan dat u uw gegevens opnieuw instelt. U kunt uw gegevens opnieuw instellen door met de rechter muisknop op het pictogram van de Azure Cosmos-emulator in het systeemvak te klikken en vervolgens op gegevens opnieuw instellen te klikken.... Als dat niet het geval is, kunt u de emulator en eventuele oudere versies van de emulator verwijderen, indien gevonden, verwijdert u de map *C:\Program files\Azure Cosmos DB emulator* en installeert u de emulator opnieuw. Zie [De lokale emulator verwijderen](local-emulator.md#uninstall) voor instructies. Als u de gegevens opnieuw instelt, gaat u naar `%LOCALAPPDATA%\CosmosDBEmulator` locatie en verwijdert u de map.

## <a name="troubleshoot-corrupted-windows-performance-counters"></a>Problemen met beschadigde Windows-prestatie meter items oplossen

* Als de Azure Cosmos-emulator vastloopt, verzamelt u de dump bestanden vanuit `%LOCALAPPDATA%\CrashDumps` de map, comprimeert u deze en opent u een ondersteunings ticket van de [Azure Portal](https://portal.azure.com).

* Als `Microsoft.Azure.Cosmos.ComputeServiceStartupEntryPoint.exe` vastloopt, kan dit een indicatie zijn dat de prestatiemeteritems beschadigd zijn. Dit probleem kunt u meestal oplossen door de volgende opdracht uit te voeren vanaf een opdrachtprompt met beheerdersrechten:

  ```cmd
  lodctr /R
   ```

## <a name="troubleshoot-connectivity-issues"></a>Verbindingsproblemen oplossen

* Als er sprake is van een verbindingsprobleem, [verzamelt u traceringsbestanden](#trace-files), comprimeert u de bestanden en opent u een ondersteuningsticket via de [Azure-portal](https://portal.azure.com).

* Als u het bericht **Service niet beschikbaar** krijgt, is het mogelijk dat de emulator de netwerkstack niet kan initialiseren. Controleer of de veilige Pulse-client of Juniper-netwerkclient is geïnstalleerd. De netwerkfilterstuurprogramma's van deze clients kunnen mogelijk de oorzaak zijn van het probleem. Doorgaans kan het probleem worden opgelost door stuurprogramma's voor netwerkfilters van derden te verwijderen. U kunt de emulator ook starten met/DisableRIO, waarmee de netwerkcommunicatie van de emulator wordt overgezet naar normale Winsock. 

* Als u **de aanvraag ' verboden ' ontvangt, wordt het bericht ': ' verzonden met een verboden versleuteling in het Transit Protocol of de versleuteling. Controleer de mini maal toegestane protocol instelling voor SSL/TLS...** verbindings problemen. Dit kan worden veroorzaakt door algemene wijzigingen in het besturings systeem (bijvoorbeeld insider preview Build 20170) of de instellingen van de browser die TLS 1,3 als standaard inschakelen. Er kan een soort gelijke fout optreden wanneer u de SDK gebruikt om een aanvraag uit te voeren op de Cosmos-emulator, zoals **Microsoft.Azure.Documents.DocumentClientException: er wordt een aanvraag gemaakt met een verboden versleuteling in het Transit Protocol of de versleuteling. Controleer de mini maal toegestane protocol instelling voor SSL/TLS-accounts**. Dit werkt op dit moment zoals verwacht, omdat de Cosmos Emulator alleen het TLS 1.2-protocol accepteert en gebruikt. De aanbevolen oplossing is om de instellingen te wijzigen en standaard te bewerken in TLS 1,2. bijvoorbeeld: in IIS-beheer navigeert u naar "sites"-> "standaard websites" en zoekt u de site bindingen voor poort 8081 en bewerkt u deze om TLS 1,3 uit te scha kelen. Een vergelijkbare bewerking kan worden uitgevoerd voor de webbrowser via de opties in Instellingen.

* Als de emulator wordt uitgevoerd en uw computer naar de slaapstand gaat of er besturingssysteemupdates worden uitgevoerd, wordt mogelijk het bericht **Service is momenteel niet beschikbaar** weergegeven. Stel de gegevens van de emulator opnieuw in door met de rechtermuisknop op het pictogram te klikken dat wordt weergegeven in het Windows-systeemvak. Selecteer vervolgens **Reset Data** (Gegevens opnieuw instellen).

## <a name="collect-trace-files"></a><a id="trace-files"></a>Traceringsbestanden verzamelen

Voor het verzamelen van foutopsporingsgegevens, voert u de volgende opdrachten uit vanaf een opdrachtprompt voor een beheerder:

1. Navigeer naar het pad waar de emulator is geïnstalleerd:

   ```bash
   cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"
   ```

1. Sluit de emulator af en Bekijk de systeem balk om te controleren of het programma is afgesloten. Het kan een minuut duren voordat de bewerking is voltooid. U kunt ook **Afsluiten** selecteren in de gebruikers interface van de Azure Cosmos-emulator.

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /shutdown
   ```

1. Start de logboek registratie met de volgende opdracht:

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /startwprtraces
   ```

1. De emulator starten

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe
   ```

1. Reproduceer het probleem. Als de Data Explorer niet werkt, hoeft u alleen te wachten totdat de browser een paar seconden wordt geopend om de fout te ondervangen.

1. Stop de registratie met de volgende opdracht:

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /stopwprtraces
   ```
   
1. Navigeer naar `%ProgramFiles%\Azure Cosmos DB Emulator` pad en zoek het bestand *docdbemulator_000001. etl* .

1. Open een ondersteuningsticket via de [Azure-portal](https://portal.azure.com) en stuur het ETL-bestand mee, evenals de stappen om het probleem te reproduceren.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u problemen met de lokale emulator kunt oplossen. U kunt nu door gaan met de volgende artikelen:

* [De Azure Cosmos-emulator certificaten exporteren voor gebruik met Java-, python-en Node.js-apps](local-emulator-export-ssl-certificates.md)
* [Opdracht regel parameters en Power shell-opdrachten gebruiken om de emulator te beheren](emulator-command-line-parameters.md)
