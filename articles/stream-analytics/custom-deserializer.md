---
title: Zelf studie-aangepaste .net-deserialisatie voor Azure stream Analytics Cloud taken
description: In deze zelf studie wordt gedemonstreerd hoe u een aangepaste .NET-deserialisatie maakt voor een Azure Stream Analytics Cloud taak met behulp van Visual Studio.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 05/06/2019
ms.openlocfilehash: 1fffeec1434cb066487bf383589554edec2e6a86
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75443697"
---
# <a name="tutorial-custom-net-deserializers-for-azure-stream-analytics"></a>Zelf studie: aangepaste .NET-deserialers voor Azure Stream Analytics

Azure Stream Analytics heeft [ingebouwde ondersteuning voor drie gegevens indelingen](stream-analytics-parsing-json.md): JSON, CSV en AVRO. Met aangepaste .net-deserialisatie kunt u gegevens lezen uit andere indelingen, zoals [protocol buffer](https://developers.google.com/protocol-buffers/), [obligatie](https://github.com/Microsoft/bond) en andere door de gebruiker gedefinieerde notaties voor Cloud-en Edge-taken.

In deze zelf studie wordt gedemonstreerd hoe u een aangepaste .NET-deserialisatie maakt voor een Azure Stream Analytics Cloud taak met behulp van Visual Studio. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Maak een aangepaste deserializer voor protocol buffer.
> * Maak een Azure Stream Analytics-taak in Visual Studio.
> * Configureer uw Stream Analytics-taak voor het gebruik van de aangepaste deserializer.
> * Voer uw Stream Analytics-taak lokaal uit om de aangepaste deserializer te testen.

## <a name="prerequisites"></a>Vereisten

* Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Installeer [Visual studio 2017](https://www.visualstudio.com/downloads/) of [Visual Studio 2015](https://www.visualstudio.com/vs/older-downloads/). Enterprise- (Ultimate/Premium), Professional- en Community-edities worden ondersteund. De Express-editie wordt niet ondersteund.

* [Installeer de stream Analytics-hulpprogram ma's voor Visual Studio](stream-analytics-tools-for-visual-studio-install.md) of werk bij naar de nieuwste versie. De volgende versies van Visual Studio worden ondersteund:
   * Visual Studio 2015
   * Visual Studio 2017

* Open **Cloud Explorer** in Visual Studio en meld u aan bij uw Azure-abonnement.

* Maak een container in uw Azure Storage-account.
De container die u maakt, wordt gebruikt voor het opslaan van assets die zijn gerelateerd aan uw Stream Analytics-taak. Als u al een opslagaccount met bestaande containers hebt, kunt u die gebruiken. Zo niet, [maak dan een nieuwe container](../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="create-a-custom-deserializer"></a>Een aangepaste deserializer maken

1. Open Visual Studio en selecteer **bestand > nieuw > project**. Zoek naar **Stream Analytics** en selecteer **Azure stream Analytics aangepaste deserialisatie project (.net)**. Geef het project een naam, zoals **protobuf deserializer**.

   ![Een Visual Studio DotNet Standard-klassen bibliotheek project maken](./media/custom-deserializer/create-dotnet-library-project.png)

2. Klik in Solution Explorer met de rechter muisknop op het **protobuf** -project en selecteer **NuGet-pakketten beheren** in het menu. Installeer vervolgens de NuGet-pakketten **micro soft. Azure. StreamAnalytics** en **Google. protobuf** .

3. Voeg de [MessageBodyProto-klasse](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyProto.cs) en de [MessageBodyDeserializer-klasse](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyDeserializer.cs) toe aan uw project.

4. Bouw het **protobuf-deserialisatie** project op.

## <a name="add-an-azure-stream-analytics-project"></a>Een Azure Stream Analytics project toevoegen

1. Klik in Solution Explorer met de rechter muisknop op de oplossing voor het **deserialiseren** van de protobuf en selecteer **> nieuw project toevoegen**. Kies onder **Azure Stream Analytics > Stream Analytics** **Azure stream Analytics toepassing**. Noem deze **ProtobufCloudDeserializer** en selecteer **OK**. 

2. Klik met de rechter muisknop op **verwijzingen** onder het **ProtobufCloudDeserializer** -Azure stream Analytics project. Voeg onder **projecten** **protobuf deserializer**toe. Deze moet automatisch voor u worden ingevuld.

## <a name="configure-a-stream-analytics-job"></a>Een Stream Analytics taak configureren

1. Dubbel klik op **JobConfig. json**. Gebruik de standaard configuraties, met uitzonde ring van de volgende instellingen:

   |Instelling|Voorgestelde waarde|
   |-------|---------------|
   |Resource voor algemene opslag instellingen|Kies gegevensbron van het huidige account|
   |Abonnement voor globale opslag instellingen| < uw abonnement >|
   |Opslag account voor algemene opslag instellingen| < uw opslag account >|
   |Resource voor aangepaste code opslag instellingen|Kies gegevensbron van het huidige account|
   |Opslag account voor aangepaste code opslag|< uw opslag account >|
   |Container voor aangepaste code opslag|< uw opslag container >|

2. Dubbel **Klik onder invoer op** **input. json**. Gebruik de standaard configuraties, met uitzonde ring van de volgende instellingen:

   |Instelling|Voorgestelde waarde|
   |-------|---------------|
   |Bron|Blob Storage|
   |Resource|Kies gegevensbron van het huidige account|
   |Abonnement|< uw abonnement >|
   |Opslagaccount|< uw opslag account >|
   |Container|< uw opslag container >|
   |Serialisatie-indeling voor gebeurtenissen|Overige (protobuf, XML, eigen...)|
   |Resource|Laden van ASA-project referentie of CodeBehind|
   |Naam van CSharp-assembly|ProtobufDeserializer. dll|
   |Klassenaam|MessageBodyProto.MessageBodyDeserializer|
   |Type gebeurtenis compressie|Geen|

3. Voeg de volgende query toe aan het **script. asaql** -bestand.

   ```sql
   SELECT * FROM Input
   ```

4. Down load het voor [beeld-protobuf-invoer bestand](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/SimulatedTemperatureEvents.protobuf). Klik in de map **ingangen** met de rechter muisknop op **input. json** en selecteer **lokale invoer toevoegen**. Dubbel klik vervolgens op **local_Input. json** en configureer de volgende instellingen:

   |Instelling|Voorgestelde waarde|
   |-------|---------------|
   |Invoeralias|Invoer|
   |Brontype|Gegevensstroom|
   |Serialisatie-indeling voor gebeurtenissen|Overige (protobuf, XML, eigen...)|
   |Naam van CSharp-assembly|ProtobufDeserializer. dll|
   |Klassenaam|MessageBodyProto.MessageBodyDeserializer|
   |Pad naar lokaal invoer bestand|< het bestandspad voor het gedownloade protobuf-invoer bestand>|

## <a name="execute-the-stream-analytics-job"></a>De Stream Analytics-taak uitvoeren

1. Open **script. asaql** en selecteer **lokaal uitvoeren**.

2. Bekijk de resultaten in **Stream Analytics lokale uitvoerings resultaten**.

U hebt een aangepaste deserializer geïmplementeerd voor uw Stream Analytics-taak! In deze zelf studie hebt u de aangepaste deserializer lokaal getest. Voor uw werkelijke gegevens kunt u de invoer en uitvoer op de juiste manier configureren. Verzend de taak vervolgens naar Azure vanuit Visual Studio om uw taak uit te voeren in de Cloud met behulp van de aangepaste deserializer die u zojuist hebt geïmplementeerd.

## <a name="debug-your-deserializer"></a>Fouten opsporen in uw deserializer

U kunt fouten opsporen in uw .NET-deserialisatie op dezelfde manier als bij fout opsporing voor standaard-.NET-code. 

1. Voeg onderbrekings punten toe in uw functie.

2. Druk op **F5** om de foutopsporing te starten. Het programma stopt bij de onderbrekingspunten, zoals verwacht.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u een resourcegroep niet meer nodig hebt, verwijdert u de resourcegroep, de streamingtaak en alle gerelateerde resources. Door de taak te verwijderen, voorkomt u dat de streaming-eenheden die door de taak worden verbruikt, in rekening worden gebracht. Als u denkt dat u de taak in de toekomst nog gaat gebruiken, kunt u deze stoppen en later opnieuw starten wanneer dat nodig is. Als u deze taak niet meer gaat gebruiken, verwijdert u alle resources die in deze zelfstudie zijn gemaakt. Daarvoor voert u de volgende stappen uit:

1. Selecteer in het menu aan de linkerkant in Azure Portal de optie **Resourcegroepen** en selecteer vervolgens de resource die u hebt gemaakt.  

2. Selecteer op de pagina van uw resourcegroep de optie **Verwijderen**, typ de naam van de resource die u wilt verwijderen in het tekstvak en selecteer vervolgens **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u een aangepaste .NET-deserialisatie implementeert voor de protocol buffer invoer-serialisatie. Ga verder naar het volgende artikel voor meer informatie over het maken van aangepaste deserialisatie:

> [!div class="nextstepaction"]
> [Verschillende .net-deserialisatie maken voor Azure stream Analytics-taken](custom-deserializer-examples.md)
