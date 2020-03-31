---
title: Bekende problemen/migratiebeperkingen met de hybride modus
description: Meer informatie over bekende problemen/migratiebeperkingen met het gebruik van Azure Database Migration Service in hybride modus.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: aedc7ea3d778d52f6f348837430987568af188ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649599"
---
# <a name="known-issuesmigration-limitations-with-using-hybrid-mode"></a>Bekende problemen/migratiebeperkingen met de hybride modus

Bekende problemen en beperkingen in verband met het gebruik van Azure Database Migration Service in hybride modus worden beschreven in de volgende secties.

## <a name="installer-fails-to-authenticate"></a>Installatieprogramma kan niet verifiëren

Nadat u het certificaat naar uw AdApp hebt geüpload, is er een vertraging van maximaal een paar minuten voordat het kan verifiëren met Azure. De installateur zal proberen om opnieuw te proberen met enige vertraging, maar het is mogelijk dat de propagatie vertraging langer is dan de opnieuw proberen, en je ziet een **FailedToGetAccessTokenException** bericht. Als het certificaat is geüpload naar de juiste AdApp en de juiste AppId is geleverd in dmsSettings.json, probeert u de installatieopdracht opnieuw uit te voeren.

## <a name="service-offline-after-successful-installation"></a>Service "offline" na succesvolle installatie

Als de service als offline wordt weergegeven nadat het installatieproces is voltooid, probeert u de volgende stappen te gebruiken.

1. Navigeer in de Azure-portal in uw instantie van Azure Database Migration Service naar het tabblad **Hybride** instellingen en controleer of de werknemer is geregistreerd door het raster van geregistreerde werknemers te controleren.

    De status van deze werknemer moet **Online**zijn, maar deze kan worden weergegeven als **Offline** als er een probleem is.

2. Controleer op de werknemerscomputer de status van de service door de volgende PowerShell-opdracht uit te voeren:

    ```
    Get-Service Scenario*
    ```

    Deze opdracht geeft u de status van de Windows-service waarop de werknemer wordt uitgevoerd. Er mag maar één resultaat zijn. Als de werknemer is gestopt, u proberen deze opnieuw te starten met de volgende PowerShell-opdracht:

    ```
    Start-Service Scenario*
    ```

    U de service ook controleren in de gebruikersinterface van Windows Services.

3. Als de Windows-service wordt uitgevoerd tussen Uitvoeren en Gestopt, ondervond de werknemer problemen met het opstarten. Controleer de hybride werknemerslogboeken van Azure Database Migration Service om het probleem te bepalen.

    - Installatieproceslogboeken worden opgeslagen in de map 'logboeken' in de map waaruit het uitvoerbare installatieprogramma is uitgevoerd.
    - Azure Database Migration Service hybrid worker logs are stored in the **WorkerLogs** folder, in the folder in which worker is installed. De standaardlocatie voor de logboekbestanden voor hybride werknemers is **C:\Program Files\DatabaseMigrationServiceHybrid\WorkerLogs**.

## <a name="using-your-own-signed-certificate"></a>Uw eigen ondertekende certificaat gebruiken

Het certificaat dat wordt gegenereerd door de actie GenerateCert is een zelfondertekend certificaat, dat mogelijk niet acceptabel is op basis van uw interne beveiligingsbeleid. In plaats van dit certificaat te gebruiken, u uw eigen certificaat opgeven en de duimafdruk opgeven in dmsSettings.json. Dit certificaat moet worden geüpload naar uw AdApp en worden geïnstalleerd op de computer waarop u de hybride werknemer azure database migration service installeert. Installeer dit certificaat vervolgens met de privésleutel in het certificaatarchief Lokale machine.

## <a name="running-the-worker-service-as-a-low-privilege-account"></a>De werknemersservice uitvoeren als een account met lage bevoegdheden

Standaard wordt de hybride werknemersservice Azure Database Migration Service uitgevoerd als het lokale systeemaccount. U het account dat voor deze service wordt gebruikt wijzigen zolang het account dat u gebruikt netwerkmachtigingen heeft. Als u het 'run as'-account van de service wilt wijzigen, gebruikt u het volgende proces.

1. Stop de service, via Windows Services of met de opdracht Stop-Service in PowerShell.

2. Werk de service bij om een ander aanmeldingsaccount te gebruiken.

3. Geef in certmgr voor certificaten voor lokale computer machtigingen aan het nieuwe account voor de **DMS Hybrid App Key-** en **DMS Scenario Engine Key Pair-certificaten.**

    a. Open certmgr om de volgende toetsen te bekijken:

    - DMS Hybrid App-toets
    - Installatiesleutel voor DMS-hybride werknemers
    - DMS Scenario Engine Key Pair

    b. Klik met de rechtermuisknop op de vermelding van de **DMS Hybrid App-toets,** wijs **Alle taken**aan en selecteer **Privésleutels beheren**.

    c. Selecteer **op** het tabblad Beveiliging de optie **Toevoegen**en voer de naam van het account in.

    d. Gebruik dezelfde stappen om machtigingen voor de nieuwe account te verlenen aan het **DMS Scenario Engine Key Pair-certificaat.**

## <a name="unregistering-the-worker-manually"></a>De werknemer handmatig uitschrijven

Als u geen toegang meer hebt tot de werknemerscomputer, u de werknemer uitschrijven en uw azure database migration service-instantie opnieuw gebruiken door de volgende stappen uit te voeren:

1. Ga in de Azure-portal naar de instantie Azure Database Migration Service en navigeer vervolgens naar de pagina **Hybride** instellingen.

   Uw werknemersvermelding wordt weergegeven in de lijst, met de status die wordt weergegeven als **Offline**.

2. Selecteer helemaal rechts van de vermelding stoteurs van de werknemer de ellips en selecteer **Vervolgens Het registreren uitschrijven selecteren**.

## <a name="addressing-issues-for-specific-migration-scenarios"></a>Problemen voor specifieke migratiescenario's aanpakken

In de onderstaande secties worden scenariospecifieke problemen beschreven die verband houden met het gebruik van de hybride modus Azure Database Migration Service om een online migratie uit te voeren.

### <a name="online-migrations-to-azure-sql-database-managed-instance"></a>Beheerde instantie Onlinemigraties naar Azure SQL Database

**Hoog CPU-gebruik**

**Probleem:** Voor online migraties naar SQL Database-beheerde instantie, zal de computer waarop de hybride werknemer wordt uitgevoerd, een hoog CPU-gebruik ondervinden als er te veel back-ups zijn of als de back-ups te groot zijn.

**Mitigatie:** gebruik gecomprimeerde back-ups om dit probleem te beperken, de migratie te splitsen zodat deze meerdere shares gebruikt of de computer waarop de hybride werknemer wordt uitgevoerd, op te schalen.
