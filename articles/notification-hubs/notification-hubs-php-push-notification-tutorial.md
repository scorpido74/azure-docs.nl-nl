---
title: Azure-meldingshubs gebruiken met PHP
description: Meer informatie over het gebruik van Azure Notification Hubs vanaf een PHP-back-end.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 0156f994-96d0-4878-b07b-49b7be4fd856
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: php
ms.devlang: php
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 9a77a9d9c8b2d71197089f66d81e07d56c780e11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263843"
---
# <a name="how-to-use-notification-hubs-from-php"></a>Notification Hubs van PHP gebruiken

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

U hebt toegang tot alle notification hubs-functies vanaf een Java/PHP/Ruby-backend via de Notification Hub REST-interface zoals beschreven in de [API's van meldingen van meldingen](https://msdn.microsoft.com/library/dn223264.aspx)van het MSDN-onderwerp.

In dit onderwerp laten we zien hoe:

* Bouw een REST-client voor Notification Hubs-functies in PHP;
* Volg de [zelfstudie Aan de slag](notification-hubs-ios-apple-push-notification-apns-get-started.md) voor uw mobiele platform naar keuze en implementeer het backend-gedeelte in PHP.

## <a name="client-interface"></a>Clientinterface

De belangrijkste clientinterface kan dezelfde methoden bieden die beschikbaar zijn in de [.NET Notification Hubs SDK,](https://msdn.microsoft.com/library/jj933431.aspx)waarmee u alle tutorials en voorbeelden die momenteel beschikbaar zijn op deze site, en bijgedragen door de community op het internet, direct vertalen.

U vindt alle code beschikbaar in de [PHP REST wrapper sample].

Bijvoorbeeld om een client te maken:

    ```php
    $hub = new NotificationHub("connection string", "hubname");
    ```

Een iOS native melding verzenden:

    ```php
    $notification = new Notification("apple", '{"aps":{"alert": "Hello!"}}');
    $hub->sendNotification($notification, null);
    ```

## <a name="implementation"></a>Implementatie

Als u dat nog niet hebt gedaan, volgt u de [zelfstudie Aan] de slag tot aan het laatste gedeelte waar u de backend moet implementeren.
Als u wilt u de code uit het [PHP REST-wrappervoorbeeld] gebruiken en rechtstreeks naar de sectie [De zelfstudie voltooien](#complete-tutorial) gaan.

Alle details om een volledige REST wrapper te implementeren zijn te vinden op [MSDN.](https://msdn.microsoft.com/library/dn530746.aspx) In deze sectie beschrijven we de PHP-implementatie van de belangrijkste stappen die nodig zijn om toegang te krijgen tot REST-eindpunten van notification hubs:

1. De verbindingsreeks parseren
2. Het autorisatietoken genereren
3. De HTTP-oproep uitvoeren

### <a name="parse-the-connection-string"></a>De verbindingsreeks parseren

Hier is de hoofdklasse die de client implementeert, waarvan de constructor de verbindingstekenreeks ontlijdt:

    ```php
    class NotificationHub {
        const API_VERSION = "?api-version=2013-10";

        private $endpoint;
        private $hubPath;
        private $sasKeyName;
        private $sasKeyValue;

        function __construct($connectionString, $hubPath) {
            $this->hubPath = $hubPath;

            $this->parseConnectionString($connectionString);
        }

        private function parseConnectionString($connectionString) {
            $parts = explode(";", $connectionString);
            if (sizeof($parts) != 3) {
                throw new Exception("Error parsing connection string: " . $connectionString);
            }

            foreach ($parts as $part) {
                if (strpos($part, "Endpoint") === 0) {
                    $this->endpoint = "https" . substr($part, 11);
                } else if (strpos($part, "SharedAccessKeyName") === 0) {
                    $this->sasKeyName = substr($part, 20);
                } else if (strpos($part, "SharedAccessKey") === 0) {
                    $this->sasKeyValue = substr($part, 16);
                }
            }
        }
    }
    ```

### <a name="create-a-security-token"></a>Een beveiligingstoken maken

Raadpleeg de Azure-documentatie voor informatie over het [maken van een SAS-beveiligingstoken.](https://docs.microsoft.com/previous-versions/azure/reference/dn495627(v=azure.100)#create-sas-security-token)

Voeg `generateSasToken` de methode `NotificationHub` toe aan de klasse om het token te maken op basis van de URI van de huidige aanvraag en de referenties die uit de verbindingstekenreeks zijn geëxtraheerd.

    ```php
    private function generateSasToken($uri) {
        $targetUri = strtolower(rawurlencode(strtolower($uri)));

        $expires = time();
        $expiresInMins = 60;
        $expires = $expires + $expiresInMins * 60;
        $toSign = $targetUri . "\n" . $expires;

        $signature = rawurlencode(base64_encode(hash_hmac('sha256', $toSign, $this->sasKeyValue, TRUE)));

        $token = "SharedAccessSignature sr=" . $targetUri . "&sig="
                    . $signature . "&se=" . $expires . "&skn=" . $this->sasKeyName;

        return $token;
    }
    ```

### <a name="send-a-notification"></a>Een melding verzenden

Laten we eerst een klasse definiëren die een melding vertegenwoordigt.

    ```php
    class Notification {
        public $format;
        public $payload;

        # array with keynames for headers
        # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
        # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
        public $headers;

        function __construct($format, $payload) {
            if (!in_array($format, ["template", "apple", "windows", "fcm", "windowsphone"])) {
                throw new Exception('Invalid format: ' . $format);
            }

            $this->format = $format;
            $this->payload = $payload;
        }
    }
    ```

Deze klasse is een container voor een native meldingsinstantie, of een set eigenschappen in het geval van een sjabloonmelding, en een set kopteksten, die indeling (native platform of sjabloon) en platformspecifieke eigenschappen bevat (zoals de vervaldatumvan Apple en WNS kopteksten).

Raadpleeg de [API's van De REST van de meldingshubs](https://msdn.microsoft.com/library/dn495827.aspx) en de indelingen van de specifieke meldingsplatforms voor alle beschikbare opties.

Gewapend met deze klasse, kunnen we nu schrijven `NotificationHub` van de send notification methoden binnen van de klasse:

    ```php
    public function sendNotification($notification, $tagsOrTagExpression="") {
        if (is_array($tagsOrTagExpression)) {
            $tagExpression = implode(" || ", $tagsOrTagExpression);
        } else {
            $tagExpression = $tagsOrTagExpression;
        }

        # build uri
        $uri = $this->endpoint . $this->hubPath . "/messages" . NotificationHub::API_VERSION;
        $ch = curl_init($uri);

        if (in_array($notification->format, ["template", "apple", "fcm"])) {
            $contentType = "application/json";
        } else {
            $contentType = "application/xml";
        }

        $token = $this->generateSasToken($uri);

        $headers = [
            'Authorization: '.$token,
            'Content-Type: '.$contentType,
            'ServiceBusNotification-Format: '.$notification->format
        ];

        if ("" !== $tagExpression) {
            $headers[] = 'ServiceBusNotification-Tags: '.$tagExpression;
        }

        # add headers for other platforms
        if (is_array($notification->headers)) {
            $headers = array_merge($headers, $notification->headers);
        }

        curl_setopt_array($ch, array(
            CURLOPT_POST => TRUE,
            CURLOPT_RETURNTRANSFER => TRUE,
            CURLOPT_SSL_VERIFYPEER => FALSE,
            CURLOPT_HTTPHEADER => $headers,
            CURLOPT_POSTFIELDS => $notification->payload
        ));

        // Send the request
        $response = curl_exec($ch);

        // Check for errors
        if($response === FALSE){
            throw new Exception(curl_error($ch));
        }

        $info = curl_getinfo($ch);

        if ($info['http_code'] <> 201) {
            throw new Exception('Error sending notification: '. $info['http_code'] . ' msg: ' . $response);
        }
    } 
    ```

De bovenstaande methoden sturen een HTTP `/messages` POST-verzoek naar het eindpunt van uw meldingshub, met de juiste hoofdtekst en kopteksten om de melding te verzenden.

## <a name="complete-the-tutorial"></a><a name="complete-tutorial"></a>De zelfstudie voltooien

Nu u de zelfstudie Aan de slag voltooien door de melding vanaf een PHP-backend te verzenden.

Initialiseer uw Client Meldinghubs (vervang de verbindingstekenreeks en de naam van de hub zoals geïnstrueerd in de [zelfstudie Aan]de slag):

    ```php
    $hub = new NotificationHub("connection string", "hubname");
    ```

Voeg vervolgens de verzendcode toe, afhankelijk van uw mobiele doelplatform.

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows Store en Windows Phone 8.1 (niet-Zilverlicht)

    ```php
    $toast = '<toast><visual><binding template="ToastText01"><text id="1">Hello from PHP!</text></binding></visual></toast>';
    $notification = new Notification("windows", $toast);
    $notification->headers[] = 'X-WNS-Type: wns/toast';
    $hub->sendNotification($notification, null);
    ```

### <a name="ios"></a>iOS

    ```php
    $alert = '{"aps":{"alert":"Hello from PHP!"}}';
    $notification = new Notification("apple", $alert);
    $hub->sendNotification($notification, null);
    ```

### <a name="android"></a>Android

    ```php
    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("fcm", $message);
    $hub->sendNotification($notification, null);
    ```

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8.0 en 8.1 Zilverlicht

    ```php
    $toast = '<?xml version="1.0" encoding="utf-8"?>' .
                '<wp:Notification xmlns:wp="WPNotification">' .
                   '<wp:Toast>' .
                        '<wp:Text1>Hello from PHP!</wp:Text1>' .
                   '</wp:Toast> ' .
                '</wp:Notification>';
    $notification = new Notification("windowsphone", $toast);
    $notification->headers[] = 'X-WindowsPhone-Target : toast';
    $notification->headers[] = 'X-NotificationClass : 2';
    $hub->sendNotification($notification, null);
    ```

### <a name="kindle-fire"></a>Kindle Fire

    ```php
    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("adm", $message);
    $hub->sendNotification($notification, null);
    ```

Als u uw PHP-code uitvoert, wordt nu een melding weergegeven op uw doelapparaat.

## <a name="next-steps"></a>Volgende stappen

In dit onderwerp hebben we laten zien hoe je een eenvoudige Java REST-client voor Notification Hubs maken. Hier kunt u het volgende doen:

* Download de volledige [PHP REST wrapper sample,]die alle bovenstaande code bevat.
* Blijf meer te weten komen over de functie Tagging-functie voor meldingenhubs in de [zelfstudie Breaking News]
* Meer informatie over pushmeldingen naar individuele gebruikers in [Gebruikers zelfstudie op de hoogte brengen]

Zie voor meer informatie ook het [PHP Developer Center.](https://azure.microsoft.com/develop/php/)

[PHP REST wrapper sample]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-php
[Zelfstudie Aan de slag]: https://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
