---
title: Blockchain-gegevensbeheer configureren met Azure-portal - Azure Blockchain-service
description: Maak en beheer Blockchain Data Manager voor Azure Blockchain Service met behulp van de Azure-portal.
ms.date: 03/30/2020
ms.topic: article
ms.reviewer: ravastra
ms.openlocfilehash: beacef0660a253c90afb507618e8a1742f0c9d2d
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529603"
---
# <a name="configure-blockchain-data-manager-using-the-azure-portal"></a>Blockchain Data Manager configureren met behulp van Azure Portal

Configureer Blockchain Data Manager voor Azure Blockchain Service om blockchain-gegevens vast te leggen en deze naar een Azure Event Grid Topic te verzenden.

Als u een blockchain-gegevensbeheerexemplaar wilt configureren, gaat u als:

* Een Blockchain Data Manager-exemplaar maken voor een Azure Blockchain Service-transactieknooppunt
* Voeg uw blockchain-toepassingen toe

## <a name="prerequisites"></a>Vereisten

* Snelstart [voltooien: maak een blockchain-lid met behulp van de Azure-portal](create-member.md) of [Quickstart: maak een Azure Blockchain Service-blockchain-lid met Azure CLI](create-member-cli.md). Azure Blockchain Service *Standard-laag* wordt aanbevolen bij het gebruik van Blockchain Data Manager.
* Een [gebeurtenisrasteronderwerp maken](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* Meer informatie over [gebeurtenishandlers in Azure Event Grid](../../event-grid/event-handlers.md)

## <a name="create-instance"></a>Instantie maken

Een instantie voor Blockchain Data Manager verbindt en bewaakt een Azure Blockchain Service-transactieknooppunt. Alleen gebruikers met toegang tot het transactieknooppunt kunnen een verbinding maken. Een instantie legt alle ruwe blok- en ruwe transactiegegevens uit het transactieknooppunt vast.

Een uitgaande verbinding verzendt blockchain-gegevens naar Azure Event Grid. U configureert één uitgaande verbinding wanneer u de instantie maakt. Blockchain Data Manager ondersteunt meerdere uitgaande netwerkverbindingen voor een evenementraster voor een bepaald Blockchain Data Manager-exemplaar. U blockchain-gegevens naar één bestemming verzenden of blockchain-gegevens naar meerdere bestemmingen verzenden. Als u een andere bestemming wilt toevoegen, voegt u alleen extra uitgaande verbindingen toe aan de instantie.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Ga naar het Azure Blockchain Service-lid dat u wilt verbinden met Blockchain Data Manager. Selecteer **Blockchain Data Manager**.
1. Selecteer **Toevoegen**.

    ![Blockchain-gegevensbeheer toevoegen](./media/data-manager-portal/add-instance.png)

    Voer de volgende details in:

    Instelling | Beschrijving
    --------|------------
    Name | Voer een unieke naam in voor een verbonden Blockchain Data Manager. De naam Blockchain Data Manager kan kleine letters en cijfers bevatten en heeft een maximale lengte van 20 tekens.
    Transactieknooppunt | Kies een transactieknooppunt. Alleen transactieknooppunten die u hebt gelezen, worden weergegeven.
    Verbindingsnaam | Voer een unieke naam in van de uitgaande verbinding waar blockchain-transactiegegevens worden verzonden.
    Eindpunt van gebeurtenisraster | Kies een gebeurtenisrasteronderwerp in hetzelfde abonnement als het exemplaar Blockchain Data Manager.

1. Selecteer **OK**.

    Het duurt minder dan een minuut om een Blockchain Data Manager-exemplaar te maken. Nadat de instantie is geïmplementeerd, wordt deze automatisch gestart. Een actief blockchain-gegevensbeheerexemplaar legt blockchain-gebeurtenissen vast vanaf het transactieknooppunt en verzendt gegevens naar de uitgaande verbindingen.

    De nieuwe instantie wordt weergegeven in de lijst met Blockchain Data Manager-exemplaren voor het Azure Blockchain Service-lid.

    ![Lijst met blockchain-gegevenslidinstanties](./media/data-manager-portal/instance-list.png)

## <a name="add-blockchain-application"></a>Blockchain-toepassing toevoegen

Als u een blockchain-toepassing toevoegt, decodeert Blockchain Data Manager de gebeurtenis- en eigenschappenstatus voor de toepassing. Anders worden alleen ruwe blok- en ruwe transactiegegevens verzonden. Blockchain Data Manager detecteert ook contractadressen wanneer het contract wordt geïmplementeerd. U meerdere blockchain-toepassingen toevoegen aan een Blockchain Data Manager-exemplaar.

> [!IMPORTANT]
> Momenteel worden blockchain-toepassingen die [Solidity-arraytypen](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays) of [toewijzingstypen](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types) declareren, niet volledig ondersteund. Eigenschappen die zijn gedeclareerd als array- of toewijzingstypen, worden niet gedecodeerd in *ContractPropertiesMsg-* of *DecodedContractEventsMsg-berichten.*

Blockchain Data Manager vereist een slim contract ABI en geïmplementeerd bytecode bestand om de toepassing toe te voegen.

### <a name="get-contract-abi-and-bytecode"></a>Ontvang Contract ABI en bytecode

Het contract ABI definieert de slimme contractinterfaces. Het beschrijft hoe om te gaan met de slimme contract. U de [Azure Blockchain Development Kit voor Ethereum-extensie](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) gebruiken om het contract ABI naar het klembord te kopiëren.

1. Vouw in het deelvenster Visual Studio Code explorer de map **build/contracts** van uw Solidity-project uit.
1. Klik met de rechtermuisknop op het JSON-bestand met contractmetagegevens. De bestandsnaam is de slimme contractnaam gevolgd door de **.json** extensie.
1. Selecteer **Abi van contract kopiëren**.

    ![Deelvenster Visual Studio Code met de ABI-selectie van het contract kopiëren](./media/data-manager-portal/abi-devkit.png)

    Het contract ABI wordt gekopieerd naar het klembord.

1. Sla de **abi-array** op als een JSON-bestand. Bijvoorbeeld, *abi.json*. U gebruikt het bestand in een latere stap.

Blockchain Data Manager vereist de geïmplementeerde bytecode voor het slimme contract. De geïmplementeerde bytecode is anders dan de doordecode voor slimme contracten. U gebruikt de Azure blockchain-ontwikkelingskitextensie om de bytecode naar het klembord te kopiëren.

1. Vouw in het deelvenster Visual Studio Code explorer de map **build/contracts** van uw Solidity-project uit.
1. Klik met de rechtermuisknop op het JSON-bestand met contractmetagegevens. De bestandsnaam is de slimme contractnaam gevolgd door de **.json** extensie.
1. Selecteer **Pertecode voor transacties kopiëren**.

    ![Deelvenster Visual Studio-code met de selectie Transactiebytecode kopiëren](./media/data-manager-portal/bytecode-devkit.png)

    De bytecode wordt gekopieerd naar het klembord.

1. Sla de **bytecode-waarde** op als een JSON-bestand. *Bytecode.json*bijvoorbeeld . U gebruikt het bestand in een latere stap.

In het volgende voorbeeld worden *abi.json-* en *bytecode.json-bestanden* weergegeven die zijn geopend in de VS-codeeditor. Uw bestanden moeten er hetzelfde uitzien.

![Voorbeeld van abi.json- en bytecode.json-bestanden](./media/data-manager-portal/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>URL van contractABI en bytecode maken

Blockchain Data Manager vereist dat het contract ABI- en bytecodebestanden toegankelijk zijn via een URL bij het toevoegen van een toepassing. U een Azure Storage-account gebruiken om een privétoegankelijke URL op te geven.

#### <a name="create-storage-account"></a>Een opslagaccount maken

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>Contractbestanden uploaden

1. Maak een nieuwe container voor het opslagaccount. Selecteer **Containers > Container**.

    ![Een opslagaccountcontainer maken](./media/data-manager-portal/create-container.png)

    | Veld | Beschrijving |
    |-------|-------------|
    | Name  | Geef de container een naam. Smartcontract *smartcontract* bijvoorbeeld |
    | Openbaar toegangsniveau | Privé *kiezen (geen anonieme toegang)* |

1. Selecteer **OK** om de container te maken.
1. Selecteer de container en selecteer **Uploaden**.
1. Kies beide JSON-bestanden die u hebt gemaakt in de sectie [Contract ABI en bytecode downloaden.](#get-contract-abi-and-bytecode)

    ![Blob uploaden](./media/data-manager-portal/upload-blobs.png)

    Selecteer **Uploaden**.

#### <a name="generate-url"></a>URL genereren

Genereer voor elke blob een handtekening voor gedeelde toegang.

1. Selecteer de ABI JSON-blob.
1. Selecteer **SAS genereren**
1. Stel de gewenste vervaldatum van de toegangshandtekening in en selecteer **Blob SAS-token en URL genereren**.

    ![SAS-token genereren](./media/data-manager-portal/generate-sas.png)

1. Kopieer de **Url van Blob SAS** en sla deze op voor de volgende sectie.
1. Herhaal de [stappen URL genereren](#generate-url) voor de JSON-blob van bytecode.

### <a name="add-application-to-instance"></a>Toepassing toevoegen aan instantie

1. Selecteer uw Blockchain Data Manager-exemplaar in de instantielijst.
1. Selecteer **Blockchain-toepassingen**.
1. Selecteer **Toevoegen**.

    ![Een blockchain-toepassing toevoegen](./media/data-manager-portal/add-application.png)

    Voer de naam van de blockchain-toepassing en het slimme contract ABI en bytecode URL's in.

    Instelling | Beschrijving
    --------|------------
    Name | Voer een unieke naam in voor de blockchain-toepassing om bij te houden.
    Contract ABI | URL-pad naar het BESTAND ABI van het contract. Zie [OBJECT ABI en URL van bytecode maken voor](#create-contract-abi-and-bytecode-url)meer informatie .
    Contractbytecode | URL-pad naar bytecodebestand. Zie [OBJECT ABI en URL van bytecode maken voor](#create-contract-abi-and-bytecode-url)meer informatie .

1. Selecteer **OK**.

    Zodra de toepassing is gemaakt, wordt de toepassing weergegeven in de lijst met blockchain-toepassingen.

    ![Blockchain-toepassingslijst](./media/data-manager-portal/artifact-list.png)

U het Azure Storage-account verwijderen of gebruiken om meer blockchain-toepassingen te configureren. Als u het Azure Storage-account wilt verwijderen, u de brongroep verwijderen. Als u de resourcegroep verwijdert, worden ook het bijbehorende opslagaccount en eventuele andere resources die zijn gekoppeld aan de resourcegroep, verwijderd.

## <a name="stop-instance"></a>Instantie stoppen

Stop het blockchain-exemplaar wanneer u wilt stoppen met het vastleggen van blockchain-gebeurtenissen en het verzenden van gegevens naar de uitgaande verbindingen. Wanneer de instantie wordt gestopt, worden er geen kosten in rekening gebracht voor Blockchain Data Manager. Ga voor meer informatie naar het overzicht van [prijzen](https://azure.microsoft.com/pricing/details/blockchain-service).

1. Ga naar **Overzicht** en selecteer **Stoppen**.

    ![Instantie stoppen](./media/data-manager-portal/stop-instance.png)

## <a name="next-steps"></a>Volgende stappen

Probeer de volgende zelfstudie met het maken van een blockchain-transactieberichtverkenner met Blockchain Data Manager en Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Blockchain Data Manager gebruiken om gegevens te verzenden naar Azure Cosmos DB](data-manager-cosmosdb.md)