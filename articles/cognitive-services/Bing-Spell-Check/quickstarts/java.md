---
title: 'Snelstartgids: spelling controleren met de REST API en Java-Bing Spellingcontrole'
titleSuffix: Azure Cognitive Services
description: Aan de slag met de Bing Spellingcontrole-REST-API om de spelling en grammatica te controleren.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.author: aahi
ms.openlocfilehash: f318a500bd4ce256690ff59f68d99af5d7a25d9e
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "83869799"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-java"></a>Snelstartgids: spelling controleren met de Bing Spellingcontrole REST API en Java

Gebruik deze quickstart om uw eerste aanroep naar de Bing Spellingcontrole REST API te maken. Deze eenvoudige Java-toepassing verzendt een aanvraag naar de API en retourneert een lijst met voorgestelde correcties. 

Hoewel deze toepassing wordt geschreven in Java, is de API een REST-webservice die compatibel is met de meeste programmeer talen. De bron code voor deze toepassing is beschikbaar op [github](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingSpellCheck.java).

## <a name="prerequisites"></a>Vereisten

* De Java Development Kit (JDK) 7 of hoger.

* Importeer de [gson-2.8.5. jar](https://libraries.io/maven/com.google.code.gson%3Agson) of de meest recente [gson](https://github.com/google/gson) -versie. Voor de uitvoering van de opdracht regel `.jar` moet u de map aan uw Java toevoegen met de hoofd klasse.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-an-application"></a>De toepassing maken en initialiseren

1. Maak een nieuw Java-project in uw favoriete IDE of editor met een klassen naam van uw keuze en importeer vervolgens de volgende pakketten:

    ```java
    import java.io.*;
    import java.net.*;
    import com.google.gson.*;
    import javax.net.ssl.HttpsURLConnection;
    ```

2. Maak variabelen voor het eindpunt, de host en het pad van de API, en uw abonnementssleutel. Maak vervolgens variabelen voor uw markt, de tekst die u wilt controleren en een teken reeks voor de spelling controle modus. U kunt het globale eind punt in de volgende code gebruiken of het [aangepaste subdomein](../../../cognitive-services/cognitive-services-custom-subdomains.md) eindpunt gebruiken dat wordt weer gegeven in de Azure portal voor uw resource.

    ```java
    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/spellcheck";

    static String key = "<ENTER-KEY-HERE>";

    static String mkt = "en-US";
    static String mode = "proof";
    static String text = "Hollo, wrld!";
    ```

## <a name="create-and-send-an-api-request"></a>Een API-aanvraag maken en verzenden

1. Maak een functie met de naam `check()` om de API-aanvraag te maken en verzenden. In deze functie voegt u de code toe die u in de volgende stappen hebt opgegeven. Een teken reeks maken voor de aanvraag parameters:

   a. Wijs uw markt code toe aan de `mkt` para meter met de `=` operator. 

   b. Voeg de `mode` para meter met de `&` operator toe en wijs vervolgens de spelling controle modus toe. 

   ```java
   public static void check () throws Exception {
       String params = "?mkt=" + mkt + "&mode=" + mode;
      // add the rest of the code snippets here (except prettify() and main())...
   }
   ```

2. Maak een URL door de teken reeks voor het eindpunt, het pad en de para meters te combi neren. Een nieuw `HttpsURLConnection` object maken.

    ```java
    URL url = new URL(host + path + params);
    HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
    ```

3. Open een verbinding met de URL. Stel de aanvraag methode in op `POST` en voeg uw aanvraag parameters toe. Zorg ervoor dat u uw abonnements sleutel aan de `Ocp-Apim-Subscription-Key` koptekst toevoegt.

    ```java
    connection.setRequestMethod("POST");
    connection.setRequestProperty("Content-Type", "application/x-www-form-urlencoded");
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", key);
    connection.setDoOutput(true);
    ```

4. Maak een nieuw `DataOutputStream` object en verzend de aanvraag naar de API.

    ```java
        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        wr.writeBytes("text=" + text);
        wr.flush();
        wr.close();
    ```

## <a name="format-and-read-the-api-response"></a>De API-reactie opmaken en lezen

1. Voeg de `prettify()` methode toe aan uw klasse, waarmee de JSON wordt opgemaakt voor een beter Lees bare uitvoer.

    ``` java
    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }
    ```

1. Maak een `BufferedReader` en lees het antwoord van de API. Druk het af naar de console.
    
    ```java
    BufferedReader in = new BufferedReader(
    new InputStreamReader(connection.getInputStream()));
    String line;
    while ((line = in.readLine()) != null) {
        System.out.println(prettify(line));
    }
    in.close();
    ```

## <a name="call-the-api"></a>De API aanroepen

In de hoofd functie van uw toepassing roept u de `check()` methode aan die u eerder hebt gemaakt.
```java
        public static void main(String[] args) {
            try {
                check();
            }
            catch (Exception e) {
                System.out.println (e);
            }
        }
```

## <a name="run-the-application"></a>De toepassing uitvoeren

Uw project bouwen en uitvoeren. Als u de opdracht regel gebruikt, gebruikt u de volgende opdrachten om de toepassing te bouwen en uit te voeren:

1. De toepassing bouwen:

   ```bash
   javac -classpath .;gson-2.2.2.jar\* <CLASS_NAME>.java
   ```

2. Voer de toepassing uit:

   ```bash
   java -cp .;gson-2.2.2.jar\* <CLASS_NAME>
   ```

## <a name="example-json-response"></a>Voorbeeld van JSON-antwoord

Een geslaagd antwoord wordt geretourneerd in de JSON-indeling, zoals u kunt zien in het volgende voorbeeld:

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app met één pagina maken](../tutorials/spellcheck.md)

- [Wat is de Bing Spellingcontrole-API?](../overview.md)
- [Naslag informatie over Bing Spellingcontrole-API V7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
