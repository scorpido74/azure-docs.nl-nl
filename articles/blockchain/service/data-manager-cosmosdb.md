---
title: Blockchain Data Manager gebruiken om Azure Cosmos DB - Azure Blockchain-service bij te werken
description: Blockchain Data Manager gebruiken voor Azure Blockchain Service om blockchain-gegevens naar Azure Cosmos DB te verzenden
ms.date: 03/08/2020
ms.topic: tutorial
ms.reviewer: chroyal
ms.openlocfilehash: 483a5246274f63549dfb2914361ede6aa001e02e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79533178"
---
# <a name="tutorial-use-blockchain-data-manager-to-send-data-to-azure-cosmos-db"></a>Zelfstudie: Blockchain Data Manager gebruiken om gegevens naar Azure Cosmos DB te verzenden

In deze zelfstudie gebruikt u Blockchain Data Manager voor Azure Blockchain Service om blockchain-transactiegegevens op te nemen in Azure Cosmos DB. Blockchain Data Manager legt blockchain-grootboekgegevens vast, transformeert en levert deze aan Azure Event Grid-onderwerpen. Vanuit Azure Event Grid gebruikt u een Azure Logic App-connector om documenten te maken in een Azure Cosmos DB-database. Wanneer u klaar bent met de zelfstudie, u blockchain-transactiegegevens verkennen in Azure Cosmos DB Data Explorer.

[![Blockchain-transactiedetails](./media/data-manager-cosmosdb/raw-msg.png)](./media/data-manager-cosmosdb/raw-msg.png#lightbox)

In deze zelfstudie hebt u:

> [!div class="checklist"]
> * Een blockchain-gegevensbeheer-exemplaar maken
> * Een blockchain-toepassing toevoegen om transactie-eigenschappen en -gebeurtenissen te decoderen
> * Een Azure Cosmos DB-account en -database maken om transactiegegevens op te slaan
> * Een Azure Logic App maken om een Azure Event Grid Topic te verbinden met Azure Cosmos DB
> * Een transactie verzenden naar een blockchain-grootboek
> * De gedecodeerde transactiegegevens weergeven in Azure Cosmos DB

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

* Snelstart [voltooien: een blockchain-lid maken met behulp van de Azure-portal](create-member.md) of [Quickstart: een Azure Blockchain Service-blockchain-lid maken met Azure CLI](create-member-cli.md)
* Snelstart [voltooien: gebruik Visual Studio Code om verbinding te maken met een Consortiumnetwerk van Azure Blockchain Service.](connect-vscode.md) De quickstart begeleidt u al bij het installeren van [Azure Blockchain Development Kit voor Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) en het opzetten van uw blockchain-ontwikkelomgeving.
* Volledige [zelfstudie: gebruik Visual Studio Code om slimme contracten te maken, te bouwen en te implementeren.](send-transaction.md) De tutorial loopt door het maken van een voorbeeld slim contract.
* Een [gebeurtenisrasteronderwerp maken](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* Meer informatie over [gebeurtenishandlers in Azure Event Grid](../../event-grid/event-handlers.md)

## <a name="create-instance"></a>Instantie maken

Een instantie voor Blockchain Data Manager verbindt en bewaakt een Azure Blockchain Service-transactieknooppunt. Een instantie legt alle ruwe blok- en ruwe transactiegegevens uit het transactieknooppunt vast. Een uitgaande verbinding verzendt blockchain-gegevens naar Azure Event Grid. U configureert één uitgaande verbinding wanneer u de instantie maakt.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Ga naar het Azure Blockchain Service-lid dat u hebt gemaakt in de vereiste [Quickstart: Maak een blockchain-lid met behulp van de Azure-portal.](create-member.md) Selecteer **Blockchain Data Manager**.
1. Selecteer **Toevoegen**.

    ![Blockchain-gegevensbeheer toevoegen](./media/data-manager-cosmosdb/add-instance.png)

    Voer de volgende details in:

    Instelling | Voorbeeld | Beschrijving
    --------|---------|------------
    Name | mywatcher mywatcher | Voer een unieke naam in voor een verbonden Blockchain Data Manager.
    Transactieknooppunt | myblockchainmember | Kies het standaardtransactieknooppunt van het Azure Blockchain Service-lid dat u in de vereiste hebt gemaakt.
    Verbindingsnaam | cosmosdb cosmosdb | Voer een unieke naam in van de uitgaande verbinding waar blockchain-transactiegegevens worden verzonden.
    Eindpunt van gebeurtenisraster | myTopic | Kies een gebeurtenisrasteronderwerp dat u in de vereiste hebt gemaakt. Opmerking: Het exemplaar Blockchain Data Manager en het gebeurtenisrasteronderwerp moeten in hetzelfde abonnement zitten.

1. Selecteer **OK**.

    Het duurt minder dan een minuut om een Blockchain Data Manager-exemplaar te maken. Nadat de instantie is geïmplementeerd, wordt deze automatisch gestart. Een actief blockchain-gegevensbeheerexemplaar legt blockchain-gebeurtenissen vast vanaf het transactieknooppunt en verzendt gegevens naar het gebeurtenisraster.

## <a name="add-application"></a>Toepassing toevoegen

Voeg de **helloblockchain** blockchain-toepassing toe, zodat Blockchain Data Manager degebeurtenis- en eigendomsstatus decodeert. Blockchain Data Manager vereist het slimme contract ABI- en bytecode-bestand om de toepassing toe te voegen.

### <a name="get-contract-abi-and-bytecode"></a>Ontvang contract ABI en bytecode

Het contract ABI definieert de slimme contractinterfaces. Het beschrijft hoe om te gaan met de slimme contract. U de [Azure Blockchain Development Kit voor Ethereum-extensie](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) gebruiken om het contract ABI naar het klembord te kopiëren.

1. Vouw in het deelvenster Visual Studio Code explorer de **map build/contracts** uit van het **helloblockchain** Solidity-project dat u hebt gemaakt in de vereiste [zelfstudie: Visual Studio Code gebruiken om slimme contracten te maken, te bouwen en te implementeren.](send-transaction.md)
1. Klik met de rechtermuisknop op het JSON-bestand met contractmetagegevens. De bestandsnaam is de slimme contractnaam gevolgd door de **.json** extensie.
1. Selecteer **Abi van contract kopiëren**.

    ![Deelvenster Visual Studio Code met de ABI-selectie van het contract kopiëren](./media/data-manager-cosmosdb/abi-devkit.png)

    Het contract ABI wordt gekopieerd naar het klembord.

1. Sla de **abi-array** op als een JSON-bestand. Bijvoorbeeld, *abi.json*. U gebruikt het bestand in een latere stap.

Blockchain Data Manager vereist de geïmplementeerde bytecode voor het slimme contract. De geïmplementeerde bytecode is anders dan de doordecode voor slimme contracten. U gebruikt de Azure blockchain-ontwikkelingskitextensie om de bytecode naar het klembord te kopiëren.

1. Vouw in het deelvenster Visual Studio Code explorer de map **build/contracts** van uw Solidity-project uit.
1. Klik met de rechtermuisknop op het JSON-bestand met contractmetagegevens. De bestandsnaam is de slimme contractnaam gevolgd door de **.json** extensie.
1. Selecteer **Pertecode voor transacties kopiëren**.

    ![Deelvenster Visual Studio-code met de selectie Transactiebytecode kopiëren](./media/data-manager-cosmosdb/bytecode-devkit.png)

    De bytecode wordt gekopieerd naar het klembord.

1. Sla de **bytecode-waarde** op als een JSON-bestand. *Bytecode.json*bijvoorbeeld . U gebruikt het bestand in een latere stap.

In het volgende voorbeeld worden *abi.json-* en *bytecode.json-bestanden* weergegeven die zijn geopend in de VS-codeeditor. Uw bestanden moeten er hetzelfde uitzien.

![Voorbeeld van abi.json- en bytecode.json-bestanden](./media/data-manager-cosmosdb/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>URL van contractABI en bytecode maken

Blockchain Data Manager vereist dat het contract ABI- en bytecodebestanden toegankelijk zijn via een URL bij het toevoegen van een toepassing. U een Azure Storage-account gebruiken om een privétoegankelijke URL op te geven.

#### <a name="create-storage-account"></a>Een opslagaccount maken

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>Contractbestanden uploaden

1. Maak een nieuwe container voor het opslagaccount. Selecteer **Containers > Container**.

    ![Een opslagaccountcontainer maken](./media/data-manager-cosmosdb/create-container.png)

    | Instelling | Beschrijving |
    |---------|-------------|
    | Name  | Geef de container een naam. Smartcontract *smartcontract* bijvoorbeeld |
    | Openbaar toegangsniveau | Privé *kiezen (geen anonieme toegang)* |

1. Selecteer **OK** om de container te maken.
1. Selecteer de container en selecteer **Uploaden**.
1. Kies beide JSON-bestanden die u hebt gemaakt in de sectie [Contract ABI en bytecode downloaden.](#get-contract-abi-and-bytecode)

    ![Blob uploaden](./media/data-manager-cosmosdb/upload-blobs.png)

    Selecteer **Uploaden**.

#### <a name="generate-url"></a>URL genereren

Genereer voor elke blob een handtekening voor gedeelde toegang.

1. Selecteer de ABI JSON-blob.
1. Selecteer **SAS genereren**
1. Stel de gewenste vervaldatum van de toegangshandtekening in en selecteer **Blob SAS-token en URL genereren**.

    ![SAS-token genereren](./media/data-manager-cosmosdb/generate-sas.png)

1. Kopieer de **Url van Blob SAS** en sla deze op voor de volgende sectie.
1. Herhaal de [stappen URL genereren](#generate-url) voor de JSON-blob van bytecode.

### <a name="add-helloblockchain-application-to-instance"></a>Helloblockchain-toepassing toevoegen aan instantie

1. Selecteer uw Blockchain Data Manager-exemplaar in de instantielijst.
1. Selecteer **Blockchain-toepassingen**.
1. Selecteer **Toevoegen**.

    ![Een blockchain-toepassing toevoegen](./media/data-manager-cosmosdb/add-application.png)

    Voer de naam van de blockchain-toepassing en het slimme contract ABI en bytecode URL's in.

    Instelling | Beschrijving
    --------|------------
    Name | Voer een unieke naam in voor de blockchain-toepassing om bij te houden.
    Contract ABI | URL-pad naar het BESTAND ABI van het contract. Zie [OBJECT ABI en URL van bytecode maken voor](#create-contract-abi-and-bytecode-url)meer informatie .
    Contractbytecode | URL-pad naar bytecodebestand. Zie [OBJECT ABI en URL van bytecode maken voor](#create-contract-abi-and-bytecode-url)meer informatie .

1. Selecteer **OK**.

    Zodra de toepassing is gemaakt, wordt de toepassing weergegeven in de lijst met blockchain-toepassingen.

    ![Blockchain-toepassingslijst](./media/data-manager-cosmosdb/artifact-list.png)

U het Azure Storage-account verwijderen of gebruiken om meer blockchain-toepassingen te configureren. Als u het Azure Storage-account wilt verwijderen, u de brongroep verwijderen. Als u de resourcegroep verwijdert, worden ook het bijbehorende opslagaccount en eventuele andere resources die zijn gekoppeld aan de resourcegroep, verwijderd.

## <a name="create-azure-cosmos-db"></a>Een Azure Cosmos DB-exemplaar maken

[!INCLUDE [cosmos-db-create-storage-account](../../../includes/cosmos-db-create-dbaccount.md)]

### <a name="add-a-database-and-container"></a>Een database en container toevoegen

U de Gegevensverkenner in de Azure-portal gebruiken om een database en container te maken.

1. Selecteer **Gegevensverkenner** op de linkernavigatie op uw Azure Cosmos DB-accountpagina en selecteer **Vervolgens Nieuwe container**.
1. Voer in het **containervenster Toevoegen** de instellingen voor de nieuwe container in.

    ![Containerinstellingen toevoegen](./media/data-manager-cosmosdb/add-container.png)

    | Instelling | Beschrijving
    |---------|-------------|
    | Database-id | Voer **blockchain-gegevens in** als de naam voor de nieuwe database. |
    | Doorvoer | Laat de doorvoer op **400** aanvraageenheden per seconde (RU/s). U kunt de doorvoer later opschalen als u de latentie wilt beperken.|
    | Container-id | Voer **Berichten** in als de naam voor uw nieuwe container. |
    | Partitiesleutel | Gebruik **/MessageType** als partitiesleutel. |

1. Selecteer **OK**. In de gegevensverkenner worden de nieuwe database en de container weergegeven die u hebt gemaakt.

## <a name="create-logic-app"></a>Een logische app maken

Azure Logic Apps helpt u bedrijfsprocessen en workflows te plannen en te automatiseren wanneer u systemen en services moet integreren. U een logische app gebruiken om Gebeurtenisraster te verbinden met Azure Cosmos DB.

1. Selecteer in [Azure Portal](https://portal.azure.com)**Een resource maken** > **Integratie** > **Logische app**.
1. Geef informatie over waar u uw logische app maken. Nadat u klaar bent, selecteert u **Maken**.

    Zie [Geautomatiseerde werkstromen maken met Azure Logic Apps](../../logic-apps/quickstart-create-first-logic-app-workflow.md)voor meer informatie over het maken van logische apps.

1. Nadat Azure uw app heeft geïmplementeerd, selecteert u uw logische app-bron.
1. Selecteer in de Logic Apps Designer onder **Sjablonen**de optie **Lege logica-app**.

### <a name="add-event-grid-trigger"></a>Trigger voor gebeurtenisraster toevoegen

Elke logische app moet beginnen met een trigger, die wordt geactiveerd wanneer er een bepaalde gebeurtenis plaatsvindt of wanneer er aan een bepaalde voorwaarde is voldaan. Telkens wanneer de trigger wordt geactiveerd, maakt de Logic Apps-engine een exemplaar van een logische app dat wordt gestart en de werkstroom uitvoert. Gebruik een Azure Event Grid-trigger om blockchain-transactiegegevens van Gebeurtenisraster naar Cosmos DB te verzenden.

1. Zoek in de Logic Apps Designer naar en selecteer de **Azure Event Grid-connector.**
1. Selecteer op het tabblad **Triggers** de optie **Wanneer een resourcegebeurtenis plaatsvindt**.
1. Maak een API-verbinding met uw gebeurtenisrasteronderwerp.

    ![Triggerinstellingen voor gebeurtenisrasters](./media/data-manager-cosmosdb/event-grid-trigger.png)

    | Instelling | Beschrijving
    |---------|-------------|
    | Abonnement | Kies het abonnement dat het onderwerp Gebeurtenisraster bevat. |
    | Resourcetype | Kies **Microsoft.EventGrid.Topics**. |
    | Resourcenaam | Kies de naam van het gebeurtenisrasteronderwerp waarin Blockchain Data Manager transactiegegevensberichten verzendt. |

### <a name="add-cosmos-db-action"></a>Cosmos DB-actie toevoegen

Voeg een actie toe om voor elke transactie een document in Cosmos DB te maken. Gebruik het type transactiebericht als partitiesleutel om de berichten te categoriseren.

1. Selecteer **Nieuwe stap**.
1. Zoek **op Een actie**kiezen naar Azure Cosmos **DB**.
1. Kies **Azure Cosmos DB > Acties > Document maken of bijwerken**.
1. Maak een API-verbinding met uw Cosmos DB-database.

    ![Cosmos DB-verbindingsinstellingen](./media/data-manager-cosmosdb/cosmosdb-connection.png)

    | Instelling | Beschrijving
    |---------|-------------|
    | Verbindingsnaam | Kies het abonnement dat het onderwerp Gebeurtenisraster bevat. |
    | DocumentDB-account | Kies het DocumentDB-account dat u hebt gemaakt in de sectie [Azure Cosmos DB-account maken.](#create-azure-cosmos-db) |

1. Voer de **database-id** en **verzamelings-id** in voor uw Azure Cosmos DB die u eerder hebt gemaakt in de sectie [Een database en container](#add-a-database-and-container) toevoegen.

1. Selecteer de **documentinstelling.** Selecteer **expressie** en kopieer en plak de volgende expressie in de pop-out *Dynamische inhoud toevoegen:*

    ```
    addProperty(triggerBody()?['data'], 'id', utcNow())
    ```

    De expressie krijgt het gegevensgedeelte van het bericht en stelt de ID in op een tijdstempelwaarde.

1. Selecteer **Nieuwe parameter toevoegen** en kies De waarde van de **partitiesleutel**.
1. Stel de waarde `"@{triggerBody()['data']['MessageType']}"`van de **partitiesleutel** in op . De waarde moet worden omgeven door dubbele aanhalingstekens.

    ![Logic Apps Designer met Cosmos DB-instellingen](./media/data-manager-cosmosdb/create-action.png)

    De waarde stelt de partitiesleutel in op het type transactiebericht.

1. Selecteer **Opslaan**.

De logische app bewaakt het gebeurtenisrasteronderwerp. Wanneer een nieuw transactiebericht wordt verzonden vanuit Blockchain Data Manager, maakt de logische app een document in Cosmos DB.

## <a name="send-a-transaction"></a>Een transactie verzenden

Stuur vervolgens een transactie naar het blockchain-grootboek om te testen wat u hebt gemaakt. Gebruik de **SendRequest-functie van** **het HelloBlockchain-contract** dat u hebt gemaakt in de vereiste [zelfstudie: Gebruik Visual Studio Code om slimme contracten te maken, te bouwen en te implementeren.](send-transaction.md)

1. Gebruik de pagina slimme contractinteractie van Azure Blockchain Development Kit om de functie **SendRequest** aan te roepen. Klik met de rechtermuisknop op **HelloBlockchain.sol** en kies **Pagina voor Slimme contractinteractie weergeven** in het menu.

    ![Kies Pagina voor slimme contractinteractie weergeven in het menu](./media/data-manager-cosmosdb/contract-interaction.png)

1. Kies **Contractactie SendRequest** en voer **Hello, Blockchain in!** voor de parameter **requestMessage.** Selecteer **Uitvoeren** om de functie **SendRequest** aan te roepen via een transactie.

    ![Actie SendRequest uitvoeren](./media/data-manager-cosmosdb/sendrequest-action.png)

Met de functie SendRequest worden de velden **RequestMessage** en **State** ingesteld. De huidige status voor **RequestMessage** is het argument dat u voorbij **Hello, Blockchain**. De **veldwaarde van de staat** blijft **Request**.

## <a name="view-transaction-data"></a>Transactiegegevens weergeven

Nu u uw Blockchain Data Manager hebt gekoppeld aan Azure Cosmos DB, u de blockchain-transactieberichten bekijken in Cosmos DB Data Explorer.

1. Ga naar de weergave Cosmos DB Data Explorer. Cosmosdb-blockchain > Data Explorer bijvoorbeeld **> blockchain-data > Berichten > Items**.

    ![Cosmos DB-gegevensverkenner](./media/data-manager-cosmosdb/data-explorer.png)

    Data Explorer bevat de blockchain-gegevensberichten die zijn gemaakt in de Cosmos DB-database.

1. Blader door de berichten door item-id te selecteren en vind het bericht met de overeenkomende transactiehash.

    [![Blockchain-transactiedetails](./media/data-manager-cosmosdb/raw-msg.png)](./media/data-manager-cosmosdb/raw-msg.png#lightbox)

    Het ruwe transactiebericht bevat details over de transactie. De eigendomsgegevens worden echter versleuteld.

    Sinds u het HelloBlockchain-slimme contract hebt toegevoegd aan het exemplaar Blockchain Data Manager, wordt er ook een **berichttype ContractProperties** verzonden dat gedecodeerde eigenschapsinformatie bevat.

1. Zoek het bericht **ContractProperties** voor de transactie. Het zou het volgende bericht in de lijst moeten zijn.

    [![Blockchain-transactiedetails](./media/data-manager-cosmosdb/properties-msg.png)](./media/data-manager-cosmosdb/properties-msg.png#lightbox)

    De array **DecodedProperties** bevat de eigenschappen van de transactie.

Gefeliciteerd! U hebt een transactieberichtverkenner gemaakt met Blockchain Data Manager en Azure Cosmos DB.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer dit niet meer nodig is, u de resources en resourcegroepen verwijderen die u voor deze zelfstudie hebt gebruikt. Een brongroep verwijderen:

1. Navigeer in de Azure-portal naar **de groep Resource** in het linkernavigatiedeelvenster en selecteer de resourcegroep die u wilt verwijderen.
1. Selecteer **Resourcegroep verwijderen**. Verwijder het verwijderen door de naam van de brongroep in te voeren en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over integreren met blockchain-grootboeken.

> [!div class="nextstepaction"]
> [De Ethereum Blockchain-connector gebruiken met Azure Logic Apps](ethereum-logic-app.md)
