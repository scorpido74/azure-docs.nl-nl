---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 82766e969f62f35f39f6545eb4c2c6bdda29c11b
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83586500"
---
[!INCLUDE [Prerequisites](prerequisites-java.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="initialize-a-project-with-gradle"></a>Een project initialiseren met Gradle

We beginnen met het maken van een werkmap voor dit project. Voer de volgende opdracht uit vanaf de opdrachtregel (of terminal):

```console
mkdir transliterate-sample
cd transliterate-sample
```

Vervolgens gaat u een Gradle-project initialiseren. Met deze opdracht maakt u essentiële buildbestanden voor Gradle, maar nog belangrijker is dat ook `build.gradle.kts` wordt gemaakt, dat tijdens runtime wordt gebruikt om de toepassing te maken en te configureren. Voer de volgende opdracht uit vanuit uw werkmap:

```console
gradle init --type basic
```

Wanneer u wordt gevraagd om een **DSL** te kiezen, selecteert u **Kotlin**.

## <a name="configure-the-build-file"></a>Het buildbestand configureren

Zoek `build.gradle.kts` en open dit met uw favoriete IDE- of teksteditor. Kopieer het vervolgens in deze buildconfiguratie:

```
plugins {
    java
    application
}
application {
    mainClassName = "Transliterate"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("com.squareup.okhttp:okhttp:2.5.0")
    compile("com.google.code.gson:gson:2.8.5")
}
```

Opmerking: dit voorbeeld heeft afhankelijkheden op OkHttp voor HTTP-aanvragen, en Gson voor het verwerken en parseren van JSON. Zie [Nieuwe Gradle-builds maken](https://guides.gradle.org/creating-new-gradle-builds/) voor meer informatie over buildconfiguraties.

## <a name="create-a-java-file"></a>Een Java-bestand maken

We gaan een map maken voor de voorbeeld-app. Voer vanuit uw werkmap de volgende opdracht uit:

```console
mkdir -p src\main\java
```

Maak vervolgens in deze map een bestand met de naam `Transliterate.java`.

## <a name="import-required-libraries"></a>Vereiste bibliotheken importeren

Open `Transliterate.java` en voeg deze importinstructies toe:

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;
```


## <a name="define-variables"></a>Variabelen definiëren

U moet eerst een openbare klasse voor uw project maken:

```java
public class Transliterate {
  // All project code goes here...
}
```

Voeg deze regels toe aan de klasse `Transliterate`. Eerst worden de abonnements sleutel en het eind punt gelezen van omgevings variabelen. U ziet dat samen met de `api-version` nog twee aanvullende para meters zijn toegevoegd aan de `url` . Deze parameters worden gebruikt om de invoertaal en de scripts voor transliteratie in te stellen. In dit voorbeeld is de taal ingesteld op Japans (`jpan`) en Latijns (`latn`). 

```java
private static String subscriptionKey = System.getenv("TRANSLATOR_TEXT_SUBSCRIPTION_KEY");
private static String endpoint = System.getenv("TRANSLATOR_TEXT_ENDPOINT");
String url = endpoint + "/transliterate?api-version=3.0&language=ja&fromScript=jpan&toScript=latn";
```
Als u een Cognitive Services abonnement op meerdere services gebruikt, moet u ook de `Ocp-Apim-Subscription-Region` in uw aanvraag parameters toevoegen. Meer [informatie over verificatie met het multi-service-abonnement](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="create-a-client-and-build-a-request"></a>Een client maken en een aanvraag samenstellen

Voeg deze regel toe aan de klasse `Transliterate` om de `OkHttpClient` te instantiëren:

```java
// Instantiates the OkHttpClient.
OkHttpClient client = new OkHttpClient();
```

Stel vervolgens de POST-aanvraag samen. Met het oog op transliteratie kunt u de tekst naar wens wijzigen.

```java
// This function performs a POST request.
public String Post() throws IOException {
    MediaType mediaType = MediaType.parse("application/json");
    RequestBody body = RequestBody.create(mediaType,
            "[{\n\t\"Text\": \"こんにちは\"\n}]");
    Request request = new Request.Builder()
            .url(url).post(body)
            .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
            .addHeader("Content-type", "application/json").build();
    Response response = client.newCall(request).execute();
    return response.body().string();
}
```

## <a name="create-a-function-to-parse-the-response"></a>Een functie maken voor het parseren van het antwoord

Met deze eenvoudige functie wordt het JSON-antwoord van de service Translator geparseerd en prettifies.

```java
// This function prettifies the json response.
public static String prettify(String json_text) {
    JsonParser parser = new JsonParser();
    JsonElement json = parser.parse(json_text);
    Gson gson = new GsonBuilder().setPrettyPrinting().create();
    return gson.toJson(json);
}
```

## <a name="put-it-all-together"></a>Alles samenvoegen

De laatste stap bestaat uit het maken van een aanvraag en het ontvangen van een antwoord. Voeg deze regels toe aan het project:

```java
public static void main(String[] args) {
    try {
        Transliterate transliterateRequest = new Transliterate();
        String response = transliterateRequest.Post();
        System.out.println(prettify(response));
    } catch (Exception e) {
        System.out.println(e);
    }
}
```

## <a name="run-the-sample-app"></a>De voorbeeld-app uitvoeren

Nu kunt u de voorbeeld-app gaan uitvoeren. Ga vanaf de opdrachtregel (of terminalsessie) naar de hoofdmap van uw werkmap en voer de volgende opdracht uit:

```console
gradle build
```

Wanneer de build is voltooid, voert u het volgende uit:

```console
gradle run
```

## <a name="sample-response"></a>Voorbeeldantwoord

```json
[
  {
    "text": "konnichiwa",
    "script": "latn"
  }
]
```

## <a name="next-steps"></a>Volgende stappen

Bekijk de API-verwijzing voor meer informatie over wat u met de vertaler kunt doen.

> [!div class="nextstepaction"]
> [API-verwijzing](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
