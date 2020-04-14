---
title: Blockchain-gegevensbeheer configureren met Azure CLI - Azure Blockchain-service
description: Een Blockchain-gegevensbeheer voor Azure Blockchain-service maken en beheren met Azure CLI
ms.date: 03/30/2020
ms.topic: article
ms.reviewer: ravastra
ms.openlocfilehash: e490803fabeed7d6234bd6984acbfb9f5270e0c0
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81254407"
---
# <a name="configure-blockchain-data-manager-using-azure-cli"></a>Blockchain Data Manager configureren met behulp van Azure CLI

Configureer Blockchain Data Manager voor Azure Blockchain Service om blockchain-gegevens vast te leggen en deze naar een Azure Event Grid Topic te sturen.

Als u een blockchain-gegevensbeheerexemplaar wilt configureren, gaat u als:

* Een Blockchain Manager-exemplaar maken
* Een invoer maken voor een Azure Blockchain Service-transactieknooppunt
* Een uitvoer maken naar een Azure-gebeurtenisrasteronderwerp
* Een blockchain-toepassing toevoegen
* Een instantie starten

## <a name="prerequisites"></a>Vereisten

* Installeer de nieuwste [Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) CLI `az login`en ben aangemeld met behulp van .
* Snelstart [voltooien: gebruik Visual Studio Code om verbinding te maken met een Azure Blockchain Service consortiumnetwerk.](connect-vscode.md) Azure Blockchain Service *Standard-laag* wordt aanbevolen bij het gebruik van Blockchain Data Manager.
* Een [gebeurtenisrasteronderwerp maken](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* Meer informatie over [gebeurtenishandlers in Azure Event Grid](../../event-grid/event-handlers.md)

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell starten

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account.

Als u Cloud Shell wilt openen, selecteert u **Proberen** in de rechterbovenhoek van een codeblok. U Cloud Shell ook starten op [https://shell.azure.com/bash](https://shell.azure.com/bash)een apart browsertabblad door naar. Klik op **Kopiëren** om de codeblokken te kopiëren, plak deze in Cloud Shell en druk vervolgens op Enter om de code uit te voeren.

Als u de CLI liever lokaal installeert en gebruikt, vereist deze quickstart Azure CLI-versie 2.0.51 of hoger. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli)als u moet installeren of upgraden.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep maken met de opdracht [az group create](https://docs.microsoft.com/cli/azure/group). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* op de *locatie Eastus geaald:*

```azurecli-interactive
az group create --name myRG --location eastus
```

## <a name="create-instance"></a>Instantie maken

Een instantie Voor Blockchain Data Manager bewaakt een Azure Blockchain Service-transactieknooppunt. Een instantie legt alle ruwe blok- en ruwe transactiegegevens uit het transactieknooppunt vast. Blockchain Data Manager publiceert een **RawBlockAndTransactionMsg-bericht** dat een superset van informatie is die is geretourneerd van web3.eth [getBlock](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#getblock) en getTransaction-query's. [getTransaction](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#gettransaction)

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Blockchain Data Manager instance name> \
                   --resource-type Microsoft.blockchain/watchers \
                   --is-full-object \
                   --properties <watcher resource properties>
```

| Parameter | Beschrijving |
|-----------|-------------|
| resource-group | Resourcegroepnaam waar u het exemplaar Blockchain Data Manager maken. |
| name | Naam van het exemplaar Blockchain Data Manager. |
| resourcetype | Het resourcetype voor een Blockchain Data Manager-exemplaar is **Microsoft.blockchain/watchers.** |
| is-full-object | Geeft aan dat eigenschappen opties bevatten voor de watcher-bron. |
| properties | TEKENREEKS MET JSON-indeling met eigenschappen voor de watcher-resource. Kan worden doorgegeven als een string of een bestand.  |

### <a name="create-instance-examples"></a>Voorbeelden van voorbeelden maken

Json-configuratievoorbeeld om een Blockchain Manager-exemplaar te maken in de regio **Oost-VS.**

``` json
{
    "location": "eastus",
    "properties": {
    }
}
```

| Element | Beschrijving |
|---------|-------------|
| location | Regio waar u de watcher-bron maken |
| properties | Eigenschappen die u moet instellen bij het maken van de watcher-bron |

Maak een Blockchain Data Manager-exemplaar met de naam *mywatcher* met behulp van een JSON-tekenreeks voor configuratie.

``` azurecli-interactive
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                     --is-full-object \
                     --properties '{"location":"eastus"}'
```

Maak een Blockchain Data Manager-exemplaar met de naam mywatcher met de naam *MYWATCHER* met behulp van een JSON-configuratiebestand.

``` azurecli
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                    --is-full-object \
                    --properties @watcher.json
```

## <a name="create-input"></a>Invoer maken

Een invoer verbindt Blockchain Data Manager met een Azure Blockchain Service-transactieknooppunt. Alleen gebruikers met toegang tot het transactieknooppunt kunnen een verbinding maken.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Input name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type inputs \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <input resource properties>
```

| Parameter | Beschrijving |
|-----------|-------------|
| resource-group | Naam van resourcegroep waar de invoerbron moet worden gemaakt. |
| name | Naam van de invoer. |
| naamruimte | Gebruik de naamruimte van de **Microsoft.Blockchain-provider.** |
| resourcetype | Het brontype voor een Blockchain Data Manager-invoer is **invoer.** |
| Bovenliggende | Het pad naar de watcher waaraan de invoer is gekoppeld. Bijvoorbeeld, **watchers / mywatcher**. |
| is-full-object | Geeft aan dat eigenschappen opties bevatten voor de invoerbron. |
| properties | Tekenreeks met JSON-indeling die eigenschappen bevat voor de invoerbron. Kan worden doorgegeven als een string of een bestand. |

### <a name="input-examples"></a>Voorbeelden invoeren

Configuratie JSON voorbeeld om een input resource te maken \<in\>de *Regio Oost-VS* die is aangesloten op Blockchain-lid .

``` json
{
    "location": "eastus",
    "properties": {
        "inputType": "Ethereum",
        "dataSource": {
            "resourceId": "/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/blockchainMembers/<Blockchain member>/transactionNodes/transaction-node"
        }
    }
}
```

| Element | Beschrijving |
|---------|-------------|
| location | Regio waar u de invoerbron maakt. |
| inputType | Grootboektype van het Azure Blockchain Service-lid. Momenteel wordt **Ethereum** ondersteund. |
| resourceId | Transactieknooppunt waaraan de invoer is verbonden. Vervang \<subscription\> \<ID,\>Resource \<groep\> en Blockchain-lid door de waarden voor de transactieknooppuntbron. De invoer maakt verbinding met het standaardtransactieknooppunt voor het Azure Blockchain Service-lid. |

Maak een invoer met de naam *myInput* voor *mywatcher* met behulp van een JSON-tekenreeks voor configuratie.

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myInput \
                   --namespace Microsoft.Blockchain \
                   --resource-type inputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus", "properties":{"inputType":"Ethereum","dataSource":{"resourceId":"/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/BlockchainMembers/<Blockchain member>/transactionNodes/transaction-node"}}}'
```

Maak een invoer met de naam *myInput* voor *mywatcher* met behulp van een JSON-configuratiebestand.

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name input \
                   --namespace Microsoft.Blockchain \ --resource-type inputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @input.json
```

## <a name="create-output"></a>Uitvoer maken

Een uitgaande verbinding verzendt blockchain-gegevens naar Azure Event Grid. U blockchain-gegevens naar één bestemming verzenden of blockchain-gegevens naar meerdere bestemmingen verzenden. Blockchain Data Manager ondersteunt meerdere uitgaande netwerkverbindingen voor een evenementraster voor een bepaald Blockchain Data Manager-exemplaar.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Output name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <output resource properties>
```

| Parameter | Beschrijving |
|-----------|-------------|
| resource-group | Naam van resourcegroep waar de uitvoerbron moet worden gemaakt. |
| name | Naam van de uitvoer. |
| naamruimte | Gebruik de naamruimte van de **Microsoft.Blockchain-provider.** |
| resourcetype | Het resourcetype voor een Blockchain Data Manager-uitvoer is **uitvoer.** |
| Bovenliggende | Het pad naar de watcher waaraan de uitvoer is gekoppeld. Bijvoorbeeld, **watchers / mywatcher**. |
| is-full-object | Geeft aan dat eigenschappen opties bevatten voor de uitvoerbron. |
| properties | Tekenreeks met JSON-indeling die eigenschappen bevat voor de uitvoerbron. Kan worden doorgegeven als een string of een bestand. |

### <a name="output-examples"></a>Voorbeelden van uitvoer

Voorbeeld van JSON voor configuratie om een uitvoerbron te maken in \<de\>regio *Oost-VS* die is verbonden met een gebeurtenisrasteronderwerp met de naam gebeurtenisrasteronderwerp .

``` json
{
    "location": "eastus",
    "properties": {
        "outputType": "EventGrid",
        "dataSource": {
            "resourceId": "/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.EventGrid/topics/<event grid topic>"
        }
    }
}
```

| Element | Beschrijving |
|---------|-------------|
| location | Regio waar u de uitvoerbron maakt. |
| outputType | Type uitvoer. Momenteel wordt **EventGrid** ondersteund. |
| resourceId | Resource waarmee de uitvoer is verbonden. Vervang \<subscription\> \<ID,\>Resource \<groep\> en Blockchain-lid door de waarden voor de gebeurtenisrasterbron. |

Maak een uitvoer met de naam *myoutput* voor *mywatcher* die verbinding maakt met een gebeurtenisrasteronderwerp met behulp van een JSON-configuratietekenreeks.

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myoutput \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus","properties":{"outputType":"EventGrid","dataSource":{"resourceId":"/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.EventGrid/topics/<event grid topic>"}}}'
```

Maak een uitvoer met de naam *myoutput* voor *mywatcher* die verbinding maakt met een gebeurtenisrasteronderwerp met behulp van een JSON-configuratiebestand.

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name myoutput \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @output.json
```

## <a name="add-blockchain-application"></a>Blockchain-toepassing toevoegen

Als u een blockchain-toepassing toevoegt, decodeert Blockchain Data Manager de gebeurtenis- en eigenschappenstatus voor de toepassing. Anders worden alleen ruwe blok- en ruwe transactiegegevens verzonden. Blockchain Data Manager detecteert ook contractadressen wanneer het contract wordt geïmplementeerd. U meerdere blockchain-toepassingen toevoegen aan een Blockchain Data Manager-exemplaar.


> [!IMPORTANT]
> Momenteel worden blockchain-toepassingen die [Solidity-arraytypen](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays) of [toewijzingstypen](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types) declareren, niet volledig ondersteund. Eigenschappen die zijn gedeclareerd als array- of toewijzingstypen, worden niet gedecodeerd in *ContractPropertiesMsg-* of *DecodedContractEventsMsg-berichten.*

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Application name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <Application resource properties>
```

| Parameter | Beschrijving |
|-----------|-------------|
| resource-group | Naam van de resourcegroep waar de toepassingsbron moet worden gemaakt. |
| name | Naam van de toepassing. |
| naamruimte | Gebruik de naamruimte van de **Microsoft.Blockchain-provider.** |
| resourcetype | Het brontype voor een Blockchain Data Manager-toepassing is **artefacten.** |
| Bovenliggende | Het pad naar de watcher waaraan de toepassing is gekoppeld. Bijvoorbeeld, **watchers / mywatcher**. |
| is-full-object | Geeft aan dat eigenschappen opties bevatten voor de toepassingsbron. |
| properties | Tekenreeks met JSON-indeling die eigenschappen bevat voor de toepassingsbron. Kan worden doorgegeven als een string of een bestand. |

### <a name="blockchain-application-examples"></a>Voorbeelden van blockchain-toepassingen

Configuratie JSON voorbeeld om een toepassingsbron in de *regio Oost-VS* die een slim contract gedefinieerd door het contract ABI en bytecode bewaakt.

``` json
{
    "location": "eastus",
    "properties": {
        "artifactType": "EthereumSmartContract",
        "content": {
            "abiFileUrl": "<ABI URL>",
            "bytecodeFileUrl": "<Bytecode URL>",
            "queryTargetTypes": [
                "ContractProperties",
                "ContractEvents"
            ]
        }
    }
}
```

| Element | Beschrijving |
|---------|-------------|
| location | Regio waar u de toepassingsbron maakt. |
| artefactType | Type toepassing. Momenteel wordt **EthereumSmartContract** ondersteund. |
| abiFileUrl | URL voor smart contract ABI JSON-bestand. Zie [ContractABI ophalen en bytecode en](data-manager-portal.md#get-contract-abi-and-bytecode) [URL contractABI en bytecode maken](data-manager-portal.md#create-contract-abi-and-bytecode-url)voor meer informatie over het verkrijgen van contract-ABI en het maken van een URL. |
| bytecodeFileUrl | URL voor smart contract ployed bytecode JSON-bestand. Zie [ContractABI ophalen en bytecode](data-manager-portal.md#get-contract-abi-and-bytecode) en [URL maken](data-manager-portal.md#create-contract-abi-and-bytecode-url)voor meer informatie over het verkrijgen van de doortecode voor slim contract en het maken van een URL. Opmerking: Blockchain Data Manager vereist de **geïmplementeerde bytecode.** |
| queryTargettypen | Gepubliceerde berichttypen. Als u **ContractProperties** opgeeft, wordt het berichttype *ContractPropertiesMsg* gepubliceerd. Als u **ContractEvents opgeeft,** wordt het berichttype *DecodedContractEventsMsg* gepubliceerd. Opmerking: De berichtentypen *RawBlockAndTransactionMsg* en *RawTransactionContractCreationMsg* worden altijd gepubliceerd. |

Maak een toepassing met de naam *myApplication* for *mywatcher* die een slim contract bewaakt dat is gedefinieerd door een JSON-tekenreeks.

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myApplication \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus","properties":{"artifactType":"EthereumSmartContract","content":{"abiFileUrl":"<ABI URL>","bytecodeFileUrl":"<Bytecode URL>","queryTargetTypes":["ContractProperties","ContractEvents"]}}}'
```

Maak een toepassing met de naam *myApplication* for *mywatcher* die een slim contract bekijkt dat is gedefinieerd met behulp van een JSON-configuratiebestand.

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name myApplication \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @artifact.json
```

## <a name="start-instance"></a>Instantie Starten

Wanneer een Instantie Van Blockchain Manager blockchain-gebeurtenissen van de gedefinieerde ingangen controleert, wordt gegevens naar de gedefinieerde uitvoer verzenden.

``` azurecli
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| Parameter | Beschrijving |
|-----------|-------------|
| action | Gebruik **start** om de watcher uit te voeren. |
| Ids | Watcher resource ID. Vervang \<de\> \<naam\>Abonnement-id, Resourcegroep en \<Watcher\> door de waarden voor de watcher-bron.|

### <a name="start-instance-example"></a>Voorbeeld van startinstantie

Start een Blockchain Data Manager-exemplaar met de naam *mywatcher*.

``` azurecli-interactive
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="stop-instance"></a>Instantie stoppen

Stop een instantie Blockchain Data Manager.

``` azurecli
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| Parameter | Beschrijving |
|-----------|-------------|
| action | Gebruik **stop** om de wachter te stoppen. |
| Ids | Naam van de watcher. Vervang \<de\> \<naam\>Abonnement-id, Resourcegroep en \<Watcher\> door de waarden voor de watcher-bron. |

### <a name="stop-watcher-example"></a>Voorbeeld van stop-watcher

Stop een instantie met de naam *mywatcher.*

``` azurecli-interactive
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="delete-instance"></a>Instantie verwijderen

Een instantie Voor Blockchain Data Manager verwijderen.

``` azurecli
az resource delete \
                   --resource-group <Resource group> \
                   --name <Watcher name> \
                   --resource-type Microsoft.Blockchain/watchers
```

| Parameter | Beschrijving |
|-----------|-------------|
| resource-group | Resourcegroepnaam van de watcher om te verwijderen. |
| name | Naam van de watcher te verwijderen. |
| resourcetype | Het brontype voor een Blockchain Data Manager-watcher is **Microsoft.blockchain/watchers.** |

### <a name="delete-instance-example"></a>Voorbeeld van instantie verwijderen

Verwijder een instantie met de naam *mywatcher* in de *myRG-brongroep.*

``` azurecli-interactive
az resource delete \
                   --resource-group myRG \
                   --name mywatcher \
                   --resource-type Microsoft.blockchain/watchers
```

## <a name="next-steps"></a>Volgende stappen

Probeer de volgende zelfstudie met het maken van een blockchain-transactieberichtverkenner met Blockchain Data Manager en Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Blockchain Data Manager gebruiken om gegevens te verzenden naar Azure Cosmos DB](data-manager-cosmosdb.md)
