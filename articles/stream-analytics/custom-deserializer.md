---
title: Zelfstudie - Aangepaste .NET-deserializers voor Azure Stream Analytics-cloudtaken
description: In deze zelfstudie wordt uitgelegd hoe u een aangepaste .NET-deserializer maakt voor een Azure Stream Analytics-cloudtaak met Visual Studio.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 05/06/2019
ms.openlocfilehash: 1fffeec1434cb066487bf383589554edec2e6a86
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75443697"
---
# <a name="tutorial-custom-net-deserializers-for-azure-stream-analytics"></a>Zelfstudie: Aangepaste .NET-deserializers voor Azure Stream Analytics

Azure Stream Analytics heeft [ingebouwde ondersteuning voor drie gegevensindelingen:](stream-analytics-parsing-json.md)JSON, CSV en Avro. Met aangepaste .NET-deserializers u gegevens uit andere indelingen lezen, zoals [Protocol Buffer,](https://developers.google.com/protocol-buffers/) [Bond](https://github.com/Microsoft/bond) en andere door de gebruiker gedefinieerde indelingen voor zowel cloud- als edge-taken.

In deze zelfstudie wordt uitgelegd hoe u een aangepaste .NET-deserializer maakt voor een Azure Stream Analytics-cloudtaak met Visual Studio. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Maak een aangepaste deserializer voor protocolbuffer.
> * Maak een Azure Stream Analytics-taak in Visual Studio.
> * Configureer uw Stream Analytics-taak om de aangepaste deserializer te gebruiken.
> * Voer uw Stream Analytics-taak lokaal uit om de aangepaste deserializer te testen.

## <a name="prerequisites"></a>Vereisten

* Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)aan.

* Visual [Studio 2017](https://www.visualstudio.com/downloads/) of [Visual Studio 2015](https://www.visualstudio.com/vs/older-downloads/)installeren . Enterprise- (Ultimate/Premium), Professional- en Community-edities worden ondersteund. Express-editie wordt niet ondersteund.

* [Installeer de Tools Stream Analytics voor Visual Studio](stream-analytics-tools-for-visual-studio-install.md) of update naar de nieuwste versie. De volgende versies van Visual Studio worden ondersteund:
   * Visual Studio 2015
   * Visual Studio 2017

* Open **Cloud Explorer** in Visual Studio en meld u aan bij uw Azure-abonnement.

* Maak een container in uw Azure Storage-account.
De container die u maakt, wordt gebruikt om assets op te slaan die verband houden met uw Stream Analytics-taak. Als u al een opslagaccount met bestaande containers hebt, kunt u die gebruiken. Zo niet, [maak dan een nieuwe container](../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="create-a-custom-deserializer"></a>Een aangepaste deserializer maken

1. Open Visual Studio en selecteer **Bestand > Nieuw > Project**. Zoek naar **Stream Analytics** en selecteer Azure Stream Analytics **Custom Deserializer Project (.NET)**. Geef het project een naam, zoals **Protobuf Deserializer**.

   ![Visual Studio dotnet-standaardbibliotheekproject maken](./media/custom-deserializer/create-dotnet-library-project.png)

2. Klik in Solution Explorer met de rechtermuisknop op uw **Protobuf Deserializer-project** en selecteer **NuGet-pakketten beheren** in het menu. Installeer vervolgens de **NuGet-pakketten van Microsoft.Azure.StreamAnalytics** en **Google.Protobuf.**

3. Voeg de [klasse MessageBodyProto](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyProto.cs) en de [klasse MessageBodyDeserializer](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyDeserializer.cs) toe aan uw project.

4. Bouw het **Protobuf Deserializer** project.

## <a name="add-an-azure-stream-analytics-project"></a>Een Azure Stream Analytics-project toevoegen

1. Klik in Solution Explorer met de rechtermuisknop op de **Protobuf Deserializer-oplossing** en selecteer **> nieuw project toevoegen**. Kies **onder Azure Stream Analytics > Stream Analytics**de optie Azure Stream **Analytics-toepassing**. Noem het **ProtobufCloudDeserializer** en selecteer **OK**. 

2. Klik met de rechtermuisknop **op Verwijzingen** onder het **ProtobufCloudDeserializer** Azure Stream Analytics-project. Voeg **onder Projecten** **Protobuf Deserializer**toe . Het moet automatisch worden ingevuld voor je.

## <a name="configure-a-stream-analytics-job"></a>Een functie Stream Analytics configureren

1. Dubbelklik op **JobConfig.json**. Gebruik de standaardconfiguraties, met uitzondering van de volgende instellingen:

   |Instelling|Voorgestelde waarde|
   |-------|---------------|
   |Bron voor globale opslaginstellingen|Kies gegevensbron van het huidige account|
   |Abonnement op globale opslaginstellingen| < uw abonnement >|
   |Opslagaccount voor globale opslaginstellingen| < uw opslagaccount >|
   |Bron van aangepaste codeopslaginstellingen|Kies gegevensbron van het huidige account|
   |Opslagaccount voor aangepaste code-opslaginstellingen|< uw opslagaccount >|
   |Container met aangepaste codeopslaginstellingen|< uw opslagcontainer >|

2. Dubbelklik **onder Invoer**op **Input.json**. Gebruik de standaardconfiguraties, met uitzondering van de volgende instellingen:

   |Instelling|Voorgestelde waarde|
   |-------|---------------|
   |Bron|Blob Storage|
   |Resource|Kies gegevensbron van het huidige account|
   |Abonnement|< uw abonnement >|
   |Opslagaccount|< uw opslagaccount >|
   |Container|< uw opslagcontainer >|
   |Indeling voor gebeurtenisserialisatie|Andere (Protobuf, XML, eigen...)|
   |Resource|Laden vanaf ASA Project Reference of CodeBehind|
   |CSharp-assemblagenaam|ProtobufDeserializer.dll|
   |Klassennaam|MessageBodyProto.MessageBodyDeserializer|
   |Gebeurteniscompressietype|Geen|

3. Voeg de volgende query toe aan het **Script.asaql-bestand.**

   ```sql
   SELECT * FROM Input
   ```

4. Download het [voorbeeldprotobuf-invoerbestand](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/SimulatedTemperatureEvents.protobuf). Klik **in** de map Invoer met de rechtermuisknop op **Input.json** en selecteer **Lokale invoer toevoegen**. Dubbelklik vervolgens op **local_Input.json** en configureer de volgende instellingen:

   |Instelling|Voorgestelde waarde|
   |-------|---------------|
   |Invoeralias|Invoer|
   |Brontype|Gegevensstroom|
   |Indeling voor gebeurtenisserialisatie|Andere (Protobuf, XML, eigen...)|
   |CSharp-assemblagenaam|ProtobufDeserializer.dll|
   |Klassennaam|MessageBodyProto.MessageBodyDeserializer|
   |Pad lokaal invoerbestand|< het bestandspad voor het gedownloade protobuf-invoerbestand>|

## <a name="execute-the-stream-analytics-job"></a>De taak Stream Analytics uitvoeren

1. Open **Script.asaql** en selecteer **Lokaal uitvoeren**.

2. Bekijk de resultaten in **de resultaten van Lokale runresultaten van Stream Analytics.**

Je hebt met succes een custom deserializer geïmplementeerd voor je Stream Analytics-taak! In deze zelfstudie hebt u de aangepaste deserializer lokaal getest. Voor uw werkelijke gegevens configureert u de invoer en uitvoer correct. Verzend de taak vervolgens in bij Azure vanuit Visual Studio om uw taak in de cloud uit te voeren met behulp van de aangepaste deserializer die u zojuist hebt geïmplementeerd.

## <a name="debug-your-deserializer"></a>Debuging uw deserializer

U uw .NET-deserializer lokaal debuggen op dezelfde manier als u standaard .NET-code debugt. 

1. Voeg breekpunten toe aan uw functie.

2. Druk op **F5** om de foutopsporing te starten. Het programma stopt bij de onderbrekingspunten, zoals verwacht.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u een resourcegroep niet meer nodig hebt, verwijdert u de resourcegroep, de streamingtaak en alle gerelateerde resources. Door de taak te verwijderen, voorkomt u dat de streaming-eenheden die door de taak worden verbruikt, in rekening worden gebracht. Als u denkt dat u de taak in de toekomst nog gaat gebruiken, kunt u deze stoppen en later opnieuw starten wanneer dat nodig is. Als u deze taak niet meer gaat gebruiken, verwijdert u alle resources die in deze zelfstudie zijn gemaakt. Daarvoor voert u de volgende stappen uit:

1. Selecteer in het menu aan de linkerkant in Azure Portal de optie **Resourcegroepen** en selecteer vervolgens de resource die u hebt gemaakt.  

2. Selecteer op de pagina van uw resourcegroep de optie **Verwijderen**, typ de naam van de resource die u wilt verwijderen in het tekstvak en selecteer vervolgens **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een aangepaste .NET-deserializer implementeert voor de serialisatie van de protocolbufferinvoer. Ga voor meer informatie over het maken van aangepaste deserializers verder naar het volgende artikel:

> [!div class="nextstepaction"]
> [Verschillende .NET-deserializers maken voor Azure Stream Analytics-taken](custom-deserializer-examples.md)
