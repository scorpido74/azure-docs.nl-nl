---
title: Azure IoT-apparaat SDK voor C - Serializer | Microsoft Documenten
description: De Serializer-bibliotheek in de Azure IoT-apparaat SDK voor C gebruiken om apparaat-apps te maken die communiceren met een IoT-hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 09/06/2016
ms.author: robinsh
ms.openlocfilehash: dfea53e62383409411925f2fe2f18d61a6855ec1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75429366"
---
# <a name="azure-iot-device-sdk-for-c--more-about-serializer"></a>Azure IoT-apparaat SDK voor C – meer informatie over serializer

Het eerste artikel in deze serie introduceerde de [inleiding tot Azure IoT-apparaat SDK voor C](iot-hub-device-sdk-c-intro.md). Het volgende artikel gaf een meer gedetailleerde beschrijving van de [Azure IoT-apparaat SDK voor C - IoTHubClient](iot-hub-device-sdk-c-iothubclient.md). Dit artikel voltooit de dekking van de SDK door een meer gedetailleerde beschrijving van de resterende component: de **serializer** bibliotheek.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

In het inleidende artikel wordt beschreven hoe u de **serializerbibliotheek** gebruiken om gebeurtenissen naar En berichten van IoT Hub te verzenden. In dit artikel breiden we die discussie uit door een completere uitleg te geven over hoe u uw gegevens modelleren met de **serialisator-macrotaal.** Het artikel bevat ook meer details over hoe de bibliotheek berichten serialiseert (en in sommige gevallen hoe u het serialisatiegedrag beheren). We beschrijven ook enkele parameters die u wijzigen die de grootte bepalen van de modellen die u maakt.

Ten slotte wordt in het artikel een aantal onderwerpen besproken die in eerdere artikelen worden behandeld, zoals bericht- en eigendomsafhandeling. Zoals we zullen ontdekken, werken deze functies op dezelfde manier met behulp van de **serializer-bibliotheek** als met de **IoTHubClient-bibliotheek.**

Alles beschreven in dit artikel is gebaseerd op de **serializer** SDK monsters. Als u mee wilt lezen, raadpleegt u de **simplesample\_amqp** en **simplesample http-toepassingen\_** die zijn opgenomen in de Azure IoT-apparaat SDK voor C.

U de [**Azure IoT-apparaat SDK voor C**](https://github.com/Azure/azure-iot-sdk-c) GitHub-repository vinden en details van de API weergeven in de C [API-verwijzing.](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

## <a name="the-modeling-language"></a>De modeltaal

Het [sdk-apparaat SDK voor C van Azure](iot-hub-device-sdk-c-intro.md) in deze serie introduceerde de Azure **IoT-apparaat SDK voor C-modelleringstaal** via het voorbeeld in de **simplesample\_amqp-toepassing:**

```C
BEGIN_NAMESPACE(WeatherStation);

DECLARE_MODEL(ContosoAnemometer,
WITH_DATA(ascii_char_ptr, DeviceId),
WITH_DATA(double, WindSpeed),
WITH_ACTION(TurnFanOn),
WITH_ACTION(TurnFanOff),
WITH_ACTION(SetAirResistance, int, Position)
);

END_NAMESPACE(WeatherStation);
```

Zoals u zien, is de modeltaal gebaseerd op C-macro's. U begint uw definitie altijd met **BEGIN\_NAMESPACE** en eindigt altijd met **\_END NAMESPACE**. Het is gebruikelijk om de naamruimte voor uw bedrijf te benoemen of, zoals in dit voorbeeld, het project waaraan u werkt.

Wat in de naamruimte gaat, zijn modeldefinities. In dit geval is er een enkel model voor een anemometer. Nogmaals, het model kan worden genoemd alles, maar meestal het model is vernoemd naar het apparaat of het type gegevens die u wilt uitwisselen met IoT Hub.  

Modellen bevatten een definitie van de gebeurtenissen die u binnendringen naar IoT Hub (de *gegevens)* en de berichten die u ontvangen van IoT Hub (de *acties).* Zoals u in het voorbeeld zien, hebben gebeurtenissen een type en een naam; acties hebben een naam en optionele parameters (elk met een type).

Wat niet wordt aangetoond in dit voorbeeld zijn aanvullende gegevenstypen die worden ondersteund door de SDK. Dat doen we als volgende.

> [!NOTE]
> IoT Hub verwijst naar de gegevens die een apparaat naar het apparaat stuurt als *gebeurtenissen,* terwijl de modelleringstaal ernaar verwijst als *gegevens* (gedefinieerd met **WITH_DATA).** IoT Hub verwijst ook naar de gegevens die u naar apparaten verzendt als berichten, terwijl de *modelleringstaal*ernaar verwijst als *acties* (gedefinieerd met **WITH_ACTION).** Houd er rekening mee dat deze termen door elkaar kunnen worden gebruikt in dit artikel.
> 
> 

## <a name="supported-data-types"></a>Ondersteunde gegevenstypen

De volgende gegevenstypen worden ondersteund in modellen die zijn gemaakt met de **serialisatorbibliotheek:**

| Type | Beschrijving |
| --- | --- |
| double |dubbel precisie zwevend puntnummer |
| int |32-bits geheel getal |
| float |enkel zwevend getal met precisie |
| long |lang geheel getal |
| int8\_t |8-bits geheel getal |
| int16\_t |16-bits geheel getal |
| int32\_t |32-bits geheel getal |
| int64\_t |64-bits geheel getal |
| Booleaanse waarde |booleaans |
| ascii\_\_char ptr |ASCII-tekenreeks |
| VERSCHUIVING\_VAN\_\_EDM-DATUM |datumtijdverschuiving |
| EDM\_GUID |GUID |
| EDM\_BINAIR |binair |
| VERKLAREN\_STRUCT |complex gegevenstype |

Laten we beginnen met het laatste gegevenstype. Met **\_DE DECLARE STRUCT** u complexe gegevenstypen definiëren, die groeperingen van de andere primitieve typen zijn. Met deze groeperingen kunnen we een model definiëren dat er als volgt uitziet:

```C
DECLARE_STRUCT(TestType,
double, aDouble,
int, aInt,
float, aFloat,
long, aLong,
int8_t, aInt8,
uint8_t, auInt8,
int16_t, aInt16,
int32_t, aInt32,
int64_t, aInt64,
bool, aBool,
ascii_char_ptr, aAsciiCharPtr,
EDM_DATE_TIME_OFFSET, aDateTimeOffset,
EDM_GUID, aGuid,
EDM_BINARY, aBinary
);

DECLARE_MODEL(TestModel,
WITH_DATA(TestType, Test)
);
```

Ons model bevat één gegevensgebeurtenis van het type **TestType**. **TestType** is een complex type dat meerdere leden bevat, die gezamenlijk de primitieve typen demonstreren die worden ondersteund door de **serialisatormodelleringstaal.**

Met een model als dit kunnen we code schrijven om gegevens naar IoT Hub te verzenden die als volgt wordt weergegeven:

```C
TestModel* testModel = CREATE_MODEL_INSTANCE(MyThermostat, TestModel);

testModel->Test.aDouble = 1.1;
testModel->Test.aInt = 2;
testModel->Test.aFloat = 3.0f;
testModel->Test.aLong = 4;
testModel->Test.aInt8 = 5;
testModel->Test.auInt8 = 6;
testModel->Test.aInt16 = 7;
testModel->Test.aInt32 = 8;
testModel->Test.aInt64 = 9;
testModel->Test.aBool = true;
testModel->Test.aAsciiCharPtr = "ascii string 1";

time_t now;
time(&now);
testModel->Test.aDateTimeOffset = GetDateTimeOffset(now);

EDM_GUID guid = { { 0x00, 0x01, 0x02, 0x03, 0x04, 0x05, 0x06, 0x07, 0x08, 0x09, 0x0A, 0x0B, 0x0C, 0x0D, 0x0E, 0x0F } };
testModel->Test.aGuid = guid;

unsigned char binaryArray[3] = { 0x01, 0x02, 0x03 };
EDM_BINARY binaryData = { sizeof(binaryArray), &binaryArray };
testModel->Test.aBinary = binaryData;

SendAsync(iotHubClientHandle, (const void*)&(testModel->Test));
```

In principe geven we een waarde aan elk lid van de **teststructuur** en bellen we **SendAsync** om de gebeurtenis **Testgegevens** naar de cloud te sturen. **SendAsync** is een helperfunctie die één gegevensgebeurtenis naar IoT Hub verzendt:

```C
void SendAsync(IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle, const void *dataEvent)
{
    unsigned char* destination;
    size_t destinationSize;
    if (SERIALIZE(&destination, &destinationSize, *(const unsigned char*)dataEvent) ==
    {
        // null terminate the string
        char* destinationAsString = (char*)malloc(destinationSize + 1);
        if (destinationAsString != NULL)
        {
            memcpy(destinationAsString, destination, destinationSize);
            destinationAsString[destinationSize] = '\0';
            IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromString(destinationAsString);
            if (messageHandle != NULL)
            {
                IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)0);

                IoTHubMessage_Destroy(messageHandle);
            }
            free(destinationAsString);
        }
        free(destination);
    }
}
```

Deze functie serialiseert de opgegeven gegevensgebeurtenis en stuurt deze naar IoT Hub met **IoTHubClient\_SendEventAsync**. Dit is dezelfde code besproken in eerdere artikelen **(SendAsync** kapselt de logica in een handige functie).

Een andere helperfunctie die in de vorige code wordt gebruikt, is **GetDateTimeOffset**. Deze functie transformeert de gegeven tijd in een waarde van type **EDM-datumtijdverschuiving:\_\_\_**

```C
EDM_DATE_TIME_OFFSET GetDateTimeOffset(time_t time)
{
    struct tm newTime;
    gmtime_s(&newTime, &time);
    EDM_DATE_TIME_OFFSET dateTimeOffset;
    dateTimeOffset.dateTime = newTime;
    dateTimeOffset.fractionalSecond = 0;
    dateTimeOffset.hasFractionalSecond = 0;
    dateTimeOffset.hasTimeZone = 0;
    dateTimeOffset.timeZoneHour = 0;
    dateTimeOffset.timeZoneMinute = 0;
    return dateTimeOffset;
}
```

Als u deze code uitvoert, wordt het volgende bericht naar IoT Hub verzonden:

```C
{"aDouble":1.100000000000000, "aInt":2, "aFloat":3.000000, "aLong":4, "aInt8":5, "auInt8":6, "aInt16":7, "aInt32":8, "aInt64":9, "aBool":true, "aAsciiCharPtr":"ascii string 1", "aDateTimeOffset":"2015-09-14T21:18:21Z", "aGuid":"00010203-0405-0607-0809-0A0B0C0D0E0F", "aBinary":"AQID"}
```

Houd er rekening mee dat de serialisatie zich in JSON bevindt, het formaat dat wordt gegenereerd door de **serializerbibliotheek.** Houd er ook rekening mee dat elk lid van het geserialiseerde JSON-object overeenkomt met de leden van het **TestType** die we in ons model hebben gedefinieerd. De waarden komen ook exact overeen met die welke in de code worden gebruikt. Houd er echter rekening mee dat de binaire gegevens base64-gecodeerd zijn: "AQID" is de base64-codering van {0x01, 0x02, 0x03}.

Dit voorbeeld toont het voordeel van het gebruik van de **serializer-bibliotheek** - het stelt ons in staat om JSON naar de cloud te sturen, zonder dat we expliciet te maken hoeven te krijgen met serialisatie in onze applicatie. Het enige waar we ons zorgen over hoeven te maken is het instellen van de waarden van de gegevensgebeurtenissen in ons model en vervolgens eenvoudige API's oproepen om deze gebeurtenissen naar de cloud te sturen.

Met deze informatie kunnen we modellen definiëren die het bereik van ondersteunde gegevenstypen bevatten, inclusief complexe typen (we kunnen zelfs complexe typen opnemen binnen andere complexe typen). Echter, de geserialiseerde JSON gegenereerd door het bovenstaande voorbeeld brengt een belangrijk punt. *Hoe* we gegevens verzenden met de **serializer** bibliotheek bepaalt precies hoe de JSON wordt gevormd. Dat specifieke punt is wat we nu zullen bespreken.

## <a name="more-about-serialization"></a>Meer over serialisatie

In de vorige sectie wordt een voorbeeld van de uitvoer van de **serialisatorbibliotheek** weergegeven. In deze sectie leggen we uit hoe de bibliotheek gegevens serialiseert en hoe u dat gedrag beheren met behulp van de serialisatie-API's.

Om de discussie over serialisatie te bevorderen, werken we met een nieuw model op basis van een thermostaat. Laten we eerst wat achtergrondinformatie geven over het scenario dat we proberen aan te pakken.

We willen een thermostaat modelleren die temperatuur en vochtigheid meet. Elk stukje gegevens wordt op een andere manier naar IoT Hub verzonden. Standaard valt de thermostaat eenkeer per 2 minuten een temperatuurgebeurtenis in; een vochtigheidsgebeurtenis wordt eenmaal per 15 minuten binnengevallen. Wanneer een van beide gebeurtenissen is binnengevallen, moet deze een tijdstempel bevatten die de tijd aangeeft waarop de overeenkomstige temperatuur of vochtigheid is gemeten.

Gezien dit scenario tonen we twee verschillende manieren om de gegevens te modelleren en leggen we het effect uit dat modellering heeft op de geserialiseerde uitvoer.

### <a name="model-1"></a>Model 1

Hier is de eerste versie van een model dat het vorige scenario ondersteunt:

```C
BEGIN_NAMESPACE(Contoso);

DECLARE_STRUCT(TemperatureEvent,
int, Temperature,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_STRUCT(HumidityEvent,
int, Humidity,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureEvent, Temperature),
WITH_DATA(HumidityEvent, Humidity)
);

END_NAMESPACE(Contoso);
```

Houd er rekening mee dat het model twee gegevensgebeurtenissen bevat: **temperatuur** en **vochtigheid.** In tegenstelling tot eerdere voorbeelden is het type van elke gebeurtenis een structuur die is gedefinieerd met **DECLARE\_STRUCT**. **TemperatureEvent** omvat een temperatuurmeting en een tijdstempel; **HumidityEvent** bevat een vochtigheidsmeting en een tijdstempel. Dit model geeft ons een natuurlijke manier om de gegevens te modelleren voor het hierboven beschreven scenario. Wanneer we een evenement naar de cloud sturen, sturen we een temperatuur/tijdstempel of een vochtigheids-/tijdstempelpaar.

We kunnen een temperatuurgebeurtenis naar de cloud sturen met code zoals:

```C
time_t now;
time(&now);
thermostat->Temperature.Temperature = 75;
thermostat->Temperature.Time = GetDateTimeOffset(now);

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

We gebruiken hardgecodeerde waarden voor temperatuur en vochtigheid in de monstercode, maar stel je voor dat we deze waarden ophalen door de bijbehorende sensoren op de thermostaat te bemonsteren.

De bovenstaande code maakt gebruik van de **GetDateTimeOffset** helper die eerder is geïntroduceerd. Om redenen die later duidelijk worden, scheidt deze code expliciet de taak van het serialiseren en verzenden van de gebeurtenis. De vorige code serialiseert de temperatuurgebeurtenis in een buffer. Vervolgens is **sendMessage** een helperfunctie (opgenomen in **simplesample\_amqp)** die de gebeurtenis naar IoT Hub stuurt:

```C
static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle != NULL)
    {
        IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId);

        IoTHubMessage_Destroy(messageHandle);
    }
    free((void*)buffer);
}
```

Deze code is een subset van de **SendAsync-helper** die in de vorige sectie is beschreven, dus we zullen het hier niet opnieuw bekijken.

Wanneer we de vorige code uitvoeren om de gebeurtenis Temperatuur te verzenden, wordt deze geserialiseerde vorm van de gebeurtenis naar IoT Hub verzonden:

```C
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

We sturen een temperatuur, die van het type **TemperatureEvent**is, en die struct bevat een **temperatuur-** en **tijdlid.** Dit wordt direct weerspiegeld in de geserialiseerde gegevens.

Op dezelfde manier kunnen we een vochtigheidsgebeurtenis met deze code verzenden:

```C
thermostat->Humidity.Humidity = 45;
thermostat->Humidity.Time = GetDateTimeOffset(now);
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Het geserialiseerde formulier dat naar IoT Hub wordt verzonden, wordt als volgt weergegeven:

```C
{"Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Nogmaals, dit is zoals verwacht.

Met dit model u zich voorstellen hoe extra evenementen gemakkelijk kunnen worden toegevoegd. U definieert meer structuren met **DECLARE\_STRUCT**en neemt de bijbehorende gebeurtenis op in het model **met behulp van\_MET DATA.**

Laten we nu het model zo wijzigen dat het dezelfde gegevens bevat, maar met een andere structuur.

### <a name="model-2"></a>Model 2

Overweeg dit alternatieve model voor het bovenstaande:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

In dit geval hebben we de **DECLARE\_STRUCT-macro's** geëlimineerd en definiëren we eenvoudig de gegevensitems uit ons scenario met behulp van eenvoudige typen uit de modeltaal.

Gewoon voor het moment, negeer de **gebeurtenis Tijd.** Met dat terzijde, hier is de code om **temperatuur**binnen te vallen:

```C
time_t now;
time(&now);
thermostat->Temperature = 75;

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Deze code stuurt de volgende geserialiseerde gebeurtenis naar IoT Hub:

```C
{"Temperature":75}
```

En de code voor het verzenden van de gebeurtenis Vochtigheid wordt als volgt weergegeven:

```C
thermostat->Humidity = 45;
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Deze code stuurt dit naar IoT Hub:

```C
{"Humidity":45}
```

Tot nu toe zijn er nog steeds geen verrassingen. Laten we nu de manier waarop we de SERIALIZE-macro gebruiken, veranderen.

De **serie-macro** kan meerdere gegevensgebeurtenissen als argumenten gebruiken. Dit stelt ons in staat om het **temperatuur-** en **vochtigheidsevenement** samen te serialiseren en ze in één gesprek naar IoT Hub te sturen:

```C
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

U raden dat het resultaat van deze code is dat twee gegevensgebeurtenissen naar IoT Hub worden verzonden:

[ {"Temperatuur":75}, {"Vochtigheid":45} ]

Met andere woorden, je zou verwachten dat deze code hetzelfde is als het afzonderlijk verzenden van **temperatuur** en **vochtigheid.** Het is gewoon een gemak om beide evenementen door te geven aan **SERIALIZE** in hetzelfde gesprek. Dat is echter niet het geval. In plaats daarvan stuurt de bovenstaande code deze gebeurtenis met één gegevens naar IoT Hub:

{"Temperatuur":75, "Vochtigheid":45}

Dit lijkt misschien vreemd omdat ons model **temperatuur** en **vochtigheid** definieert als twee *afzonderlijke* gebeurtenissen:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Meer to the point, hebben we niet model deze gebeurtenissen waar **temperatuur** en **vochtigheid** zijn in dezelfde structuur:

```C
DECLARE_STRUCT(TemperatureAndHumidityEvent,
int, Temperature,
int, Humidity,
);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureAndHumidityEvent, TemperatureAndHumidity),
);
```

Als we dit model gebruikten, zou het gemakkelijker zijn om te begrijpen hoe **temperatuur** en **vochtigheid** in hetzelfde geserialiseerde bericht zouden worden verzonden. Het is echter mogelijk niet duidelijk waarom het werkt op die manier wanneer u beide gegevens gebeurtenissen doorgeven aan **SERIALIZE** met behulp van model 2.

Dit gedrag is gemakkelijker te begrijpen als u de veronderstellingen kent die de **serializerbibliotheek** maakt. Om dit te begrijpen, laten we teruggaan naar ons model:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Denk aan dit model in object-georiënteerde termen. In dit geval modelleren we een fysiek apparaat (een thermostaat) en dat apparaat bevat kenmerken zoals **temperatuur** en **vochtigheid.**

We kunnen de volledige status van ons model verzenden met code zoals:

```C
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity, thermostat->Time) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Ervan uitgaande dat de waarden temperatuur, vochtigheid en tijd zijn ingesteld, zouden we een gebeurtenis als deze naar IoT Hub zien:

```C
{"Temperature":75, "Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Soms wilt u misschien alleen *bepaalde* eigenschappen van het model naar de cloud sturen (dit geldt vooral als uw model een groot aantal gegevensgebeurtenissen bevat). Het is handig om alleen een subset van gegevensgebeurtenissen te verzenden, zoals in ons eerdere voorbeeld:

```C
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Dit genereert precies dezelfde serialized gebeurtenis als of we een **TemperatureEvent** hadden gedefinieerd met een **temperatuur-** en **tijdlid,** net zoals we deden met model 1. In dit geval konden we precies dezelfde serialized gebeurtenis genereren met behulp van een ander model (model 2) omdat we **SERIALIZE** op een andere manier noemden.

Het belangrijkste punt is dat als u meerdere gegevensgebeurtenissen doorgeeft aan **SERIALIZE,** deze ervan uitgaat dat elke gebeurtenis een eigenschap is in één JSON-object.

De beste aanpak hangt af van u en hoe u denkt over uw model. Als u 'gebeurtenissen' naar de cloud verzendt en elke gebeurtenis een gedefinieerde set eigenschappen bevat, is de eerste benadering heel logisch. In dat geval zou u **DECLARE\_STRUCT** gebruiken om de structuur van elke gebeurtenis te definiëren en deze vervolgens op te nemen in uw model met de **macro\_MET GEGEVENS.** Dan stuur je elke gebeurtenis zoals we deden in het eerste voorbeeld hierboven. In deze benadering zou u slechts één gegevensgebeurtenis doorgeven aan **SERIALIZER**.

Als u denkt over uw model in een object-georiënteerde manier, dan is de tweede aanpak kan bij u passen. In dit geval zijn de elementen die met **GEGEVENS\_** zijn gedefinieerd de "eigenschappen" van uw object. U geeft alle subset van gebeurtenissen door aan **SERIALIZE** die u leuk vindt, afhankelijk van hoeveel van de status van uw object u naar de cloud wilt verzenden.

Nether aanpak is goed of fout. Wees je bewust van hoe de **serializer** bibliotheek werkt, en kies de modellering aanpak die het beste past bij uw behoeften.

## <a name="message-handling"></a>Afhandeling van berichten

Tot nu toe heeft dit artikel alleen gesproken over het verzenden van gebeurtenissen naar IoT Hub en heeft het niet zijn opgetreden voor het ontvangen van berichten. De reden hiervoor is dat wat we moeten weten over het ontvangen van berichten grotendeels is behandeld in het artikel [Azure IoT-apparaat SDK voor C](iot-hub-device-sdk-c-intro.md). Terugroepen uit dat artikel dat u berichten verwerkt door een bericht terugbelfunctie te registreren:

```C
IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather)
```

Vervolgens schrijft u de terugbelfunctie die wordt aangeroepen wanneer een bericht wordt ontvangen:

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    IOTHUBMESSAGE_DISPOSITION_RESULT result;
    const unsigned char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = EXECUTE_COMMAND_ERROR;
    }
    else
    {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
            printf("failed to malloc\r\n");
            result = EXECUTE_COMMAND_ERROR;
        }
        else
        {
            memcpy(temp, buffer, size);
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

Deze implementatie van **IoTHubMessage** roept de specifieke functie voor elke actie in uw model aan. Als uw model bijvoorbeeld deze actie definieert:

```C
WITH_ACTION(SetAirResistance, int, Position)
```

U moet een functie definiëren met deze handtekening:

```C
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

**SetAirResistance** wordt vervolgens aangeroepen wanneer dat bericht naar uw apparaat wordt verzonden.

Wat we nog niet hebben uitgelegd is hoe de geserialiseerde versie van het bericht eruit ziet. Met andere woorden, als u een **SetAirResistance-bericht** naar uw apparaat wilt verzenden, hoe ziet dat er dan uit?

Als u een bericht naar een apparaat verzendt, doet u dit via de Azure IoT-service SDK. U moet nog steeds weten welke tekenreeks u wilt verzenden om een bepaalde actie aan te roepen. De algemene indeling voor het verzenden van een bericht wordt als volgt weergegeven:

```C
{"Name" : "", "Parameters" : "" }
```

U verzendt een geserialiseerd JSON-object met twee eigenschappen: **Naam** is de naam van de actie (bericht) en **Parameters** bevat de parameters van die actie.

Als u bijvoorbeeld **SetAirResistance** wilt aanroepen, u dit bericht naar een apparaat verzenden:

```C
{"Name" : "SetAirResistance", "Parameters" : { "Position" : 5 }}
```

De actienaam moet exact overeenkomen met een actie die in uw model is gedefinieerd. De parameternamen moeten ook overeenkomen. Let ook op geval gevoeligheid. **Naam** en **parameters** zijn altijd hoofdletters. Zorg ervoor dat u de case van uw actienaam en parameters in uw model aanpast. In dit voorbeeld is de actienaam "SetAirResistance" en niet "setairresistance".

De twee andere acties **TurnFanOn** en **TurnFanOff** kunnen worden aangeroepen door deze berichten naar een apparaat te sturen:

```C
{"Name" : "TurnFanOn", "Parameters" : {}}
{"Name" : "TurnFanOff", "Parameters" : {}}
```

In deze sectie wordt alles beschreven wat u moet weten bij het verzenden van gebeurtenissen en het ontvangen van berichten met de **serializerbibliotheek.** Voordat u verder gaat, bespreken we een aantal parameters die u configureren die bepalen hoe groot uw model is.

## <a name="macro-configuration"></a>Macroconfiguratie

Als u de **Serializer-bibliotheek** gebruikt, is een belangrijk onderdeel van de SDK om op de hoogte te zijn van de azure-c-shared-utility-bibliotheek.

Als u de Azure-iot-sdk-c-repository van GitHub `git submodule update --init` hebt gekloond en de opdracht hebt uitgegeven, vindt u deze gedeelde hulpprogrammabibliotheek hier:

```C
.\\c-utility
```

Als u de bibliotheek niet hebt gekloond, u deze [hier](https://github.com/Azure/azure-c-shared-utility)vinden.

In de gedeelde hulpprogrammabibliotheek vindt u de volgende map:

```C
azure-c-shared-utility\\macro\_utils\_h\_generator.
```

Deze map bevat een Visual Studio-oplossing genaamd **macro\_utils\_h\_generator.sln:**

  ![Schermafbeelding van de Visual Studio-oplossing maco_utils_h_generator](media/iot-hub-device-sdk-c-serializer/01-macro_utils_h_generator.png)

Het programma in deze oplossing genereert het **\_macro-utils.h-bestand.** Er is een\_standaard macro utils.h-bestand bij de SDK. Met deze oplossing u bepaalde parameters wijzigen en vervolgens het kopbestand opnieuw maken op basis van deze parameters.

De twee belangrijkste parameters die betrekking hebben op **zijn nArithmetic** en **nMacroParameters**\_, die worden gedefinieerd in deze twee lijnen gevonden in macro-utils.tt:

```C
<#int nArithmetic=1024;#>
<#int nMacroParameters=124;/*127 parameters in one macro deﬁnition in C99 in chapter 5.2.4.1 Translation limits*/#>
```

Deze waarden zijn de standaardparameters die bij de SDK zijn opgenomen. Elke parameter heeft de volgende betekenis:

* nMacroParameters : hiermee bepaalt u hoeveel\_parameters u hebben in één declare-model-macrodefinitie.
* nArithmetic – Hiermee bepaalt u het totale aantal leden dat in een model is toegestaan.

De reden dat deze parameters belangrijk zijn, is omdat ze bepalen hoe groot uw model kan zijn. Overweeg bijvoorbeeld deze modeldefinitie:

```C
DECLARE_MODEL(MyModel,
WITH_DATA(int, MyData)
);
```

Zoals eerder vermeld, **DECLARE\_MODEL** is gewoon een C macro. De namen van het model en de **\_MET DATA-instructie** (nog een macro) zijn parameters van **\_DECLARE MODEL**. **nMacroParameters** definieert hoeveel parameters kunnen worden opgenomen in **DECLARE-MODEL\_**. Effectief, dit definieert hoeveel gegevens gebeurtenis en actie declaratie die u hebben. Met de standaardlimiet van 124 betekent dit dat u een model definiëren met een combinatie van ongeveer 60 acties en gegevensgebeurtenissen. Als u deze limiet probeert te overschrijden, ontvangt u compilerfouten die op dit lijkt:

  ![Schermafbeelding van compilerfouten voor macroparameters](media/iot-hub-device-sdk-c-serializer/02-nMacroParametersCompilerErrors.png)

De **parameter nArithmetic** gaat meer over de interne werking van de macrotaal dan over uw toepassing.  Hiermee bepaalt u het totale aantal leden dat u in uw model hebben, inclusief **DECLARE_STRUCT** macro's. Als u compilerfouten zoals deze begint te zien, dan zou u moeten proberen het verhogen **van nArithmetic:**

   ![Schermafbeelding van rekencompilerfouten](media/iot-hub-device-sdk-c-serializer/03-nArithmeticCompilerErrors.png)

Als u deze parameters wilt wijzigen, wijzigt u de waarden in het macro-utils.tt-bestand,\_compileert u de macro-utils\_\_h\_generator.sln-oplossing en voert u het gecompileerde programma uit. Wanneer u dit doet,\_wordt een nieuw macro-utils.h-bestand gegenereerd en in het . \\gangbare\\inc-directory.

Om de nieuwe versie van\_macro utils.h te gebruiken, verwijdert u het **serializer** NuGet-pakket uit uw oplossing en in plaats daarvan het **serializer** Visual Studio-project. Hierdoor kan uw code worden gecompileerd tegen de broncode van de serializerbibliotheek. Dit omvat de\_bijgewerkte macro utils.h. Als u dit wilt doen voor **simplesample\_amqp,** moet u eerst het NuGet-pakket voor de serializerbibliotheek uit de oplossing verwijderen:

   ![Schermafbeelding van het verwijderen van het NuGet-pakket voor de serializer-bibliotheek](media/iot-hub-device-sdk-c-serializer/04-serializer-github-package.png)

Voeg dit project vervolgens toe aan uw Visual Studio-oplossing:

> . \\c\\\\serializer\\\\bouwen windows serializer.vcxproj
> 
> 

Wanneer u klaar bent, moet uw oplossing er als volgt uitzien:

   ![Schermafbeelding van de simplesample_amqp Visual Studio-oplossing](media/iot-hub-device-sdk-c-serializer/05-serializer-project.png)

Nu wanneer u uw oplossing\_compileert, wordt de bijgewerkte macro utils.h opgenomen in uw binaire.

Houd er rekening mee dat het verhogen van deze waarden hoog genoeg de compilerlimieten kan overschrijden. Tot op dit punt is de **nMacroParameters** de belangrijkste parameter waarmee u zich moet bezighouden. De C99-spec geeft aan dat in een macrodefinitie minimaal 127 parameters zijn toegestaan. De Microsoft compiler volgt de spec precies (en heeft een limiet van 127), dus je zult niet in staat zijn om **nMacroParameters** te verhogen dan de standaard. Andere compilers kunnen u toestaan om dit te doen (bijvoorbeeld, de GNU compiler ondersteunt een hogere limiet).

Tot nu toe hebben we alles besproken wat u moet weten over het schrijven van code met de **serializer** bibliotheek. Voordat u besluit, laten we een aantal onderwerpen uit eerdere artikelen die u zich misschien afvragen over.

## <a name="the-lower-level-apis"></a>De API's op een lager niveau
De voorbeeldtoepassing waarop dit artikel zich concentreerde is **simplesample\_amqp**. In dit voorbeeld worden de API's op een hoger niveau (de**niet-LL)** gebruikt om gebeurtenissen te verzenden en berichten te ontvangen. Als u deze API's gebruikt, wordt er een achtergrondthread uitgevoerd die zorgt voor zowel het verzenden van gebeurtenissen als het ontvangen van berichten. U echter de API's (LOWER-Level) gebruiken om deze achtergrondthread te elimineren en expliciete controle over te nemen wanneer u gebeurtenissen verzendt of berichten ontvangt vanuit de cloud.

Zoals beschreven in een [vorig artikel,](iot-hub-device-sdk-c-iothubclient.md)is er een reeks functies die bestaat uit de hogere niveau API's:

* IotHubclient\_maakt fromconnectiontekenreeks
* IoTHubClient\_SendEventAsync
* IoTHubClient\_SetMessageCallback
* IoTHubClient\_vernietigen

Deze API's worden gedemonstreerd in **simplesample\_amqp**.

Er is ook een analoge set van lagere-level API's.

* IotHubClient\_\_LL maaktfromconnectiontekenreeks
* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_\_LL vernietigen

Houd er rekening mee dat de API's op een lager niveau precies op dezelfde manier werken als in de vorige artikelen. U de eerste set API's gebruiken als u een achtergrondthread wilt om het verzenden van gebeurtenissen en het ontvangen van berichten te verwerken. U gebruikt de tweede set API's als u expliciete controle wilt over wanneer u gegevens verzendt en ontvangt van IoT Hub. Beide set API's werken even goed met de **serializer** bibliotheek.

Zie de **\_simplesample http-toepassing** voor een voorbeeld van hoe de API's op lager niveau worden gebruikt met de **serializer-bibliotheek.**

## <a name="additional-topics"></a>Extra onderwerpen
Een paar andere onderwerpen die het vermelden waard zijn, zijn het afhandelen van eigendommen, met behulp van alternatieve apparaatreferenties en configuratieopties. Dit zijn allemaal onderwerpen die in een [vorig artikel](iot-hub-device-sdk-c-iothubclient.md). Het belangrijkste punt is dat al deze functies op dezelfde manier werken met de **serializer-bibliotheek** als met de **IoTHubClient-bibliotheek.** Als u bijvoorbeeld eigenschappen wilt koppelen aan een gebeurtenis van uw model, gebruikt u **IoTHubMessage-eigenschappen\_** en **Map**\_**AddorUpdate**op dezelfde manier als eerder beschreven:

```C
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Of de gebeurtenis is gegenereerd vanuit de **serializer-bibliotheek** of handmatig is gemaakt met behulp van de **IoTHubClient-bibliotheek,** maakt niet uit.

Voor de alternatieve apparaatreferenties werkt het gebruik van **\_IoTHubClient LL\_Create** net zo goed als **IoTHubClient\_CreateFromConnectionString** voor het toewijzen van een **IOTHUB\_CLIENT\_HANDLE**.

Tot slot u, als u de **serializer-bibliotheek** gebruikt, configuratieopties instellen met **IoTHubClient\_\_LL SetOption,** net zoals u deed bij het gebruik van de **IoTHubClient-bibliotheek.**

Een functie die uniek is voor de **serializer** bibliotheek zijn de initialisatie API's. Voordat u met de bibliotheek gaan werken, moet u **\_serializer init**bellen:

```C
serializer_init(NULL);
```

Dit gebeurt vlak voordat u **IoTHubClient\_CreateFromConnectionString**belt.

Op dezelfde manier, als je klaar bent met het werken met de bibliotheek, de laatste oproep die u zult maken is om **serializer\_deinit:**

```C
serializer_deinit();
```

Anders werken alle andere hierboven genoemde functies in de **serializerbibliotheek** hetzelfde als in de **IoTHubClient-bibliotheek.** Zie het [vorige artikel](iot-hub-device-sdk-c-iothubclient.md) in deze serie voor meer informatie over een van deze onderwerpen.

## <a name="next-steps"></a>Volgende stappen

In dit artikel worden de unieke aspecten van de **serialisatorbibliotheek** in de **Azure IoT-apparaat SDK voor C**in detail beschreven. Met de verstrekte informatie moet u een goed inzicht hebben in hoe u modellen gebruiken om gebeurtenissen te verzenden en berichten van IoT Hub te ontvangen.

Hiermee wordt ook de driedelige serie over het ontwikkelen van toepassingen met het **Azure IoT-apparaat SDK voor C**afgesloten. Dit moet genoeg informatie zijn om niet alleen je op weg te helpen, maar je ook een grondig inzicht te geven in hoe de API's werken. Voor meer informatie, zijn er een paar monsters in de SDK niet hier gedekt. Anders is de [Azure IoT SDK-documentatie](https://github.com/Azure/azure-iot-sdk-c) een goede bron voor aanvullende informatie.

Zie de [Azure IoT SDKs](iot-hub-devguide-sdks.md)voor meer informatie over het ontwikkelen voor IoT Hub.

Zie [AI implementeren naar edge-apparaten met Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)om de mogelijkheden van IoT Hub verder te verkennen.