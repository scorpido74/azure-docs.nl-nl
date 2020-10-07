---
title: bestand opnemen
description: bestand opnemen
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: a9c8d604e5564526936f37edcc9eec5891443a47
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779601"
---
## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Java Development Kit (JDK)](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable&preserve-view=true) versie 8 of hoger.
- [Apache Maven](https://maven.apache.org/download.cgi).
- Een actieve Communication Services-resource en verbindingsreeks. [Een Communication Services-resource maken](../create-communication-resource.md).

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-java-application"></a>Een nieuwe Java-toepassing maken

Open uw terminal- of opdrachtvenster en navigeer naar de map waarin u uw Java-toepassing wilt maken. Voer de onderstaande opdracht uit om het Java-project te genereren op basis van de maven-archetype-snelstart-sjabloon.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

U ziet dat de taak ' genereren ' een map heeft gemaakt met dezelfde naam als de `artifactId`. In deze map bevat de map src/main/Java de broncode van het project, de `src/test/java directory` bevat de testbron en het bestand `pom.xml` het projectobjectmodel van het project of POM.

### <a name="install-the-package"></a>Het pakket installeren

Open het bestand **pom.xml** in uw teksteditor. Voeg het volgende afhankelijkheidselement toe aan de groep met afhankelijkheden.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-administration</artifactId>
    <version>1.0.0-beta.2</version> 
</dependency>
```

### <a name="set-up-the-app-framework"></a>Het app-framework instellen

Ga als volgt te werk vanuit de projectmap:

1. Ga naar de map */src/main/java/com/communication/quickstart*
1. Open het bestand *App.java* in uw editor
1. De `System.out.println("Hello world!");`-instructie vervangen
1. Voeg `import`-instructies toe

Gebruik de volgende code om te beginnen:

```java
import com.azure.communication.common.CommunicationUser;
import com.azure.communication.administration.models.CommunicationIdentityToken;
import com.azure.communication.administration.CommunicationIdentityClient;
import com.azure.communication.administration.CommunicationIdentityClientBuilder;
import java.io.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - User Access Tokens Quickstart");
        // Quickstart code goes here
    }
}
```

[!INCLUDE [User Access Tokens Object Model](user-access-tokens-object-model.md)]

## <a name="authenticate-the-client"></a>De client verifiëren

Instanteer een `CommunicationIdentityClient` met de toegangssleutel en het eindpunt van uw resource. Meer informatie over het [beheren van de verbindingsreeks van uw resource](../create-communication-resource.md#store-your-connection-string).

Voeg de volgende code aan de `main` methode toe:

```java
// Your can find your endpoint and access token from your resource in the Azure Portal
String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";
String accessToken = "SECRET";

// Create an HttpClient builder of your choice and customize it
// Use com.azure.core.http.netty.NettyAsyncHttpClientBuilder if that suits your needs
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
    .endpoint(endpoint)
    .credential(new CommunicationClientCredential(accessToken))
    .httpClient(httpClient)
    .buildClient();
```

U kunt de client initialiseren met een aangepaste HTTP-client. Hiermee implementeert u de `com.azure.core.http.HttpClient`-interface. De bovenstaande code toont het gebruik van de [Azure Core Netty HTTP-client](https://docs.microsoft.com/java/api/overview/azure/core-http-netty-readme?view=azure-java-stable&preserve-view=true) die wordt verschaft door `azure-core`.

## <a name="create-a-user"></a>Een gebruiker maken

Azure Communication Services onderhoudt een lichte identiteitsmap. Gebruik de methode `createUser` om een nieuwe vermelding in de map te maken met een unieke `Id`. U moet een toewijzing onderhouden tussen de gebruikers van uw toepassing en de door de Communication Services gegenereerde identiteiten (bijv. door ze op te slaan in de database van uw toepassingsserver).

```java
CommunicationUser user = communicationIdentityClient.createUser();
System.out.println("\nCreated a user with ID: " + user.getId());
```

## <a name="issue-user-access-tokens"></a>Tokens voor gebruikerstoegang uitgeven

Gebruik de methode `issueToken` om een toegangstoken voor een Communication Services-gebruiker uit te geven. Als u de optionele `user` parameter niet opgeeft, wordt er een nieuwe gebruiker gemaakt en geretourneerd met het token.

```java
// Issue an access token with the "voip" scope for a new user
List<String> scopes = new ArrayList<>(Arrays.asList("voip"));
CommunicationUserToken response = communicationIdentityClient.issueToken(user, scopes);
OffsetDateTime expiresOn = response.getExpiresOn();
String token = response.getToken();
String userId = response.getUser().getId();
System.out.println("\nIssued a access token with 'voip' scope for identity with ID: " + userId + ": " + token);
System.out.println(token);
```

Tokens voor gebruikerstoegang zijn kortdurige referenties die opnieuw moeten worden uitgegeven om te voor komen dat uw gebruikers problemen ondervinden met de service. De responseigenschap `expiresAt` geeft de levensduur van het token aan.

## <a name="revoke-user-access-tokens"></a>Tokens voor gebruikerstoegang intrekken

In sommige gevallen moet u de tokens voor gebruikerstoegang wellicht intrekken, bijvoorbeeld wanneer een gebruiker het wachtwoord wijzigt dat wordt gebruikt voor verificatie bij uw service. U kunt de methode `revokeTokens` gebruiken om alle toegangstokens van een gebruiker ongeldig te maken.

```java  
communicationIdentityClient.revokeTokens(user, OffsetDateTime.now());
System.out.println("\nRevoked tokens for the user with ID: " + user.getId());
```

## <a name="delete-a-user"></a>Een gebruiker verwijderen

Als u een gebruiker verwijdert, worden alle actieve tokens ingetrokken en wordt voorkomen dat nieuwe tokens voor de identiteiten worden uitgegeven. Ook wordt alle persistente inhoud verwijderd die aan de gebruiker is gekoppeld.

```java
communicationIdentityClient.deleteUser(user);
System.out.println("\nSuccessfully deleted the identity with ID: " + user.getId());
```

## <a name="run-the-code"></a>De code uitvoeren

Navigeer naar de map die het bestand *pom.xml* bevat en compileer het project met behulp van de volgende `mvn`-opdracht.

```console
mvn compile
```

Bouw vervolgens het pakket.

```console
mvn package
```

Voer de volgende `mvn`-opdracht uit om de app uit te voeren.

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```
