---
title: Block Chain configureren Data Manager met behulp van Azure Portal-Azure Block Chain-Service
description: Block Chain Data Manager voor de Azure Block Chain-service maken en beheren met behulp van de Azure Portal.
ms.date: 03/30/2020
ms.topic: article
ms.reviewer: ravastra
ms.openlocfilehash: 08f5a4a807087afce13dd4a6e96c0e9dd0a36103
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81260595"
---
# <a name="configure-blockchain-data-manager-using-the-azure-portal"></a>Blockchain Data Manager configureren met behulp van Azure Portal

Configureer Block Chain Data Manager voor de Azure Block Chain-service om Block Chain-gegevens vast te leggen en deze naar een Azure Event Grid onderwerp te verzenden.

Als u een Block Chain Data Manager-exemplaar wilt configureren, doet u het volgende:

* Een Block Chain Data Manager-exemplaar maken voor een Azure Block Chain Service-transactie knooppunt
* Uw Block Chain-toepassingen toevoegen

## <a name="prerequisites"></a>Vereisten

* Volledige [Snelstartgids: Maak een Block Chain-lid met behulp van de Azure Portal](create-member.md) of [Quick Start: een Azure Block Chain Service Block Chain-lid maken met behulp van Azure cli](create-member-cli.md). De *Standard* -laag van de Azure Block Chain-service wordt aanbevolen bij het gebruik van Block Chain Data Manager.
* Een [Event grid onderwerp](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) maken
* Meer informatie over [gebeurtenis-handlers in azure Event grid](../../event-grid/event-handlers.md)

## <a name="create-instance"></a>Exemplaar maken

Een Block Chain Data Manager-exemplaar maakt verbinding met een Azure Block Chain Service-transactie knooppunt en bewaakt deze. Alleen gebruikers met toegang tot het transactie knooppunt kunnen een verbinding maken. Een exemplaar legt alle onbewerkte blok keringen en ruwe transactie gegevens vast van het trans actie-knoop punt. Block Chain Data Manager een **RawBlockAndTransactionMsg** -bericht publiceert dat een hoofd verzameling informatie is die wordt geretourneerd door web3. ETH [GetBlock](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#getblock) -en [getTransaction](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#gettransaction) -query's.

Een uitgaande verbinding verzendt Block Chain-gegevens naar Azure Event Grid. Wanneer u het exemplaar maakt, configureert u één uitgaande verbinding. Block Chain Data Manager ondersteunt meerdere uitgaande verbindingen van Event Grid onderwerp voor een gegeven block Chain Data Manager exemplaar. U kunt Block Chain-gegevens naar één bestemming verzenden of block Chain-gegevens verzenden naar meerdere bestemmingen. Als u een andere bestemming wilt toevoegen, voegt u extra uitgaande verbindingen toe aan het exemplaar.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Ga naar het lid van de Azure Block Chain-service waarmee u verbinding wilt maken met block Chain Data Manager. Selecteer **block chain data manager**.
1. Selecteer **Toevoegen**.

    ![Block Chain Data Manager toevoegen](./media/data-manager-portal/add-instance.png)

    Voer de volgende details in:

    Instelling | Beschrijving
    --------|------------
    Naam | Voer een unieke naam in voor een verbonden Block Chain Data Manager. De naam van de Block Chain-Data Manager mag kleine letters en cijfers bevatten en mag Maxi maal 20 tekens lang zijn.
    Transactie knooppunt | Kies een transactie knooppunt. Alleen transactie knooppunten waarvoor u lees toegang hebt, worden weer gegeven.
    Verbindingsnaam | Voer een unieke naam in voor de uitgaande verbinding waar Block Chain-transactie gegevens worden verzonden.
    Event grid-eind punt | Kies een event grid-onderwerp in hetzelfde abonnement als de Block Chain-Data Manager instantie.

1. Selecteer **OK**.

    Het duurt minder dan een minuut om een Block Chain Data Manager-exemplaar te maken. Nadat het exemplaar is geïmplementeerd, wordt het automatisch gestart. Een actief Block Chain Data Manager-exemplaar legt Block Chain-gebeurtenissen vast van het trans actie-knoop punt en verzendt gegevens naar de uitgaande verbindingen.

    De nieuwe instantie wordt weer gegeven in de lijst met block Chain-Data Manager instanties voor het lid van de Azure Block Chain-service.

    ![Lijst met instanties van Block Chain-gegevens leden](./media/data-manager-portal/instance-list.png)

## <a name="add-blockchain-application"></a>Block Chain-toepassing toevoegen

Als u een Block Chain-toepassing toevoegt, Block Chain Data Manager de status van de gebeurtenis en eigenschap decoderen voor de toepassing. Anders worden alleen onbewerkte en onbewerkte transactie gegevens verzonden. Block Chain Data Manager detecteert ook contract adressen wanneer het contract wordt geïmplementeerd. U kunt meerdere Block Chain-toepassingen toevoegen aan een Block Chain Data Manager-exemplaar.

> [!IMPORTANT]
> Momenteel worden Block Chain-toepassingen die een type vaste- [matrix](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays) of [toewijzings typen](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types) declareren, niet volledig ondersteund. Eigenschappen die zijn gedeclareerd als matrix-of toewijzings typen, worden niet gedecodeerd in *ContractPropertiesMsg* -of *DecodedContractEventsMsg* -berichten.

Voor Block Chain Data Manager is een ABI en een geïmplementeerd byte code-bestand vereist om de toepassing toe te voegen.

### <a name="get-contract-abi-and-bytecode"></a>ABI en byte code van het contract ophalen

In het contract ABI worden de slimme contract interfaces gedefinieerd. Hierin wordt beschreven hoe u met het slimme contract communiceert. U kunt de [Azure Block Chain Development Kit voor Ethereum-extensie](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) gebruiken om de contract Abi naar het klem bord te kopiëren.

1. Vouw in het deel venster Visual Studio code Explorer de map **Build/contract** van het volheid-project uit.
1. Klik met de rechter muisknop op het JSON-bestand met meta gegevens van het contract. De bestands naam is de naam van het slimme contract gevolgd door de extensie **. json** .
1. Selecteer **contract Abi kopiëren**.

    ![Visual Studio code-deel venster met de selectie van het Kopieer contract ABI](./media/data-manager-portal/abi-devkit.png)

    Het contract ABI wordt gekopieerd naar het klem bord.

1. Sla de **Abi** -matrix op als een JSON-bestand. Bijvoorbeeld *Abi. json*. U gebruikt het bestand in een latere stap.

Voor Block Chain Data Manager is de geïmplementeerde byte code voor het slimme contract vereist. De geïmplementeerde byte code wijkt af van de byte code van het Smart-contract. U gebruikt de Azure Block Chain Development Kit-extensie om de byte code naar het klem bord te kopiëren.

1. Vouw in het deel venster Visual Studio code Explorer de map **Build/contract** van het volheid-project uit.
1. Klik met de rechter muisknop op het JSON-bestand met meta gegevens van het contract. De bestands naam is de naam van het slimme contract gevolgd door de extensie **. json** .
1. Selecteer de **byte code van de kopieer actie**.

    ![Deel venster Visual Studio met de optie byte code ring kopiëren](./media/data-manager-portal/bytecode-devkit.png)

    De byte code wordt gekopieerd naar het klem bord.

1. Sla de **byte code** -waarde op als een JSON-bestand. Bijvoorbeeld: *byte code. json*. U gebruikt het bestand in een latere stap.

In het volgende voor beeld ziet u de bestanden *Abi. json* en *byte code. json* die zijn geopend in de VS code-editor. Uw bestanden moeten er ongeveer als volgt uitzien.

![Voor beeld van Abi. json en byte code. json-bestanden](./media/data-manager-portal/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>Een contract-ABI en byte code-URL maken

Block Chain Data Manager vereist dat de ABI-en byte code-bestanden van het contract toegankelijk zijn voor een URL wanneer u een toepassing toevoegt. U kunt een Azure Storage-account gebruiken om een privé toegankelijke URL op te geven.

#### <a name="create-storage-account"></a>Een opslagaccount maken

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>Contract bestanden uploaden

1. Maak een nieuwe container voor het opslag account. Selecteer **Containers > container**.

    ![Een opslag account container maken](./media/data-manager-portal/create-container.png)

    | Veld | Beschrijving |
    |-------|-------------|
    | Naam  | Geef de container een naam. Bijvoorbeeld *smartcontract* |
    | Openbaar toegangsniveau | Kies *privé (geen anonieme toegang)* |

1. Selecteer **OK** om de container te maken.
1. Selecteer de container en selecteer vervolgens **uploaden**.
1. Kies beide JSON-bestanden die u hebt gemaakt in de sectie [contract Abi en byte code ophalen](#get-contract-abi-and-bytecode) .

    ![BLOB uploaden](./media/data-manager-portal/upload-blobs.png)

    Selecteer **Uploaden**.

#### <a name="generate-url"></a>URL genereren

Genereer een gedeelde toegangs handtekening voor elke blob.

1. Selecteer de JSON-BLOB ABI.
1. Selecteer **SAS genereren**
1. Stel de gewenste verval datum voor toegangs handtekeningen in en selecteer vervolgens **BLOB-SAS-token en URL genereren**.

    ![SAS-token genereren](./media/data-manager-portal/generate-sas.png)

1. Kopieer de **BLOB SAS-URL** en sla deze op in de volgende sectie.
1. Herhaal de stappen voor het genereren van de [URL](#generate-url) voor de byte code-JSON-blob.

### <a name="add-application-to-instance"></a>Toepassing toevoegen aan exemplaar

1. Selecteer uw Block Chain-Data Manager-exemplaar in de lijst met exemplaren.
1. Selecteer **Block Chain-toepassingen**.
1. Selecteer **Toevoegen**.

    ![Een Block Chain-toepassing toevoegen](./media/data-manager-portal/add-application.png)

    Voer de naam van de Block Chain-toepassing en de ABI-en byte code-Url's van het slimme contract in.

    Instelling | Beschrijving
    --------|------------
    Naam | Voer een unieke naam in voor de Block Chain-toepassing die u wilt bijhouden.
    ABI contract | URL-pad naar het ABI-bestand van het contract. Zie [Create contract Abi en byte code URL](#create-contract-abi-and-bytecode-url)(Engelstalig) voor meer informatie.
    Byte code van contract | URL-pad naar het byte code-bestand. Zie [Create contract Abi en byte code URL](#create-contract-abi-and-bytecode-url)(Engelstalig) voor meer informatie.

1. Selecteer **OK**.

    Zodra de toepassing is gemaakt, wordt de toepassing weer gegeven in de lijst met block Chain-toepassingen.

    ![Block Chain-toepassings lijst](./media/data-manager-portal/artifact-list.png)

U kunt het Azure Storage-account verwijderen of gebruiken om meer Block Chain-toepassingen te configureren. Als u het Azure Storage account wilt verwijderen, kunt u de resource groep verwijderen. Als u de resourcegroep verwijdert, worden ook het bijbehorende opslagaccount en eventuele andere resources die zijn gekoppeld aan de resourcegroep, verwijderd.

## <a name="stop-instance"></a>Exemplaar stoppen

Stop het block chain manager-exemplaar wanneer u wilt stoppen met het vastleggen van Block Chain-gebeurtenissen en het verzenden van gegevens naar de uitgaande verbindingen. Wanneer het exemplaar wordt gestopt, worden er geen kosten in rekening gebracht voor Block Chain Data Manager. Ga voor meer informatie naar het overzicht van [prijzen](https://azure.microsoft.com/pricing/details/blockchain-service).

1. Ga naar **overzicht** en selecteer **stoppen**.

    ![Exemplaar stoppen](./media/data-manager-portal/stop-instance.png)

## <a name="next-steps"></a>Volgende stappen

Probeer de volgende zelf studie om een Block Chain-transactie bericht Verkenner te maken met behulp van Block Chain Data Manager en Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Blockchain Data Manager gebruiken om gegevens te verzenden naar Azure Cosmos DB](data-manager-cosmosdb.md)