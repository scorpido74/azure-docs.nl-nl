---
title: Azure IoT-apparaat SDK voor C - IoTHubClient | Microsoft Documenten
description: De IoTHubClient-bibliotheek in de Azure IoT-apparaat SDK voor C gebruiken om apparaatapps te maken die communiceren met een IoT-hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 08/29/2017
ms.author: robinsh
ms.openlocfilehash: fd3e02101f206ebdb183da87089eadcbc9619b33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68883179"
---
# <a name="azure-iot-device-sdk-for-c--more-about-iothubclient"></a>Azure IoT-apparaat SDK voor C – meer informatie over IoTHubClient

[Azure IoT-apparaat SDK voor C](iot-hub-device-sdk-c-intro.md) is het eerste artikel in deze serie dat de **Azure IoT-apparaat SDK voor C**introduceert. Dat artikel legde uit dat er twee architecturale lagen in SDK zijn. Aan de basis bevindt zich de **IoTHubClient-bibliotheek** die de communicatie met IoT Hub rechtstreeks beheert. Er is ook de **serializer** bibliotheek die bouwt op de top van dat te bieden serialisatie diensten. In dit artikel geven we meer details over de **IoTHubClient-bibliotheek.**

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

In het vorige artikel werd beschreven hoe u de **IoTHubClient-bibliotheek** gebruiken om gebeurtenissen naar IoT Hub te verzenden en berichten te ontvangen. Dit artikel breidt die discussie uit door uit te leggen hoe u nauwkeuriger beheren *wanneer* u gegevens verzendt en ontvangt, zodat u kennis maken met de **API's op een lager niveau.** We leggen ook uit hoe u eigenschappen aan gebeurtenissen koppelt (en ophalen uit berichten) met behulp van de functies voor het afhandelen van eigendommen in de **IoTHubClient-bibliotheek.** Tot slot geven we aanvullende uitleg over verschillende manieren om berichten te verwerken die zijn ontvangen van IoT Hub.

Het artikel wordt afgesloten met betrekking tot een aantal verschillende onderwerpen, waaronder meer over apparaatreferenties en hoe u het gedrag van de **IoTHubClient** wijzigen via configuratieopties.

We gebruiken de **IoTHubClient** SDK-voorbeelden om deze onderwerpen uit te leggen. Als u mee wilt volgen, raadpleegt u het **\_\_voorbeeld http-\_** en **\_iothubclientvoorbeeld\_van iothub-clients\_** die zijn opgenomen in het Azure IoT-apparaat SDK voor C. Alles wat in de volgende secties wordt beschreven, wordt in deze voorbeelden gedemonstreerd.

U de [**Azure IoT-apparaat SDK voor C**](https://github.com/Azure/azure-iot-sdk-c) GitHub-repository vinden en details van de API weergeven in de C [API-verwijzing.](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

## <a name="the-lower-level-apis"></a>De API's op een lager niveau

Het vorige artikel beschreef de basiswerking van de **IotHubClient** in het kader van de **\_iothub client\_sample\_amqp-toepassing.** Er werd bijvoorbeeld uitgelegd hoe u de bibliotheek initialiseren met behulp van deze code.

```C
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Er werd ook beschreven hoe gebeurtenissen kunnen worden verzonden met behulp van deze functieaanroep.

```C
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

Het artikel beschreef ook hoe u berichten ontvangen door een terugbelfunctie te registreren.

```C
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

Het artikel toonde ook hoe u bronnen gratis met behulp van code, zoals de volgende.

```C
IoTHubClient_Destroy(iotHubClientHandle);
```

Er zijn begeleidende functies voor elk van deze API's:

* IotHubClient\_\_LL maaktfromconnectiontekenreeks
* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_\_LL vernietigen

Deze functies bevatten allemaal **LL** in de API-naam. Andere **LL** deel van de naam, de parameters van elk van deze functies zijn identiek aan hun niet-LL tegenhangers. Echter, het gedrag van deze functies is anders op een belangrijke manier.

Wanneer u **IoTHubClient\_CreateFromConnectionString**aanroept, maken de onderliggende bibliotheken een nieuwe thread die op de achtergrond wordt uitgevoerd. Deze thread verzendt gebeurtenissen naar en ontvangt berichten van IoT Hub. Geen dergelijke thread wordt gemaakt bij het werken met de **LL** API's. De creatie van de achtergrond draad is een gemak voor de ontwikkelaar. U hoeft zich geen zorgen te maken over het expliciet verzenden van gebeurtenissen en het ontvangen van berichten van IoT Hub - dit gebeurt automatisch op de achtergrond. De **LL** API's geven u daarentegen expliciete controle over de communicatie met IoT Hub, als u dat nodig hebt.

Om dit concept beter te begrijpen, laten we eens kijken naar een voorbeeld:

Wanneer u **IoTHubClient\_SendEventAsync**belt, plaatst u de gebeurtenis in een buffer. De achtergrondthread die is gemaakt wanneer u **IoTHubClient\_CreateFromConnectionString** aanroept, controleert deze buffer voortdurend en verzendt alle gegevens die deze bevat naar IoT Hub. Dit gebeurt op de achtergrond op hetzelfde moment dat de hoofdthread ander werk uitvoert.

Wanneer u een terugbelfunctie registreert voor berichten met **IoTHubClient\_SetCallCallback,** instrueert u de SDK om de achtergrondthread de callback-functie te laten aanroepen wanneer een bericht wordt ontvangen, onafhankelijk van de hoofdthread.

De **API's van LL** maken geen achtergrondthread. In plaats daarvan moet een nieuwe API worden opgeroepen om expliciet gegevens van IoT Hub te verzenden en te ontvangen. Dit wordt aangetoond in het volgende voorbeeld.

De **\_iothub\_\_client sample http-toepassing** die is opgenomen in de SDK toont de API's op een lager niveau. In dat voorbeeld sturen we gebeurtenissen naar IoT Hub met code zoals:

```C
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_LL_Over_HTTP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));

IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

De eerste drie regels maken het bericht en de laatste regel verzendt de gebeurtenis. Echter, zoals eerder vermeld, het verzenden van de gebeurtenis betekent dat de gegevens gewoon wordt geplaatst in een buffer. Er wordt niets verzonden op het netwerk wanneer we **IoTHubClient\_\_LL SendEventAsync**bellen. Om de gegevens daadwerkelijk naar IoT Hub te laten vallen, moet u **IoTHubClient\_\_LL DoWork**bellen, zoals in dit voorbeeld:

```C
while (1)
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(100);
}
```

Deze code (van de **iohub client\_\_\_sample http-toepassing)** roept herhaaldelijk **IoTHubClient\_LL\_DoWork**. Elke keer **dat\_IoTHubClient\_LL DoWork** wordt aangeroepen, worden sommige gebeurtenissen van de buffer naar IoT Hub verzonden en wordt een bericht in de wachtrij naar het apparaat verzonden. Het laatste geval betekent dat als we een callback-functie voor berichten hebben geregistreerd, de callback wordt aangeroepen (ervan uitgaande dat berichten in de wachtrij staan). We zouden een dergelijke callback-functie hebben geregistreerd met code zoals:

```C
IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext)
```

De reden dat **\_IoTHubClient\_LL DoWork** vaak in een lus wordt aangeroepen, is dat elke keer dat het wordt aangeroepen, het een *aantal* gebufferde gebeurtenissen naar IoT Hub verzendt en het volgende bericht ophaalt dat in *de* wachtrij staat voor het apparaat. Het is niet gegarandeerd dat elk gesprek alle gebufferde gebeurtenissen verzendt of alle in de wachtrij staande berichten ophaalt. Als u alle gebeurtenissen in de buffer wilt verzenden en vervolgens verder wilt gaan met andere verwerkingen, u deze lus vervangen door code zoals:

```C
IOTHUB_CLIENT_STATUS status;

while ((IoTHubClient_LL_GetSendStatus(iotHubClientHandle, &status) == IOTHUB_CLIENT_OK) && (status == IOTHUB_CLIENT_SEND_STATUS_BUSY))
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(100);
}
```

Deze code roept **IoTHubClient\_\_LL DoWork** aan totdat alle gebeurtenissen in de buffer naar IoT Hub zijn verzonden. Let op: dit betekent ook niet dat alle berichten in de wachtrij zijn ontvangen. Een deel van de reden hiervoor is dat het controleren op "alle" berichten is niet zo deterministisch een actie. Wat gebeurt er als u "alle" berichten ophaalt, maar er onmiddellijk daarna een andere naar het apparaat wordt verzonden? Een betere manier om daarmee om te gaan is met een geprogrammeerde time-out. De functie voor het terugroepen van berichten kan bijvoorbeeld elke keer dat deze wordt aangeroepen een timer opnieuw instellen. U vervolgens logica schrijven om de verwerking voort te zetten *X* als er bijvoorbeeld geen berichten zijn ontvangen in de laatste X-seconden.

Wanneer u klaar bent met het binnendringen van gebeurtenissen en het ontvangen van berichten, moet u de bijbehorende functie aanroepen om resources op te schonen.

```C
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

In principe is er slechts een set van API's te verzenden en te ontvangen gegevens met een achtergrond thread en een andere set van API's die hetzelfde doet zonder de achtergrond thread. Veel ontwikkelaars geven misschien de voorkeur aan de niet-LL API's, maar de API's op een lager niveau zijn handig wanneer de ontwikkelaar expliciete controle wil over netwerktransmissies. Sommige apparaten verzamelen bijvoorbeeld gegevens in de loop van de tijd en vallen alleen gebeurtenissen binnen met bepaalde intervallen (bijvoorbeeld één keer per uur of één keer per dag). De API's op een lager niveau geven u de mogelijkheid om expliciet te controleren wanneer u gegevens van IoT Hub verzendt en ontvangt. Anderen zullen gewoon de voorkeur aan de eenvoud die de lagere niveau API's bieden. Alles gebeurt op de belangrijkste draad in plaats van wat werk gebeurt op de achtergrond.

Welk model u ook kiest, zorg ervoor dat u consistent bent in welke API's u gebruikt. Als u begint met het aanroepen van **IoTHubClient\_\_LL CreateFromConnectionString,** moet u ervoor zorgen dat u alleen de bijbehorende API's op een lager niveau gebruikt voor eventuele follow-upwerkzaamheden:

* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_\_LL vernietigen
* IoTHubClient\_LL\_DoWork

Het tegendeel is ook waar. Als u begint met **IoTHubClient\_CreateFromConnectionString,** gebruikt u de niet-LL-API's voor extra verwerkingen.

Zie in de Azure IoT-apparaat SDK voor C het **voorbeeld http-toepassing\_\_\_van de Iothub-client** voor een volledig voorbeeld van de API's op een lager niveau. De **\_iothub\_\_client sample amqp** applicatie kan worden verwezen voor een volledig voorbeeld van de niet-LL API's.

## <a name="property-handling"></a>Verwerking van onroerend goed

Tot nu toe, toen we het verzenden van gegevens beschreven, hebben we verwezen naar de hoofdtekst van het bericht. Houd bijvoorbeeld rekening met deze code:

```C
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Hello World");
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));
IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

In dit voorbeeld wordt een bericht verzonden naar IoT Hub met de tekst 'Hello World'. IoT Hub maakt het echter ook mogelijk om eigenschappen aan elk bericht te koppelen. Eigenschappen zijn naam/waardeparen die aan het bericht kunnen worden gekoppeld. We kunnen bijvoorbeeld de vorige code wijzigen om een eigenschap aan het bericht toe te voegen:

```C
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

We beginnen met het bellen **van\_IoTHubMessage Properties** en het doorgeven van het handvat van onze boodschap. Wat we terug krijgen is een **MAP\_HANDLE** referentie die ons in staat stelt om te beginnen met het toevoegen van eigenschappen. Dit laatste wordt bereikt door **map\_addorupdate**aan te\_roepen , waarbij wordt verwezen naar een MAP HANDLE, de naam van de eigenschap en de eigenschapswaarde. Met deze API kunnen we zoveel eigenschappen toevoegen als we willen.

Wanneer de gebeurtenis wordt gelezen vanuit **gebeurtenishubs,** kan de ontvanger de eigenschappen opsommen en de bijbehorende waarden ophalen. Dit wordt bijvoorbeeld bereikt door in .NET toegang te krijgen tot de [verzameling Eigenschappen op het object EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.properties.aspx).

In het vorige voorbeeld koppelen we eigenschappen aan een gebeurtenis die we naar IoT Hub verzenden. Eigenschappen kunnen ook worden gekoppeld aan berichten die zijn ontvangen van IoT Hub. Als we eigenschappen uit een bericht willen ophalen, kunnen we code gebruiken, zoals de volgende in onze functie voor het terugroepen van berichten:

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .

    // Retrieve properties from the message
    MAP_HANDLE mapProperties = IoTHubMessage_Properties(message);
    if (mapProperties != NULL)
    {
        const char*const* keys;
        const char*const* values;
        size_t propertyCount = 0;
        if (Map_GetInternals(mapProperties, &keys, &values, &propertyCount) == MAP_OK)
        {
            if (propertyCount > 0)
            {
                printf("Message Properties:\r\n");
                for (size_t index = 0; index < propertyCount; index++)
                {
                    printf("\tKey: %s Value: %s\r\n", keys[index], values[index]);
                }
                printf("\r\n");
            }
        }
    }

    . . .
}
```

De aanroep naar **IoTHubMessage-eigenschappen\_** retourneert de referentie **\_MAP HANDLE.** Vervolgens geven we die verwijzing door naar **Map\_GetInternals** om een verwijzing te verkrijgen naar een array van de naam/waardeparen (evenals een telling van de eigenschappen). Op dat moment is het een eenvoudige kwestie van het opsommen van de eigenschappen om naar de waarden die we willen.

U hoeft geen eigenschappen in uw toepassing te gebruiken. Als u ze echter op gebeurtenissen moet instellen of ophalen uit berichten, maakt de **IoTHubClient-bibliotheek** het eenvoudig.

## <a name="message-handling"></a>Afhandeling van berichten

Zoals eerder vermeld, wanneer berichten binnenkomen van IoT Hub, reageert de **IoTHubClient-bibliotheek** door een beroep te doen op een geregistreerde terugbelfunctie. Er is een terugkeerparameter van deze functie die wat extra uitleg verdient. Hier is een fragment van de callback-functie in de **voorbeeldtoepassing iothub-clienthttp:\_\_\_**

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Houd er rekening mee dat het retourtype **iothubmessage-dispositieresultaat\_\_** is en in dit specifieke geval **iothubmessage geaccepteerd retourneren.\_** Er zijn andere waarden die we kunnen retourneren van deze functie die de manier waarop de **IoTHubClient-bibliotheek** reageert op de terugroepen van het bericht veranderen. Hier zijn de opties.

* **IOTHUBMESSAGE\_GEACCEPTEERD** – Het bericht is verwerkt. De **IoTHubClient-bibliotheek** zal de callback-functie niet opnieuw aanroepen met hetzelfde bericht.

* **IOTHUBMESSAGE\_AFGEWEZEN** – Het bericht is niet verwerkt en er is geen wens om dit in de toekomst te doen. De **IoTHubClient-bibliotheek** mag de terugbelfunctie niet opnieuw aanroepen met hetzelfde bericht.

* **IOTHUBMESSAGE\_VERLATEN** – Het bericht is niet verwerkt, maar de **IoTHubClient-bibliotheek** moet de callback-functie opnieuw aanroepen met hetzelfde bericht.

Voor de eerste twee retourcodes stuurt de **IoTHubClient-bibliotheek** een bericht naar IoT Hub dat aangeeft dat het bericht uit de apparaatwachtrij moet worden verwijderd en niet opnieuw moet worden bezorgd. Het netto-effect is hetzelfde (het bericht wordt verwijderd uit de wachtrij van het apparaat), maar of het bericht is geaccepteerd of afgewezen, wordt nog steeds geregistreerd.  Het opnemen van dit onderscheid is handig voor afzenders van het bericht die naar feedback kunnen luisteren en erachter kunnen komen of een apparaat een bepaald bericht heeft geaccepteerd of afgewezen.

In het laatste geval wordt er ook een bericht naar IoT Hub verzonden, maar het geeft aan dat het bericht opnieuw moet worden bezorgd. Meestal verlaat u een bericht als u een fout tegenkomt, maar wilt u proberen het bericht opnieuw te verwerken. Het weigeren van een bericht is daarentegen geschikt wanneer u een onherstelbare fout tegenkomt (of als u gewoon besluit dat u het bericht niet wilt verwerken).

Houd in ieder geval rekening met de verschillende retourcodes, zodat u het gewenste gedrag uitlokken uit de **IoTHubClient-bibliotheek.**

## <a name="alternate-device-credentials"></a>Alternatieve apparaatreferenties

Zoals eerder uitgelegd, is het eerste wat u moet doen bij het werken met de **IoTHubClient-bibliotheek** het verkrijgen van een **IOTHUB-clientgreep\_\_** met een oproep zoals het volgende:

```C
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

De argumenten voor **IoTHubClient\_CreateFromConnectionString** zijn de tekenreeks voor apparaatverbinding en een parameter die het protocol aangeeft dat we gebruiken om te communiceren met IoT Hub. De tekenreeks voor apparaatverbinding heeft een indeling die als volgt wordt weergegeven:

```C
HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY
```

Er zijn vier gegevens in deze tekenreeks: IoT Hub-naam, IoT Hub-achtervoegsel, apparaat-ID en gedeelde toegangssleutel. U krijgt de volledig gekwalificeerde domeinnaam (FQDN) van een IoT-hub wanneer u uw IoT-hub-instantie maakt in de Azure-portal - dit geeft u de naam van de IoT-hub (het eerste deel van de FQDN) en het IoT-hub-achtervoegvoegsel (de rest van de FQDN). U krijgt de apparaat-id en de gedeelde toegangssleutel wanneer u uw apparaat registreert met IoT Hub (zoals beschreven in het [vorige artikel](iot-hub-device-sdk-c-intro.md)).

**IoTHubClient\_CreateFromConnectionString** biedt u een manier om de bibliotheek te initialiseren. Als u dat liever hebt, u een nieuwe **\_\_IOTHUB-clientgreep maken** met behulp van deze afzonderlijke parameters in plaats van de tekenreeks voor apparaatverbinding. Dit wordt bereikt met de volgende code:

```C
IOTHUB_CLIENT_CONFIG iotHubClientConfig;
iotHubClientConfig.iotHubName = "";
iotHubClientConfig.deviceId = "";
iotHubClientConfig.deviceKey = "";
iotHubClientConfig.iotHubSuffix = "";
iotHubClientConfig.protocol = HTTP_Protocol;
IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_LL_Create(&iotHubClientConfig);
```

Dit bereikt hetzelfde als **IoTHubClient\_CreateFromConnectionString**.

Het lijkt misschien duidelijk dat u **IoTHubClient\_CreateFromConnectionString** wilt gebruiken in plaats van deze meer verbose methode van initialisatie. Houd er echter rekening mee dat wanneer u een apparaat registreert in IoT Hub, u een apparaat-id en apparaatsleutel (geen verbindingstekenreeks) krijgt. Het *SDK-hulpprogramma voor apparaatverkenners* dat in het [vorige artikel](iot-hub-device-sdk-c-intro.md) is geïntroduceerd, gebruikt bibliotheken in de Azure **IoT-service SDK** om de tekenreeks voor apparaatverbinding te maken vanaf de apparaat-id, apparaatsleutel en IoT Hub-hostnaam. Het aanroepen van **IoTHubClient\_\_LL Create** heeft dus de voorkeur omdat het u de stap bespaart om een verbindingstekenreeks te genereren. Gebruik welke methode geschikt is.

## <a name="configuration-options"></a>Configuratie-opties

Tot nu toe alles beschreven over de manier waarop de **IoTHubClient** bibliotheek werkt weerspiegelt zijn standaard gedrag. Er zijn echter een paar opties die u instellen om de werking van de bibliotheek te wijzigen. Dit wordt bereikt door gebruik te maken van de **IoTHubClient\_\_LL SetOption** API. Neem dit voorbeeld:

```C
unsigned int timeout = 30000;
IoTHubClient_LL_SetOption(iotHubClientHandle, "timeout", &timeout);
```

Er zijn een paar opties die vaak worden gebruikt:

* **SetBatching** (bool) – Als **dit waar is,** worden gegevens die naar IoT Hub worden verzonden in batches verzonden. Als **dit niet waar is,** worden berichten afzonderlijk verzonden. De standaardinstelling is **onwaar.** Batching via AMQP / AMQP-WS, evenals het toevoegen van systeemeigenschappen op D2C-berichten, wordt ondersteund.

* **Time-out** (niet-ondertekend int) – Deze waarde wordt weergegeven in milliseconden. Als het verzenden van een HTTPS-verzoek of het ontvangen van een antwoord langer duurt dan deze tijd, dan is de verbinding een time-out.

De batching optie is belangrijk. Standaard valt de bibliotheek gebeurtenissen afzonderlijk in (één gebeurtenis is wat u ook doorgeeft aan **IoTHubClient\_\_LL SendEventAsync).** Als de batchoptie **waar**is, verzamelt de bibliotheek zoveel gebeurtenissen als mogelijk vanuit de buffer (tot de maximale berichtgrootte die IoT Hub accepteert).  De gebeurtenisbatch wordt verzonden naar IoT Hub in één HTTPS-gesprek (de afzonderlijke gebeurtenissen worden gebundeld in een JSON-array). Het inschakelen van batching resulteert meestal in grote prestatieverbeteringen, omdat u de retourvluchten van het netwerk vermindert. Het vermindert ook de bandbreedte aanzienlijk omdat u één set HTTPS-headers verzendt met een gebeurtenisbatch in plaats van een set kopteksten voor elke afzonderlijke gebeurtenis. Tenzij u een specifieke reden hebt om anders te doen, wilt u meestal batching inschakelen.

## <a name="next-steps"></a>Volgende stappen

In dit artikel wordt in detail het gedrag beschreven van de **IoTHubClient-bibliotheek** in de **Azure IoT-apparaat SDK voor C.** Met deze informatie moet u een goed inzicht hebben in de mogelijkheden van de **IoTHubClient-bibliotheek.** Het tweede artikel in deze serie is [Azure IoT-apparaat SDK voor C - Serializer](iot-hub-device-sdk-c-serializer.md), die vergelijkbare details over de **serializer-bibliotheek** biedt.

Zie de [Azure IoT SDKs](iot-hub-devguide-sdks.md)voor meer informatie over het ontwikkelen voor IoT Hub.

Zie [AI implementeren naar edge-apparaten met Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)om de mogelijkheden van IoT Hub verder te verkennen.
