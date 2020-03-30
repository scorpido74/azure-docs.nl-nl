---
title: Azure IoT Central uitbreiden met aangepaste regels en meldingen | Microsoft Documenten
description: Configureer als ontwikkelaar van een oplossing een IoT Central-toepassing om e-mailmeldingen te verzenden wanneer een apparaat stopt met het verzenden van telemetrie. Deze oplossing maakt gebruik van Azure Stream Analytics, Azure Functions en SendGrid.
author: dominicbetts
ms.author: dobett
ms.date: 12/02/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 0e161cf83662df671b8cfb100ddc12c3b3e7359f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158143"
---
# <a name="extend-azure-iot-central-with-custom-rules-using-stream-analytics-azure-functions-and-sendgrid"></a>Azure IoT Central uitbreiden met aangepaste regels met Stream Analytics, Azure-functies en SendGrid



Deze handleiding laat u als oplossingsontwikkelaar zien hoe u uw IoT Central-toepassing uitbreiden met aangepaste regels en meldingen. In het voorbeeld wordt een melding naar een operator verzonden wanneer een apparaat stopt met het verzenden van telemetrie. De oplossing maakt gebruik van een [Azure Stream Analytics-query](https://docs.microsoft.com/azure/stream-analytics/) om te detecteren wanneer een apparaat is gestopt met het verzenden van telemetrie. De taak Stream Analytics maakt gebruik van [Azure-functies](https://docs.microsoft.com/azure/azure-functions/) om meldingen te verzenden via [SendGrid](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/).

Deze handleiding laat u zien hoe u IoT Central verder uitbreiden dan wat het al kan doen met de ingebouwde regels en acties.

In deze handleiding leer je hoe je:

* Telemetrie streamen vanuit een IoT Central-toepassing met *continue gegevensexport*.
* Maak een Stream Analytics-query die detecteert wanneer een apparaat is gestopt met het verzenden van gegevens.
* Stuur een e-mailmelding met de Azure-functies en SendGrid-services.

## <a name="prerequisites"></a>Vereisten

Als u de stappen in deze handleiding wilt uitvoeren, hebt u een actief Azure-abonnement nodig.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

### <a name="iot-central-application"></a>IoT Central-toepassing

Maak een IoT Central-toepassing op de azure [IoT Central-applicatiebeheerwebsite](https://aka.ms/iotcentral) met de volgende instellingen:

| Instelling | Waarde |
| ------- | ----- |
| Prijsplan | Standard |
| Toepassingsjabloon | In-store analytics – conditiebewaking |
| De naam van de toepassing | De standaardinstelling accepteren of uw eigen naam kiezen |
| URL | Accepteer de standaardinstelling of kies uw eigen unieke URL-voorvoegsel |
| Directory | Uw Azure Active Directory-tenant |
| Azure-abonnement | Uw Azure-abonnement |
| Regio | Uw dichtstbijzijnde regio |

De voorbeelden en screenshots in dit artikel maken gebruik van de **regio Verenigde Staten.** Kies een locatie dicht bij u en zorg ervoor dat u al uw bronnen in dezelfde regio maakt.

Deze toepassingssjabloon bevat twee gesimuleerde thermostaatapparaten die telemetrie verzenden.

### <a name="resource-group"></a>Resourcegroep

Gebruik de [Azure-portal om een brongroep](https://portal.azure.com/#create/Microsoft.ResourceGroup) met de naam **DetectStoppedDevices** te maken om de andere resources die u maakt te bevatten. Maak uw Azure-resources op dezelfde locatie als uw IoT Central-toepassing.

### <a name="event-hubs-namespace"></a>Event Hubs-naamruimte

Gebruik de [Azure-portal om een naamruimte voor gebeurtenishubs te maken](https://portal.azure.com/#create/Microsoft.EventHub) met de volgende instellingen:

| Instelling | Waarde |
| ------- | ----- |
| Name    | Kies uw naamruimtenaam |
| Prijscategorie | Basic |
| Abonnement | Uw abonnement |
| Resourcegroep | GedetecteerdE gestopte apparaten |
| Locatie | VS - oost |
| Doorvoereenheden | 1 |

### <a name="stream-analytics-job"></a>Stream Analytics-taak

Gebruik de [Azure-portal om een Stream Analytics-taak te maken](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob) met de volgende instellingen:

| Instelling | Waarde |
| ------- | ----- |
| Name    | Kies uw functienaam |
| Abonnement | Uw abonnement |
| Resourcegroep | GedetecteerdE gestopte apparaten |
| Locatie | VS - oost |
| Hostingomgeving | Cloud |
| Streaming-eenheden | 3 |

### <a name="function-app"></a>Function App

Gebruik de [Azure-portal om een functie-app te maken](https://portal.azure.com/#create/Microsoft.FunctionApp) met de volgende instellingen:

| Instelling | Waarde |
| ------- | ----- |
| Naam van app    | De naam van de functie-app kiezen |
| Abonnement | Uw abonnement |
| Resourcegroep | GedetecteerdE gestopte apparaten |
| OS | Windows |
| Hostingabonnement | Verbruiksabonnement |
| Locatie | VS - oost |
| Runtimestack | .NET |
| Storage | Nieuwe maken |

### <a name="sendgrid-account"></a>SendGrid-account

Gebruik de [Azure-portal om een SendGrid-account te maken](https://portal.azure.com/#create/Sendgrid.sendgrid) met de volgende instellingen:

| Instelling | Waarde |
| ------- | ----- |
| Name    | Kies de naam van uw SendGrid-account |
| Wachtwoord | Een wachtwoord maken |
| Abonnement | Uw abonnement |
| Resourcegroep | GedetecteerdE gestopte apparaten |
| Prijscategorie | F1 Free |
| Contactgegevens | De vereiste informatie invullen |

Wanneer u alle vereiste resources hebt gemaakt, ziet de resourcegroep **DetectStoppedDevices** eruit als de volgende schermafbeelding:

![Brongroep Gestopte apparaten detecteren](media/howto-create-custom-rules/resource-group.png)

## <a name="create-an-event-hub"></a>Een Event Hub maken

U een IoT Central-toepassing configureren om continu telemetrie naar een gebeurtenishub te exporteren. In deze sectie maakt u een gebeurtenishub om telemetrie van uw IoT Central-toepassing te ontvangen. De gebeurtenishub levert de telemetrie aan uw Stream Analytics-taak voor verwerking.

1. Navigeer in de Azure-portal naar de naamruimte van uw gebeurtenishubs en selecteer **+ Gebeurtenishub**.
1. Geef uw gebeurtenishub **een centrale naam**en selecteer **Maken**.

De naamruimte van je gebeurtenishubs ziet eruit als de volgende schermafbeelding:

![Event Hubs-naamruimte](media/howto-create-custom-rules/event-hubs-namespace.png)

## <a name="get-sendgrid-api-key"></a>SendGrid-API-sleutel verzenden

Uw functie-app heeft een SendGrid API-sleutel nodig om e-mailberichten te verzenden. Ga als lid van het nieuws over een SendGrid API-sleutel:

1. Navigeer in de Azure-portal naar uw SendGrid-account. Kies vervolgens **Beheren** om toegang te krijgen tot uw SendGrid-account.
1. Kies in uw SendGrid-account **Instellingen**en vervolgens **API-sleutels**. Kies **API-sleutel maken:**

    ![SendGrid-API-sleutel maken](media/howto-create-custom-rules/sendgrid-api-keys.png)

1. Maak op de pagina **API-sleutel maken** een sleutel met de naam **AzureFunctionAccess** met **machtigingen voor volledige toegang.**
1. Noteer de API-sleutel, die heb je nodig bij het configureren van je functie-app.

## <a name="define-the-function"></a>De functie definiëren

Deze oplossing maakt gebruik van een Azure Functions-app om een e-mailmelding te verzenden wanneer de taak Stream Analytics een gestopt apparaat detecteert. Ga als u uw functie-app maken:

1. Navigeer in de Azure-portal naar de instantie **App-service** in de brongroep **DetectStoppedDevices.**
1. Selecteer **+** om een nieuwe functie te maken.
1. Kies op de pagina **EEN ONTWIKKELOMGEVING** KIEZEN de optie **In-portal** en selecteer **Doorgaan**.
1. Kies **webhook + API** op de pagina **EEN FUNCTIE MAKEN** en selecteer Vervolgens **Maken**.

De portal maakt een standaardfunctie genaamd **HttpTrigger1:**

![StandaardHTTP-triggerfunctie](media/howto-create-custom-rules/default-function.png)

### <a name="configure-function-bindings"></a>Functiebindingen configureren

Als u e-mails wilt verzenden met SendGrid, moet u de bindingen voor uw functie als volgt configureren:

1. Selecteer **Integreren,** kies de uitvoer **HTTP ($return)** en selecteer **verwijderen**.
1. Kies **+ Nieuwe uitvoer,** kies **SendGrid**en kies **Selecteer .** Kies **Installeren** om de SendGrid-extensie te installeren.
1. Wanneer de installatie is voltooid, selecteert u **De teruggavewaarde van de functie gebruiken**. Voeg een geldig **Adres toe** om e-mailmeldingen te ontvangen.  Voeg een geldig **van adres** toe om te gebruiken als afzender van de e-mail.
1. Selecteer **nieuw** naast **SendGrid API Key App Setting**. Voer **SendGridAPIKey** in als de sleutel en de SendGrid-API-sleutel die u eerder als waarde hebt opgemerkt. Selecteer vervolgens **Maken**.
1. Kies **Opslaan** om de SendGrid-bindingen voor uw functie op te slaan.

De integratie-instellingen zien eruit als de volgende screenshot:

![Functie-app-integraties](media/howto-create-custom-rules/function-integrate.png)

### <a name="add-the-function-code"></a>De functiecode toevoegen

Als u uw functie wilt implementeren, voegt u de C#-code toe om het binnenkomende HTTP-verzoek te ontzeggen en verzendt u de e-mails als volgt:

1. Kies de functie **HttpTrigger1** in de functie-app en vervang de C#-code door de volgende code:

    ```csharp
    #r "Newtonsoft.Json"
    #r "..\bin\SendGrid.dll"

    using System;
    using SendGrid.Helpers.Mail;
    using Microsoft.Azure.WebJobs.Host;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    public static SendGridMessage Run(HttpRequest req, ILogger log)
    {
        string requestBody = new StreamReader(req.Body).ReadToEnd();
        log.LogInformation(requestBody);
        var notifications = JsonConvert.DeserializeObject<IList<Notification>>(requestBody);

        SendGridMessage message = new SendGridMessage();
        message.Subject = "Contoso device notification";

        var content = "The following device(s) have stopped sending telemetry:<br/><br/><table><tr><th>Device ID</th><th>Time</th></tr>";
        foreach(var notification in notifications) {
            log.LogInformation($"No message received - Device: {notification.deviceid}, Time: {notification.time}");
            content += $"<tr><td>{notification.deviceid}</td><td>{notification.time}</td></tr>";
        }
        content += "</table>";
        message.AddContent("text/html", content);

        return message;
    }

    public class Notification
    {
        public string deviceid { get; set; }
        public string time { get; set; }
    }
    ```

    Mogelijk ziet u een foutbericht totdat u de nieuwe code opslaat.

1. Selecteer **Opslaan** om de functie op te slaan.

### <a name="test-the-function-works"></a>Test de functie werkt

Als u de functie in de portal wilt testen, kiest u **eerst Logboeken** onder aan de codeeditor. Kies vervolgens **Testen** rechts van de codeeditor. Gebruik de volgende JSON als **de instantie Request:**

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

De functielogboekberichten worden weergegeven in het deelvenster **Logboeken:**

![Functielogboekuitvoer](media/howto-create-custom-rules/function-app-logs.png)

Na een paar minuten ontvangt het e-mailadres **Aan** een e-mail met de volgende inhoud:

```txt
The following device(s) have stopped sending telemetry:

Device ID    Time
test-device-1    2019-05-02T14:23:39.527Z
test-device-2    2019-05-02T14:23:50.717Z
test-device-3    2019-05-02T14:24:28.919Z
```

## <a name="add-stream-analytics-query"></a>Stream Analytics-query toevoegen

Deze oplossing maakt gebruik van een Stream Analytics-query om te detecteren wanneer een apparaat langer dan 120 seconden stopt met het verzenden van telemetrie. De query gebruikt de telemetrie van de gebeurtenishub als invoer. De taak stuurt de queryresultaten naar de functie-app. In deze sectie configureert u de taak Stream Analytics:

1. Navigeer in de Azure-portal naar uw taak Stream Analytics, kies onder **Vacatures-topologie** **invoer**selecteren , kies **+ Streaminvoer toevoegen**en kies vervolgens **Gebeurtenishub**.
1. Gebruik de informatie in de volgende tabel om de invoer te configureren met behulp van de gebeurtenishub die u eerder hebt gemaakt en kies **Opslaan:**

    | Instelling | Waarde |
    | ------- | ----- |
    | Invoeralias | centraltelemetrie |
    | Abonnement | Uw abonnement |
    | Event hub-naamruimte | Naamruimte van uw gebeurtenishub |
    | Event Hub-naam | Bestaande - **centrale export gebruiken** |

1. Selecteer **onder Topologie van taken** **uitvoer,** kies **+ Toevoegen**en kies vervolgens **azure-functie**.
1. Gebruik de gegevens in de volgende tabel om de uitvoer te configureren en kies **Opslaan:**

    | Instelling | Waarde |
    | ------- | ----- |
    | Uitvoeralias | e-mailmelding |
    | Abonnement | Uw abonnement |
    | Function App | Uw functie-app |
    | Functie  | HttpTrigger1 |

1. Selecteer **onder Topologie van vacatures**de optie **Query** en vervang de bestaande query door de volgende SQL:

    ```sql
    with
    LeftSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid1, 
        EventEnqueuedUtcTime AS time1
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    ),
    RightSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid2, 
        EventEnqueuedUtcTime AS time2
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    )

    SELECT
        LeftSide.deviceid1 as deviceid,
        LeftSide.time1 as time
    INTO
        [emailnotification]
    FROM
        LeftSide
        LEFT OUTER JOIN
        RightSide 
        ON
        LeftSide.deviceid1=RightSide.deviceid2 AND DATEDIFF(second,LeftSide,RightSide) BETWEEN 1 AND 120
        where
        -- Find records where a device didn't send a message 120 seconds
        RightSide.deviceid2 is NULL
    ```

1. Selecteer **Opslaan**.
1. Als u de taak Stream Analytics wilt starten, kiest u **Overzicht**en vervolgens **Nu** **nu**en **vervolgens start:**

    ![Stream Analytics](media/howto-create-custom-rules/stream-analytics.png)

## <a name="configure-export-in-iot-central"></a>Export configureren in IoT Central

Navigeer op de website [azure IoT Central-toepassingsbeheer](https://aka.ms/iotcentral) naar de IoT Central-toepassing die u hebt gemaakt op basis van de Contoso-sjabloon. In deze sectie configureert u de toepassing om de telemetrie van de gesimuleerde apparaten naar uw gebeurtenishub te streamen. Ga als u de export wilt configureren:

1. Navigeer naar de pagina **Gegevensexporteren,** selecteer **+ Nieuw**en vervolgens Azure **Event Hubs**.
1. Gebruik de volgende instellingen om de export te configureren en selecteer **Opslaan:**

    | Instelling | Waarde |
    | ------- | ----- |
    | Weergavenaam | Exporteren naar gebeurtenishubs |
    | Ingeschakeld | Aan |
    | Event Hubs-naamruimte | Naamruimte van uw gebeurtenishubs |
    | Event Hub | centrale uitvoer |
    | Metingen | Aan |
    | Apparaten | Uit |
    | Apparaatsjablonen | Uit |

![Configuratie van continue gegevensexport](media/howto-create-custom-rules/cde-configuration.png)

Wacht tot de exportstatus **wordt uitgevoerd** voordat u verdergaat.

## <a name="test"></a>Test

Als u de oplossing wilt testen, u de continue gegevensexport van IoT Central naar gesimuleerde gestopte apparaten uitschakelen:

1. Navigeer in uw IoT Central-toepassing naar de pagina **Gegevensexporteren** en selecteer de exportconfiguratie **Exporteren naar gebeurtenishubs.**
1. **Ingeschakeld instellen** in **Uit en** kies **Opslaan**.
1. Na ten minste twee minuten ontvangt het E-mailadres **Aan** een of meer e-mails die lijken op de volgende voorbeeldinhoud:

    ```txt
    The following device(s) have stopped sending telemetry:

    Device ID         Time
    Thermostat-Zone1  2019-11-01T12:45:14.686Z
    ```

## <a name="tidy-up"></a>Opruimen

Als u na deze how-to wilt opruimen en onnodige kosten wilt voorkomen, verwijdert u de brongroep **DetectStoppedDevices** in de Azure-portal.

U de IoT Central-toepassing verwijderen van de pagina **Beheer** in de toepassing.

## <a name="next-steps"></a>Volgende stappen

In deze handleiding leerde je hoe je:

* Telemetrie streamen vanuit een IoT Central-toepassing met *continue gegevensexport*.
* Maak een Stream Analytics-query die detecteert wanneer een apparaat is gestopt met het verzenden van gegevens.
* Stuur een e-mailmelding met de Azure-functies en SendGrid-services.

Nu u weet hoe u aangepaste regels en meldingen maken, is de voorgestelde volgende stap om te leren hoe [Azure IoT Central worden uitgebreid met aangepaste analyses.](howto-create-custom-analytics.md)
