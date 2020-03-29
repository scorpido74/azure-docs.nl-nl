---
title: Azure-meldingshubs gebruiken met Java
description: Meer informatie over het gebruik van Azure Notification Hubs vanaf een Java-back-end.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 4c3f966d-0158-4a48-b949-9fa3666cb7e4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: java
ms.devlang: java
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: d48973cc7c5ed1fc7ae3f96128d488f3f1df3a05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263860"
---
# <a name="how-to-use-notification-hubs-from-java"></a>Notification Hubs gebruiken vanuit Java

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

In dit onderwerp worden de belangrijkste functies van de nieuwe volledig ondersteunde officiële Azure Notification Hub Java SDK beschreven.
Dit project is een open-source project en u de volledige SDK-code bekijken op [Java SDK.]

In het algemeen hebt u toegang tot alle functies van Notification Hubs vanaf een Java/PHP/Python/Ruby back-end via de Notification Hub REST-interface zoals beschreven in de [API's van de](https://msdn.microsoft.com/library/dn223264.aspx)MSDN-onderwerp Notification Hubs . Deze Java SDK biedt een dunne wrapper over deze REST interfaces in Java.

De SDK ondersteunt momenteel:

* CRUD op meldingshubs
* CRUD op registraties
* Installatiebeheer
* Registraties importeren/exporteren
* Regelmatige verzendingen
* Geplande verzendingen
* Async operaties via Java NIO
* Ondersteunde platforms: APNS (iOS), FCM (Android), WNS (Windows Store-apps), MPNS (Windows Phone), ADM (Amazon Kindle Fire), Baidu (Android zonder Google-services)

## <a name="sdk-usage"></a>SDK-gebruik

### <a name="compile-and-build"></a>Compileren en bouwen

[Maven gebruiken]

Bouwen:

    mvn package

## <a name="code"></a>Code

### <a name="notification-hub-cruds"></a>Cruds van meldingshub

**Een NamespaceManager maken:**

    ```java
    NamespaceManager namespaceManager = new NamespaceManager("connection string")
    ```

**Meldingshub maken:**

    ```java
    NotificationHubDescription hub = new NotificationHubDescription("hubname");
    hub.setWindowsCredential(new WindowsCredential("sid","key"));
    hub = namespaceManager.createNotificationHub(hub);
    ```

 OF

    ```java
    hub = new NotificationHub("connection string", "hubname");
    ```

**Meldingshub ontvangen:**

    ```java
    hub = namespaceManager.getNotificationHub("hubname");
    ```

**Meldingshub bijwerken:**

    ```java
    hub.setMpnsCredential(new MpnsCredential("mpnscert", "mpnskey"));
    hub = namespaceManager.updateNotificationHub(hub);
    ```

**Meldingshub verwijderen:**

    ```java
    namespaceManager.deleteNotificationHub("hubname");
    ```

### <a name="registration-cruds"></a>Registratie CRUDs

**Een Notification Hub-client maken:**

    ```java
    hub = new NotificationHub("connection string", "hubname");
    ```

**Windows-registratie maken:**

    ```java
    WindowsRegistration reg = new WindowsRegistration(new URI(CHANNELURI));
    reg.getTags().add("myTag");
    reg.getTags().add("myOtherTag");
    hub.createRegistration(reg);
    ```

**IOS-registratie maken:**

    ```java
    AppleRegistration reg = new AppleRegistration(DEVICETOKEN);
    reg.getTags().add("myTag");
    reg.getTags().add("myOtherTag");
    hub.createRegistration(reg);
    ```

Op dezelfde manier u registraties maken voor Android (FCM), Windows Phone (MPNS) en Kindle Fire (ADM).

**Sjabloonregistraties maken:**

    ```java
    WindowsTemplateRegistration reg = new WindowsTemplateRegistration(new URI(CHANNELURI), WNSBODYTEMPLATE);
    reg.getHeaders().put("X-WNS-Type", "wns/toast");
    hub.createRegistration(reg);
    ```

**Maak registraties met een registratie-ID + upsert-patroon:**

Verwijdert duplicaten als gevolg van verloren reacties als registratie-geïdentificeerde-geïdentificeerde-geïdentificeerde gegevens op het apparaat worden opgeslagen:

    ```java
    String id = hub.createRegistrationId();
    WindowsRegistration reg = new WindowsRegistration(id, new URI(CHANNELURI));
    hub.upsertRegistration(reg);
    ```

**Registraties bijwerken:**

    ```java
    hub.updateRegistration(reg);
    ```

**Registraties verwijderen:**

    ```java
    hub.deleteRegistration(regid);
    ```

**Queryregistraties:**

* **Ontvang één registratie:**

    ```java
    hub.getRegistration(regid);
    ```

* **Alle registraties in hub:**

    ```java
    hub.getRegistrations();
    ```

* **Registraties met tag:**

    ```java
    hub.getRegistrationsByTag("myTag");
    ```

* **Ontvang registraties per kanaal:**

    ```java
    hub.getRegistrationsByChannel("devicetoken");
    ```

Alle verzamelingsquery's ondersteunen $top- en vervolgtokens.

### <a name="installation-api-usage"></a>Installatie-API-gebruik

Installatie-API is een alternatief mechanisme voor registratiebeheer. In plaats van het onderhouden van meerdere registraties, die niet triviaal zijn en gemakkelijk verkeerd of inefficiënt kunnen worden gedaan, is het nu mogelijk om een enkel installatieobject te gebruiken.

Installatie bevat alles wat u nodig hebt: pushkanaal (apparaattoken), tags, sjablonen, secundaire tegels (voor WNS en APNS). U hoeft de service niet meer te bellen om id's te krijgen - genereer alleen GUID of een andere id, bewaar deze op het apparaat en stuur samen met pushkanaal (device token) naar je backend.

Op de backend, moet je alleen `CreateOrUpdateInstallation`een enkele oproep te doen om ; het is volledig idempotent, dus voel je vrij om opnieuw te proberen indien nodig.

Als voorbeeld voor Amazon Kindle Fire:

    ```java
    Installation installation = new Installation("installation-id", NotificationPlatform.Adm, "adm-push-channel");
    hub.createOrUpdateInstallation(installation);
    ```

Als u het wilt bijwerken:

    ```java
    installation.addTag("foo");
    installation.addTemplate("template1", new InstallationTemplate("{\"data\":{\"key1\":\"$(value1)\"}}","tag-for-template1"));
    installation.addTemplate("template2", new InstallationTemplate("{\"data\":{\"key2\":\"$(value2)\"}}","tag-for-template2"));
    hub.createOrUpdateInstallation(installation);
    ```

Voor geavanceerde scenario's gebruikt u de gedeeltelijke updatemogelijkheid, waarmee alleen bepaalde eigenschappen van het installatieobject kunnen worden gewijzigd. Gedeeltelijke update is subset van JSON-patchbewerkingen die u uitvoeren tegen het object Installatie.

    ```java
    PartialUpdateOperation addChannel = new PartialUpdateOperation(UpdateOperationType.Add, "/pushChannel", "adm-push-channel2");
    PartialUpdateOperation addTag = new PartialUpdateOperation(UpdateOperationType.Add, "/tags", "bar");
    PartialUpdateOperation replaceTemplate = new PartialUpdateOperation(UpdateOperationType.Replace, "/templates/template1", new InstallationTemplate("{\"data\":{\"key3\":\"$(value3)\"}}","tag-for-template1")).toJson());
    hub.patchInstallation("installation-id", addChannel, addTag, replaceTemplate);
    ```

Installatie verwijderen:

    ```java
    hub.deleteInstallation(installation.getInstallationId());
    ```

`CreateOrUpdate`, `Patch`, `Delete` en uiteindelijk `Get`in overeenstemming zijn met . Uw gevraagde bewerking gaat tijdens het gesprek gewoon naar de systeemwachtrij en wordt op de achtergrond uitgevoerd. Get is niet ontworpen voor het hoofdscenario runtime, maar alleen voor foutopsporings- en probleemoplossingsdoeleinden, het wordt strak beperkt door de service.

De stroom verzenden voor installaties is hetzelfde als voor registraties. Om de melding te richten op de specifieke installatie - gebruik gewoon tag "InstallationId:{desired-id}". In dit geval is de code:

    ```java
    Notification n = Notification.createWindowsNotification("WNS body");
    hub.sendNotification(n, "InstallationId:{installation-id}");
    ```

Voor een van de vele sjablonen:

    ```java
    Map<String, String> prop =  new HashMap<String, String>();
    prop.put("value3", "some value");
    Notification n = Notification.createTemplateNotification(prop);
    hub.sendNotification(n, "InstallationId:{installation-id} && tag-for-template1");
    ```

### <a name="schedule-notifications-available-for-standard-tier"></a>Meldingen plannen (beschikbaar voor STANDAARDlaag)

Hetzelfde als regelmatig verzenden, maar met een extra parameter - scheduledTime, die zegt wanneer de melding moet worden geleverd. Service accepteert elk punt van tijd tussen nu + 5 minuten en nu + 7 dagen.

**Een Windows-native melding plannen:**

    ```java
    Calendar c = Calendar.getInstance();
    c.add(Calendar.DATE, 1);
    Notification n = Notification.createWindowsNotification("WNS body");
    hub.scheduleNotification(n, c.getTime());
    ```

### <a name="importexport-available-for-standard-tier"></a>Importeren/exporteren (beschikbaar voor STANDAARDlaag)

Mogelijk moet u bulkbewerking uitvoeren tegen registraties. Meestal is het voor integratie met een ander systeem of een enorme fix om de tags bij te werken. We raden het gebruik van de get/update-stroom af als er duizenden registraties bij betrokken zijn. De import/export-mogelijkheid van het systeem is ontworpen om het scenario te dekken. U geeft toegang tot een blobcontainer onder uw opslagaccount als bron van binnenkomende gegevens en locatie voor uitvoer.

**Een exporttaak indienen:**

    ```java
    NotificationHubJob job = new NotificationHubJob();
    job.setJobType(NotificationHubJobType.ExportRegistrations);
    job.setOutputContainerUri("container uri with SAS signature");
    job = hub.submitNotificationHubJob(job);
    ```

**Een importtaak indienen:**

    ```java
    NotificationHubJob job = new NotificationHubJob();
    job.setJobType(NotificationHubJobType.ImportCreateRegistrations);
    job.setImportFileUri("input file uri with SAS signature");
    job.setOutputContainerUri("container uri with SAS signature");
    job = hub.submitNotificationHubJob(job);
    ```

**Wacht tot een klus geklaard is:**

    ```java
    while(true){
        Thread.sleep(1000);
        job = hub.getNotificationHubJob(job.getJobId());
        if(job.getJobStatus() == NotificationHubJobStatus.Completed)
            break;
    }
    ```

**Alle vacatures krijgen:**

    ```java
    List<NotificationHubJob> jobs = hub.getAllNotificationHubJobs();
    ```

**URI met SAS-handtekening:**

 Deze URL is de URL van een blobbestand of blobcontainer plus een set parameters zoals machtigingen en vervaldatum plus handtekening van al deze dingen die zijn gemaakt met behulp van de SAS-sleutel van het account. Azure Storage Java SDK heeft uitgebreide mogelijkheden, waaronder het maken van deze URI's. Als eenvoudig alternatief, neem `ImportExportE2E` een kijkje op de testklasse (van de GitHub locatie) die fundamentele en compacte implementatie van ondertekening algoritme heeft.

### <a name="send-notifications"></a>Meldingen verzenden

Het object Notification is gewoon een hoofdtekst met kopteksten, sommige hulpprogrammamethoden helpen bij het bouwen van de objecten voor native en sjabloonmeldingen.

* **Windows Store en Windows Phone 8.1 (niet-Zilverlicht)**

    ```java
    String toast = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello from Java!</text></binding></visual></toast>";
    Notification n = Notification.createWindowsNotification(toast);
    hub.sendNotification(n);
    ```

* **iOS**

    ```java
    String alert = "{\"aps\":{\"alert\":\"Hello from Java!\"}}";
    Notification n = Notification.createAppleNotification(alert);
    hub.sendNotification(n);
    ```

* **Android**

    ```java
    String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
    Notification n = Notification.createFcmNotification(message);
    hub.sendNotification(n);
    ```

* **Windows Phone 8.0 en 8.1 Zilverlicht**

    ```java
    String toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                    "<wp:Toast>" +
                        "<wp:Text1>Hello from Java!</wp:Text1>" +
                    "</wp:Toast> " +
                "</wp:Notification>";
    Notification n = Notification.createMpnsNotification(toast);
    hub.sendNotification(n);
    ```

* **Kindle Fire**

    ```java
    String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
    Notification n = Notification.createAdmNotification(message);
    hub.sendNotification(n);
    ```

* **Verzenden naar tags**
  
    ```java
    Set<String> tags = new HashSet<String>();
    tags.add("boo");
    tags.add("foo");
    hub.sendNotification(n, tags);
    ```

* **Verzenden naar tagexpressie**

    ```java
    hub.sendNotification(n, "foo && ! bar");
    ```

* **Sjabloonmelding verzenden**

    ```java
    Map<String, String> prop =  new HashMap<String, String>();
    prop.put("prop1", "v1");
    prop.put("prop2", "v2");
    Notification n = Notification.createTemplateNotification(prop);
    hub.sendNotification(n);
    ```

Als u uw Java-code uitvoert, moet er nu een melding worden weergegeven op uw doelapparaat.

## <a name="next-steps"></a><a name="next-steps"></a>Volgende stappen

In dit onderwerp is te zien hoe u een eenvoudige Java REST-client voor Notification Hubs maken. Hier kunt u het volgende doen:

* Download de volledige [Java SDK,]die de volledige SDK-code bevat.
* Speel met de samples:
  * [Aan de slag met meldingshubs]
  * [Stuur het laatste nieuws]
  * [Lokaal nieuws verzenden]
  * [Meldingen verzenden naar geverifieerde gebruikers]
  * [Cross-platform meldingen verzenden naar geverifieerde gebruikers]

[Java SDK]: https://github.com/Azure/azure-notificationhubs-java-backend
[Get started tutorial]: notification-hubs-ios-apple-push-notification-apns-get-started.md
[Aan de slag met meldingshubs]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Stuur het laatste nieuws]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Lokaal nieuws verzenden]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Meldingen verzenden naar geverifieerde gebruikers]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Cross-platform meldingen verzenden naar geverifieerde gebruikers]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Maven]: https://maven.apache.org/
