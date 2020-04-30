---
title: 'Snelstart: Cassandra-API met Python - Azure Cosmos DB'
description: In deze snelstart ziet u hoe u de Apache Cassandra-API in Azure Cosmos DB gebruikt om een profieltoepassing te maken met Python.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.devlang: python
ms.topic: quickstart
ms.date: 09/24/2018
ms.openlocfilehash: 0b432653c452b6763e746f61b86e881c9cee62cc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77134692"
---
# <a name="quickstart-build-a-cassandra-app-with-python-sdk-and-azure-cosmos-db"></a>Quick Start: een Cassandra-app bouwen met python SDK en Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [Java](create-cassandra-java.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
>  

In deze Quick Start maakt u een Azure Cosmos DB Cassandra-API-account en gebruikt u een Cassandra python-app die is gekloond van GitHub om een Cassandra-data base en-container te maken. Azure Cosmos DB is een database service met meerdere modellen waarmee u snel documenten, tabellen, sleutel waarden en grafische data bases met globale distributie en mogelijkheden voor horizontale schaal kunt maken en er query's op uitvoert.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak er gratis een](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Of [Probeer gratis Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) zonder een Azure-abonnement.
- [Python 2.7.14 + of 3.4 +](https://www.python.org/downloads/).
- [Git](https://git-scm.com/downloads).
- [Python-stuur programma voor Apache Cassandra](https://github.com/datastax/python-driver).

## <a name="create-a-database-account"></a>Een databaseaccount maken

Voordat u een documentdatabase kunt maken, moet u een Cassandra-account maken met Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

We gaan nu een Cassandra API-app klonen vanuit GitHub, de verbindingsreeks instellen en de app uitvoeren. U zult zien hoe gemakkelijk het is om op een programmatische manier met gegevens te werken. 

1. Open een opdrachtprompt. Maak een nieuwe map met de naam `git-samples`. Sluit vervolgens de opdrachtprompt.

    ```bash
    md "C:\git-samples"
    ```

2. Open een git-terminalvenster, bijvoorbeeld git bash, en gebruik de `cd`-opdracht om naar de nieuwe map te gaan voor het installeren van de voorbeeld-app.

    ```bash
    cd "C:\git-samples"
    ```

3. Voer de volgende opdracht uit om de voorbeeldopslagplaats te klonen. Deze opdracht maakt een kopie van de voorbeeld-app op uw computer.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-python-getting-started.git
    ```

## <a name="review-the-code"></a>De code bekijken

Deze stap is optioneel. Als u wilt weten hoe de databaseresources met de code worden gemaakt, kunt u de volgende codefragmenten bekijken. De code fragmenten zijn allemaal afkomstig uit het *pyquickstart.py* -bestand. Als u deze stap wilt overslaan, kunt u verdergaan naar [Uw verbindingsreeks bijwerken](#update-your-connection-string). 

* De waarden van de gebruikersnaam en het wachtwoord zijn ingesteld met behulp van de pagina Verbindingsreeks in de Azure-portal. `path\to\cert` bevat een pad naar een X509-certificaat. 

   ```python
    ssl_opts = {
            'ca_certs': 'path\to\cert',
            'ssl_version': ssl.PROTOCOL_TLSv1_2
            }
    auth_provider = PlainTextAuthProvider( username=cfg.config['username'], password=cfg.config['password'])
    cluster = Cluster([cfg.config['contactPoint']], port = cfg.config['port'], auth_provider=auth_provider, ssl_options=ssl_opts)
    session = cluster.connect()
   
   ```

* De `cluster` wordt geïnitialiseerd met contactPoint-informatie. Het contactPoint wordt opgehaald uit Azure Portal.

    ```python
   cluster = Cluster([cfg.config['contactPoint']], port = cfg.config['port'], auth_provider=auth_provider)
    ```

* De `cluster` maakt verbinding met de Cassandra-API van Azure Cosmos DB.

    ```python
    session = cluster.connect()
    ```

* Er wordt een nieuwe keyspace gemaakt.

    ```python
   session.execute('CREATE KEYSPACE IF NOT EXISTS uprofile WITH replication = {\'class\': \'NetworkTopologyStrategy\', \'datacenter1\' : \'1\' }')
    ```

* Er wordt een nieuwe tabel gemaakt.

   ```
   session.execute('CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)');
   ```

* Sleutel/waarde-entiteiten worden ingevoegd.

    ```Python
    insert_data = session.prepare("INSERT INTO  uprofile.user  (user_id, user_name , user_bcity) VALUES (?,?,?)")
    session.execute(insert_data, [1,'Lybkov','Seattle'])
    session.execute(insert_data, [2,'Doniv','Dubai'])
    session.execute(insert_data, [3,'Keviv','Chennai'])
    session.execute(insert_data, [4,'Ehtevs','Pune'])
    session.execute(insert_data, [5,'Dnivog','Belgaum'])
    ....
    
    ```

* Voer een query uit om alle sleutelwaarden op te halen.

    ```Python
    rows = session.execute('SELECT * FROM uprofile.user')
    ```  
    
* Voer een query uit om een sleutelwaarde op te halen.

    ```Python
    
    rows = session.execute('SELECT * FROM uprofile.user where user_id=1')
    ```  

## <a name="update-your-connection-string"></a>Uw verbindingsreeks bijwerken

Ga nu terug naar Azure Portal om de verbindingsreeksinformatie op te halen en kopieer deze in de app. De verbindingsreeks stelt uw app in staat om te communiceren met de gehoste database.

1. Selecteer in uw Azure Cosmos DB-account [Azure portal](https://portal.azure.com/)in de Azure Portal **verbindings reeks**. 

    Gebruik de ![knop Kopiëren](./media/create-cassandra-python/copy.png) aan de rechterkant van het scherm om de bovenste waarde (het CONTACT POINT) te kopiëren.

    ![Een gebruikersnaam, wachtwoord en toegangspunt die worden gebruikt voor toegang in Azure Portal, blade Verbindingsreeks, bekijken en kopiëren](./media/create-cassandra-python/keys.png)

2. Open het *config.py* -bestand. 

3. Plak de waarde CONTACT POINT vanuit de portal over `<FILLME>` op regel 10.

    Regel 10 moet nu ongeveer als volgt uitzien: 

    `'contactPoint': 'cosmos-db-quickstarts.cassandra.cosmosdb.azure.com:10350'`

4. Kopieer de waarde van USERNAME vanuit de portal en plak deze over `<FILLME>` op regel 6.

    Regel 6 moet nu ongeveer als volgt uitzien: 

    `'username': 'cosmos-db-quickstart',`
    
5. Kopieer de waarde van PASSWORD vanuit de portal en plak deze over `<FILLME>` op regel 8.

    Regel 8 moet nu ongeveer als volgt uitzien:

    `'password' = '2Ggkr662ifxz2Mg==`';`

6. Sla het *config.py* -bestand op.
    
## <a name="use-the-x509-certificate"></a>Het X509-certificaat gebruiken

1. Down load het Baltimore Cyber Trust-basis certificaat [https://cacert.omniroot.com/bc2025.crt](https://cacert.omniroot.com/bc2025.crt)lokaal van. Wijzig de naam van het bestand met de bestands extensie *. CER*.

   Het certificaat heeft serienummer `02:00:00:b9` en SHA1-vingerafdruk `d4🇩🇪20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74`.

2. Open *pyquickstart.py* en wijzig het `path\to\cert` punt naar het nieuwe certificaat.

3. Sla *pyquickstart.py*op.

## <a name="run-the-python-app"></a>De Python-app uitvoeren

1. Gebruik de opdracht cd in de git-terminal om naar de map `azure-cosmos-db-cassandra-python-getting-started` te gaan. 

2. Voer de volgende opdrachten uit om de vereiste modules te installeren:

    ```python
    python -m pip install cassandra-driver
    python -m pip install prettytable
    python -m pip install requests
    python -m pip install pyopenssl
    ```

2. Voer de volgende opdracht uit om uw python-toepassing te starten:

    ```
    python pyquickstart.py
    ```

3. Controleer of de resultaten op de opdrachtregel aan de verwachting voldoen.

    Druk op CTRL + C om de uitvoering van het programma te stoppen en het console venster te sluiten. 

    ![De uitvoer bekijken en controleren](./media/create-cassandra-python/output.png)
    
4. Open **Data Explorer** in de Azure-portal om deze nieuwe gegevens te bekijken, te wijzigen, een query erop uit te voeren of er iets anders mee te doen. 

    ![De gegevens bekijken in Data Explorer](./media/create-cassandra-python/data-explorer.png)

## <a name="review-slas-in-the-azure-portal"></a>SLA’s bekijken in Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een Azure Cosmos DB-account maakt met Cassandra-API en een Cassandra python-app uitvoert die een Cassandra-data base en-container maakt. U kunt nu aanvullende gegevens in uw Azure Cosmos DB-account importeren. 

> [!div class="nextstepaction"]
> [Cassandra-gegevens importeren in Azure Cosmos DB](cassandra-import-data.md)

