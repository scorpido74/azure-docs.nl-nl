---
title: Gebruik Block Chain Data Manager om Azure Cosmos DB-Azure Block Chain-service bij te werken
description: Gebruik Block Chain Data Manager voor de Azure Block Chain-service om Block Chain-gegevens te verzenden naar Azure Cosmos DB
ms.date: 12/04/2019
ms.topic: tutorial
ms.reviewer: chroyal
ms.openlocfilehash: 79c39d9883b5ba618e368b0ff6d3e95f1af5bd96
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977388"
---
# <a name="tutorial-use-blockchain-data-manager-to-send-data-to-azure-cosmos-db"></a>Zelf studie: Block Chain Data Manager gebruiken om gegevens te verzenden naar Azure Cosmos DB

In deze zelf studie gebruikt u Block Chain Data Manager voor de Azure Block Chain-service om Block Chain-transactie gegevens op te nemen in Azure Cosmos DB. Block Chain Data Manager legt, transformeert en levert Block Chain grootboek gegevens aan Azure Event Grid onderwerpen. Vanuit Azure Event Grid gebruikt u een Azure Logic app-connector om documenten te maken in een Azure Cosmos DB-Data Base. Wanneer u klaar bent met de zelf studie, kunt u Block Chain-transactie gegevens in Azure Cosmos DB Data Explorer verkennen.

[Details van Block Chain-trans actie ![](./media/data-manager-cosmosdb/raw-msg.png)](./media/data-manager-cosmosdb/raw-msg.png#lightbox)

In deze zelfstudie hebt u:

> [!div class="checklist"]
> * Een Block Chain Data Manager-exemplaar maken
> * Een Block Chain-toepassing toevoegen voor het decoderen van transactie-eigenschappen en-gebeurtenissen
> * Een Azure Cosmos DB-account en-data base maken om transactie gegevens op te slaan
> * Een Azure Logic-app maken om een Azure Event Grid onderwerp te koppelen aan Azure Cosmos DB
> * Een trans actie naar een Block Chain-groot boek verzenden
> * De gedecodeerde transactie gegevens in Azure Cosmos DB weer geven

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

* Volledige [Snelstartgids: Maak een Block Chain-lid met behulp van de Azure Portal](create-member.md) of [Quick Start: een Azure Block Chain Service Block Chain-lid maken met behulp van Azure cli](create-member-cli.md)
* Volledige [Snelstartgids: gebruik Visual Studio code om verbinding te maken met een Azure Block Chain Service consortium-netwerk](connect-vscode.md). De Snelstartgids helpt u bij het installeren [van Azure Block Chain Development Kit voor Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) en het instellen van uw Block Chain-ontwikkel omgeving.
* Volledige [zelf studie: gebruik Visual Studio code om slimme contracten te maken, bouwen en implementeren](send-transaction.md). In de zelf studie wordt uitgelegd hoe u een voor beeld van een slim contract maakt.
* Een [Event grid onderwerp](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) maken
* Meer informatie over [gebeurtenis-handlers in azure Event grid](../../event-grid/event-handlers.md)

## <a name="create-instance"></a>Exemplaar maken

Een Block Chain Data Manager-exemplaar maakt verbinding met een Azure Block Chain Service-transactie knooppunt en bewaakt deze. Een exemplaar legt alle onbewerkte blok keringen en ruwe transactie gegevens vast van het trans actie-knoop punt. Een uitgaande verbinding verzendt Block Chain-gegevens naar Azure Event Grid. Wanneer u het exemplaar maakt, configureert u één uitgaande verbinding.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Ga naar het lid van de Azure Block Chain-service dat u hebt gemaakt in de Snelstartgids voor vereisten [: Maak een Block Chain-lid met behulp van de Azure Portal](create-member.md). Selecteer **block chain data manager**.
1. Selecteer **Toevoegen**.

    ![Block Chain Data Manager toevoegen](./media/data-manager-cosmosdb/add-instance.png)

    Voer de volgende details in:

    Instelling | Voorbeeld | Beschrijving
    --------|---------|------------
    Naam | mywatcher | Voer een unieke naam in voor een verbonden Block Chain Data Manager.
    Transactie knooppunt | myblockchainmember | Kies het standaard transactie knooppunt van het lid van de Azure Block Chain-service dat u in de vereiste hebt gemaakt.
    Verbindingsnaam | cosmosdb | Voer een unieke naam in voor de uitgaande verbinding waar Block Chain-transactie gegevens worden verzonden.
    Event grid-eind punt | myTopic | Kies een event grid-onderwerp dat u in de vereiste hebt gemaakt. Opmerking: het block Chain Data Manager-exemplaar en het onderwerp Event grid moeten zich in hetzelfde abonnement bevindt.

1. Selecteer **OK**.

    Het duurt minder dan een minuut om een Block Chain Data Manager-exemplaar te maken. Nadat het exemplaar is geïmplementeerd, wordt het automatisch gestart. Een actief Block Chain Data Manager-exemplaar legt Block Chain-gebeurtenissen vast van het trans actie-knoop punt en verzendt gegevens naar Event grid.

## <a name="add-application"></a>Toepassing toevoegen

Voeg de **helloblockchain** Block Chain-toepassing toe, zodat Block Chain Data Manager decodeert gebeurtenis en eigenschaps status. Block Chain Data Manager vereist het ABI en byte code-bestand van het Smart contract om de toepassing toe te voegen.

### <a name="get-contract-abi-and-bytecode"></a>ABI en byte code van het contract ophalen

In het contract ABI worden de slimme contract interfaces gedefinieerd. Hierin wordt beschreven hoe u met het slimme contract communiceert. U kunt de [Azure Block Chain Development Kit voor Ethereum-extensie](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) gebruiken om de contract Abi naar het klem bord te kopiëren.

1. Vouw in het deel venster Visual Studio code Explorer de map **Build/contract** van het **helloblockchain** volheid-project uit dat u hebt gemaakt in de hand [leiding voor vereisten: gebruik Visual Studio code om slimme contracten te maken, te bouwen en te implementeren](send-transaction.md).
1. Klik met de rechter muisknop op het JSON-bestand met meta gegevens van het contract. De bestands naam is de naam van het slimme contract gevolgd door de extensie **. json** .
1. Selecteer **contract Abi kopiëren**.

    ![Visual Studio code-deel venster met de selectie van het Kopieer contract ABI](./media/data-manager-cosmosdb/abi-devkit.png)

    Het contract ABI wordt gekopieerd naar het klem bord.

1. Sla de **Abi** -matrix op als een JSON-bestand. Bijvoorbeeld *Abi. json*. U gebruikt het bestand in een latere stap.

Voor Block Chain Data Manager is de geïmplementeerde byte code voor het slimme contract vereist. De geïmplementeerde byte code wijkt af van de byte code van het Smart-contract. U gebruikt de Azure Block Chain Development Kit-extensie om de byte code naar het klem bord te kopiëren.

1. Vouw in het deel venster Visual Studio code Explorer de map **Build/contract** van het volheid-project uit.
1. Klik met de rechter muisknop op het JSON-bestand met meta gegevens van het contract. De bestands naam is de naam van het slimme contract gevolgd door de extensie **. json** .
1. Selecteer de **byte code van de kopieer actie**.

    ![Deel venster Visual Studio met de optie byte code ring kopiëren](./media/data-manager-cosmosdb/bytecode-devkit.png)

    De byte code wordt gekopieerd naar het klem bord.

1. Sla de **byte code** -waarde op als een JSON-bestand. Bijvoorbeeld: *byte code. json*. U gebruikt het bestand in een latere stap.

In het volgende voor beeld ziet u de bestanden *Abi. json* en *byte code. json* die zijn geopend in de VS code-editor. Uw bestanden moeten er ongeveer als volgt uitzien.

![Voor beeld van Abi. json en byte code. json-bestanden](./media/data-manager-cosmosdb/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>Een contract-ABI en byte code-URL maken

Block Chain Data Manager vereist dat de ABI-en byte code-bestanden van het contract toegankelijk zijn voor een URL wanneer u een toepassing toevoegt. U kunt een Azure Storage-account gebruiken om een privé toegankelijke URL op te geven.

#### <a name="create-storage-account"></a>Een opslagaccount maken

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>Contract bestanden uploaden

1. Maak een nieuwe container voor het opslag account. Selecteer **Containers > container**.

    ![Een opslag account container maken](./media/data-manager-cosmosdb/create-container.png)

    | Instelling | Beschrijving |
    |---------|-------------|
    | Naam  | Geef de container een naam. Bijvoorbeeld *smartcontract* |
    | Openbaar toegangsniveau | Kies *privé (geen anonieme toegang)* |

1. Selecteer **OK** om de container te maken.
1. Selecteer de container en selecteer vervolgens **uploaden**.
1. Kies beide JSON-bestanden die u hebt gemaakt in de sectie [contract Abi en byte code ophalen](#get-contract-abi-and-bytecode) .

    ![Blob uploaden](./media/data-manager-cosmosdb/upload-blobs.png)

    Selecteer **Uploaden**.

#### <a name="generate-url"></a>URL genereren

Genereer een gedeelde toegangs handtekening voor elke blob.

1. Selecteer de JSON-BLOB ABI.
1. Selecteer **SAS genereren**
1. Stel de gewenste verval datum voor toegangs handtekeningen in en selecteer vervolgens **BLOB-SAS-token en URL genereren**.

    ![SAS-token genereren](./media/data-manager-cosmosdb/generate-sas.png)

1. Kopieer de **BLOB SAS-URL** en sla deze op in de volgende sectie.
1. Herhaal de stappen voor het genereren van de [URL](#generate-url) voor de byte code-JSON-blob.

### <a name="add-helloblockchain-application-to-instance"></a>Helloblockchain-toepassing toevoegen aan instantie

1. Selecteer uw Block Chain-Data Manager-exemplaar in de lijst met exemplaren.
1. Selecteer **Block Chain-toepassingen**.
1. Selecteer **Toevoegen**.

    ![Een Block Chain-toepassing toevoegen](./media/data-manager-cosmosdb/add-application.png)

    Voer de naam van de Block Chain-toepassing en de ABI-en byte code-Url's van het slimme contract in.

    Instelling | Beschrijving
    --------|------------
    Naam | Voer een unieke naam in voor de Block Chain-toepassing die u wilt bijhouden.
    ABI contract | URL-pad naar het ABI-bestand van het contract. Zie [Create contract Abi en byte code URL](#create-contract-abi-and-bytecode-url)(Engelstalig) voor meer informatie.
    Byte code van contract | URL-pad naar het byte code-bestand. Zie [Create contract Abi en byte code URL](#create-contract-abi-and-bytecode-url)(Engelstalig) voor meer informatie.

1. Selecteer **OK**.

    Zodra de toepassing is gemaakt, wordt de toepassing weer gegeven in de lijst met block Chain-toepassingen.

    ![Block Chain-toepassings lijst](./media/data-manager-cosmosdb/artifact-list.png)

U kunt het Azure Storage-account verwijderen of gebruiken om meer Block Chain-toepassingen te configureren. Als u het Azure Storage account wilt verwijderen, kunt u de resource groep verwijderen. Als u de resourcegroep verwijdert, worden ook het bijbehorende opslagaccount en eventuele andere resources die zijn gekoppeld aan de resourcegroep, verwijderd.

## <a name="create-azure-cosmos-db"></a>Azure Cosmos DB maken

[!INCLUDE [cosmos-db-create-storage-account](../../../includes/cosmos-db-create-dbaccount.md)]

### <a name="add-a-database-and-container"></a>Een Data Base en container toevoegen

U kunt de Data Explorer in de Azure Portal gebruiken om een Data Base en container te maken.

1. Selecteer **Data Explorer** in de linkernavigatiebalk op de pagina Azure Cosmos DB-account en selecteer vervolgens **nieuwe container**.
1. Voer in het deel venster **container toevoegen** de instellingen voor de nieuwe container in.

    ![Container instellingen toevoegen](./media/data-manager-cosmosdb/add-container.png)

    | Instelling | Beschrijving
    |---------|-------------|
    | Database-id | Voer **Block chain-data** in als de naam voor de nieuwe data base. |
    | Doorvoer | De door Voer bij **400** aanvraag eenheden per seconde (ru/s) behouden. U kunt de doorvoer later opschalen als u de latentie wilt beperken.|
    | Container-id | Voer **berichten** in als de naam voor de nieuwe container. |
    | Partitiesleutel | Gebruik **/MessageType** als partitie sleutel. |

1. Selecteer **OK**. In de Data Explorer worden de nieuwe data base en de door u gemaakte container weer gegeven.

## <a name="create-logic-app"></a>Een logische app maken

Azure Logic Apps helpt u bij het plannen en automatiseren van bedrijfs processen en werk stromen wanneer u systemen en services moet integreren. U kunt een logische app gebruiken om Event Grid te verbinden met Azure Cosmos DB.

1. Selecteer in [Azure Portal](https://portal.azure.com) **Een resource maken** > **Integratie** > **Logische app**.
1. Geef details op over het maken van uw logische app. Wanneer u klaar bent, selecteert u **maken**.

    Zie [automatische werk stromen maken met Azure Logic apps](../../logic-apps/quickstart-create-first-logic-app-workflow.md)voor meer informatie over het maken van logische apps.

1. Nadat Azure uw app heeft geïmplementeerd, selecteert u de resource van de logische app.
1. Selecteer in de ontwerp functie voor Logic Apps onder **sjablonen**de optie **lege logische app**.

### <a name="add-event-grid-trigger"></a>Event Grid trigger toevoegen

Elke logische app moet beginnen met een trigger, die wordt geactiveerd wanneer er een bepaalde gebeurtenis plaatsvindt of wanneer er aan een bepaalde voorwaarde is voldaan. Telkens wanneer de trigger wordt geactiveerd, maakt de Logic Apps-engine een exemplaar van een logische app dat wordt gestart en de werkstroom uitvoert. Gebruik een Azure Event Grid trigger om Block Chain-transactie gegevens van Event Grid naar Cosmos DB te verzenden.

1. Zoek en selecteer in de Logic Apps Designer de **Azure Event grid** -connector.
1. Selecteer op het tabblad **Triggers** **Wanneer een resource gebeurtenis optreedt**.
1. Een API-verbinding maken met uw Event Grid onderwerp.

    ![Trigger instellingen voor gebeurtenis raster](./media/data-manager-cosmosdb/event-grid-trigger.png)

    | Instelling | Beschrijving
    |---------|-------------|
    | Abonnement | Kies het abonnement dat het Event Grid onderwerp bevat. |
    | Resourcetype | Kies **micro soft. EventGrid. topics**. |
    | Naam resource | Kies de naam van het Event Grid onderwerp waarin Block Chain Data Manager transactie gegevens berichten verzendt. |

### <a name="add-cosmos-db-action"></a>Cosmos DB actie toevoegen

Voeg een actie toe om een document in Cosmos DB te maken voor elke trans actie. Gebruik het transactie bericht type als de partitie sleutel om de berichten te categoriseren.

1. Selecteer **Nieuwe stap**.
1. Zoek naar **Azure Cosmos DB**op **een actie kiezen**.
1. Kies **Azure Cosmos DB > acties > document maken of bijwerken**.
1. Een API-verbinding maken met uw Cosmos DB-Data Base.

    ![Verbindings instellingen Cosmos DB](./media/data-manager-cosmosdb/cosmosdb-connection.png)

    | Instelling | Beschrijving
    |---------|-------------|
    | Verbindingsnaam | Kies het abonnement dat het Event Grid onderwerp bevat. |
    | DocumentDB-account | Kies het DocumentDB-account dat u hebt gemaakt in de sectie [Azure Cosmos DB-account maken](#create-azure-cosmos-db) . |

1. Voer de **Data Base-id** en de **verzamelings-id** in voor uw Azure Cosmos DB die u eerder hebt gemaakt in de sectie [een Data Base en container toevoegen](#add-a-database-and-container) .

1. Selecteer de **document** instelling. In de pop-out *dynamische inhoud toevoegen* selecteert u **expressie** en kopieert en plakt u de volgende expressie:

    ```
    addProperty(triggerBody()?['data'], 'id', utcNow())
    ```

    Met de expressie wordt het gegevens gedeelte van het bericht opgehaald en wordt de ID ingesteld op een time stamp-waarde.

1. Selecteer **nieuwe para meter toevoegen** en kies **partitie sleutel waarde**.
1. Stel de **partitie sleutel waarde** in op `"@{triggerBody()['data']['MessageType']}"`. De waarde moet tussen dubbele aanhalings tekens worden geplaatst.

    ![Logic Apps Designer met Cosmos DB instellingen](./media/data-manager-cosmosdb/create-action.png)

    Met de waarde wordt de partitie sleutel ingesteld op het type transactie bericht.

1. Selecteer **Opslaan**.

De logische app bewaakt het Event Grid onderwerp. Wanneer een nieuw transactie bericht wordt verzonden vanuit Block Chain Data Manager, maakt de logische app een document in Cosmos DB.

## <a name="send-a-transaction"></a>Een trans actie verzenden

Vervolgens verzendt u een trans actie naar het block Chain-groot boek om te testen wat u hebt gemaakt. Gebruik het script **SendRequest. js** dat u in de vereiste [zelf studie hebt gemaakt: Visual Studio code gebruiken om slimme contracten te maken, te bouwen en te implementeren](send-transaction.md).

Gebruik in het Terminal venster van VS code Truffle om het script uit te voeren op uw consortium Block chain-netwerk. Selecteer in de menu balk van het Terminal venster het tabblad **Terminal** en **Power shell** in de vervolg keuzelijst.

``` PowerShell
truffle exec sendrequest.js --network <blockchain network>
```

Vervang \<Block chain-netwerk\> door de naam van het block chain-netwerk dat is gedefinieerd in **Truffle-config. js**.

![Trans actie verzenden](./media/data-manager-cosmosdb/send-request.png)

## <a name="view-transaction-data"></a>Transactie gegevens weer geven

Nu u de Block Chain-Data Manager hebt verbonden met Azure Cosmos DB, kunt u de Block Chain-transactie berichten bekijken in Cosmos DB Data Explorer.

1. Ga naar de weer gave Cosmos DB Data Explorer. Bijvoorbeeld **cosmosdb-block chain > Data Explorer > Block Chain-Data >-berichten > items**.

    ![Cosmos DB Data Explorer](./media/data-manager-cosmosdb/data-explorer.png)

    Data Explorer geeft een lijst van de Block Chain-gegevens berichten die zijn gemaakt in de Cosmos DB-Data Base.

1. Blader door de berichten door item-ID te selecteren en het bericht te zoeken met de overeenkomende trans actie-hash.

    [Details van Block Chain-trans actie ![](./media/data-manager-cosmosdb/raw-msg.png)](./media/data-manager-cosmosdb/raw-msg.png#lightbox)

    Het bericht van de onbewerkte trans actie bevat details over de trans actie. De informatie over de eigenschap is echter versleuteld.

    Omdat u het HelloBlockchain-slimme contract hebt toegevoegd aan het block Chain Data Manager-exemplaar, wordt er ook een **ContractProperties** -bericht type verzonden dat gedecodeerde eigenschaps informatie bevat.

1. Zoek het **ContractProperties** -bericht voor de trans actie. Dit moet het volgende bericht in de lijst zijn.

    [Details van Block Chain-trans actie ![](./media/data-manager-cosmosdb/properties-msg.png)](./media/data-manager-cosmosdb/properties-msg.png#lightbox)

    De **DecodedProperties** -matrix bevat de eigenschappen van de trans actie.

Gefeliciteerd! U hebt een transactie bericht Verkenner gemaakt met behulp van Block Chain Data Manager en Azure Cosmos DB.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de resources en resource groepen die u voor deze zelf studie hebt gebruikt, verwijderen wanneer u deze niet meer nodig hebt. Een resource groep verwijderen:

1. In de Azure-portal, gaat u naar **resourcegroep** in het navigatiedeelvenster links en selecteert u de resourcegroep die u wilt verwijderen.
1. Selecteer **Resourcegroep verwijderen**. Bevestig de verwijdering door de naam van de resourcegroep in te voeren en selecteer **verwijderen**.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het integreren met block Chain-grootten.

> [!div class="nextstepaction"]
> [De Ethereum Block Chain-connector gebruiken met Azure Logic Apps](ethereum-logic-app.md)
