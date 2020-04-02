---
title: Gegevens migreren van Cassandra naar Azure Cosmos DB Cassandra API met Blitzz
description: Meer informatie over het migreren van gegevens uit de Apache Cassandra-database naar Azure Cosmos DB Cassandra API met Blitzz.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: b2e7f371e587c1c7f0debfa018ea8f25a30718a8
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548097"
---
# <a name="migrate-data-from-cassandra-to-azure-cosmos-db-cassandra-api-account-using-blitzz"></a>Gegevens migreren van Cassandra naar Azure Cosmos DB Cassandra API-account met Blitzz

Cassandra API in Azure Cosmos DB is een geweldige keuze geworden voor zakelijke workloads die worden uitgevoerd op Apache Cassandra om verschillende redenen, zoals: 

* **Geen overhead van beheer en bewaking:** Het elimineert de overhead van het beheren en bewaken van een groot aantal instellingen in OS, JVM en yaml-bestanden en hun interacties.

* **Aanzienlijke kostenbesparingen:** U kosten besparen met Azure Cosmos DB, inclusief de kosten van VM's, bandbreedte en alle toepasselijke licenties. Bovendien hoeft u de kosten van datacenters, servers, SSD-opslag, netwerken en elektriciteit niet te beheren. 

* **Mogelijkheid om bestaande code en hulpprogramma's te gebruiken:** Azure Cosmos DB biedt compatibiliteit op draadprotocolniveau met bestaande Cassandra SDK's en hulpprogramma's. Door deze compatibiliteit kunt u bestaande codebase gebruiken met de Azure Cosmos DB Cassandra-API, met slechts een klein aantal wijzigingen.

Er zijn verschillende manieren om databaseworkloads van het ene platform naar het andere te migreren. [Blitzz](https://www.blitzz.io) is een tool die een veilige en betrouwbare manier biedt om zero downtime migratie uit te voeren van een verscheidenheid aan databases naar Azure Cosmos DB. In dit artikel worden de stappen beschreven die nodig zijn om gegevens van de Apache Cassandra-database te migreren naar Azure Cosmos DB Cassandra API met Blitzz.

## <a name="benefits-using-blitzz-for-migration"></a>Voordelen met Blitzz voor migratie

Blitzz's migratieoplossing volgt een stapsgewijze aanpak om complexe operationele workloads te migreren. De volgende zijn enkele van de belangrijkste aspecten van Blitzz's zero-downtime migratieplan:

* Het biedt automatische migratie van bedrijfslogica (tabellen, indexen, weergaven) van apache Cassandra-database naar Azure Cosmos DB. U hoeft geen schema's handmatig te maken.

* Blitzz biedt volume- en parallelle databasereplicatie. Hiermee kunnen zowel de bron- als doelplatforms tijdens de migratie worden gesynchroniseerd met behulp van een techniek genaamd Change-Data-Capture (CDC). Door CDC te gebruiken, haalt Blitzz continu een stroom van wijzigingen uit de brondatabase (Apache Cassandra) en past deze toe op de doeldatabase (Azure Cosmos DB).

* Het is fouttolerant en garandeert precies één keer de levering van gegevens, zelfs tijdens een hardware- of softwarestoring in het systeem.

* Het beveiligt de gegevens tijdens het transport met behulp van een verscheidenheid van beveiligingsmethoden zoals TLS, encryptie.

## <a name="steps-to-migrate-data"></a>Stappen om gegevens te migreren

In deze sectie worden de stappen beschreven die nodig zijn om Blitzz in te stellen en worden gegevens van de Apache Cassandra-database gemigreerd naar Azure Cosmos DB.

1. Voeg vanaf de computer waar u de Blitzz replicant wilt installeren een beveiligingscertificaat toe. Dit certificaat is vereist door de Blitzz-replicant om een TLS-verbinding met het opgegeven Azure Cosmos DB-account tot stand te brengen. U het certificaat toevoegen met de volgende stappen:

   ```bash
   wget https://cacert.omniroot.com/bc2025.crt
   mv bc2025.crt bc2025.cer
   keytool -keystore $JAVA_HOME/lib/security/cacerts -importcert -alias bc2025ca -file bc2025.cer
   ```

1. U de Blitzz installatie en de binaire bestanden, hetzij door het aanvragen van een demo op de [Blitzz website](https://www.blitzz.io). U ook een [e-mail](mailto:success@blitzz.io) sturen naar het team.

   ![Blitzz replicant tool downloaden](./media/cassandra-migrate-cosmos-db-blitzz/blitzz-replicant-download.png)

   ![Blitzz replicant bestanden](./media/cassandra-migrate-cosmos-db-blitzz/replicant-files.png)

1. Stel vanaf de CLI-terminal de configuratie van de brondatabase in. Open het configuratiebestand met de **`vi conf/conn/cassandra.yml`** opdracht en voeg een door komma gescheiden lijst met IP-adressen toe van de Cassandra-knooppunten, poortnummer, gebruikersnaam, wachtwoord en alle andere vereiste details. Het volgende is een voorbeeld van inhoud in het configuratiebestand:

   ```bash
   type: CASSANDRA
  
   host: 172.17.0.2
   port: 9042

   username: 'cassandra'
   password: 'cassandra'

   max-connections: 30

   ```

   ![Verbindingseditor voor Openen](./media/cassandra-migrate-cosmos-db-blitzz/open-connection-editor-cassandra.png)

   ![Cassandra-verbindingsconfiguratie](./media/cassandra-migrate-cosmos-db-blitzz/cassandra-connection-configuration.png)

   Nadat u de configuratiegegevens hebt ingevuld, slaat u het bestand op en sluit u deze.

1. Optioneel u het filterbestand van de brondatabase instellen. Het filterbestand geeft aan welke schema's of tabellen moeten worden gemigreerd. Open het configuratiebestand met de **`vi filter/cassandra_filter.yml`** opdracht en voer de volgende configuratiegegevens in:

   ```bash

   allow:
   -    schema: “io_blitzz”
   Types: [TABLE]
   ```

   Nadat u de databasefiltergegevens hebt ingevuld, slaat u het bestand op en sluit u deze.

1. Vervolgens stelt u de configuratie van de doeldatabase in. Voordat u de configuratie definieert, [maakt u een Azure Cosmos DB Cassandra API-account](create-cassandra-dotnet.md#create-a-database-account) en maakt u vervolgens een Keyspace en een tabel om de gemigreerde gegevens op te slaan. Omdat u overstapt van Apache Cassandra naar Cassandra API in Azure Cosmos DB, u dezelfde partitiesleutel gebruiken die u met Apache cassandra hebt gebruikt.

1. Voordat u de gegevens migreert, verhoogt u de containerdoorvoer tot de hoeveelheid die nodig is om uw toepassing snel te laten migreren. U bijvoorbeeld de doorvoer verhogen tot 100000 RU's. Als u de doorvoer schaalt voordat u met de migratie begint, u uw gegevens in minder tijd migreren.

   ![Azure Cosmos-container schalen in de hele](./media/cassandra-migrate-cosmos-db-blitzz/scale-throughput.png)

   Verlaag de doorvoer nadat de migratie is voltooid. Op basis van de hoeveelheid gegevens die is opgeslagen en de VOOR elke bewerking benodigde RU's, u de benodigde doorvoer schatten na gegevensmigratie. Zie [Doorvoer van containers en databases inrichten voor](set-throughput.md) meer informatie over het schatten van de vereiste RU's en Ru/s schatten met behulp van de artikelen azure cosmos [DB-capaciteitsplanner.](estimate-ru-with-capacity-planner.md)

1. Download het **contactpunt, de poort, gebruikersnaam**en **het primaire wachtwoord** van uw Azure Cosmos-account in het deelvenster **Verbindingstekenreeks.** U gebruikt deze waarden in het configuratiebestand.

1. Stel vanaf de CLI-terminal de configuratie van de doeldatabase in. Open het configuratiebestand met de **`vi conf/conn/cosmosdb.yml`** opdracht en voeg een door komma's gescheiden lijst met hostURI, poortnummer, gebruikersnaam, wachtwoord en andere vereiste parameters toe. In het volgende voorbeeld ziet u de inhoud van het configuratiebestand:

   ```bash
   type: COSMOSDB

   host: '<Azure Cosmos account’s Contact point>'
   port: 10350

   username: 'blitzzdemo'
   password: '<Your Azure Cosmos account’s primary password>'

   max-connections: 30
   ```

1. Migreer vervolgens de gegevens met Blitzz. U de Blizz replicant **volledig** uitvoeren of **snapshot-modus:**

   * **Volledige modus** - In deze modus blijft de replicant worden uitgevoerd na migratie en luistert naar eventuele wijzigingen op de bron Apache Cassandra systeem. Als er wijzigingen worden gedetecteerd, worden ze in realtime gerepliceerd op het doel Azure Cosmos-account.

   * **Momentopnamemodus** : in deze modus u schemamigratie en eenmalige gegevensreplicatie uitvoeren. Realtime replicatie wordt niet ondersteund met deze optie.

   Door de bovenstaande twee modi te gebruiken, kan migratie worden uitgevoerd met nul downtime. 

1. Voer de volgende opdracht uit om gegevens te migreren vanaf de Blitzz replicant CLI-terminal:

   ```bash
   ./bin/replicant full conf/conn/cassandra.yaml conf/conn/cosmosdb.yaml --filter filter/cassandra_filter.yaml --replace-existing
   ```

   De replicant UI toont de replicatievoortgang. Zodra de schemamigratie- en momentopnamebewerking is uitgevoerd, wordt de voortgang 100% weergegeven. Nadat de migratie is voltooid, u de gegevens in de doelazure cosmos-database valideren.

   ![Cassandra-gegevensmigratie-uitvoer](./media/cassandra-migrate-cosmos-db-blitzz/cassandra-data-migration-output.png)


1. Omdat u de volledige modus voor migratie hebt gebruikt, u bewerkingen uitvoeren zoals gegevens invoegen, bijwerken of verwijderen in de bron Apache Cassandra-database. Later valideren dat ze worden gerepliceerd real-time op de beoogde Azure Cosmos database. Zorg ervoor dat u na de migratie de doorvoer die is geconfigureerd voor uw Azure Cosmos-container vermindert.

1. U de replicant elk punt stoppen en opnieuw starten met **--hervatten** schakelaar. De replicatie wordt hervat vanaf het punt dat is gestopt zonder in te leveren op de consistentie van de gegevens. In de volgende opdracht ziet u hoe u de hervattingsschakelaar gebruikt.

   ```bash
   ./bin/replicant full conf/conn/cassandra.yaml conf/conn/cosmosdb.yaml --filter filter/cassandra_filter.yaml --replace-existing --resume
   ```

Zie de [Blitzz replicant demo](https://www.youtube.com/watch?v=fsUhF9LUZmM)voor meer informatie over de gegevensmigratie naar bestemming, real-time migratie.

## <a name="next-steps"></a>Volgende stappen

* [Doorvoer voor containers en databases inrichten](set-throughput.md) 
* [Aanbevolen procedures voor partitiesleutel](partitioning-overview.md#choose-partitionkey)
* [RU/s schatten met behulp van de artikelen azure cosmos DB-capaciteitsplanner](estimate-ru-with-capacity-planner.md)