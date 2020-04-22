---
title: De Azure IoT-apparaat SDK voor C | Microsoft Documenten
description: Ga aan de slag met de Azure IoT-apparaat SDK voor C en leer hoe u apparaatapps maakt die communiceren met een IoT-hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: robinsh
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: b9b27bb142cb729536a3b7a561ed8b8ff5e0ccf5
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731308"
---
# <a name="azure-iot-device-sdk-for-c"></a>Azure IoT-apparaat SDK voor C

De **Azure IoT-apparaat SDK** is een set bibliotheken die zijn ontworpen om het proces van het verzenden van berichten naar en het ontvangen van berichten van de Azure **IoT Hub-service** te vereenvoudigen. Er zijn verschillende varianten van de SDK, die elk gericht zijn op een specifiek platform, maar in dit artikel wordt de **Azure IoT-apparaat SDK voor C beschreven.**

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

De Azure IoT-apparaat SDK voor C is geschreven in ANSI C (C99) om de draagbaarheid te maximaliseren. Deze functie maakt de bibliotheken zeer geschikt om te werken op meerdere platforms en apparaten, vooral waar het minimaliseren van schijf- en geheugenvoetafdruk een prioriteit is.

Er zijn een breed scala aan platforms waarop de SDK is getest (zie de [Azure Certified for IoT-apparaatcatalogus](https://catalog.azureiotsolutions.com/) voor meer informatie). Hoewel dit artikel walkthroughs bevat van voorbeeldcode die wordt uitgevoerd op het Windows-platform, is de code die in dit artikel wordt beschreven identiek over het bereik van ondersteunde platforms.

De volgende video bevat een overzicht van de Azure IoT SDK voor C:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-IoT-C-SDK-insights/Player]

In dit artikel wordt u kennis laten maken met de architectuur van de Azure IoT-apparaat SDK voor C. Het laat zien hoe u de apparaatbibliotheek initialiseren, gegevens naar IoT Hub verzenden en er berichten van ontvangen. De informatie in dit artikel moet voldoende zijn om aan de slag te gaan met de SDK, maar biedt ook aanwijzingen voor aanvullende informatie over de bibliotheken.

## <a name="sdk-architecture"></a>SDK-architectuur

U de [**Azure IoT-apparaat SDK voor C**](https://github.com/Azure/azure-iot-sdk-c) GitHub-repository vinden en details van de API weergeven in de C [API-verwijzing.](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

De nieuwste versie van de bibliotheken is te vinden in de **hoofdvestiging** van de repository:

  ![Schermafbeelding van de hoofdvertakking van de opslagplaats](./media/iot-hub-device-sdk-c-intro/RepoMasterBranch.png)

* De kernimplementatie van de SDK bevindt zich in de **iothub-clientmap\_** die de implementatie van de laagste API-laag in de SDK bevat: de **IoTHubClient-bibliotheek.** De **IoTHubClient-bibliotheek** bevat API's die raw messaging implementeren voor het verzenden van berichten naar IoT Hub en het ontvangen van berichten van IoT Hub. Wanneer u deze bibliotheek gebruikt, bent u verantwoordelijk voor het implementeren van berichtserialisatie, maar andere details over het communiceren met IoT Hub worden voor u behandeld.

* De **serialisatormap** bevat helperfuncties en voorbeelden die u laten zien hoe u gegevens serialiseren voordat u deze met de clientbibliotheek naar Azure IoT Hub verzendt. Het gebruik van de serialisator is niet verplicht en wordt verstrekt als een gemak. Als u de **serializer-bibliotheek wilt** gebruiken, definieert u een model dat de gegevens opgeeft die naar IoT Hub moeten worden verzonden en welke berichten u ervan verwacht te ontvangen. Zodra het model is gedefinieerd, biedt de SDK u een API-oppervlak waarmee u eenvoudig werken met berichten van apparaat tot cloud en cloud-to-device zonder u zorgen te hoeven maken over de serialisatiedetails. De bibliotheek is afhankelijk van andere open-source bibliotheken die transport implementeren met behulp van protocollen zoals MQTT en AMQP.

* De **IoTHubClient-bibliotheek** is afhankelijk van andere open-sourcebibliotheken:

  * De [azure C-gesynchroniseerde hulpprogrammabibliotheek,](https://github.com/Azure/azure-c-shared-utility) die algemene functionaliteit biedt voor basistaken (zoals tekenreeksen, lijstmanipulatie en IO) die nodig zijn voor verschillende Azure-gerelateerde Cd-s.Co

  * De [Azure uAMQP-bibliotheek,](https://github.com/Azure/azure-uamqp-c) een client-side implementatie van AMQP geoptimaliseerd voor resource beperkte apparaten.

  * De [Azure uMQTT-bibliotheek,](https://github.com/Azure/azure-umqtt-c) een bibliotheek voor algemene doeleinden die het MQTT-protocol implementeert en is geoptimaliseerd voor apparaten met beperkte resources.

Het gebruik van deze bibliotheken is gemakkelijker te begrijpen door te kijken naar voorbeeldcode. De volgende secties lopen u door een aantal van de voorbeeldtoepassingen die zijn opgenomen in de SDK. Deze walkthrough moet u een goed gevoel geven voor de verschillende mogelijkheden van de architecturale lagen van de SDK en een inleiding tot hoe de API's werken.

## <a name="before-you-run-the-samples"></a>Voordat u de monsters uitvoert

Voordat u de voorbeelden in de Azure IoT-apparaat SDK voor C uitvoeren, moet u [een instantie van de IoT Hub-service maken](iot-hub-create-through-portal.md) in uw Azure-abonnement. Voer vervolgens de volgende taken uit:

* Uw ontwikkelomgeving voorbereiden
* Apparaatreferenties verkrijgen.

### <a name="prepare-your-development-environment"></a>Uw ontwikkelomgeving voorbereiden

Pakketten worden verstrekt voor gemeenschappelijke platforms (zoals NuGet voor Windows of apt_get voor Debian en Ubuntu) en de monsters gebruiken deze pakketten indien beschikbaar. In sommige gevallen moet u de SDK voor of op uw apparaat compileren. Als u de SDK wilt compileren, [raadpleegt u Uw ontwikkelomgeving voorbereiden](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) in de GitHub-repository.

Download een kopie van de SDK van GitHub om de voorbeeldcode van de toepassing te verkrijgen. Haal uw exemplaar van de bron uit de **hoofdvestiging** van de [GitHub-repository.](https://github.com/Azure/azure-iot-sdk-c)


### <a name="obtain-the-device-credentials"></a>De apparaatreferenties verkrijgen

Nu u de broncode van het voorbeeld hebt, moet u vervolgens een set apparaatreferenties krijgen. Als u een apparaat toegang wilt geven tot een IoT-hub, moet u het apparaat eerst toevoegen aan het identiteitsregister van de IoT Hub. Wanneer u uw apparaat toevoegt, krijgt u een set apparaatreferenties die u nodig hebt om het apparaat verbinding te kunnen maken met de IoT-hub. De voorbeeldtoepassingen die in de volgende sectie worden besproken, verwachten deze referenties in de vorm van een **tekenreeks voor apparaatverbinding.**

Er zijn verschillende open-source tools om u te helpen uw IoT-hub te beheren.

* Een Windows-toepassing genaamd [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer).

* Een cross-platform Visual Studio Code extensie genaamd [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

* Een cross-platform Python CLI genaamd [de IoT-extensie voor Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

Deze zelfstudie maakt gebruik van het grafische *hulpmiddel explorer.* U de *Azure IoT-hulpprogramma's voor VS-code* gebruiken als u zich ontwikkelt in VS-code. U de *IoT-extensie voor Azure CLI 2.0* ook gebruiken als u liever een CLI-tool gebruikt.

Het hulpprogramma voor apparaatverkenners gebruikt de Azure IoT-servicebibliotheken om verschillende functies uit te voeren op IoT Hub, waaronder het toevoegen van apparaten. Als u het gereedschap Apparaatverkenner gebruikt om een apparaat toe te voegen, krijgt u een verbindingstekenreeks voor uw apparaat. U hebt deze verbindingstekenreeks nodig om de voorbeeldtoepassingen uit te voeren.

Als u niet bekend bent met het apparaatverkenners, wordt in de volgende procedure beschreven hoe u het gebruiken om een apparaat toe te voegen en een tekenreeks voor apparaatverbinding te verkrijgen.

1. Zie De Device Explorer [voor IoT Hub-apparaten gebruiken](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer)als u het gereedschap Device Explorer wilt installeren.

1. Wanneer u het programma uitvoert, ziet u deze interface:

   ![Schermafbeelding van Device Explorer Twin](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinConfigTab.png)

1. Voer uw **IoT Hub Connection String** in het eerste veld in en klik op **Bijwerken**. Met deze stap configureert u de tool zodat deze kan communiceren met IoT Hub. 

De **verbindingstekenreeks** is te vinden onder **IoT Hub Service** > **Settings** > **Shared Access Policy** > **iothubowner**.

1. Wanneer de verbindingstekenreeks van de IoT-hub is geconfigureerd, klikt u op het tabblad **Beheer:**

   ![Device Explorer Twin / Schermbeheer](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinManagementTab.png)

Op dit tabblad beheert u de apparaten die zijn geregistreerd in uw IoT-hub.

1. U maakt een apparaat door op de knop **Maken** te klikken. Een dialoogvenster wordt weergegeven met een set vooraf ingevulde toetsen (primair en secundair). Voer een **apparaat-id in** en klik op **Maken**.

   ![Schermafbeelding apparaat maken](./media/iot-hub-device-sdk-c-intro/CreateDevice.png)

1. Wanneer het apparaat wordt gemaakt, worden de apparaten lijst bijgewerkt met alle geregistreerde apparaten, met inbegrip van degene die u zojuist hebt gemaakt. Als u met de rechtermuisknop op uw nieuwe apparaat klikt, ziet u dit menu:

   ![Met de rechtermuisknop op Device Explorer Twin](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinManagementTab_RightClick.png)

1. Als u **Verbindingstekenreeks kopiëren voor geselecteerd apparaat**kiest, wordt de tekenreeks voor apparaatverbinding naar het klembord gekopieerd. Bewaar een kopie van de tekenreeks van de apparaatverbinding. U hebt het nodig bij het uitvoeren van de voorbeeldtoepassingen die in de volgende secties zijn beschreven.

Wanneer u de bovenstaande stappen hebt voltooid, u beginnen met het uitvoeren van bepaalde code. De meeste voorbeelden hebben een constante boven aan het hoofdbronbestand waarmee u een verbindingstekenreeks invoeren. De bijbehorende regel uit de **iothub_client-monsters\_\_iothub_convenience_sample** toepassing wordt bijvoorbeeld als volgt weergegeven.

```c
static const char* connectionString = "[device connection string]";
```

## <a name="use-the-iothubclient-library"></a>De IoTHubClient-bibliotheek gebruiken

Binnen de **\_iothub-clientmap** in de [azure-iot-sdk-c-repository](https://github.com/azure/azure-iot-sdk-c) is er een **voorbeeldmap** die een toepassing bevat die **iothub-clientsample\_\_mqtt wordt\_** genoemd.

De Windows-versie van de **iothub_client\_voorbeelden\_iothub_convenience_sample** toepassing bevat de volgende Visual Studio-oplossing:

  ![Visual Studio Solution Explorer](./media/iot-hub-device-sdk-c-intro/iothub-client-sample-mqtt.png)

> [!NOTE]
> Als Visual Studio u vraagt om het project te retargeten op de nieuwste versie, accepteert u de prompt.

Deze oplossing bevat één project. Er zijn vier NuGet-pakketten geïnstalleerd in deze oplossing:

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.MqttTransport
* Microsoft.azure.iothub.iothubclient
* Microsoft.Azure.umqtt

U hebt altijd het **Microsoft.Azure.C.SharedUtility-pakket** nodig wanneer u met de SDK werkt. Dit voorbeeld maakt gebruik van het MQTT-protocol, daarom moet u de **Pakketten Microsoft.Azure.umqtt** en **Microsoft.Azure.IoTHub.MqttTransport** bevatten (er zijn gelijkwaardige pakketten voor AMQP en HTTPS). Omdat het voorbeeld de **IoTHubClient-bibliotheek** gebruikt, moet u ook het **Microsoft.Azure.IoTHub.IoTHubClient-pakket** in uw oplossing opnemen.

U vindt de implementatie voor de voorbeeldtoepassing in de **iothub_client\_voorbeelden\_iothub_convenience_sample** bronbestand.

In de volgende stappen wordt deze voorbeeldtoepassing gebruikt om u door te laten gaan wat nodig is om de **IoTHubClient-bibliotheek** te gebruiken.

### <a name="initialize-the-library"></a>De bibliotheek initialiseren

> [!NOTE]
> Voordat u met de bibliotheken gaat werken, moet u mogelijk een aantal platformspecifieke initialisatie uitvoeren. Als u bijvoorbeeld AMQP op Linux wilt gebruiken, moet u de OpenSSL-bibliotheek initialiseren. De samples in de [GitHub repository](https://github.com/Azure/azure-iot-sdk-c) roepen het utility function **platform\_init** wanneer de client start en bel de **platform\_deinit** functie voor het verlaten. Deze functies worden gedeclareerd in het headerbestand platform.h. Bekijk de definities van deze functies voor uw doelplatform in de [repository](https://github.com/Azure/azure-iot-sdk-c) om te bepalen of u een platformspecifieke initialisatiecode in uw client moet opnemen.

Als u met de bibliotheken wilt gaan werken, wijst u eerst een IoT Hub-clientgreep toe:

```c
if ((iotHubClientHandle = 
  IoTHubClient_LL_CreateFromConnectionString(connectionString, MQTT_Protocol)) == NULL)
{
    (void)printf("ERROR: iotHubClientHandle is NULL!\r\n");
}
else
{
    ...
```

U geeft een kopie van de apparaatverbindingstekenreeks die u van het apparaatverkennerhebt verkregen, door aan deze functie. U wijst ook het te gebruiken communicatieprotocol aan. In dit voorbeeld wordt MQTT gebruikt, maar AMQP en HTTPS zijn ook opties.

Wanneer u een geldige **\_IOTHUB-CLIENTHANDLE\_hebt,** u de API's bellen om berichten van en naar IoT Hub te verzenden en te ontvangen.

### <a name="send-messages"></a>Berichten verzenden

De voorbeeldtoepassing stelt een lus in om berichten naar uw IoT-hub te verzenden. Het volgende fragment:

- Hiermee maakt u een bericht.
- Hiermee voegt u een eigenschap toe aan het bericht.
- Stuurt een bericht.

Maak eerst een bericht:

```c
size_t iterator = 0;
do
{
    if (iterator < MESSAGE_COUNT)
    {
        sprintf_s(msgText, sizeof(msgText), "{\"deviceId\":\"myFirstDevice\",\"windSpeed\":%.2f}", avgWindSpeed + (rand() % 4 + 2));
        if ((messages[iterator].messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText))) == NULL)
        {
            (void)printf("ERROR: iotHubMessageHandle is NULL!\r\n");
        }
        else
        {
            messages[iterator].messageTrackingId = iterator;
            MAP_HANDLE propMap = IoTHubMessage_Properties(messages[iterator].messageHandle);
            (void)sprintf_s(propText, sizeof(propText), "PropMsg_%zu", iterator);
            if (Map_AddOrUpdate(propMap, "PropName", propText) != MAP_OK)
            {
                (void)printf("ERROR: Map_AddOrUpdate Failed!\r\n");
            }

            if (IoTHubClient_LL_SendEventAsync(iotHubClientHandle, messages[iterator].messageHandle, SendConfirmationCallback, &messages[iterator]) != IOTHUB_CLIENT_OK)
            {
                (void)printf("ERROR: IoTHubClient_LL_SendEventAsync..........FAILED!\r\n");
            }
            else
            {
                (void)printf("IoTHubClient_LL_SendEventAsync accepted message [%d] for transmission to IoT Hub.\r\n", (int)iterator);
            }
        }
    }
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1);

    iterator++;
} while (g_continueRunning);
```

Elke keer dat u een bericht verzendt, geeft u een verwijzing op naar een terugbelfunctie die wordt aangeroepen wanneer de gegevens worden verzonden. In dit voorbeeld wordt de callback-functie **SendConfirmationCallback**genoemd. In het volgende fragment wordt deze functie voor terugbellen weergegeven:

```c
static void SendConfirmationCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    EVENT_INSTANCE* eventInstance = (EVENT_INSTANCE*)userContextCallback;
    (void)printf("Confirmation[%d] received for message tracking id = %zu with result = %s\r\n", callbackCounter, eventInstance->messageTrackingId, MU_ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
    /* Some device specific action code goes here... */
    callbackCounter++;
    IoTHubMessage_Destroy(eventInstance->messageHandle);
}
```

Let op de oproep naar de **functie\_IoTHubMessage Destroy** wanneer u klaar bent met het bericht. Met deze functie worden de resources vrijgegeven die zijn toegewezen aan het maken van het bericht.

### <a name="receive-messages"></a>Berichten ontvangen

Het ontvangen van een bericht is een asynchrone bewerking. Eerst registreert u de terugroep om aan te roepen wanneer het apparaat een bericht ontvangt:

```c
if (IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext) != IOTHUB_CLIENT_OK)
{
    (void)printf("ERROR: IoTHubClient_LL_SetMessageCallback..........FAILED!\r\n");
}
else
{
    (void)printf("IoTHubClient_LL_SetMessageCallback...successful.\r\n");
    ...
```

De laatste parameter is een nietige aanwijzer naar wat je wilt. In het voorbeeld is het een aanwijzer naar een geheel getal, maar het kan een aanwijzer zijn naar een complexere gegevensstructuur. Met deze parameter kan de terugbelfunctie op gedeelde status werken met de beller van deze functie.

Wanneer het apparaat een bericht ontvangt, wordt de geregistreerde terugroepfunctie aangeroepen. Met deze terugbelfunctie wordt het als volgt opgehaald:

* De bericht-id en correlatie-ID van het bericht.
* De inhoud van het bericht.
* Alle aangepaste eigenschappen van het bericht.

```c
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    int* counter = (int*)userContextCallback;
    const char* buffer;
    size_t size;
    MAP_HANDLE mapProperties;
    const char* messageId;
    const char* correlationId;

    // Message properties
    if ((messageId = IoTHubMessage_GetMessageId(message)) == NULL)
    {
        messageId = "<null>";
    }

    if ((correlationId = IoTHubMessage_GetCorrelationId(message)) == NULL)
    {
        correlationId = "<null>";
    }

    // Message content
    if (IoTHubMessage_GetByteArray(message, (const unsigned char**)&buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        (void)printf("unable to retrieve the message data\r\n");
    }
    else
    {
        (void)printf("Received Message [%d]\r\n Message ID: %s\r\n Correlation ID: %s\r\n Data: <<<%.*s>>> & Size=%d\r\n", *counter, messageId, correlationId, (int)size, buffer, (int)size);
        // If we receive the work 'quit' then we stop running
        if (size == (strlen("quit") * sizeof(char)) && memcmp(buffer, "quit", size) == 0)
        {
            g_continueRunning = false;
        }
    }

    // Retrieve properties from the message
    mapProperties = IoTHubMessage_Properties(message);
    if (mapProperties != NULL)
    {
        const char*const* keys;
        const char*const* values;
        size_t propertyCount = 0;
        if (Map_GetInternals(mapProperties, &keys, &values, &propertyCount) == MAP_OK)
        {
            if (propertyCount > 0)
            {
                size_t index;

                printf(" Message Properties:\r\n");
                for (index = 0; index < propertyCount; index++)
                {
                    (void)printf("\tKey: %s Value: %s\r\n", keys[index], values[index]);
                }
                (void)printf("\r\n");
            }
        }
    }

    /* Some device specific action code goes here... */
    (*counter)++;
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Gebruik de functie **IoTHubMessage GetByteArray\_** om het bericht op te halen, dat in dit voorbeeld een tekenreeks is.

### <a name="uninitialize-the-library"></a>De bibliotheek ontinitialiseren

Wanneer u klaar bent met het verzenden van gebeurtenissen en het ontvangen van berichten, u de IE-bibliotheek ontzeggen. Geef hiervoor de volgende functieaanroep uit:

```c
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Met deze oproep worden de resources vrijgemaakt die eerder zijn toegewezen door de functie **IoTHubClient\_CreateFromConnectionString.**

Zoals u zien, is het eenvoudig om berichten te verzenden en te ontvangen met de **IoTHubClient-bibliotheek.** De bibliotheek behandelt de details van de communicatie met IoT Hub, inclusief welk protocol te gebruiken (vanuit het perspectief van de ontwikkelaar, dit is een eenvoudige configuratie optie).

De **IoTHubClient-bibliotheek** biedt ook nauwkeurige controle over hoe u de gegevens die uw apparaat naar IoT Hub verzendt, seriseren. In sommige gevallen is dit niveau van controle een voordeel, maar in andere is het een implementatie detail dat u niet wilt worden betrokken bij. Als dat het geval is, u overwegen de **serialisatorbibliotheek** te gebruiken, die in de volgende sectie wordt beschreven.

## <a name="use-the-serializer-library"></a>De serializerbibliotheek gebruiken

Conceptueel bevindt de **serializerbibliotheek** zich bovenop de **IoTHubClient-bibliotheek** in de SDK. Het maakt gebruik van de **IoTHubClient-bibliotheek** voor de onderliggende communicatie met IoT Hub, maar het voegt modelleringsmogelijkheden toe die de last van het omgaan met berichtserialisatie van de ontwikkelaar wegnemen. Hoe deze bibliotheek werkt, blijkt het best uit een voorbeeld.

In de **serializer** map in de [azure-iot-sdk-c repository](https://github.com/Azure/azure-iot-sdk-c), is een **samples** map die een applicatie genaamd **simplesample\_mqtt**bevat. De Windows-versie van dit voorbeeld bevat de volgende Visual Studio-oplossing:

  ![Visual Studio-oplossing voor mqtt-voorbeeld](./media/iot-hub-device-sdk-c-intro/simplesample_mqtt.png)

> [!NOTE]
> Als Visual Studio u vraagt om het project te retargeten op de nieuwste versie, accepteert u de prompt.

Net als bij het vorige voorbeeld bevat deze verschillende NuGet-pakketten:

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.MqttTransport
* Microsoft.azure.iothub.iothubclient
* Microsoft.Azure.IoTHub.Serializer
* Microsoft.Azure.umqtt

U hebt de meeste van deze pakketten in het vorige voorbeeld gezien, maar **Microsoft.Azure.IoTHub.Serializer** is nieuw. Dit pakket is vereist wanneer u de **serializerbibliotheek** gebruikt.

U vindt de implementatie van de voorbeeldtoepassing in de **iothub_client\_voorbeelden\_iothub_convenience_sample** bestand.

De volgende secties lopen u door de belangrijkste delen van dit voorbeeld.

### <a name="initialize-the-library"></a>De bibliotheek initialiseren

Als u met de **serialisatorbibliotheek** wilt gaan werken, belt u de initialisatie-API's:

```c
if (serializer_init(NULL) != SERIALIZER_OK)
{
    (void)printf("Failed on serializer_init\r\n");
}
else
{
    IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle = IoTHubClient_LL_CreateFromConnectionString(connectionString, MQTT_Protocol);
    srand((unsigned int)time(NULL));
    int avgWindSpeed = 10;

    if (iotHubClientHandle == NULL)
    {
        (void)printf("Failed on IoTHubClient_LL_Create\r\n");
    }
    else
    {
        ContosoAnemometer* myWeather = CREATE_MODEL_INSTANCE(WeatherStation, ContosoAnemometer);
        if (myWeather == NULL)
        {
            (void)printf("Failed on CREATE_MODEL_INSTANCE\r\n");
        }
        else
        {
...
```

De call naar de **serializer\_init-functie** is een eenmalige aanroep en initialiseert de onderliggende bibliotheek. Vervolgens belt u de functie **IoTHubClient\_\_LL CreateFromConnectionString** aan, dezelfde API als in het voorbeeld van **IoTHubClient.** Met deze oproep wordt de tekenreeks van de apparaatverbinding ingesteld (in dit gesprek kiest u ook het protocol dat u wilt gebruiken). Dit voorbeeld gebruikt MQTT als transport, maar kan AMQP of HTTPS gebruiken.

Roep ten slotte de functie **MODEL-instantie\_\_MAKEN** aan. **WeatherStation** is de naamruimte van het model en **ContosoAnemometer** is de naam van het model. Zodra de modelinstantie is gemaakt, u deze gebruiken om berichten te verzenden en te ontvangen. Het is echter belangrijk om te begrijpen wat een model is.

### <a name="define-the-model"></a>Het model definiëren

Een model in de **serializer-bibliotheek** definieert de berichten die uw apparaat naar IoT Hub en de berichten kan verzenden, *acties* in de modeltaal, die het kan ontvangen. U definieert een model met een set C-macro's zoals in de **iothub_client\_iothub_convenience_sample\_** voorbeeldtoepassing:

```c
BEGIN_NAMESPACE(WeatherStation);

DECLARE_MODEL(ContosoAnemometer,
WITH_DATA(ascii_char_ptr, DeviceId),
WITH_DATA(int, WindSpeed),
WITH_ACTION(TurnFanOn),
WITH_ACTION(TurnFanOff),
WITH_ACTION(SetAirResistance, int, Position)
);

END_NAMESPACE(WeatherStation);
```

De **\_macro's NAAMRUIMTE** BEGIN en **\_END NAMESPACE** nemen beide de naamruimte van het model als argument. De verwachting is dat alles tussen deze macro's is de definitie van uw model of modellen, en de gegevensstructuren die de modellen gebruiken.

In dit voorbeeld is er een enkel model genaamd **ContosoAnemometer**. Dit model definieert twee gegevens die uw apparaat naar IoT Hub kan verzenden: **DeviceId** en **WindSpeed.** Het definieert ook drie acties (berichten) die uw apparaat kan ontvangen: **TurnFanOn,** **TurnFanOff**en **SetAirResistance**. Elk gegevenselement heeft een type en elke actie heeft een naam (en optioneel een set parameters).

De gegevens en acties die in het model zijn gedefinieerd, definiëren een API-oppervlak dat u gebruiken om berichten naar IoT Hub te verzenden en te reageren op berichten die naar het apparaat worden verzonden. Het gebruik van dit model is het best te begrijpen door middel van een voorbeeld.

### <a name="send-messages"></a>Berichten verzenden

Het model definieert de gegevens die u naar IoT Hub verzenden. In dit voorbeeld betekent dit een van de twee gegevensitems die zijn gedefinieerd met de **WITH_DATA** macro. Er zijn verschillende stappen nodig om **DeviceId-** en **WindSpeed-waarden** naar een IoT-hub te verzenden. De eerste is het instellen van de gegevens die u wilt verzenden:

```c
myWeather->DeviceId = "myFirstDevice";
myWeather->WindSpeed = avgWindSpeed + (rand() % 4 + 2);
```

Met het model dat u eerder hebt gedefinieerd, u de waarden instellen door leden van een **struct**in te stellen. Serialiseer vervolgens het bericht dat u wilt verzenden:

```c
unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, myWeather->DeviceId, myWeather->WindSpeed) != CODEFIRST_OK)
{
    (void)printf("Failed to serialize\r\n");
}
else
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
    free(destination);
}
```

Deze code serialiseert het apparaat-naar-cloud naar een buffer (waarnaar naar **bestemming**wordt verwezen). De code roept vervolgens de functie **sendMessage** aan om het bericht naar IoT Hub te verzenden:

```c
static void sendMessage(IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle == NULL)
    {
        printf("unable to create a new IoTHubMessage\r\n");
    }
    else
    {
        if (IoTHubClient_LL_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId) != IOTHUB_CLIENT_OK)
        {
            printf("failed to hand over the message to IoTHubClient");
        }
        else
        {
            printf("IoTHubClient accepted the message for delivery\r\n");
        }
        IoTHubMessage_Destroy(messageHandle);
    }
    messageTrackingId++;
}
```

De een-na-laatste parameter van **IoTHubClient\_\_LL SendEventAsync** is een verwijzing naar een callback-functie die wordt aangeroepen wanneer de gegevens met succes worden verzonden. Hier is de callback-functie in het voorbeeld:

```c
void sendCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    unsigned int messageTrackingId = (unsigned int)(uintptr_t)userContextCallback;

    (void)printf("Message Id: %u Received.\r\n", messageTrackingId);

    (void)printf("Result Call Back Called! Result is: %s \r\n", MU_ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
}
```

De tweede parameter is een aanwijzer naar de gebruikerscontext; dezelfde aanwijzer is doorgegeven aan **IoTHubClient\_\_LL SendEventAsync**. In dit geval is de context een eenvoudige teller, maar het kan alles zijn wat je wilt.

Dat is alles wat er is om device-to-cloud berichten te verzenden. Het enige wat overblijft is hoe je berichten ontvangen.

### <a name="receive-messages"></a>Berichten ontvangen

Het ontvangen van een bericht werkt op dezelfde manier als de manier waarop berichten werken in de **IoTHubClient-bibliotheek.** Eerst registreert u een terugbelfunctie voor berichten:

```c
if (IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, 
  IoTHubMessage, myWeather) != IOTHUB_CLIENT_OK)
{
    printf("unable to IoTHubClient_SetMessageCallback\r\n");
}
else
{
...
```

Vervolgens schrijft u de terugbelfunctie die wordt aangeroepen wanneer een bericht wordt ontvangen:

```c
static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    IOTHUBMESSAGE_DISPOSITION_RESULT result;
    const unsigned char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = IOTHUBMESSAGE_ABANDONED;
    }
    else
    {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
            printf("failed to malloc\r\n");
            result = IOTHUBMESSAGE_ABANDONED;
        }
        else
        {
            (void)memcpy(temp, buffer, size);
            temp[size] = '\0';
            EXECUTE_COMMAND_RESULT executeCommandResult = EXECUTE_COMMAND(userContextCallback, temp);
            result =
                (executeCommandResult == EXECUTE_COMMAND_ERROR) ? IOTHUBMESSAGE_ABANDONED :
                (executeCommandResult == EXECUTE_COMMAND_SUCCESS) ? IOTHUBMESSAGE_ACCEPTED :
                IOTHUBMESSAGE_REJECTED;
            free(temp);
        }
    }
    return result;
}
```

Deze code is boilerplate -- het is hetzelfde voor elke oplossing. Deze functie ontvangt het bericht en zorgt ervoor dat het wordt geleid naar de juiste functie via de oproep om OPDRACHT UIT te **VOEREN.\_** De functie die op dit punt wordt aangeroepen, is afhankelijk van de definitie van de acties in uw model.

Wanneer u een actie in uw model definieert, moet u een functie implementeren die wordt aangeroepen wanneer uw apparaat het bijbehorende bericht ontvangt. Als uw model bijvoorbeeld deze actie definieert:

```c
WITH_ACTION(SetAirResistance, int, Position)
```

Definieer een functie met deze handtekening:

```c
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

Merk op hoe de naam van de functie overeenkomt met de naam van de actie in het model en dat de parameters van de functie overeenkomen met de parameters die voor de actie zijn opgegeven. De eerste parameter is altijd vereist en bevat een aanwijzer naar de instantie van uw model.

Wanneer het apparaat een bericht ontvangt dat overeenkomt met deze handtekening, wordt de bijbehorende functie aangeroepen. Daarom is het niet alleen een kwestie van de boilerplate-code van **IoTHubMessage**opnemen, maar ook het definiëren van een eenvoudige functie voor elke actie die in uw model wordt gedefinieerd.

### <a name="uninitialize-the-library"></a>De bibliotheek ontinitialiseren

Wanneer u klaar bent met het verzenden van gegevens en het ontvangen van berichten, u de IoT-bibliotheek ontzeggen:

```c
...
        DESTROY_MODEL_INSTANCE(myWeather);
    }
    IoTHubClient_LL_Destroy(iotHubClientHandle);
}
serializer_deinit();
```

Elk van deze drie functies sluit aan bij de drie eerder beschreven initialisatiefuncties. Als u deze API's aanroept, u eerder toegewezen resources vrijmaken.

## <a name="next-steps"></a>Volgende stappen

Dit artikel ging over de basisprincipes van het gebruik van de bibliotheken in de **Azure IoT-apparaat SDK voor C.** Het gaf u voldoende informatie om te begrijpen wat er in de SDK, de architectuur en hoe u aan de slag met de Windows-voorbeelden. Het volgende artikel zet de beschrijving van de SDK voort door meer uit te leggen [over de IoTHubClient-bibliotheek.](iot-hub-device-sdk-c-iothubclient.md)

Zie de [Azure IoT SDKs](iot-hub-devguide-sdks.md)voor meer informatie over het ontwikkelen voor IoT Hub.

Zie:

* [AI implementeren op Edge-apparaten met Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
