---
title: Releaseopmerkingen voor Data Management Gateway
description: Data Management Gateway tory release notes
services: data-factory
author: nabhishek
manager: anandsub
ms.assetid: 14762e82-76d9-41c4-ba9f-14a54da29c36
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 838e523f74a21c44958ddb6dc88e4dab3526d81a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065001"
---
# <a name="release-notes-for-data-management-gateway"></a>Releaseopmerkingen voor Data Management Gateway
> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [zelf gehoste runtime voor integratie in V2.](../create-self-hosted-integration-runtime.md)

Een van de uitdagingen voor moderne data-integratie is het verplaatsen van data van en naar on-premises naar cloud. Data Factory maakt deze integratie met Data Management Gateway, een agent die u on-premises installeren om hybride gegevensverkeer mogelijk te maken.

Zie de volgende artikelen voor gedetailleerde informatie over Data Management Gateway en hoe u deze gebruiken:

*  [Gegevensbeheergateway](data-factory-data-management-gateway.md)
*  [Gegevens verplaatsen tussen on-premises en cloud met Azure Data Factory](data-factory-move-data-between-onprem-and-cloud.md)


## <a name="current-version"></a>HUIDIGE VERSIE 
We houden de Release notes hier niet meer bij. Ontvang [hier](https://go.microsoft.com/fwlink/?linkid=853077) de laatste releasenotes




## <a name="earlier-versions"></a>Eerdere versies
## <a name="21063477"></a>2.10.6347.7
### <a name="enhancements-"></a>Verbeteringen-
- U DNS-vermeldingen toevoegen aan de servicebus op de witte lijst in plaats van alle Azure IP-adressen van uw firewall op de witte lijst te krijgen (indien nodig). U vindt de betreffende DNS-vermelding op Azure-portal (Data Factory -> 'Author and Deploy' -> 'Gateways' -> 'serviceUrls' (in JSON)
- HDFS-connector ondersteunt nu zelfondertekende openbare certificaten door tls-validatie over te slaan.
- Opgelost: Probleem met gateway offline tijdens de update (vanwege klokscheefheid)


## <a name="2963132"></a>2.9.6313.2
### <a name="enhancements-"></a>Verbeteringen-
-   U DNS-vermeldingen toevoegen aan servicebus op de witte lijst in plaats van alle Azure IP-adressen van uw firewall op de witte lijst te krijgen (indien nodig). Hier vindt u meer informatie.
-   U nu gegevens kopiëren naar/van een enkele blokblob tot 4,75 TB, wat de maximaal ondersteunde grootte van blokblob is. (eerdere limiet was 195 GB).
-   Opgelost: probleem met het geheugen tijdens het uitpakken van verschillende kleine bestanden tijdens kopieeractiviteit.
-   Opgelost: Indexeer het probleem buiten bereik tijdens het kopiëren van Document DB naar een on-premises SQL Server met idempotentiefunctie.
-   Opgelost: SQL-cleanup script werkt niet met on-premises SQL Server van Wizard Kopiëren.
-   Opgelost: Kolomnaam met spatie aan het einde werkt niet in kopieeractiviteit.

## <a name="28662833"></a>2.8.66283.3
### <a name="enhancements-"></a>Verbeteringen-
- Opgelost: Probleem met ontbrekende referenties bij opnieuw opstarten gatewaymachine.
- Opgelost: probleem met registratie tijdens gatewayherstel met behulp van een back-upbestand.


## <a name="2762401"></a>2.7.6240.1
### <a name="enhancements-"></a>Verbeteringen-
- Opgelost: Onjuist lezen van decimale null-waarde van Oracle als bron.

## <a name="2661922"></a>2.6.6192.2
### <a name="whats-new"></a>Nieuwe functies
- Klanten kunnen feedback geven over de registratieervaring van gateways.
- Een nieuwe compressieindeling ondersteunen: ZIP (Leeglopen)

### <a name="enhancements-"></a>Verbeteringen-
- Prestatieverbetering voor Oracle Sink, HDFS-bron.
- Bug fix voor gateway automatische update, gateway parallelle verwerkingscapaciteit.


## <a name="2561641"></a>2.5.6164.1
### <a name="enhancements"></a>Verbeteringen 
- Verbeterde en robuustere gatewayregistratie-ervaring- Nu u de voortgangsstatus bijhouden tijdens het gatewayregistratieproces, waardoor de registratie-ervaring responsiever wordt.
- Verbetering in gatewayherstelproces- U de gateway nog steeds herstellen, zelfs als u het gatewayback-upbestand met deze update niet hebt. Hiervoor moet u de gekoppelde servicereferenties opnieuw instellen in Portal.
- Bug fix.

## <a name="2461511"></a>2.4.6151.1

### <a name="whats-new"></a>Nieuwe functies

- U nu gegevensbronreferenties lokaal opslaan. De referenties zijn versleuteld. De gegevensbronreferenties kunnen worden hersteld en hersteld met behulp van het back-upbestand dat kan worden geëxporteerd vanuit de bestaande gateway, allemaal on-premises.

### <a name="enhancements-"></a>Verbeteringen-

- Verbeterde en robuustere gatewayregistratie-ervaring.
- Ondersteuning voor automatische detectie van QuoteChar-configuratie voor tekstindeling in de wizard Kopiëren en verbeter de nauwkeurigheid van de algehele formaatdetectie.

## <a name="2361002"></a>2.3.6100.2

- Ondersteuning firstRowAsHeader en SkipLineCount automatische detectie in copy wizard voor tekstbestanden in on-premises Bestandssysteem en HDFS.
- De stabiliteit van de netwerkverbinding tussen gateway en servicebus verbeteren
- Een paar bug fixes


## <a name="2260721"></a>2.2.6072.1

*  Ondersteunt het instellen van HTTP-proxy voor de gateway met behulp van Gateway Configuration Manager. Indien geconfigureerd, zijn Azure Blob, Azure Table, Azure Data Lake en Document DB toegankelijk via HTTP-proxy.
*  Ondersteunt headerhandling voor TextFormat bij het kopiëren van gegevens van/naar Azure Blob, Azure Data Lake Store, on-premises bestandssysteem en on-premises HDFS.
*  Ondersteunt het kopiëren van gegevens uit Append Blob en Page Blob samen met de reeds ondersteunde Block Blob.
*  Introduceert een nieuwe gateway status **Online (Limited),** wat aangeeft dat de belangrijkste functionaliteit van de gateway werkt, behalve de interactieve bewerkingsondersteuning voor Wizard Kopiëren.
*  Verbetert de robuustheid van gatewayregistratie met behulp van registratiesleutel.

## <a name="216040"></a>2.1.6040.

*  DB2 driver is nu opgenomen in het gateway installatiepakket. U hoeft dit niet afzonderlijk te installeren.
*  DB2 driver ondersteunt nu z/OS en DB2 voor i (AS/400) samen met de reeds ondersteunde platforms (Linux, Unix en Windows).
*  Ondersteunt het gebruik van Azure Cosmos DB als bron of bestemming voor on-premises gegevensarchieven
*  Ondersteunt het kopiëren van gegevens van/naar koude/warme blobopslag samen met het reeds ondersteunde opslagaccount voor algemene doeleinden.
*  Hiermee u verbinding maken met on-premises SQL Server via gateway met externe inlogrechten.  

## <a name="2060131"></a>2.0.6013.1

*  U de taal/cultuur selecteren die door een gateway moet worden gebruikt tijdens handmatige installatie.

*  Wanneer gateway niet werkt zoals verwacht, u ervoor kiezen om gatewaylogboeken van de afgelopen zeven dagen naar Microsoft te sturen om het oplossen van het probleem te vergemakkelijken. Als gateway niet is verbonden met de cloudservice, u ervoor kiezen gatewaylogboeken op te slaan en te archiveren.  

*  Verbeteringen in de gebruikersinterface voor gatewayconfiguratiebeheer:

    *  De status van de gateway zichtbaarder maken op het tabblad Start.

    *  Gereorganiseerde en vereenvoudigde besturingselementen.

    *  U gegevens uit een opslag kopiëren met behulp van het [codevrije kopieergereedschap.](data-factory-copy-data-wizard-tutorial.md) Zie [Gefaseerde kopie](data-factory-copy-activity-performance.md#staged-copy) voor meer informatie over deze functie in het algemeen.
*  U Data Management Gateway gebruiken om gegevens rechtstreeks vanuit een on-premises SQL Server-database naar Azure Machine Learning te brengen.

*  Prestatieverbeteringen

    * Verbeter de prestaties bij het weergeven van Schema/Preview tegen SQL Server in codevrije kopieertool.

## <a name="11259531"></a>1.12.5953.1

*  Opgeloste fouten

## <a name="11159181"></a>1.11.5918.1

*  De maximale grootte van het gebeurtenislogboek van de gateway is verhoogd van 1 MB naar 40 MB.

*  Er wordt een waarschuwingsdialoogvenster weergegeven voor het geval een herstart nodig is tijdens de automatische update van de gateway. U ervoor kiezen om direct opnieuw op te starten dan of later.

*  In het geval dat automatische update mislukt, probeert gateway installer automatisch bijwerken drie keer op maximum.

*  Prestatieverbeteringen

    * Verbeter de prestaties voor het laden van grote tabellen vanaf de on-premises server in codevrije kopieerscenario.

*  Opgeloste fouten

## <a name="11058921"></a>1.10.5892.1

*  Prestatieverbeteringen

*  Opgeloste fouten

## <a name="1958652"></a>1.9.5865.2

*  Zero touch auto update mogelijkheden
*  Nieuw ladepictogram met statusindicatoren gateway
*  Mogelijkheid om "Nu bijwerken" van de client
*  Mogelijkheid om de updateplanningstijd in te stellen
*  PowerShell-script voor het in-/uitschakelen van automatische updates
*  Ondersteuning voor JSON-indeling  
*  Prestatieverbeteringen
*  Opgeloste fouten

## <a name="1858221"></a>1.8.5822.1

*  Problemen met probleemoplossing verbeteren
*  Prestatieverbeteringen
*  Opgeloste fouten

### <a name="1757951"></a>1.7.5795.1

*  Prestatieverbeteringen
*  Opgeloste fouten

### <a name="1757641"></a>1.7.5764.1

*  Prestatieverbeteringen
*  Opgeloste fouten

### <a name="1657351"></a>1.6.5735.1

*  Ondersteuning van on-premises HDFS Source/Sink
*  Prestatieverbeteringen
*  Opgeloste fouten

### <a name="1656961"></a>1.6.5696.1

*  Prestatieverbeteringen
*  Opgeloste fouten

### <a name="1656761"></a>1.6.5676.1

*  Diagnostische hulpprogramma's ondersteunen in Configuratiebeheer
*  Kolommen in de ondersteuningstabel voor tabelgegevensbronnen voor Azure Data Factory
*  SQL DW ondersteunen voor Azure Data Factory
*  Ondersteuning voor teruggetrokken in BlobSource en FileSource voor Azure Data Factory
*  Ondersteuningskopieergedrag : voeg Bestanden, Hiërarchie behouden en Hiërarchie af in BlobSink en FileSink met binaire kopie voor Azure-gegevensfabriek
*  Voortgang van de rapportage van ondersteuningsactiviteiten voor Azure Data Factory
*  Validatie van gegevensbronconnectiviteit voor Azure-gegevensfabriek
*  Opgeloste fouten

### <a name="1656721"></a>1.6.5672.1

*  Naam van de ondersteuningstabel voor ODBC-gegevensbron voor Azure Data Factory
*  Prestatieverbeteringen
*  Opgeloste fouten

### <a name="1656581"></a>1.6.5658.1

*  Bestandssink voor Azure-gegevensfabriek ondersteunen
*  Ondersteuning voor het behouden van hiërarchie in binaire kopie voor Azure Data Factory
*  Idempotentie voor ondersteuningsactiviteit voor Azure-gegevensfabriek
*  Opgeloste fouten

### <a name="1656401"></a>1.6.5640.1

*  Ondersteuning voor nog 3 gegevensbronnen voor Azure Data Factory (ODBC, OData, HDFS)
*  Teken van offerte ondersteunen in csv-parser voor Azure Data Factory
*  Compressieondersteuning (BZip2)
*  Opgeloste fouten

### <a name="1556121"></a>1.5.5612.1

*  Ondersteuning bieden voor vijf relationele databases voor Azure Data Factory (MySQL, PostgreSQL, DB2, Teradata en Sybase)
*  Compressieondersteuning (Gzip en Leeglopen)
*  Prestatieverbeteringen
*  Opgeloste fouten

### <a name="1455491"></a>1.4.5549.1

*  Ondersteuning voor Oracle-gegevensbronnen toevoegen voor Azure Data Factory
*  Prestatieverbeteringen
*  Opgeloste fouten

### <a name="1454921"></a>1.4.5492.1

*  Unified binary that supports both Microsoft Azure Data Factory and Office 365 Power BI services
*  De configuratie-gebruikersinterface en het registratieproces verfijnen
*  Azure Data Factory – Azure Ingress- en Egress-ondersteuning voor SQL Server-gegevensbron

### <a name="1253031"></a>1.2.5303.1

*  Los een time-outprobleem op om meer tijdrovende gegevensbronverbindingen te ondersteunen.

### <a name="1155268"></a>1.1.5526.8

*  Vereist .NET Framework 4.5.1 als voorwaarde tijdens de installatie.

### <a name="1051442"></a>1.0.5144.2

*  Geen wijzigingen die van invloed zijn op Azure Data Factory-scenario's.
