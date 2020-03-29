---
title: Meldingshubs gebruiken met Python
description: Meer informatie over het gebruik van Azure Notification Hubs vanuit een Python-toepassing.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 5640dd4a-a91e-4aa0-a833-93615bde49b4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: python
ms.devlang: php
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 1ff8c382813654b1dee38a99bf2cc0ca67afbedd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76313824"
---
# <a name="how-to-use-notification-hubs-from-python"></a>Meldingshubs van Python gebruiken

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

U hebt toegang tot alle functies van Notification Hubs vanaf een Java/PHP/Python/Ruby back-end via de Notification Hub REST-interface zoals beschreven in het MSDN-artikel [Notification Hubs REST API's.](https://msdn.microsoft.com/library/dn223264.aspx)

> [!NOTE]
> Dit is een voorbeeldreferentie-implementatie voor het implementeren van de melding die in Python wordt uitgevoerd en is niet de officieel ondersteunde Notifications Hub Python SDK. Het monster is gemaakt met Python 3.4.

In dit artikel leest u informatie over:

- Bouw een REST-client voor Notification Hubs-functies in Python.
- Stuur meldingen via de Python-interface naar de API's voor de rest van de meldingshub.
- Ontvang een dump van de HTTP REST-aanvraag/-reactie voor foutopsporing/educatief doel.

U de [zelfstudie Aan de slag](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) voor uw mobiele platform naar keuze volgen en het back-endgedeelte in Python implementeren.

> [!NOTE]
> Het bereik van de steekproef is alleen beperkt tot het verzenden van meldingen en het doet geen registratiebeheer.

## <a name="client-interface"></a>Clientinterface

De hoofdclientinterface kan dezelfde methoden bieden die beschikbaar zijn in de [SDK .NET Notification Hubs.](https://msdn.microsoft.com/library/jj933431.aspx) Deze interface u direct vertalen alle tutorials en monsters die momenteel beschikbaar zijn op deze site, en bijgedragen door de gemeenschap op het internet.

U vindt alle code beschikbaar in de [Python REST wrapper monster].

Bijvoorbeeld om een client te maken:

```python
isDebug = True
hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
```

Ga als lid van het Oproosteren van Windows:

```python
wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello world!</text></binding></visual></toast>"""
hub.send_windows_notification(wns_payload)
```

## <a name="implementation"></a>Implementatie

Als u dat nog niet hebt gedaan, volgt u de [zelfstudie Aan] de slag tot aan het laatste gedeelte waar u de back-end moet implementeren.

Alle details om een volledige REST wrapper te implementeren zijn te vinden op [MSDN.](https://msdn.microsoft.com/library/dn530746.aspx) In deze sectie wordt de Python-implementatie beschreven van de belangrijkste stappen die nodig zijn om toegang te krijgen tot REST-eindpunten van meldingenhubs en meldingen te verzenden

1. De verbindingsreeks parseren
2. Het autorisatietoken genereren
3. Een melding verzenden met http rest API

### <a name="parse-the-connection-string"></a>De verbindingsreeks parseren

Hier is de hoofdklasse die de client implementeert, waarvan de constructor de verbindingstekenreeks ontlijdt:

```python
class NotificationHub:
    API_VERSION = "?api-version=2013-10"
    DEBUG_SEND = "&test"

    def __init__(self, connection_string=None, hub_name=None, debug=0):
        self.HubName = hub_name
        self.Debug = debug

        # Parse connection string
        parts = connection_string.split(';')
        if len(parts) != 3:
            raise Exception("Invalid ConnectionString.")

        for part in parts:
            if part.startswith('Endpoint'):
                self.Endpoint = 'https' + part[11:]
            if part.startswith('SharedAccessKeyName'):
                self.SasKeyName = part[20:]
            if part.startswith('SharedAccessKey'):
                self.SasKeyValue = part[16:]
```

### <a name="create-security-token"></a>Beveiligingstoken maken

De details van het maken van beveiligingstoken's zijn [hier](https://msdn.microsoft.com/library/dn495627.aspx)beschikbaar.
Voeg de volgende `NotificationHub` methoden toe aan de klasse om het token te maken op basis van de URI van de huidige aanvraag en de referenties die uit de verbindingstekenreeks zijn geëxtraheerd.

```python
@staticmethod
def get_expiry():
    # By default returns an expiration of 5 minutes (=300 seconds) from now
    return int(round(time.time() + 300))


@staticmethod
def encode_base64(data):
    return base64.b64encode(data)


def sign_string(self, to_sign):
    key = self.SasKeyValue.encode('utf-8')
    to_sign = to_sign.encode('utf-8')
    signed_hmac_sha256 = hmac.HMAC(key, to_sign, hashlib.sha256)
    digest = signed_hmac_sha256.digest()
    encoded_digest = self.encode_base64(digest)
    return encoded_digest


def generate_sas_token(self):
    target_uri = self.Endpoint + self.HubName
    my_uri = urllib.parse.quote(target_uri, '').lower()
    expiry = str(self.get_expiry())
    to_sign = my_uri + '\n' + expiry
    signature = urllib.parse.quote(self.sign_string(to_sign))
    auth_format = 'SharedAccessSignature sig={0}&se={1}&skn={2}&sr={3}'
    sas_token = auth_format.format(signature, expiry, self.SasKeyName, my_uri)
    return sas_token
```

### <a name="send-a-notification-using-http-rest-api"></a>Een melding verzenden met http rest API

Laat eerst een klasse definiëren die een melding vertegenwoordigt.

```python
class Notification:
    def __init__(self, notification_format=None, payload=None, debug=0):
        valid_formats = ['template', 'apple', 'fcm',
                         'windows', 'windowsphone', "adm", "baidu"]
        if not any(x in notification_format for x in valid_formats):
            raise Exception(
                "Invalid Notification format. " +
                "Must be one of the following - 'template', 'apple', 'fcm', 'windows', 'windowsphone', 'adm', 'baidu'")

        self.format = notification_format
        self.payload = payload

        # array with keynames for headers
        # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
        # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry
        # in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
        self.headers = None
```

Deze klasse is een container voor een native meldingsinstantie of een set eigenschappen van een sjabloonmelding, een set kopteksten, die indeling (native platform of sjabloon) en platformspecifieke eigenschappen bevat (zoals de eigenschap Expiration van Apple en WNS-headers).

Raadpleeg de [API's van De REST van de meldingshubs](https://msdn.microsoft.com/library/dn495827.aspx) en de indelingen van de specifieke meldingsplatforms voor alle beschikbare opties.

Schrijf nu met deze klasse de verzendmeldingsmethoden binnen in de `NotificationHub` klasse.

```python
def make_http_request(self, url, payload, headers):
    parsed_url = urllib.parse.urlparse(url)
    connection = http.client.HTTPSConnection(
        parsed_url.hostname, parsed_url.port)

    if self.Debug > 0:
        connection.set_debuglevel(self.Debug)
        # adding this querystring parameter gets detailed information about the PNS send notification outcome
        url += self.DEBUG_SEND
        print("--- REQUEST ---")
        print("URI: " + url)
        print("Headers: " + json.dumps(headers, sort_keys=True,
                                       indent=4, separators=(' ', ': ')))
        print("--- END REQUEST ---\n")

    connection.request('POST', url, payload, headers)
    response = connection.getresponse()

    if self.Debug > 0:
        # print out detailed response information for debugging purpose
        print("\n\n--- RESPONSE ---")
        print(str(response.status) + " " + response.reason)
        print(response.msg)
        print(response.read())
        print("--- END RESPONSE ---")

    elif response.status != 201:
        # Successful outcome of send message is HTTP 201 - Created
        raise Exception(
            "Error sending notification. Received HTTP code " + str(response.status) + " " + response.reason)

    connection.close()


def send_notification(self, notification, tag_or_tag_expression=None):
    url = self.Endpoint + self.HubName + '/messages' + self.API_VERSION

    json_platforms = ['template', 'apple', 'fcm', 'adm', 'baidu']

    if any(x in notification.format for x in json_platforms):
        content_type = "application/json"
        payload_to_send = json.dumps(notification.payload)
    else:
        content_type = "application/xml"
        payload_to_send = notification.payload

    headers = {
        'Content-type': content_type,
        'Authorization': self.generate_sas_token(),
        'ServiceBusNotification-Format': notification.format
    }

    if isinstance(tag_or_tag_expression, set):
        tag_list = ' || '.join(tag_or_tag_expression)
    else:
        tag_list = tag_or_tag_expression

    # add the tags/tag expressions to the headers collection
    if tag_list != "":
        headers.update({'ServiceBusNotification-Tags': tag_list})

    # add any custom headers to the headers collection that the user may have added
    if notification.headers is not None:
        headers.update(notification.headers)

    self.make_http_request(url, payload_to_send, headers)


def send_apple_notification(self, payload, tags=""):
    nh = Notification("apple", payload)
    self.send_notification(nh, tags)


def send_fcm_notification(self, payload, tags=""):
    nh = Notification("fcm", payload)
    self.send_notification(nh, tags)


def send_adm_notification(self, payload, tags=""):
    nh = Notification("adm", payload)
    self.send_notification(nh, tags)


def send_baidu_notification(self, payload, tags=""):
    nh = Notification("baidu", payload)
    self.send_notification(nh, tags)


def send_mpns_notification(self, payload, tags=""):
    nh = Notification("windowsphone", payload)

    if "<wp:Toast>" in payload:
        nh.headers = {'X-WindowsPhone-Target': 'toast',
                      'X-NotificationClass': '2'}
    elif "<wp:Tile>" in payload:
        nh.headers = {'X-WindowsPhone-Target': 'tile',
                      'X-NotificationClass': '1'}

    self.send_notification(nh, tags)


def send_windows_notification(self, payload, tags=""):
    nh = Notification("windows", payload)

    if "<toast>" in payload:
        nh.headers = {'X-WNS-Type': 'wns/toast'}
    elif "<tile>" in payload:
        nh.headers = {'X-WNS-Type': 'wns/tile'}
    elif "<badge>" in payload:
        nh.headers = {'X-WNS-Type': 'wns/badge'}

    self.send_notification(nh, tags)


def send_template_notification(self, properties, tags=""):
    nh = Notification("template", properties)
    self.send_notification(nh, tags)
```

Deze methoden sturen een HTTP POST-verzoek naar het eindpunt /messages van uw meldingshub, met de juiste hoofdtekst en kopteksten om de melding te verzenden.

### <a name="using-debug-property-to-enable-detailed-logging"></a>De eigenschap Foutopsporing gebruiken om gedetailleerde logboekregistratie in te schakelen

Als u de eigenschap Foutopsporing inschakelt terwijl de meldingshub wordt geïnitialeerd, worden gedetailleerde logboekinformatie over de HTTP-aanvraag- en reactiedump en de gedetailleerde meldingsberichtverzonden weergegeven.
De [eigenschap TestSend van Meldingenhubs](https://docs.microsoft.com/previous-versions/azure/reference/dn495827(v=azure.100)) retourneert gedetailleerde informatie over de uitkomst van de meldingsverzonding.
Om het te gebruiken - initialiseren met behulp van de volgende code:

```python
hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
```

De HTTP-URL voor het verzenden van meldingen hub wordt als gevolg hiervan toegevoegd met een 'test'-querytekenreeks.

## <a name="complete-the-tutorial"></a><a name="complete-tutorial"></a>De zelfstudie voltooien

Nu u de zelfstudie Aan de slag voltooien door de melding vanaf een Python-back-end te verzenden.

Initialiseer uw Client Meldinghubs (vervang de verbindingstekenreeks en de naam van de hub zoals geïnstrueerd in de [zelfstudie Aan]de slag):

```python
hub = NotificationHub("myConnectionString", "myNotificationHubName")
```

Voeg vervolgens de verzendcode toe, afhankelijk van uw mobiele doelplatform. In dit voorbeeld worden ook methoden op een hoger niveau toegevoegd om het verzenden van meldingen op basis van het platform mogelijk te maken, bijvoorbeeld send_windows_notification voor windows; send_apple_notification (voor appel) etc.

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows Store en Windows Phone 8.1 (niet-Zilverlicht)

```python
wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Test</text></binding></visual></toast>"""
hub.send_windows_notification(wns_payload)
```

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8.0 en 8.1 Zilverlicht

```python
hub.send_mpns_notification(toast)
```

### <a name="ios"></a>iOS

```python
alert_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_apple_notification(alert_payload)
```

### <a name="android"></a>Android

```python
fcm_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_fcm_notification(fcm_payload)
```

### <a name="kindle-fire"></a>Kindle Fire

```python
adm_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_adm_notification(adm_payload)
```

### <a name="baidu"></a>Baidu

```python
baidu_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_baidu_notification(baidu_payload)
```

Als u uw Python-code uitvoert, moet er een melding worden weergegeven op uw doelapparaat.

## <a name="examples"></a>Voorbeelden

### <a name="enabling-the-debug-property"></a>De `debug` eigenschap inschakelen

Wanneer u de foutopsporingsvlag inschakelt tijdens het initialiseren van de NotificationHub, ziet u gedetailleerde HTTP-aanvraag- en reactiedump en NotificationOutcome zoals het volgende, waar u begrijpen welke HTTP-headers in het verzoek worden doorgegeven en welk HTTP-antwoord was ontvangen van de Notification Hub:

![][1]

U ziet bijvoorbeeld het resultaat van de meldingshub.

- wanneer het bericht naar de pushmeldingsservice wordt verzonden.
    ```xml
    <Outcome>The Notification was successfully sent to the Push Notification System</Outcome>
    ```
- Als er geen doelen zijn gevonden voor een pushmelding, dan ziet u waarschijnlijk de volgende uitvoer als de reactie (wat aangeeft dat er geen registraties zijn gevonden om de melding af te leveren, waarschijnlijk omdat de registraties een aantal niet-overeenkomende tags)
    ```xml
    '<NotificationOutcome xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect" xmlns:i="https://www.w3.org/2001/XMLSchema-instance"><Success>0</Success><Failure>0</Failure><Results i:nil="true"/></NotificationOutcome>'
    ```

### <a name="broadcast-toast-notification-to-windows"></a>Pop-upmelding uitzenden naar Windows

Let op de kopteksten die worden verzonden wanneer u een melding van een uitzendingspop verzendt naar de Windows-client.

```python
hub.send_windows_notification(wns_payload)
```

![][2]

### <a name="send-notification-specifying-a-tag-or-tag-expression"></a>Melding verzenden met vermelding van een tag (of tagexpressie)

Let op de Tags HTTP-header, die wordt toegevoegd aan het HTTP-verzoek (in het onderstaande voorbeeld wordt de melding alleen verzonden naar registraties met 'sport'-payload)

```python
hub.send_windows_notification(wns_payload, "sports")
```

![][3]

### <a name="send-notification-specifying-multiple-tags"></a>Melding verzenden met meerdere tags

Zie hoe de HTTP-header tags worden gewijzigd wanneer meerdere tags worden verzonden.

```python
tags = {'sports', 'politics'}
hub.send_windows_notification(wns_payload, tags)
```

![][4]

### <a name="templated-notification"></a>Aangemeld met sjabloon

De http-header wordt opgemaakt en de payload-instantie wordt verzonden als onderdeel van de HTTP-aanvraaginstantie:

**Clientzijde - geregistreerde sjabloon:**

```python
var template = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(greeting_en)</text></binding></visual></toast>";
```

**Server kant - het verzenden van de payload:**

```python
template_payload = {'greeting_en': 'Hello', 'greeting_fr': 'Salut'}
hub.send_template_notification(template_payload)
```

![][5]

## <a name="next-steps"></a>Volgende stappen

In dit artikel is te zien hoe u een Python REST-client voor Notification Hubs maakt. Hier kunt u het volgende doen:

- Download de volledige [Python REST wrapper monster,]die alle code in dit artikel bevat.
- Lees verder over de functie Tagging-functie voor meldingenhubs in de [zelfstudie Breaking News]
- Lees verder over de functie Sjablonen voor meldingenhubs in de [zelfstudie Nieuws lokaliseren]

<!-- URLs -->
[Voorbeeld van python REST-wrapper]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-python
[Zelfstudie Aan de slag]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Zelfstudie Breaking News]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[Zelfstudie Nieuws lokaliseren]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/

<!-- Images. -->
[1]: ./media/notification-hubs-python-backend-how-to/DetailedLoggingInfo.png
[2]: ./media/notification-hubs-python-backend-how-to/BroadcastScenario.png
[3]: ./media/notification-hubs-python-backend-how-to/SendWithOneTag.png
[4]: ./media/notification-hubs-python-backend-how-to/SendWithMultipleTags.png
[5]: ./media/notification-hubs-python-backend-how-to/TemplatedNotification.png
