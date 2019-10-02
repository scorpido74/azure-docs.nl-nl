---
title: De Ethereum Block Chain-connector gebruiken met Azure Logic Apps
description: De Ethereum Block Chain-connector gebruiken met Azure Logic Apps om slimme contract functies te activeren en te reageren op slimme contract gebeurtenissen.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/26/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: chrisseg
manager: femila
ms.openlocfilehash: 48f862bea6df50480ce4f9d21993693da34cfbc9
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71720399"
---
# <a name="how-to-use-ethereum-blockchain-connector-with-azure-logic-apps"></a>Ethereum Block Chain-connector gebruiken met Azure Logic Apps

Gebruik de [Ethereum Block Chain-connector](https://docs.microsoft.com/connectors/blockchainethereum/) met [Azure Logic apps](https://docs.microsoft.com/azure/logic-apps/) om slimme contract acties uit te voeren en te reageren op slimme contract gebeurtenissen. U wilt bijvoorbeeld een micro service op REST basis maken die gegevens uit een Block Chain-boek houding retourneert. Met behulp van een logische app kunt u HTTP-aanvragen accepteren die query's uitvoeren op gegevens die zijn opgeslagen in een Block Chain-groot boek.

## <a name="prerequisites"></a>Vereisten

* Voer de optionele vereiste [Quickstart: Gebruik Visual Studio code om verbinding te maken met een Azure Block Chain service](connect-vscode.md)consortium-netwerk. De Snelstartgids helpt u bij het installeren [van de Azure Block Chain Development Kit voor Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) en het instellen van uw Block Chain-ontwikkel omgeving.

## <a name="create-a-logic-app"></a>Een logische app maken

Azure Logic Apps helpt u bij het plannen, automatiseren van bedrijfs processen en werk stromen wanneer u systemen en services moet integreren. Eerst maakt u een logica die gebruikmaakt van de Ethereum Block Chain-connector.

1. Kies in het [Azure Portal](https://portal.azure.com) **een resource > integratie > logische app maken**.
1. Onder **logische app maken**geeft u details op waar u uw logische app kunt maken. Wanneer u klaar bent, selecteert u **maken**.

    Zie [Azure Logic apps maken](../../logic-apps/quickstart-create-first-logic-app-workflow.md)voor meer informatie over het maken van Azure Logic apps.

1. Nadat Azure uw app heeft geïmplementeerd, selecteert u de resource van de logische app.
1. Kies in de Logic Apps Designer onder **sjablonen**de optie **lege logische app**.

Elke logische app moet beginnen met een trigger, die wordt geactiveerd wanneer er een bepaalde gebeurtenis plaatsvindt of wanneer er aan een bepaalde voorwaarde is voldaan. Telkens wanneer de trigger wordt geactiveerd, maakt de Logic Apps-engine een exemplaar van een logische app dat wordt gestart en de werkstroom uitvoert.

De Ethereum Block Chain-connector heeft één trigger en verschillende acties. Welke trigger of actie u gebruikt, is afhankelijk van uw scenario.

Kies een van de volgende secties als uw werk stroom:

* Hiermee wordt geactiveerd wanneer een gebeurtenis op de Block Chain plaatsvindt, [de gebeurtenis trigger gebruiken](#use-the-event-trigger).
* Query's uitvoeren of een slim contract implementeren, [acties gebruiken](#use-actions).
* Volgt een veelvoorkomend scenario, [Genereer een werk stroom met behulp van de Developer Kit](#generate-a-workflow).

## <a name="use-the-event-trigger"></a>De gebeurtenis trigger gebruiken

Gebruik Ethereum Block Chain-gebeurtenis triggers wanneer u wilt dat een logische app wordt uitgevoerd nadat een info contract gebeurtenis plaatsvindt. U wilt bijvoorbeeld een e-mail verzenden wanneer een slimme contract functie wordt aangeroepen.

1. Kies de Ethereum Block Chain-connector in de ontwerp functie voor logische apps.
1. Kies op het tabblad **Triggers** **Wanneer een info contract gebeurtenis zich voordoet**.
1. Wijzig of [Maak een API-verbinding](#create-an-api-connection) met uw Azure Block Chain-service.
1. Voer de details in van het slimme contract dat u wilt controleren op gebeurtenissen.

    ![Eigenschappen van gebeurtenis trigger](./media/ethereum-logic-app/event-properties.png)

    | Eigenschap | Description |
    |----------|-------------|
    | **ABI contract** | Met de ABI (contract Application Binary Interface) worden de Smart contract interfaces gedefinieerd. [De ABI van het contract ophalen](#get-contract-abi). |
    | **Info contract adres** | Het contract adres is het doel adres van het slimme contract op de Ethereum Block chain. [Het adres van het contract ophalen](#get-contract-address). |
    | **Gebeurtenis naam** | Kies een slimme contract gebeurtenis om te controleren. De logische app wordt geactiveerd door de gebeurtenis. |
    | **Interval** en **frequentie** | Kies hoe vaak u wilt controleren op de gebeurtenis. |

1. Selecteer **Opslaan**.

Als u uw logische app wilt volt ooien, kunt u een nieuwe stap toevoegen waarmee een actie wordt uitgevoerd op basis van de gebeurtenis trigger Ethereum Block chain. U kunt bijvoorbeeld een e-mail bericht verzenden.

## <a name="use-actions"></a>Acties gebruiken

Gebruik de Ethereum Block Chain-acties wanneer u wilt dat een logische app een actie uitvoert op het block chaine groot boek. Stel dat u een REST-gebaseerde micro service wilt maken die een functie van een slim contract aanroept wanneer een HTTP-aanvraag wordt gedaan bij een logische app.

Voor connector acties is een trigger vereist. U kunt een Ethereum Block Chain-connector actie gebruiken als de volgende stap na een trigger. Bijvoorbeeld een HTTP-aanvraag trigger voor een microsservice.

1. Selecteer in de ontwerp functie van de logische app de optie **nieuwe stap** na een trigger.
1. Kies de Ethereum Block Chain-connector.
1. Kies op het tabblad **acties** een van de beschik bare acties.

    ![Actie-eigenschappen](./media/ethereum-logic-app/action-properties.png)

1. Wijzig of [Maak een API-verbinding](#create-an-api-connection) met uw Azure Block Chain-service.
1. Afhankelijk van de actie die u hebt gekozen, geeft u de volgende details op over de functie van het slimme contract.

    | Eigenschap | Description |
    |----------|-------------|
    | **ABI contract** | Met de ABI (contract Application Binary Interface) worden de Smart contract interfaces gedefinieerd. [De ABI van het contract ophalen](#get-contract-abi). |
    | **Byte code van contract** | De gecompileerde byte code van het Smart contract. [De byte code van het contract ophalen](#get-contract-bytecode). |
    | **Info contract adres** | Het contract adres is het doel adres van het slimme contract op de Ethereum Block chain. [Het adres van het contract ophalen](#get-contract-address). |
    | **Naam van de functie voor slimme contracten** | Kies de naam van de functie voor de actie. De lijst wordt ingevuld op basis van de details in het contract ABI. |

    Nadat u de naam van een slimme contract functie hebt gekozen, ziet u mogelijk de vereiste velden voor functie parameters. Voer de waarden of dynamische inhoud in die vereist zijn voor uw scenario.

U kunt nu uw logische app gebruiken. Wanneer de logische app-gebeurtenis wordt geactiveerd, wordt de actie Ethereum Block Chain uitgevoerd. Bijvoorbeeld, een HTTP-aanvraag trigger voert een Ethereum Block Chain-actie uit om een Smart contract status waarde op te vragen die resulteert in een HTTP-antwoord waardoor de waarde wordt geretourneerd.

## <a name="generate-a-workflow"></a>Een werk stroom genereren

De Azure Block Chain Development Kit voor Ethereum Visual Studio code extension kan werk stromen voor logische apps genereren voor veelvoorkomende scenario's. Er zijn vier scenario's beschikbaar:

* Gegevens publiceren naar een Azure SQL Database
* Gebeurtenis publiceren naar een Azure Event Grid of Azure Service Bus
* Rapport publicatie
* Micro service op REST basis

 Azure Block Chain Development Kit maakt gebruik van truffle om Block Chain-ontwikkeling te vereenvoudigen. Als u een logische app op basis van een slim contract wilt genereren, hebt u een Truffle-oplossing nodig voor het slimme contract. U hebt ook een verbinding met uw Azure Block Chain Service consortium-netwerk nodig. Zie [Visual Studio code gebruiken om verbinding te maken met een Azure Block Chain Service consortium Network Quick](connect-vscode.md)start voor meer informatie.

Met de volgende stappen wordt bijvoorbeeld een op REST gebaseerde micro service Logic-app gegenereerd op basis van het Smart-contract **HelloBlockchain** Quick Start:

1. Vouw in de VS code Explorer Sidebar de map **contracten** in uw oplossing uit.
1. Klik met de rechter muisknop op **HelloBlockchain. Sol** en kies micro **Services voor slimme contracten genereren** in het menu.

    ![Een logische app genereren](./media/ethereum-logic-app/generate-logic-app.png)

1. Kies in het opdracht palet **logische app**.
1. Voer het **contract adres**in. Zie [het contract adres ophalen](#get-contract-address)voor meer informatie.
1. Kies het Azure-abonnement en de resource groep voor de logische app.

    De configuratie van de logische app en de code bestanden worden gegenereerd in de **generatedLogicApp** -map.

1. Bekijk de **generatedLogicApp/HelloBlockchain-** map. Er is een JSON-bestand van de logische app voor elke functie, gebeurtenis en eigenschap van een slim contract.
1. Open de **generatedLogicApp/HelloBlockchain/service/eigenschap. Bestand RequestMessage. logicapp. json** en kopieer de inhoud.

    ![JSON voor de eigenschap RequestMessage](./media/ethereum-logic-app/requestmessage.png)

1. Selecteer in uw logische app de **code weergave Logic app**. Vervang de bestaande JSON door de gegenereerde JSON van de logische app.

    ![Configuratie van de logische app vervangen in de code weergave](./media/ethereum-logic-app/code-view.png)

1. Selecteer **Designer** om over te scha kelen naar de ontwerp weergave.
1. De logische app bevat de basis stappen voor het scenario. U moet echter de configuratie gegevens voor de Ethereum Block Chain-connector bijwerken.
1. Selecteer de stap **verbindingen** en wijzig of [Maak een API-verbinding](#create-an-api-connection) met uw Azure Block Chain-service.

    ![Micro service Logic app](./media/ethereum-logic-app/microservice-logic-app.png)

1. U kunt nu uw logische app gebruiken. Als u de micro service op REST basis wilt testen, geeft u een HTTP POST-aanvraag voor de URL van de logische app-aanvraag. Kopieer de **http post-URL** van de stap **Wanneer een HTTP-aanvraag is ontvangen** .

    ![HTTP POST-URL](./media/ethereum-logic-app/post-url.png)

1. Gebruik krul om een HTTP POST-aanvraag te maken. Vervang de tekst van de tijdelijke aanduiding **\<HTTP post-URL @ no__t-2** door de URL van de vorige stap.

    ``` bash
    curl -d "{}" -H "Content-Type: application/json" -X POST "<HTTP POST URL>"
    ```

    De krul opdracht retourneert een reactie van de logische app. In dit geval wordt de uitvoer van de **RequestMessage** -functie van het slimme contract.

    ![Uitvoer van RequestMessage-eigenschap](./media/ethereum-logic-app/curl.png)

Voor meer informatie over het gebruik van de Development Kit raadpleegt u [Azure Block Chain Development Kit for Ethereum wiki](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki) page (Engelstalig).

## <a name="create-an-api-connection"></a>Een API-verbinding maken

Er is een API-verbinding met een Block Chain vereist voor de Ethereum Block Chain-connector. U kunt de API-connector gebruiken voor meerdere Logic apps. Sommige eigenschappen zijn vereist en andere zijn afhankelijk van uw scenario.

> [!IMPORTANT]
> Een persoonlijke sleutel of account adres en wacht woord zijn vereist voor het maken van trans acties in een Block chain. Er is slechts één vorm van verificatie nodig. U hoeft niet zowel de persoonlijke sleutel als de account gegevens op te geven. Voor het uitvoeren van een query op contracten is geen trans actie vereist. Als u acties gebruikt waarmee de contract status wordt opgevraagd, zijn de persoonlijke sleutel of het account adres en het wacht woord niet vereist.

Voor het instellen van een verbinding met een lid van de Azure Block Chain-Service, zijn de volgende eigenschappen mogelijk afhankelijk van uw scenario.

| Eigenschap | Description |
|----------|-------------|
|**Verbindingsnaam** | De naam van de API-verbinding. Vereist. |
|**RPC-eind punt Ethereum** | Het HTTP-adres van het knoop punt van de Azure Block Chain Service-trans actie. Vereist. [Het RPC-eind punt ophalen](#get-rpc-endpoint). |
|**Persoonlijke sleutel** | Persoonlijke sleutel van het Ethereum-account. De persoonlijke sleutel of het account adres en het wacht woord zijn vereist voor trans acties. [De persoonlijke sleutel ophalen](#get-private-key). |
|**Account adres** | Adres van lid van Azure Block Chain Service-account. De persoonlijke sleutel of het account adres en het wacht woord zijn vereist voor trans acties. [Het account adres ophalen](#get-account-address). |
|**Account wachtwoord** | Het wacht woord voor het account wordt ingesteld wanneer u het lid maakt. Zie [Ethereum-account](consortium.md#ethereum-account)voor meer informatie over het opnieuw instellen van het wacht woord.|

## <a name="get-rpc-endpoint"></a>RPC-eind punt ophalen

Het RPC-eindpunt adres van de Azure Block Chain-service is vereist om verbinding te maken met een Block chain-netwerk. U kunt een eindpunt adres ophalen met behulp van de Azure Block Chain Development Kit voor Ethereum of de Azure Portal.

**Met Development Kit:**

1. Klik onder **Azure Block Chain-Service** in Visual Studio code met de rechter muisknop op het consortium.
1. Selecteer **RPC-eind punt kopiëren**.

    ![RPC-eind punt kopiëren](./media/ethereum-logic-app/devkit-rpc.png)

    Het RPC-eind punt wordt gekopieerd naar het klem bord.

**Azure Portal gebruiken:**

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Navigeer naar uw Azure Block Chain service-lid. Selecteer **transactie knooppunten** en de koppeling standaard transactie knooppunt.

    ![Standaard transactie knooppunt selecteren](./media/ethereum-logic-app/transaction-nodes.png)

1. Selecteer **verbindings reeksen > toegangs sleutels**.
1. Kopieer het eindpunt adres van **https (toegangs sleutel 1)** of toegangs sleutel 2.

    ![Verbindingsreeks](./media/ethereum-logic-app/connection-string.png)

    Het RPC-eind punt is de HTTPS-URL, inclusief het adres en de toegangs sleutel van uw Azure Block Chain Service-trans actie-knoop punt.

## <a name="get-private-key"></a>Persoonlijke sleutel ophalen

De persoonlijke sleutel van het Ethereum-account kan worden gebruikt voor verificatie bij het verzenden van een trans actie naar de Block chain. De open bare en persoonlijke sleutels van uw Ethereum-account worden gegenereerd op basis van een geheugen steun van 12 woorden. Azure Block Chain Development Kit voor Ethereum genereert een instructie wanneer u verbinding maakt met een Azure Block Chain Service consortium-lid. U kunt een eindpunt adres ophalen met de uitbrei ding Development Kit.

1. Open in Visual Studio code het opdracht palet (F1).
1. Kies **Azure Block chain: Persoonlijke sleutel @ no__t-0 ophalen.
1. Selecteer de optie door u op te slaan wanneer u verbinding maakt met het consortium.

    ![Optie voor selecteren](./media/ethereum-logic-app/private-key.png)

    De persoonlijke sleutel wordt gekopieerd naar het klem bord.

## <a name="get-account-address"></a>Account adres ophalen

De leden account en het wacht woord kunnen worden gebruikt voor verificatie bij het verzenden van een trans actie naar het block chain. Het wacht woord wordt ingesteld wanneer u het lid maakt.

1. Ga in het Azure Portal naar de overzichts pagina van de Azure Block Chain-service.
1. Kopieer het adres van het **lid-account** .

    ![Leden account kopiëren](./media/ethereum-logic-app/member-account.png)

Zie [Ethereum-account](consortium.md#ethereum-account)voor meer informatie over het account adres en het wacht woord.

## <a name="get-contract-abi"></a>Contract ABI ophalen

Met de ABI (contract Application Binary Interface) worden de Smart contract interfaces gedefinieerd. Hierin wordt beschreven hoe u met het slimme contract communiceert. U kunt contract ABI ophalen met behulp van de Azure Block Chain Development Kit voor Ethereum of van het bestand met de meta gegevens van het programma voor de volheid.

**Met Development Kit:**

Als u Development Kit of Truffle gebruikt om uw slimme contract te maken, kunt u de uitbrei ding gebruiken om de contract ABI naar het klem bord te kopiëren.

1. Vouw in het deel venster Visual Studio code Explorer de map **Build/contract** van het volheid-project uit.
1. Klik met de rechter muisknop op het JSON-bestand met meta gegevens van het contract. De bestands naam is de naam van het slimme contract gevolgd door de extensie **. json** .
1. Kies **copy contract Abi**.

    ![Contract ABI kopiëren met behulp van DevKit](./media/ethereum-logic-app/abi-devkit.png)

    Het contract ABI wordt gekopieerd naar het klem bord.

**Het meta gegevens bestand van het contract gebruiken:**

1. Open het meta gegevensbestand van de opdracht in de map **Build/contract** van het volheid-project. De bestands naam is de naam van het slimme contract gevolgd door de extensie **. json** .
1. Zoek de sectie **Abi** in het JSON-bestand.
1. Kopieer de JSON-matrix **Abi** .

    ![Sectie contract ABI in meta gegevens](./media/ethereum-logic-app/abi-metadata.png)

## <a name="get-contract-bytecode"></a>Byte code van contract ophalen

De byte code van het contract is het gecompileerde slimme contract dat door de virtuele Ethereum-machine wordt uitgevoerd. U kunt een contract-byte code ophalen met behulp van de Azure Block Chain Development Kit voor Ethereum of vanuit de volheid-compiler.

**Met Development Kit:**

Als u Development Kit of Truffle gebruikt om uw slimme contract te maken, kunt u de extensie gebruiken om de byte code van het contract te kopiëren naar het klem bord.

1. Vouw in het deel venster Visual Studio code Explorer de map **Build/contract** van het volheid-project uit.
1. Klik met de rechter muisknop op het JSON-bestand met meta gegevens van het contract. De bestands naam is de naam van het slimme contract gevolgd door de extensie **. json** .
1. Kies **kopie van contract-byte code kopiëren**.

    ![Byte code van contract kopiëren met DevKit](./media/ethereum-logic-app/bytecode-devkit.png)

    De byte code van het contract wordt gekopieerd naar het klem bord.

**Het meta gegevens bestand van het contract gebruiken:**

1. Open het meta gegevensbestand van de opdracht in de map **Build/contract** van het volheid-project. De bestands naam is de naam van het slimme contract gevolgd door de extensie **. json** .
1. Zoek het **byte code** -element in het JSON-bestand.
1. Kopieer de waarde van de **byte code** .

    ![Byte code kopiëren met meta gegevens](./media/ethereum-logic-app/bytecode-metadata.png)

**Het volheid-Compileer programma gebruiken:**

Gebruik de opdracht `solc --bin <smart contract>.sol` om de byte code van het contract te genereren.

## <a name="get-contract-address"></a>Contract adres ophalen

Het contract adres is het doel adres van het slimme contract op de Ethereum Block chain. U kunt dit adres gebruiken om een trans actie-of query status van een slim contract te verzenden. U kunt het contract adres ophalen uit de Truffle-migratie of het meta gegevens bestand van het contract.

**Truffle migrate output gebruiken:**

Truffle geeft het contract adres na de implementatie van het slimme contract weer. Kopieer het **contract adres** uit de uitvoer.

![Contract adres van truffle-uitvoer](./media/ethereum-logic-app/contract-address-truffle.png)

**Het meta gegevens bestand van het contract gebruiken:**

1. Open het meta gegevensbestand van de opdracht in de map **Build/contract** van het volheid-project. De bestands naam is de naam van het slimme contract gevolgd door de extensie **. json** .
1. Zoek de sectie **netwerken** in het JSON-bestand.
1. Particuliere netwerken worden aangeduid met een netwerk-ID met gehele getallen. Zoek de adres waarde binnen de sectie netwerk.
1. Kopieer de **adres** waarde.

![Contract adres van meta gegevens](./media/ethereum-logic-app/contract-address-metadata.png)

## <a name="next-steps"></a>Volgende stappen

Bekijk gang bare [scenario's waarmee Block Chain verbinding maakt met behulp van Azure Logic apps](https://channel9.msdn.com/Shows/Blocktalk/Doing-more-with-Logic-Apps?term=logic%20apps%20blockchain&lang-en=true).
