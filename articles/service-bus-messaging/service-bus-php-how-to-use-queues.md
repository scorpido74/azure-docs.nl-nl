---
title: Azure Service Bus wachtrijen gebruiken met PHP
description: In deze zelf studie leert u hoe u PHP-toepassingen kunt maken om berichten te verzenden naar en berichten van een Service Bus wachtrij te ontvangen.
services: service-bus-messaging
documentationcenter: php
author: axisc
manager: timlt
editor: spelluru
ms.assetid: e29c829b-44c5-4350-8f2e-39e0c380a9f2
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: quickstart
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: fcb735d81cac587c75a133ad582f2a839551dcfa
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "76760688"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-php"></a>Snelstartgids: Service Bus wachtrijen gebruiken met PHP
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

In deze zelf studie leert u hoe u PHP-toepassingen kunt maken om berichten te verzenden naar en berichten van een Service Bus wachtrij te ontvangen. 

## <a name="prerequisites"></a>Vereisten
1. Een Azure-abonnement. U hebt een Azure-account nodig om deze zelfstudie te voltooien. U kunt de [voor delen](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) van uw MSDN-abonnee activeren of zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Als u geen wachtrij hebt om mee te werken, voert u de stappen in het [Azure Portal gebruik uit om een service bus wachtrij](service-bus-quickstart-portal.md) artikel te maken om een wachtrij te maken.
    1. Lees het kort **overzicht** van service bus- **wacht rijen**. 
    2. Maak een Service Bus **naam ruimte**. 
    3. Haal de **Connection String**op. 

        > [!NOTE]
        > In deze zelf studie maakt u een **wachtrij** in de service bus naam ruimte met behulp van PHP. 
3. [Azure-SDK voor PHP](https://github.com/Azure/azure-sdk-for-php)

## <a name="create-a-php-application"></a>Een PHP-toepassing maken
De enige vereiste voor het maken van een PHP-toepassing die toegang heeft tot de Azure-Blob service, is het verwijzen van klassen in de [Azure SDK voor php](https://github.com/Azure/azure-sdk-for-php) vanuit uw code. U kunt alle ontwikkel hulpprogramma's gebruiken om uw toepassing of Klad blok te maken.

> [!NOTE]
> Voor de PHP-installatie moet ook de [openssl-extensie](https://php.net/openssl) zijn geïnstalleerd en ingeschakeld.

In deze hand leiding maakt u gebruik van service functies, die vanuit een PHP-toepassing lokaal kunnen worden aangeroepen, of in code die wordt uitgevoerd binnen een Azure-webrole,-werk functie of-website.

## <a name="get-the-azure-client-libraries"></a>De Azure-client bibliotheken ophalen
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Uw toepassing configureren voor het gebruik van Service Bus
Ga als volgt te werk om de Service Bus-wachtrij-Api's te gebruiken:

1. Verwijs naar het autoloader-bestand met behulp van de [require_once][require_once] -instructie.
2. Verwijs naar alle klassen die u kunt gebruiken.

In het volgende voor beeld ziet u hoe u het autoloader-bestand `ServicesBuilder` opneemt en naar de klasse verwijst.

> [!NOTE]
> In dit voor beeld (en andere voor beelden in dit artikel) wordt ervan uitgegaan dat u de PHP-client bibliotheken voor Azure hebt geïnstalleerd via Composer. Als u de bibliotheken hand matig of als een peer-pakket hebt geïnstalleerd, moet u verwijzen naar het autoloader-bestand **WindowsAzure. php** .
> 
> 

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

In de onderstaande voor beelden wordt `require_once` de instructie altijd weer gegeven, maar alleen de klassen waarnaar het voor beeld moet worden uitgevoerd, worden verwezen.

## <a name="set-up-a-service-bus-connection"></a>Een Service Bus verbinding instellen
Als u een Service Bus-client wilt instantiëren, moet u eerst een geldig connection string in deze indeling hebben:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Waar `Endpoint` bevindt zich doorgaans `[yourNamespace].servicebus.windows.net`in de indeling.

Als u een Azure-serviceclient wilt maken, moet u `ServicesBuilder` de-klasse gebruiken. U kunt het volgende doen:

* Geef het connection string rechtstreeks door aan het bestand.
* Gebruik de **CloudConfigurationManager (CCM)** om meerdere externe bronnen voor de Connection String te controleren:
  * Standaard wordt er ondersteuning geboden voor één externe bron-omgevings variabelen
  * U kunt nieuwe bronnen toevoegen door de `ConnectionStringSource` klasse uit te breiden

In de voorbeelden die hier worden beschreven, wordt de verbindingsreeks rechtstreeks doorgegeven.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-queue"></a>Een wachtrij maken
U kunt beheer bewerkingen uitvoeren voor Service Bus wachtrijen via de `ServiceBusRestProxy` -klasse. Een `ServiceBusRestProxy` object wordt samengesteld via de `ServicesBuilder::createServiceBusService` fabrieks methode met een geschikte Connection String die de token machtigingen voor het beheer van de sleutel bevat.

In het volgende voor beeld ziet u hoe `ServiceBusRestProxy` u een `ServiceBusRestProxy->createQueue` aanroepen maakt om een `myqueue` wachtrij te `MySBNamespace` maken met de naam in een service naam ruimte:

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\QueueInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    $queueInfo = new QueueInfo("myqueue");

    // Create queue.
    $serviceBusRestProxy->createQueue($queueInfo);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [!NOTE]
> U kunt de `listQueues` methode voor `ServiceBusRestProxy` objecten gebruiken om te controleren of een wachtrij met een opgegeven naam al bestaat in een naam ruimte.
> 
> 

## <a name="send-messages-to-a-queue"></a>Berichten verzenden naar een wachtrij
Als u een bericht wilt verzenden naar een Service Bus wachtrij, wordt de `ServiceBusRestProxy->sendQueueMessage` methode door uw toepassing aangeroepen. De volgende code laat zien hoe u een bericht verzendt naar `myqueue` de wachtrij die eerder in `MySBNamespace` de service naam ruimte is gemaakt.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\BrokeredMessage;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message");

    // Send message.
    $serviceBusRestProxy->sendQueueMessage("myqueue", $message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Berichten die worden verzonden naar (en ontvangen van) Service Bus-wacht rijen zijn exemplaren van de klasse [BrokeredMessage][BrokeredMessage] . [BrokeredMessage][BrokeredMessage] -objecten hebben een set standaard methoden en eigenschappen die worden gebruikt voor het opslaan van aangepaste toepassingsspecifieke eigenschappen en een hoofd tekst van wille keurige toepassings gegevens.

Service Bus-wachtrijen ondersteunen een maximale berichtgrootte van 256 kB in de [Standard-laag](service-bus-premium-messaging.md) en 1 MB in de [Premium-laag](service-bus-premium-messaging.md). De koptekst, die de standaard- en aangepaste toepassingseigenschappen bevat, kan maximaal 64 kB groot zijn. Er is geen limiet voor het aantal berichten in een wachtrij, maar er is een limiet voor de totale grootte van de berichten in een wachtrij. Deze bovengrens voor de wachtrij grootte is 5 GB.

## <a name="receive-messages-from-a-queue"></a>Berichten van een wachtrij ontvangen

De beste manier om berichten uit een wachtrij te ontvangen, is door `ServiceBusRestProxy->receiveQueueMessage` een methode te gebruiken. Berichten kunnen in twee verschillende modi worden ontvangen: [*ReceiveAndDelete*](/dotnet/api/microsoft.servicebus.messaging.receivemode) en [*PeekLock*](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock). **PeekLock** is de standaardmodus.

Wanneer u de [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) -modus gebruikt, ontvangt u een eenmalige bewerking. dat wil zeggen, wanneer Service Bus een lees aanvraag voor een bericht in een wachtrij ontvangt, wordt het bericht gemarkeerd als verbruikt en wordt het naar de toepassing geretourneerd. De [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode)-modus is het eenvoudigste model en werkt het beste voor scenario's waarin een toepassing het niet verwerken van een bericht bij een fout kan tolereren. Neem bijvoorbeeld een scenario waarin de consument de ontvangstaanvraag uitgeeft en het systeem vervolgens vastloopt voordat de aanvraag wordt verwerkt. Omdat Service Bus het bericht als verbruikt heeft gemarkeerd, wordt het bericht dat voor het vastlopen is verbruikt, overgeslagen wanneer de toepassing opnieuw wordt gestart en opnieuw bezig is met het uitvoeren van berichten.

In de standaard [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) -modus wordt een bericht ontvangen van een bewerking met twee fasen, waardoor het mogelijk is om toepassingen te ondersteunen die geen ontbrekende berichten kunnen verdragen. Wanneer Service Bus een aanvraag ontvangt, wordt het volgende bericht weer gegeven dat moet worden gebruikt, wordt dit vergrendeld om te voor komen dat andere gebruikers het ontvangen, en wordt het vervolgens naar de toepassing geretourneerd. Nadat de toepassing klaar is met de verwerking van het bericht (of het op betrouw bare wijze opslaat voor toekomstige verwerking), wordt de tweede fase van het ontvangst proces voltooid `ServiceBusRestProxy->deleteMessage`door het ontvangen bericht door te geven aan. Als Service Bus de `deleteMessage` oproep ziet, wordt het bericht gemarkeerd als verbruikt en wordt het uit de wachtrij verwijderd.

In het volgende voor beeld ziet u hoe u een bericht ontvangt en verwerkt met behulp van de [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) -modus (de standaard modus).

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    // Set the receive mode to PeekLock (default is ReceiveAndDelete).
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();

    // Receive message.
    $message = $serviceBusRestProxy->receiveQueueMessage("myqueue", $options);
    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";

    /*---------------------------
        Process message here.
    ----------------------------*/

    // Delete message. Not necessary if peek lock is not set.
    echo "Message deleted.<br />";
    $serviceBusRestProxy->deleteMessage($message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Het vastlopen van de toepassing en onleesbare berichten afhandelen

Service Bus biedt functionaliteit om netjes te herstellen bij fouten in uw toepassing of problemen bij het verwerken van een bericht. Als een ontvanger toepassing het bericht om de een of andere reden niet kan verwerken, kan de `unlockMessage` methode op het ontvangen bericht worden aangeroepen (in plaats `deleteMessage` van de methode). Dit leidt ertoe dat Service Bus het bericht in de wachtrij ontgrendelt en het beschikbaar maakt om opnieuw te worden ontvangen, ofwel door dezelfde verbruiks toepassing of door een andere consumerende toepassing.

Er is ook een time-out gekoppeld aan een bericht dat in de wachtrij is vergrendeld. als de toepassing het bericht niet kan verwerken voordat de time-out van de vergren deling verloopt (bijvoorbeeld als de toepassing vastloopt), wordt het bericht automatisch door Service Bus ontgrendeld en wordt het beschikbaar gemaakt om opnieuw te worden ontvangen.

In het geval dat de toepassing vastloopt na het verwerken van het bericht `deleteMessage` , maar voordat de aanvraag is verzonden, wordt het bericht opnieuw aan de toepassing bezorgd wanneer het opnieuw wordt gestart. Dit wordt vaak *ten minste eenmaal* verwerken genoemd; dat wil zeggen dat elk bericht ten minste één keer wordt verwerkt, maar in bepaalde situaties kan hetzelfde bericht opnieuw worden bezorgd. Als het scenario dubbele verwerking niet kan verdragen, wordt het aanbevolen om aanvullende logica toe te voegen aan toepassingen voor het afhandelen van dubbele bericht bezorging. Dit wordt vaak bereikt met de `getMessageId` methode van het bericht, dat constant blijft tijdens bezorgings pogingen.

> [!NOTE]
> U kunt Service Bus-resources beheren met [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Met de Service Bus Explorer kunnen gebruikers verbinding maken met een Service Bus naam ruimte en de Messa ging-entiteiten op een eenvoudige manier beheren. Het hulp programma biedt geavanceerde functies zoals de functionaliteit voor importeren/exporteren of de mogelijkheid om onderwerp, wacht rijen, abonnementen, relay-Services, Notification hubs en Events hubs te testen. 

## <a name="next-steps"></a>Volgende stappen
Nu u de basis principes van Service Bus wacht rijen hebt geleerd, raadpleegt u [wacht rijen, onderwerpen en abonnementen][Queues, topics, and subscriptions] voor meer informatie.

Ga naar het [PHP-ontwikkelaars centrum](https://azure.microsoft.com/develop/php/)voor meer informatie.

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[require_once]: https://php.net/require_once


