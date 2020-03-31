---
title: Veelgestelde vragen over Azure Notification Hubs (veelgestelde vragen) | Microsoft Documenten
description: Veelgestelde vragen over het ontwerpen en implementeren van oplossingen op Azure Notification Hubs.
services: notification-hubs
documentationcenter: mobile
author: sethmanheim
manager: femila
editor: jwargo
keywords: pushnotificaties, pushmeldingen, iOS pushmeldingen, android push meldingen, ios push, android push
ms.assetid: 7b385713-ef3b-4f01-8b1f-ffe3690bbd40
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 11/13/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 11/13/2019
ms.openlocfilehash: 3212520f37d33a2d8fb1b071506f688b9f75f15c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263826"
---
# <a name="push-notifications-with-azure-notification-hubs-frequently-asked-questions"></a>Pushmeldingen met Azure Notification Hubs: veelgestelde vragen

## <a name="general"></a>Algemeen

### <a name="what-is-the-resource-structure-of-notification-hubs"></a>Wat is de resourcestructuur van Notification Hubs?

Azure Notification Hubs heeft twee resourceniveaus: hubs en naamruimten. Een hub is een enkele pushbron die de push-informatie van één app op cross-platform kan bevatten. Een namespace is een verzameling hubs in één regio. Aanbevolen toewijzing komt overeen met één naamruimte met één app. Binnen een naamruimte u een productiehub hebben die werkt met uw productie-app, een testhub die werkt met uw test-app, enzovoort.

### <a name="what-is-the-price-model-for-notification-hubs"></a>Wat is het prijsmodel voor Notification Hubs?

De laatste prijsdetails zijn te vinden op de pagina [Notificatie Hubs Prijzen.] Meldingshubs worden gefactureerd op naamruimteniveau. (Zie 'Wat is de resourcestructuur van meldingshubs?' voor de definitie van een naamruimte? Notification Hubs biedt drie lagen:

* **Gratis**: Deze laag is een goed uitgangspunt voor het verkennen van pushmogelijkheden. Het wordt niet aanbevolen voor productie-apps. U krijgt 500 apparaten en 1 miljoen pushes inbegrepen per naamruimte per maand, zonder SLA-garantie (Service Level Agreement).
* **Basis:** Deze laag (of de standaardlaag) wordt aanbevolen voor kleinere productie-apps. Je krijgt 200.000 apparaten en 10 miljoen pushes opgenomen per naamruimte per maand als een basislijn.
* **Standaard:** Deze laag wordt aanbevolen voor middelgrote tot grote productie-apps. Je krijgt 10 miljoen apparaten en 10 miljoen pushes opgenomen per naamruimte per maand als een basislijn. Bevat uitgebreide telemetrie (aanvullende gegevens over de pushstatus aanwezig).

Standaardfuncties:

* **Uitgebreide telemetrie:** U meldingshubs per berichttelemetrie gebruiken om pushverzoeken en feedback van het platformmeldingssysteem bij te houden voor foutopsporing.
* **Multi-tenancy**: U werken met referenties van het Platform Notification System op naamruimteniveau. Met deze optie u tenants eenvoudig splitsen in hubs binnen dezelfde naamruimte.
* **Geplande push:** U meldingen plannen die op elk gewenst moment worden verzonden.
* **Bulkbewerkingen**: Hiermee kunnen registraties export/importfunctionaliteit worden uitgeschakeld zoals beschreven in het document [Registraties exporteren/importeren.]

### <a name="what-is-the-notification-hubs-sla"></a>Wat is de SLA van de Meldingshubs?

Voor standaard- en standaardmeldingshubskunnen correct geconfigureerde toepassingen pushmeldingen verzenden of ten minste 99,9 procent van de tijd registratiebeheerbewerkingen uitvoeren. Ga voor meer informatie over de SLA naar de [SLA-pagina van meldingenhubs.](https://azure.microsoft.com/support/legal/sla/notification-hubs/)

> [!NOTE]
> Omdat pushmeldingen afhankelijk zijn van platformmeldingssystemen van derden, zoals de Push Notification Service (APNs) van Apple en de Firebase Cloud Messaging (FCM) van Google, is er geen SLA-garantie voor de levering van deze berichten. Nadat Notification Hubs de batches naar Platform Notification Systems (SLA gegarandeerd) stuurt, is het de verantwoordelijkheid van de Platform Notification Systems om de pushes te leveren (geen SLA gegarandeerd).

### <a name="how-do-i-upgrade-or-downgrade-my-hub-or-namespace-to-a-different-tier"></a>Hoe kan ik mijn hub of naamruimte upgraden of downgraden naar een andere laag?

Ga naar de naamruimten van **[Azure-portal-hubs]** > **Notification Hubs Namespaces** of **meldingshubs**. Selecteer de resource die u wilt bijwerken en ga naar **Prijsniveau**. Let op de volgende vereisten:

* De bijgewerkte prijscategorie is van toepassing op *alle* hubs in de naamruimte waarmee u werkt.
* Als het aantal apparaten de limiet overschrijdt van de laag waartoe u downgrading, moet u apparaten verwijderen voordat u downgraden.

## <a name="design-and-development"></a>Ontwerp en ontwikkeling

### <a name="which-server-side-platforms-do-you-support"></a>Welke server-side platforms ondersteunt u?

Server SDKs zijn beschikbaar voor .NET, Java, Node.js, PHP en Python. Api's voor meldingshubs zijn gebaseerd op REST-interfaces, zodat u rechtstreeks met REST API's werken als u verschillende platforms gebruikt of geen extra afhankelijkheid wilt. Ga voor meer informatie naar de PAGINA [Rest API's van meldingenhubs.]

### <a name="which-client-platforms-do-you-support"></a>Welke clientplatforms ondersteunt u?

Pushmeldingen worden ondersteund voor [iOS,](notification-hubs-ios-apple-push-notification-apns-get-started.md) [Android,](notification-hubs-android-push-notification-google-fcm-get-started.md) [Windows Universal,](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) [Windows Phone,](notification-hubs-windows-mobile-push-notifications-mpns.md) [Android China (via Baidu),](notification-hubs-baidu-china-android-notifications-get-started.md)Xamarin[(iOS](xamarin-notification-hubs-ios-push-notification-apns-get-started.md) en Android en [Safari](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari). Ga voor meer informatie naar de [zelfstudiepagina Van Meldingenhubs Aan de slag.]

### <a name="do-you-support-text-message-email-or-web-notifications"></a>Ondersteunt u sms-berichten, e-mailberichten of webmeldingen?

Notification Hubs stuurt meldingen naar apparaten met mobiele apps. Het biedt geen e-mail of sms-mogelijkheden. Notification Hubs biedt ook geen in-browser push notificatie delivery service out of the box. Klanten kunnen deze functie implementeren met SignalR bovenop de ondersteunde serverplatforms. 

### <a name="how-many-devices-can-i-support-if-i-send-push-notifications-via-notification-hubs"></a>Hoeveel apparaten kan ik ondersteunen als ik pushmeldingen verstuur via Notification Hubs?

Raadpleeg de pagina [Notificatiehubs prijzen] voor meer informatie over het aantal ondersteunde apparaten.

Als u ondersteuning nodig hebt voor meer dan 10 miljoen geregistreerde apparaten, moet u uw apparaten op meerdere hubs verdelen.

### <a name="how-many-push-notifications-can-i-send-out"></a>Hoeveel pushmeldingen kan ik verzenden?

Afhankelijk van de geselecteerde laag worden Azure Notification Hubs automatisch geschaald op basis van het aantal meldingen dat door het systeem stroomt.

> [!NOTE]
> De totale gebruikskosten kunnen stijgen op basis van het aantal verzonden pushmeldingen. Zorg ervoor dat u op de hoogte bent van de laaglimieten die zijn beschreven op de pagina [Notificatiehubs.Sure] that you're aware of the tier limits outlined on the Notification Hubs Pricing page.

Onze klanten gebruiken Notification Hubs om dagelijks miljoenen pushmeldingen te versturen. U hoeft niets speciaals te doen om het bereik van uw pushmeldingen te schalen zolang u Azure Notification Hubs gebruikt.

### <a name="how-long-does-it-take-for-sent-push-notifications-to-reach-my-device"></a>Hoe lang duurt het voordat verzonden pushmeldingen mijn apparaat bereiken?

In een scenario voor normaal gebruik, waarbij de binnenkomende belasting consistent is en zelfs Azure Notification Hubs ten minste *1 miljoen pushmeldingen per minuut*kunnen verwerken. Dit tarief kan variëren afhankelijk van het aantal tags, de aard van de inkomende verzendt, en andere externe factoren.

Tijdens de geschatte levertijd berekent de service de doelen per platform en stuurt berichten naar de Push Notification Service (PNS) op basis van de geregistreerde tags of tagexpressies. Het is de verantwoordelijkheid van de PNS om meldingen naar het apparaat te sturen.

De PNS garandeert geen SLA voor het leveren van meldingen. De meeste pushmeldingen worden echter binnen een paar minuten (meestal binnen 10 minuten) naar doelapparaten geleverd vanaf het moment dat ze naar Meldingshubs worden verzonden. Een paar meldingen kunnen meer tijd in beslag nemen.

> [!NOTE]
> Azure Notification Hubs heeft een beleid om pushmeldingen die niet binnen 30 minuten aan de PNS worden geleverd, te laten vallen. Deze vertraging kan om een aantal redenen gebeuren, maar meestal omdat de PNS uw toepassing throttling.

### <a name="is-there-any-latency-guarantee"></a>Is er een latentiegarantie?

Vanwege de aard van pushmeldingen (ze worden geleverd door een externe, platformspecifieke PNS), is er geen latentiegarantie. Meestal worden de meeste pushmeldingen binnen een paar minuten bezorgd.

### <a name="what-do-i-need-to-consider-when-designing-a-solution-with-namespaces-and-notification-hubs"></a>Waar moet ik rekening mee houden bij het ontwerpen van een oplossing met naamruimten en meldingshubs?

#### <a name="mobile-appenvironment"></a>Mobiele app/omgeving

* Gebruik één meldingshub per mobiele app, per omgeving.
* In een scenario met meerdere tenants moet elke tenant een aparte hub hebben.
* Deel nooit dezelfde meldingshub voor productie- en testomgevingen. Deze praktijk kan problemen veroorzaken bij het verzenden van meldingen. (Apple biedt Sandbox- en Production Push-eindpunten, elk met afzonderlijke referenties.)
* Standaard u testmeldingen naar uw geregistreerde apparaten verzenden via de Azure-portal of de azure-geïntegreerde component in Visual Studio. De drempelwaarde is ingesteld op 10 apparaten die willekeurig zijn geselecteerd uit de registratiegroep.

> [!NOTE]
> Als uw hub oorspronkelijk is geconfigureerd met een Apple Sandbox-certificaat en vervolgens is geconfigureerd om een Apple-productiecertificaat te gebruiken, zijn de oorspronkelijke apparaattokens ongeldig. Ongeldige tokens zorgen ervoor dat pushes mislukken. Scheid uw productie- en testomgevingen en gebruik verschillende hubs voor verschillende omgevingen.

#### <a name="pns-credentials"></a>PNS-referenties

Wanneer een mobiele app is geregistreerd bij de ontwikkelaarsportal van een platform (bijvoorbeeld Apple of Google), worden een app-id en beveiligingstokens verzonden. De app backend biedt deze tokens aan pns van het platform, zodat pushmeldingen naar apparaten kunnen worden verzonden. Beveiligingstokens kunnen in de vorm van certificaten (bijvoorbeeld Apple iOS of Windows Phone) of beveiligingssleutels (bijvoorbeeld Google Android of Windows) zijn. Ze moeten zijn geconfigureerd in meldingshubs. Configuratie wordt meestal gedaan op het niveau van de meldingshub, maar het kan ook op naamruimteniveau in een scenario met meerdere tenant's.

#### <a name="namespaces"></a>Naamruimten

Naamruimten kunnen worden gebruikt voor het groeperen van implementatie. Ze kunnen ook worden gebruikt om alle meldingshubs voor alle tenants van dezelfde app weer te geven in een scenario met meerdere tenants.

#### <a name="geo-distribution"></a>Geo-distributie

Geo-distributie is niet altijd cruciaal in push-notificatiescenario's. Verschillende PNS'en (bijvoorbeeld APNs of FCM) die pushmeldingen leveren aan apparaten worden niet gelijkmatig verdeeld.

Als u een toepassing hebt die wereldwijd wordt gebruikt, u hubs in verschillende naamruimten maken met behulp van de service Meldingenhubs in verschillende Azure-regio's over de hele wereld.

> [!NOTE]
> We raden deze regeling niet aan omdat het uw beheerkosten verhoogt, met name voor registraties. Het mag alleen gebeuren als er een expliciete behoefte is.

### <a name="should-i-do-registrations-from-the-app-backend-or-directly-through-client-devices"></a>Moet ik registraties van de back-end van de app of rechtstreeks via clientapparaten doen?

Registraties van de backend van de app zijn handig wanneer u clients moet verifiëren voordat u de registratie maakt. Ze zijn ook handig als u tags hebt die moeten worden gemaakt of gewijzigd door de back-end van de app op basis van app-logica. Ga voor meer informatie naar de [backendregistratierichtlijnen] en [backendregistratierichtlijnen 2] pagina's.

### <a name="what-is-the-push-notification-delivery-security-model"></a>Wat is het beveiligingsmodel voor het leveren van pushmeldingen?

Azure Notification Hubs maakt gebruik van een beveiligingsmodel op basis van [gedeelde toegangsclients.](../storage/common/storage-dotnet-shared-access-signature-part-1.md) U de tokens voor gedeelde toegangshandtekeningen gebruiken op het niveau van de hoofdnaamruimte of op het niveau van de gedetailleerde meldingshub. Handtekeningtokens voor gedeelde toegang kunnen worden ingesteld om verschillende autorisatieregels te volgen, bijvoorbeeld om berichtmachtigingen te verzenden of om te luisteren naar meldingsmachtigingen. Zie het [beveiligingsmodeldocument Van De hubs voor meldingen] voor meer informatie.

### <a name="how-should-i-handle-sensitive-payload-in-push-notifications"></a>Hoe moet ik omgaan met gevoelige payload in pushmeldingen?

Alle meldingen worden geleverd aan doelapparaten door de PNS van het platform. Wanneer een melding wordt verzonden naar Azure Notification Hubs, wordt deze verwerkt en doorgegeven aan de desbetreffende PNS.

Alle verbindingen, van de afzender tot de Azure Notification Hubs tot de PNS, gebruiken HTTPS.

> [!NOTE]
> Azure Notification Hubs registreert niet de payload van berichten.

Om gevoelige payloads te verzenden, raden we u aan een Secure Push-patroon te gebruiken. De afzender levert een ping-melding met een bericht-id aan het apparaat zonder de gevoelige payload. Wanneer de app op het apparaat de payload ontvangt, roept de app rechtstreeks een beveiligde API aan om de berichtgegevens op te halen. Ga naar de [zelfstudiepagina van Notification Hubs Secure Push] voor een handleiding over het implementeren van dit patroon.

## <a name="operations"></a>Bewerkingen

### <a name="what-support-is-provided-for-disaster-recovery"></a>Welke ondersteuning wordt geboden voor herstel na noodgevallen?

We bieden dekking voor het herstel van metadata-nood (de naam Van Notification Hubs, de verbindingstekenreeks en andere kritieke informatie). Wanneer een scenario voor noodherstel wordt geactiveerd, zijn registratiegegevens het *enige segment* van de infrastructuur van Notification Hubs dat verloren gaat. U moet een oplossing implementeren om deze gegevens na het herstel opnieuw in uw nieuwe hub te verwerken:

1. Maak een secundaire meldingenhub in een ander datacenter. We raden u aan er vanaf het begin een te maken om u te beschermen tegen een gebeurtenis voor noodherstel die van invloed kan zijn op uw beheermogelijkheden. U er ook een maken op het moment van de ramprecovery-gebeurtenis.

2. Vul de secundaire meldingshub in met de registraties van uw primaire meldingshub. We raden niet aan om te proberen registraties op beide hubs te behouden en ze synchroon te houden als er registraties binnenkomen. Deze praktijk werkt niet goed vanwege de inherente tendens van registraties te vervallen aan de PNS kant. Notification Hubs ruimt ze op omdat het PNS-feedback ontvangt over verlopen of ongeldige registraties.  

We hebben twee aanbevelingen voor app-backends:

* Gebruik een back-end van de app die een bepaalde set registraties aan het einde onderhoudt. Het kan vervolgens een bulk insert uitvoeren in de secundaire melding hub.
* Gebruik een back-end voor apps die regelmatig registraties van de primaire meldingshub als back-up ontvangt. Het kan vervolgens een bulk insert uitvoeren in de secundaire melding hub.

> [!NOTE]
> Registraties Export/import-functionaliteit die beschikbaar is in de standaardlaag wordt beschreven in het document [Registraties exporteren/importeren.]

Als u geen back-end hebt, voert de app een nieuwe registratie uit in de secundaire meldingshub wanneer de app wordt gestart op doelapparaten. Uiteindelijk zal de secundaire meldingshub alle actieve apparaten hebben geregistreerd.

Er zal een periode zijn waarin apparaten met ongeopende apps geen meldingen ontvangen.

### <a name="is-all-of-my-data-stored-in-encrypted-form"></a>Zijn al mijn gegevens in versleutelde vorm opgeslagen?

Azure Notification Hubs versleutelt alle klantgegevens in rust, met uitzondering van registratietags. Om deze reden moet u geen persoonlijke of vertrouwelijke gegevens opslaan met behulp van tags.

### <a name="is-there-audit-log-capability"></a>Is er controle log vermogen?

Ja. Alle beheerbewerkingen van Meldingenhubs werken het Azure-activiteitenlogboek bij waaraan wordt weergegeven in de [Azure-portal.] Het Azure-activiteitenlogboek biedt inzicht in de bewerkingen die worden uitgevoerd op resources in uw abonnementen. Met behulp van het activiteitenlogboek u bepalen wat, wie en wanneer voor schrijfbewerkingen (PUT, POST, DELETE) die zijn gemaakt voor de bronnen in uw abonnement. U ook de status van de bewerkingen en andere relevante eigenschappen begrijpen. Echter. het activiteitenlogboek bevat geen leesbewerking (GET).

## <a name="monitoring-and-troubleshooting"></a>Monitoring en probleemoplossing

### <a name="what-troubleshooting-capabilities-are-available"></a>Welke probleemoplossingsmogelijkheden zijn beschikbaar?

Azure Notification Hubs biedt verschillende functies voor het oplossen van problemen, met name voor het meest voorkomende scenario van gevallen van vallende meldingen. Zie de whitepaper [voor het oplossen van problemen met meldingen hubs] voor meer informatie.

### <a name="what-telemetry-features-are-available"></a>Welke telemetriefuncties zijn beschikbaar?

Azure Notification Hubs maakt het mogelijk telemetriegegevens in de [Azure-portal]te bekijken. Details van de statistieken zijn beschikbaar op de pagina [Statistieken van meldingenhubs.]

U ook programmatisch toegang krijgen tot statistieken. Raadpleeg voor meer informatie de volgende artikelen:

- [Azure Monitor-statistieken ophalen met .NET](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/). In dit voorbeeld worden de gebruikersnaam en het wachtwoord gebruikt. Als u een certificaat wilt gebruiken, overbelast u de Methode FromServicePrincipal om een certificaat te verstrekken zoals in [dit voorbeeld](https://github.com/Azure/azure-libraries-for-net/blob/master/src/ResourceManagement/ResourceManager/Authentication/AzureCredentialsFactory.cs)wordt weergegeven. 
- [Statistieken en activiteitslogboeken voor een resource opvragen](https://azure.microsoft.com/resources/samples/monitor-dotnet-query-metrics-activitylogs/)
- [Azure Monitoring REST API-walkthrough](../azure-monitor/platform/rest-api-walkthrough.md)

> [!NOTE]
> Succesvolle meldingen betekenen simpelweg dat pushmeldingen zijn geleverd aan de externe PNS (bijvoorbeeld APNs voor iOS en macOS of FCM voor Android-apparaten). Het is de verantwoordelijkheid van de PNS om de meldingen te leveren aan doelapparaten. Doorgaans stelt de PNS leveringsstatistieken niet bloot aan derden.  

[Azure-portal]: https://portal.azure.com
[Prijzen voor meldingenhubs]: https://azure.microsoft.com/pricing/details/notification-hubs/
[Notification Hubs SLA]: https://azure.microsoft.com/support/legal/sla/
[Notification Hubs REST API's]: https://msdn.microsoft.com/library/azure/dn530746.aspx
[Zelfstudies voor meldingenhubs aan de slag]: https://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[Mobile Services Pricing]: https://azure.microsoft.com/pricing/details/mobile-services/
[Richtlijnen voor back-endregistratie]: https://msdn.microsoft.com/library/azure/dn743807.aspx
[Richtlijnen voor back-endregistratie 2]: https://msdn.microsoft.com/library/azure/dn530747.aspx
[Beveiligingsmodel voor meldingshubs]: https://msdn.microsoft.com/library/azure/dn495373.aspx
[Zelfstudie voor Secure Push-beveiligd bericht van Meldingenhubs]: https://azure.microsoft.com/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/
[Probleemoplossing voor meldingenhubs]: https://azure.microsoft.com/documentation/articles/notification-hubs-diagnosing/
[Statistieken over meldingenhubs]: ../azure-monitor/platform/metrics-supported.md#microsoftnotificationhubsnamespacesnotificationhubs
[Registraties exporteren/importeren]: https://docs.microsoft.com/azure/notification-hubs/export-modify-registrations-bulk
[Azure-portal]: https://portal.azure.com
[complete samples]: https://github.com/Azure/azure-notificationhubs-samples
[App Service Pricing]: https://azure.microsoft.com/pricing/details/app-service/
