---
title: De Azure Cosmos DB Emulator-certificaten exporteren
description: Bij het ontwikkelen in talen en runtimes die geen gebruikmaken van het Windows-certificaat archief, moet u de TLS/SSL-certificaten exporteren en beheren. Dit bericht bevat stapsgewijze instructies.
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
author: deborahc
ms.author: dech
ms.openlocfilehash: c72dbf24df850d8b0f7e5f26a873b78f5664c9e0
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82200935"
---
# <a name="export-the-azure-cosmos-db-emulator-certificates-for-use-with-java-python-and-nodejs"></a>De Azure Cosmos DB Emulator-certificaten exporteren voor gebruik met Java, Python en Node.js

[**De emulator downloaden**](https://aka.ms/cosmosdb-emulator)

De Azure Cosmos DB-emulator biedt een lokale omgeving die de Azure Cosmos DB-Service voor ontwikkelings doeleinden emuleert, met inbegrip van het gebruik van TLS-verbindingen. Dit bericht laat zien hoe u de TLS/SSL-certificaten exporteert voor gebruik in talen en runtimes die niet worden geïntegreerd met het Windows-certificaat archief, zoals Java, dat gebruikmaakt van een eigen [certificaat archief](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html) en python dat gebruikmaakt van [socket-wrappers](https://docs.python.org/2/library/ssl.html) en node. js, dat gebruikmaakt van [tlsSocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback). Meer informatie over de emulator vindt u in [Azure Cosmos DB Emulator gebruiken voor ontwikkeling en tests](./local-emulator.md).

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Certificaten rouleren
> * TLS/SSL-certificaat exporteren
> * Meer informatie over het gebruik van het certificaat in Java, Python en Node.js

## <a name="certification-rotation"></a>Certificering rouleren

Certificaten in Azure Cosmos DB Local Emulator worden gegenereerd wanneer de emulator de eerste keer wordt uitgevoerd. Er zijn twee certificaten. Eén wordt gebruikt voor het maken van verbinding met de lokale emulator en één voor het beheren van geheimen in de emulator. Het certificaat dat u wilt exporteren, is het verbindingscertificaat met de beschrijvende naam DocumentDBEmulatorCertificate.

Beide certificaten kunnen opnieuw worden gegenereerd door te klikken op **Gegevens herstellen**, zoals hieronder wordt weergegeven, vanuit Azure Cosmos DB Emulator dat actief is in het Windows-systeemvak. Als u de certificaten opnieuw genereert en deze hebt geïnstalleerd in het Java-certificaatarchief of ze ergens anders gebruikt, moet u deze bijwerken. Anders maakt de toepassing geen verbinding meer met de lokale emulator.

![Gegevens herstellen met de lokale emulator van Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-reset-data.png)

## <a name="how-to-export-the-azure-cosmos-db-tlsssl-certificate"></a>Het Azure Cosmos DB TLS/SSL-certificaat exporteren

1. Start het Windows-certificaatbeheer door certlm.msc uit te voeren en ga naar de map Persoonlijk->Certificaten om het certificaat met de beschrijvende naam **DocumentDbEmulatorCertificate** te openen.

    ![Stap 1 van exporteren met lokale Azure DB Cosmos-emulator](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-1.png)

2. Klik op **Details** en klik op **OK**.

    ![Stap 2 van exporteren met lokale Azure DB Cosmos-emulator](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-2.png)

3. Klik op **Kopiëren naar bestand... **.

    ![Stap 3 van exporteren met lokale Azure DB Cosmos-emulator](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-3.png)

4. Klik op **Volgende**.

    ![Stap 4 van exporteren met lokale Azure DB Cosmos-emulator](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-4.png)

5. Klik op **Nee, persoonlijke sleutel niet exporteren** en klik op **Volgende**.

    ![Stap 5 van exporteren met lokale Azure DB Cosmos-emulator](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-5.png)

6. Klik op **Base-64 encoded X.509 (.CER)** en klik op **Volgende**.

    ![Stap 6 van exporteren met lokale Azure DB Cosmos-emulator](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-6.png)

7. Geef het certificaat een naam, in dit geval **documentdbemulatorcert**, en klik op **Volgende**.

    ![Stap 7 van exporteren met lokale Azure DB Cosmos-emulator](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-7.png)

8. Klik op **Voltooien**.

    ![Stap 8 van exporteren met lokale Azure DB Cosmos-emulator](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-8.png)

## <a name="how-to-use-the-certificate-in-java"></a>Het certificaat gebruiken in Java

Bij het uitvoeren van Java-toepassingen of MongoDB-toepassingen die gebruikmaken van de Java-client, is het eenvoudiger om het certificaat te installeren in `-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>"` het standaard certificaat archief van Java dan door de vlaggen door te geven. Bijvoorbeeld, de opgenomen java-demo toepassing`https://localhost:8081/_explorer/index.html`() is afhankelijk van het standaard certificaat archief.

Volg de instructies in [Een certificaat toevoegen aan het Java CA certificaatarchief](https://docs.microsoft.com/azure/java-add-certificate-ca-store) om het X.509-certificaat te importeren in het standaardcertificaatarchief van Java. Vergeet niet dat u in de map %JAVA_HOME% werkt wanneer u keytool uitvoert.

Zodra het ' CosmosDBEmulatorCertificate ' TLS/SSL-certificaat is geïnstalleerd, moet uw toepassing verbinding kunnen maken en de lokale Azure Cosmos DB-emulator gebruiken. Als u problemen blijft ondervinden, leest u het artikel [Fouten in SSL/TLS-verbindingen opsporen](https://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html). Het certificaat is waarschijnlijk niet geïnstalleerd in het archief %JAVA_HOME%/jre/lib/security/cacerts store. Als u bijvoorbeeld meerdere versies van Java hebt geïnstalleerd, kan de toepassing een ander cacerts-archief gebruiken dan het archief dat u hebt bijgewerkt.

## <a name="how-to-use-the-certificate-in-python"></a>Het certificaat gebruiken in Python

Standaard wordt de [python-SDK (versie 2.0.0 of hoger)](sql-api-sdk-python.md) voor de SQL-API gebruikt om niet te proberen het TLS/SSL-certificaat te gebruiken wanneer u verbinding maakt met de lokale emulator. Als u echter TLS-validatie wilt gebruiken, kunt u de voor beelden in de documentatie van [python-socket-wrappers](https://docs.python.org/2/library/ssl.html) volgen.

## <a name="how-to-use-the-certificate-in-nodejs"></a>Het certificaat gebruiken in Node.js

Standaard wordt de [node. js SDK (versie 1.10.1 of hoger)](sql-api-sdk-node.md) voor de SQL-API gebruikt om niet te proberen het TLS/SSL-certificaat te gebruiken wanneer u verbinding maakt met de lokale emulator. Als u echter TLS-validatie wilt gebruiken, kunt u de voor beelden in de [node. js-documentatie](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback)volgen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende gedaan:

> [!div class="checklist"]
> * Certificaten gerouleerd
> * Het TLS/SSL-certificaat is geëxporteerd
> * Geleerd over het gebruik van het certificaat in Java, Python en Node.js

U kunt nu doorgaan naar de sectie Concepten voor meer informatie over Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Instelbare gegevensconsistentieniveaus in Azure Cosmos DB](../cosmos-db/consistency-levels.md)
