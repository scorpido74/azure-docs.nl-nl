---
title: Ethereum Blockchain-connector gebruiken met Azure Logic Apps - Azure Blockchain-service
description: Gebruik de Ethereum Blockchain-connector met Azure Logic Apps om slimme contractfuncties te activeren en te reageren op slimme contractgebeurtenissen.
ms.date: 10/14/2019
ms.topic: article
ms.reviewer: chrisseg
ms.openlocfilehash: 4a9acfd6098ed45fd92c7e3047b5d1446eeddbd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74325224"
---
# <a name="use-the-ethereum-blockchain-connector-with-azure-logic-apps"></a>De Ethereum Blockchain-connector gebruiken met Azure Logic Apps

Gebruik de [Ethereum Blockchain-connector](https://docs.microsoft.com/connectors/blockchainethereum/) met [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/) om slimme contractacties uit te voeren en te reageren op slimme contractgebeurtenissen. Stel dat u een op rust gebaseerde microservice wilt maken die informatie uit een blockchain-grootboek retourneert. Door een logische app te gebruiken, u HTTP-verzoeken accepteren die informatie opvragen die is opgeslagen in een blockchain-grootboek.

## <a name="prerequisites"></a>Vereisten

Voltooi de optionele vereiste [Quickstart: Gebruik Visual Studio Code om verbinding te maken met een Azure Blockchain Service consortiumnetwerk.](connect-vscode.md) De quickstart begeleidt u al bij het installeren van [Azure Blockchain Development Kit voor Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) en het opzetten van uw blockchain-ontwikkelomgeving.

## <a name="create-a-logic-app"></a>Een logische app maken

Azure Logic Apps helpt u bedrijfsprocessen en workflows te plannen en te automatiseren wanneer u systemen en services moet integreren. Eerst maak je een logica die de Ethereum Blockchain-connector gebruikt.

1. Selecteer in [Azure Portal](https://portal.azure.com)**Een resource maken** > **Integratie** > **Logische app**.
1. Geef **onder Logica-app maken**informatie op waar u uw logische app maken. Nadat u klaar bent, selecteert u **Maken**.

    Zie [Geautomatiseerde werkstromen maken met Azure Logic Apps](../../logic-apps/quickstart-create-first-logic-app-workflow.md)voor meer informatie over het maken van logische apps.

1. Nadat Azure uw app heeft geïmplementeerd, selecteert u uw logische app-bron.
1. Selecteer in de Logic Apps Designer onder **Sjablonen**de optie **Lege logica-app**.

Elke logische app moet beginnen met een trigger, die wordt geactiveerd wanneer er een bepaalde gebeurtenis plaatsvindt of wanneer er aan een bepaalde voorwaarde is voldaan. Telkens wanneer de trigger wordt geactiveerd, maakt de Logic Apps-engine een exemplaar van een logische app dat wordt gestart en de werkstroom uitvoert.

De Ethereum Blockchain-connector heeft één trigger en verschillende acties. Welke trigger of actie u gebruikt, is afhankelijk van uw scenario.

Als uw werkstroom:

* Hiermee wordt geactiveerd wanneer een gebeurtenis plaatsvindt op de blockchain, [Gebruik de gebeurtenistrigger](#use-the-event-trigger).
* Query's of implementeert een slim contract, [Acties gebruiken](#use-actions).
* Volgt een veelvoorkomend scenario, [Een werkstroom genereren met behulp van de ontwikkelaarskit.](#generate-a-workflow)

## <a name="use-the-event-trigger"></a>De gebeurtenistrigger gebruiken

Gebruik Ethereum Blockchain-gebeurtenistriggers wanneer u een logische app wilt uitvoeren nadat een slimme contractgebeurtenis optreedt. U wilt bijvoorbeeld een e-mail verzenden wanneer een slimme contractfunctie wordt aangeroepen.

1. Selecteer in de Logic Apps Designer de Ethereum Blockchain-connector.
1. Selecteer op het tabblad **Triggers** de optie **Wanneer een slimme contractgebeurtenis plaatsvindt**.
1. Een [API-verbinding met](#create-an-api-connection) Azure Blockchain Service wijzigen of maken.
1. Voer de details in over het slimme contract dat u wilt controleren op gebeurtenissen.

    ![Logic Apps Designer with Event trigger properties Logic Apps Designer with Event trigger properties Logic Apps Designer with Event trigger properties Logic Apps](./media/ethereum-logic-app/event-properties.png)

    | Eigenschap | Beschrijving |
    |----------|-------------|
    | **Contract ABI** | De contractapplicatie binaire interface (ABI) definieert de smart contract interfaces. Zie [Het contract ABI opvragen](#get-the-contract-abi)voor meer informatie. |
    | **Slim contractadres** | Het contractadres is het slimme contractbestemmingsadres op de Ethereum-blockchain. Zie [Het contractadres opvragen](#get-the-contract-address)voor meer informatie. |
    | **Gebeurtenisnaam** | Selecteer een slimme contractgebeurtenis om te controleren. De gebeurtenis activeert de logische app. |
    | **Interval** en **frequentie** | Selecteer hoe vaak u wilt controleren op het evenement. |

1. Selecteer **Opslaan**.

Om uw logica-app te voltooien, u een nieuwe stap toevoegen die een actie uitvoert op basis van de trigger van De Ethereum Blockchain-gebeurtenis. Stuur bijvoorbeeld een e-mail.

## <a name="use-actions"></a>Acties gebruiken

Gebruik de Ethereum Blockchain-acties wanneer u een logische app wilt om een actie uit te voeren op het blockchain-grootboek. U wilt bijvoorbeeld een op RUST gebaseerde microservice maken die een slimme contractfunctie aanroept wanneer een HTTP-aanvraag wordt ingediend bij een logische app.

Connectoracties vereisen een trigger. U een Ethereum Blockchain-connectoractie gebruiken als de volgende stap na een trigger, zoals een HTTP-aanvraagtrigger voor een microservice.

1. Selecteer in de Maker van Logische apps de optie **Nieuwe stap** na een trigger.
1. Selecteer de Ethereum Blockchain-connector.
1. Selecteer op het tabblad **Acties** een van de beschikbare acties.

    ![Logic Apps Designer with Actions eigenschappen](./media/ethereum-logic-app/action-properties.png)

1. Een [API-verbinding met](#create-an-api-connection) Azure Blockchain Service wijzigen of maken.
1. Afhankelijk van de actie die u hebt gekozen, geeft u de volgende details over uw slimme contractfunctie.

    | Eigenschap | Beschrijving |
    |----------|-------------|
    | **Contract ABI** | Het contract ABI definieert de slimme contractinterfaces. Zie [Het contract ABI opvragen](#get-the-contract-abi)voor meer informatie. |
    | **Contractbytecode** | De gecompileerde slimme contractbytecode. Zie [De contractbytecode opvragen](#get-the-contract-bytecode)voor meer informatie . |
    | **Slim contractadres** | Het contractadres is het slimme contractbestemmingsadres op de Ethereum-blockchain. Zie [Het contractadres opvragen](#get-the-contract-address)voor meer informatie. |
    | **Naam van de functie Slim contract** | Selecteer de naam van de functie Slimme contract voor de actie. De lijst wordt ingevuld op basis van de details in het contract ABI. |

    Nadat u een naam van een slimme contractfunctie hebt geselecteerd, ziet u mogelijk vereiste velden voor functieparameters. Voer de waarden of dynamische inhoud in die nodig zijn voor uw scenario.

U nu uw logica-app gebruiken. Wanneer de gebeurtenis logische app wordt geactiveerd, wordt de Ethereum Blockchain-actie uitgevoerd. Een HTTP-aanvraagtrigger voert bijvoorbeeld een Ethereum-blockchain-actie uit om een slimme contractstatuswaarde op te vragen. Deze query resulteert in een HTTP-antwoord dat de waarde retourneert.

## <a name="generate-a-workflow"></a>Een werkstroom genereren

De Azure Blockchain Development Kit voor Ethereum Visual Studio Code-extensie kan logische app-workflows genereren voor veelvoorkomende scenario's. Er zijn vier scenario's beschikbaar:

* Het publiceren van gegevens naar een Azure SQL-database-exemplaar
* Gebeurtenispublicatie naar een instantie van Azure Event Grid of Azure Service Bus
* Publicatie van rapport
* REST-gebaseerde microservice

 De Azure Blockchain Development Kit gebruikt Truffel om de ontwikkeling van blockchain te vereenvoudigen. Om een logische app te genereren op basis van een slim contract, heb je een Truffel-oplossing nodig voor het slimme contract. U hebt ook een verbinding nodig met uw Azure Blockchain Service-consortiumnetwerk. Zie [Visual Studio Code gebruiken om snel verbinding te maken met een Azure Blockchain Service consortiumnetwerk](connect-vscode.md)voor meer informatie.

De volgende stappen genereren bijvoorbeeld een REST-gebaseerde microservicelogische app op basis van het smart contract voor quickstart **HelloBlockchain:**

1. Vouw in de zijbalk van de Visual Studio Code explorer de **map contracten** in uw oplossing uit.
1. Klik met de rechtermuisknop op **HelloBlockchain.sol** en selecteer **Microservices genereren voor slimme contracten** in het menu.

    ![Deelvenster Visual Studio Code met de selectie Microservices genereren voor slimme contracten](./media/ethereum-logic-app/generate-logic-app.png)

1. Selecteer **Logic App**in het opdrachtpalet .
1. Voer het **contractadres in**. Zie [Het contractadres opvragen](#get-the-contract-address)voor meer informatie.
1. Selecteer de Azure-abonnements- en brongroep voor de logische app.

    De configuratie- en codebestanden van de logische app worden gegenereerd in de **map generatedLogicApp.**

1. Bekijk de **map generatedLogicApp/HelloBlockchain.** Er is een JSON-bestand voor logica-apps voor elke slimme contractfunctie, gebeurtenis en eigenschap.
1. Open de **eigenschap generatedLogicApp/HelloBlockchain/Service/property. RequestMessage.logicapp.json** bestand en kopieer de inhoud.

    ![JSON-bestand met code om te kopiëren](./media/ethereum-logic-app/requestmessage.png)

1. Selecteer in uw logische app **de codeweergave Logische app**. Vervang de bestaande JSON door de gegenereerde logica-app JSON.

    ![Logische app-codeweergave met nieuwe vervangen app-code](./media/ethereum-logic-app/code-view.png)

1. Selecteer **Ontwerper** om over te schakelen naar de ontwerpweergave.
1. De logische app bevat de basisstappen voor het scenario. U moet echter de configuratiegegevens voor de Ethereum Blockchain-connector bijwerken.
1. Selecteer de stap **Verbindingen** en wijzig of [maak een API-verbinding met](#create-an-api-connection) Azure Blockchain Service.

    ![Ontwerpweergave met de selectie Verbindingen](./media/ethereum-logic-app/microservice-logic-app.png)

1. U nu uw logica-app gebruiken. Als u de op REST gebaseerde microservice wilt testen, geeft u een HTTP POST-verzoek uit aan de URL van de logische app-aanvraag. Kopieer de INHOUD van de **HTTP-POST-URL** van de stap **Wanneer een HTTP-aanvraag wordt ontvangen.**

    ![Deelvenster Logische apps-ontwerper met de HTTP-POST-URL](./media/ethereum-logic-app/post-url.png)

1. Gebruik cURL om een HTTP POST-aanvraag te maken. Vervang de URL van de tijdelijke * \<aanduidinghttp post\> * door de URL uit de vorige stap.

    ``` bash
    curl -d "{}" -H "Content-Type: application/json" -X POST "<HTTP POST URL>"
    ```

    Met de opdracht cURL retourneert u een antwoord van de logische app. In dit geval is het antwoord de uitvoer van de smart contract-functie **RequestMessage.**

    ![Codeuitvoer van de smartcontractfunctie RequestMessage](./media/ethereum-logic-app/curl.png)

Zie de [Wikipagina azure blockchain-ontwikkeling voor Ethereum voor](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki)meer informatie over het gebruik van de ontwikkelingskit.

## <a name="create-an-api-connection"></a>Een API-verbinding maken

Een API-verbinding met een blockchain is vereist voor de Ethereum Blockchain-connector. U de API-connector voor meerdere logische apps gebruiken. Sommige eigenschappen zijn vereist en andere zijn afhankelijk van uw scenario.

> [!IMPORTANT]
> Een privésleutel of accountadres en wachtwoord zijn vereist voor het maken van transacties op een blockchain. Er is slechts één vorm van authenticatie nodig. U hoeft niet zowel de privésleutel als de accountgegevens op te geven. Voor het opvragen van contracten is geen transactie vereist. Als u acties gebruikt die de contractstatus van query's uitvoeren, zijn de privésleutel of het accountadres en het wachtwoord niet vereist.

Om u te helpen bij het instellen van een verbinding met een Azure Blockchain Service-lid, heeft de volgende lijst mogelijke eigenschappen die u mogelijk nodig hebt, afhankelijk van uw scenario.

| Eigenschap | Beschrijving |
|----------|-------------|
|**Verbindingsnaam** | Naam van de API-verbinding. Vereist. |
|**Ethereum RPC-eindpunt** | HTTP-adres van het transactieknooppunt Azure Blockchain Service. Vereist. Zie [Het RPC-eindpunt ophalen](#get-the-rpc-endpoint)voor meer informatie. |
|**Persoonlijke sleutel** | Ethereum account privésleutel. Privésleutel of accountadres en wachtwoord zijn vereist voor transacties. Zie [De privésleutel ophalen](#get-the-private-key)voor meer informatie. |
|**Accountadres** | Azure Blockchain Service-accountadres. Privésleutel of accountadres en wachtwoord zijn vereist voor transacties. Zie [Het accountadres opvragen](#get-the-account-address)voor meer informatie. |
|**Accountwachtwoord** | Het accountwachtwoord wordt ingesteld wanneer u het lid maakt. Zie [Ethereum-account](consortium.md#ethereum-account)voor informatie over het opnieuw instellen van het wachtwoord.|

## <a name="get-the-rpc-endpoint"></a>Het RPC-eindpunt downloaden

Het RPC-eindpuntadres van Azure Blockchain Service is vereist om verbinding te maken met een blockchain-netwerk. U het eindpuntadres ophalen met behulp van de Azure Blockchain Development Kit voor Ethereum of de Azure-portal.

**Ga als het gaat om het gebruik van de development kit:**

1. Klik onder **Azure Blockchain Service** in Visual Studio Code met de rechtermuisknop op het consortium.
1. Selecteer **RPC-eindpuntadres kopiëren**.

    ![Deelvenster Visual Studio-code met het consortium met de selectie RPC-eindpunten kopiëren](./media/ethereum-logic-app/devkit-rpc.png)

    Het RPC-eindpunt wordt naar het klembord gekopieerd.

**Ga als een te meer meting in de Azure-portal:**

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Ga naar uw Azure Blockchain Service-lid. Selecteer **Transactieknooppunten** en de standaardkoppeling transactieknooppunt.

    ![Transactieknooppunten pagina met de (standaard knooppunt) selectie](./media/ethereum-logic-app/transaction-nodes.png)

1. Selecteer**Toegangstoetsen voor** **verbindingstekenreeksen** > .
1. Kopieer het eindpuntadres van **HTTPS (Access-toets 1)** of **HTTPS (Access-toets 2)**.

    ![Azure-portal met de toegangssleutels voor verbindingstekenreeksen](./media/ethereum-logic-app/connection-string.png)

    Het RPC-eindpunt is de HTTPS-URL, die het adres en de toegangssleutel van uw azure blockchain-servicelidtransactieknooppunt bevat.

## <a name="get-the-private-key"></a>Haal de privésleutel

U de privésleutel van het Ethereum-account gebruiken om te verifiëren wanneer u een transactie naar de blockchain verzendt. De openbare en privésleutels van uw Ethereum-account worden gegenereerd uit een ezelsbruggetje van 12 woorden. De Azure Blockchain Development Kit voor Ethereum genereert een ezelsbruggetje wanneer u verbinding maakt met een Azure Blockchain Service-consortiumlid. U het eindpuntadres ophalen met behulp van de uitbreiding van de ontwikkelkit.

1. Open in Visual Studio Code het opdrachtpalet (F1).
1. Selecteer **Azure Blockchain: privésleutel ophalen**.
1. Selecteer het ezelsbruggetje dat je hebt opgeslagen wanneer je verbinding maakt met het consortiumlid.

    ![Opdrachtpalet met een optie om het ezelsbruggetje te selecteren](./media/ethereum-logic-app/private-key.png)

    De privésleutel wordt gekopieerd naar uw klembord.

## <a name="get-the-account-address"></a>Het accountadres opvragen

U het ledenaccount en wachtwoord gebruiken om te verifiëren wanneer u een transactie naar de blockchain verzendt. Het wachtwoord wordt ingesteld wanneer u het lid maakt.

1. Ga in de Azure-portal naar de overzichtspagina van uw Azure Blockchain Service.
1. Kopieer het **accountadres van het lid.**

    ![Overzichtspagina met het accountadres van het lid](./media/ethereum-logic-app/member-account.png)

Zie [Ethereum-account](consortium.md#ethereum-account)voor meer informatie over het accountadres en wachtwoord.

## <a name="get-the-contract-abi"></a>Ontvang het contract ABI

Het contract ABI definieert de slimme contractinterfaces. Het beschrijft hoe om te gaan met de slimme contract. U het contract ABI krijgen met behulp van de Azure Blockchain Development Kit voor Ethereum. U het ook krijgen uit het bestand met contractmetagegevens dat is gemaakt door de samensesoliditeitscompiler.

**Ga als het gaat om het gebruik van de development kit:**

Als u de ontwikkelingskit of truffel hebt gebruikt om uw slimme contract op te bouwen, u de verlenging gebruiken om het contract ABI naar het klembord te kopiëren.

1. Vouw in het deelvenster Visual Studio Code explorer de map **build/contracts** van uw Solidity-project uit.
1. Klik met de rechtermuisknop op het JSON-bestand met contractmetagegevens. De bestandsnaam is de slimme contractnaam gevolgd door de **.json** extensie.
1. Selecteer **Abi van contract kopiëren**.

    ![Deelvenster Visual Studio Code met de ABI-selectie van het contract kopiëren](./media/ethereum-logic-app/abi-devkit.png)

    Het contract ABI wordt gekopieerd naar het klembord.

**Ga als nodig de toepassing van het bestand met contractmetagegevens gebruiken:**

1. Open het bestand met contractmetagegevens in de map **build/contracts** van uw Solidity-project. De bestandsnaam is de slimme contractnaam gevolgd door de **.json** extensie.
1. Zoek de **abi** sectie in het JSON-bestand.
1. Kopieer de **abi** JSON-array.

    ![ABI-code in het bestand met metagegevens van het contract](./media/ethereum-logic-app/abi-metadata.png)

## <a name="get-the-contract-bytecode"></a>De contractbytecode ophalen

De contractbytecode is het gecompileerde slimme contract uitgevoerd door de Virtuele Machine van Ethereum. U de contractbytecode krijgen met behulp van de Azure Blockchain Development Kit voor Ethereum. U het ook krijgen van de Solidity compiler.

**Ga als het gaat om het gebruik van de development kit:**

Als u de ontwikkelingskit of truffel hebt gebruikt om uw slimme contract op te bouwen, u de verlenging gebruiken om de contractbytecode naar het klembord te kopiëren.

1. Vouw in het deelvenster Visual Studio Code explorer de map **build/contracts** van uw Solidity-project uit.
1. Klik met de rechtermuisknop op het JSON-bestand met contractmetagegevens. De bestandsnaam is de slimme contractnaam gevolgd door de **.json** extensie.
1. Selecteer **Contractbytecode kopiëren**.

    ![Deelvenster Visual Studio-code met de selectie Contractbytecode kopiëren](./media/ethereum-logic-app/bytecode-devkit.png)

    De contractbytecode wordt gekopieerd naar het klembord.

**Ga als nodig de toepassing van het bestand met contractmetagegevens gebruiken:**

1. Open het bestand met contractmetagegevens in de map **build/contracts** van uw Solidity-project. De bestandsnaam is de slimme contractnaam gevolgd door de **.json** extensie.
1. Zoek het **element bytecode** in het JSON-bestand.
1. Kopieer de **waarde van de bytecode.**

    ![Deelvenster Visual Studio-code met bytecode in de metagegevens](./media/ethereum-logic-app/bytecode-metadata.png)

**Ga als u de samenstollende compiler Soliditeit gebruiken:**

Gebruik de `solc --bin <smart contract>.sol` opdracht om de contractbytecode te genereren.

## <a name="get-the-contract-address"></a>Het contractadres opvragen

Het contractadres is het slimme contractbestemmingsadres op de Ethereum-blockchain. U gebruikt dit adres om een transactie- of querystatus van een slim contract te verzenden. U het contractadres ophalen van de truffelmigratie-uitvoer of het bestand met metagegevens van het contract.

**Ga als gevolg van het gebruik van de truffelmigratie-uitvoer:**

Truffel geeft het contractadres weer na de implementatie van het slimme contract. Kopieer het **contractadres** uit de uitvoer.

![Truffelmigratie-uitvoer met het contractadres in Visual Studio Code](./media/ethereum-logic-app/contract-address-truffle.png)

**Ga als nodig de toepassing van het bestand met contractmetagegevens gebruiken:**

1. Open het bestand met contractmetagegevens in de map **build/contracts** van uw Solidity-project. De bestandsnaam is de slimme contractnaam gevolgd door de **.json** extensie.
1. Zoek de **sectie netwerken** in het JSON-bestand.
1. Particuliere netwerken worden geïdentificeerd door een gehele netwerk-ID. Zoek de adreswaarde in de netwerksectie.
1. Kopieer de **adreswaarde.**

![Metagegevens met de adreswaarde in Visual Studio Code](./media/ethereum-logic-app/contract-address-metadata.png)

## <a name="next-steps"></a>Volgende stappen

Bekijk veelvoorkomende scenario's in de video [Meer doen met Logische apps](https://channel9.msdn.com/Shows/Blocktalk/Doing-more-with-Logic-Apps?term=logic%20apps%20blockchain&lang-en=true).
