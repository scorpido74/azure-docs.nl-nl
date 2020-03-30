---
title: Azure Service Bus-wachtrijen gebruiken met PHP
description: In deze zelfstudie leert u hoe u PHP-toepassingen maakt om berichten naar berichten te verzenden en te ontvangen vanuit een wachtrij voor servicebussen.
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76760688"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-php"></a>Snelstart: Wachtrijen voor servicebussen gebruiken met PHP
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

In deze zelfstudie leert u hoe u PHP-toepassingen maakt om berichten naar berichten te verzenden en te ontvangen vanuit een wachtrij voor servicebussen. 

## <a name="prerequisites"></a>Vereisten
1. Een Azure-abonnement. U hebt een Azure-account nodig om deze zelfstudie te voltooien. U uw [MSDN-abonneevoordelen](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) activeren of u aanmelden voor een [gratis account.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
2. Als u geen wachtrij hebt om mee te werken, voert u stappen uit in de [Azure-portal gebruiken om een wachtrijartikel voor servicebus te maken](service-bus-quickstart-portal.md) om een wachtrij te maken.
    1. Lees het snelle **overzicht** van wachtrijen voor **servicebussen.** 
    2. Een **naamruimte**voor servicebus maken . 
    3. Haal de **verbindingstekenreeks**. 

        > [!NOTE]
        > U maakt een **wachtrij** in de naamruimte servicebus met behulp van PHP in deze zelfstudie. 
3. [Azure-SDK voor PHP](https://github.com/Azure/azure-sdk-for-php)

## <a name="create-a-php-application"></a>Een PHP-toepassing maken
De enige vereiste voor het maken van een PHP-toepassing die toegang heeft tot de Azure Blob-service, is de verwijzing naar klassen in de [Azure SDK voor PHP](https://github.com/Azure/azure-sdk-for-php) vanuit uw code. U alle ontwikkeltools gebruiken om uw toepassing of Kladblok te maken.

> [!NOTE]
> Uw PHP-installatie moet ook de [OpenSSL-extensie](https://php.net/openssl) hebben geïnstalleerd en ingeschakeld.

In deze handleiding gebruikt u servicefuncties, die lokaal vanuit een PHP-toepassing kunnen worden aangeroepen of in code die wordt uitgevoerd binnen een Azure-webrol, werknemersrol of website.

## <a name="get-the-azure-client-libraries"></a>De Azure-clientbibliotheken oppakken
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Uw toepassing configureren om Service Bus te gebruiken
Ga als volgt te werk om de API's voor de wachtrij servicebus te gebruiken:

1. Verwijs naar het autoloaderbestand met de [require_once-instructie.][require_once]
2. Verwijs naar alle klassen die u zou kunnen gebruiken.

In het volgende voorbeeld ziet u hoe `ServicesBuilder` u het autoloaderbestand opneemt en naar de klasse verwijst.

> [!NOTE]
> In dit voorbeeld (en andere voorbeelden in dit artikel) wordt ervan uitgegaan dat u de PHP-clientbibliotheken voor Azure via Composer hebt geïnstalleerd. Als u de bibliotheken handmatig of als EEN PEAR-pakket hebt geïnstalleerd, moet u verwijzen naar het **windowsAzure.php-autoloaderbestand.**
> 
> 

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

In de onderstaande `require_once` voorbeelden wordt de instructie altijd weergegeven, maar alleen naar de klassen die nodig zijn voor het voorbeeld om uit te voeren, wordt verwezen.

## <a name="set-up-a-service-bus-connection"></a>Een ServiceBus-verbinding instellen
Als u een Service Bus-client wilt instantiëren, moet u eerst een geldige verbindingstekenreeks in deze indeling hebben:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Waar `Endpoint` is meestal van `[yourNamespace].servicebus.windows.net`het formaat .

Als u een Azure-serviceclient `ServicesBuilder` wilt maken, moet u de klasse gebruiken. U kunt:

* Geef de verbindingstekenreeks er rechtstreeks aan door.
* Gebruik de **CCM (CloudConfigurationManager)** om meerdere externe bronnen voor de verbindingstekenreeks te controleren:
  * Standaard wordt het geleverd met ondersteuning voor een externe bron - omgevingsvariabelen
  * U nieuwe bronnen toevoegen `ConnectionStringSource` door de klasse uit te breiden

In de voorbeelden die hier worden beschreven, wordt de verbindingsreeks rechtstreeks doorgegeven.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-queue"></a>Een wachtrij maken
U beheerbewerkingen uitvoeren voor `ServiceBusRestProxy` wachtrijen voor servicebus via de klasse. Een `ServiceBusRestProxy` object wordt gebouwd `ServicesBuilder::createServiceBusService` via de fabrieksmethode met een geschikte verbindingstekenreeks die de tokenmachtigingen inkapselt om het te beheren.

In het volgende voorbeeld ziet u `ServiceBusRestProxy` hoe `ServiceBusRestProxy->createQueue` u een `myqueue` a-a-a-oproep instantiëren en aanroepen om een wachtrij te maken met de naamruimte van een `MySBNamespace` service:

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
> U `listQueues` de `ServiceBusRestProxy` methode voor objecten gebruiken om te controleren of er al een wachtrij met een opgegeven naam bestaat binnen een naamruimte.
> 
> 

## <a name="send-messages-to-a-queue"></a>Berichten verzenden naar een wachtrij
Als u een bericht naar een wachtrij `ServiceBusRestProxy->sendQueueMessage` voor servicebussen wilt verzenden, roept uw toepassing de methode op. In de volgende code ziet u `myqueue` hoe u `MySBNamespace` een bericht verzendt naar de wachtrij die eerder is gemaakt in de servicenaamruimte.

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

Berichten die naar wachtrijen van (en ontvangen van) ServiceBus worden verzonden, zijn exemplaren van de klasse [BrokeredMessage.][BrokeredMessage] [BrokeredMessage-objecten][BrokeredMessage] hebben een set standaardmethoden en -eigenschappen die worden gebruikt om aangepaste toepassingsspecifieke eigenschappen vast te houden en een verzameling willekeurige toepassingsgegevens.

Service Bus-wachtrijen ondersteunen een maximale berichtgrootte van 256 kB in de [Standard-laag](service-bus-premium-messaging.md) en 1 MB in de [Premium-laag](service-bus-premium-messaging.md). De koptekst, die de standaard- en aangepaste toepassingseigenschappen bevat, kan maximaal 64 kB groot zijn. Er is geen limiet voor het aantal berichten in een wachtrij, maar er is een limiet voor de totale grootte van de berichten in een wachtrij. Deze bovengrens voor de grootte van de wachtrij is 5 GB.

## <a name="receive-messages-from-a-queue"></a>Berichten ontvangen vanuit een wachtrij

De beste manier om berichten uit een `ServiceBusRestProxy->receiveQueueMessage` wachtrij te ontvangen, is door een methode te gebruiken. Berichten kunnen worden ontvangen in twee verschillende modi: [*ReceiveAndDelete*](/dotnet/api/microsoft.servicebus.messaging.receivemode) en [*PeekLock*](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock). **PeekLock** is de standaardmodus.

Wanneer u [de ReceiveAndDelete-modus](/dotnet/api/microsoft.servicebus.messaging.receivemode) gebruikt, is ontvangen een bewerking met één schot; dat wil zeggen, wanneer Service Bus een leesverzoek ontvangt voor een bericht in een wachtrij, markeert het bericht als verbruikt en retourneert het naar de toepassing. De [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode)-modus is het eenvoudigste model en werkt het beste voor scenario's waarin een toepassing het niet verwerken van een bericht bij een fout kan tolereren. Neem bijvoorbeeld een scenario waarin de consument de ontvangstaanvraag uitgeeft en het systeem vervolgens vastloopt voordat de aanvraag wordt verwerkt. Omdat Service Bus het bericht heeft gemarkeerd als wordt verbruikt, heeft de toepassing het bericht dat voorafgaand aan de crash is verbruikt, wanneer de toepassing opnieuw wordt opgestart en opnieuw berichten begint te consumeren, het bericht gemist dat voorafgaand aan de crash is verbruikt.

In de standaard [PeekLock-modus](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) wordt het ontvangen van een bericht een tweetrapsbewerking, waardoor het mogelijk is om toepassingen te ondersteunen die ontbrekende berichten niet kunnen tolereren. Wanneer Service Bus een verzoek ontvangt, wordt het volgende bericht gevonden dat moet worden verbruikt, wordt het vergrendeld om te voorkomen dat andere consumenten het ontvangen en stuurt het vervolgens terug naar de toepassing. Nadat de toepassing is voltooid verwerking van het bericht (of slaat het betrouwbaar voor toekomstige verwerking), `ServiceBusRestProxy->deleteMessage`het voltooit de tweede fase van het ontvangstproces door het doorgeven van het ontvangen bericht aan . Wanneer Service Bus `deleteMessage` de oproep ziet, wordt het bericht als verbruikt markeren en wordt het uit de wachtrij verwijderd.

In het volgende voorbeeld ziet u hoe u een bericht ontvangt en verwerkt met de [PeekLock-modus](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) (de standaardmodus).

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

Service Bus biedt functionaliteit om netjes te herstellen bij fouten in uw toepassing of problemen bij het verwerken van een bericht. Als een ontvanger toepassing niet in staat is om het `unlockMessage` bericht te verwerken om `deleteMessage` wat voor reden dan is, dan kan het de methode op het ontvangen bericht (in plaats van de methode) aanroepen. Dit zorgt ervoor dat Service Bus het bericht in de wachtrij ontgrendelt en beschikbaar maakt om opnieuw te worden ontvangen, hetzij door dezelfde verbruikende toepassing, hetzij door een andere verbruikende toepassing.

Er is ook een time-out gekoppeld aan een bericht dat is vergrendeld in de wachtrij, en als de toepassing het bericht niet verwerkt voordat de time-out van de vergrendeling verloopt (bijvoorbeeld als de toepassing vastloopt), ontgrendelt Service Bus het bericht automatisch en maakt het beschikbaar om opnieuw te ontvangen.

In het geval dat de toepassing crasht `deleteMessage` na het verwerken van het bericht, maar voordat het verzoek wordt uitgegeven, wordt het bericht opnieuw geleverd aan de toepassing wanneer het opnieuw wordt opgestart. Dit wordt vaak *At Least Once* processing genoemd; dat wil zeggen, elk bericht wordt minstens één keer verwerkt, maar in bepaalde situaties kan hetzelfde bericht opnieuw worden bezorgd. Als het scenario dubbele verwerking niet kan tolereren, wordt het wordt aanbevolen om extra logica toe te voegen aan toepassingen om dubbele berichtbezorging te verwerken. Dit wordt vaak `getMessageId` bereikt met behulp van de methode van het bericht, die constant blijft over de levering pogingen.

> [!NOTE]
> U servicebusbronnen beheren met [Service Bus Explorer.](https://github.com/paolosalvatori/ServiceBusExplorer/) Met de Service Bus Explorer kunnen gebruikers eenvoudig verbinding maken met een naamruimte van een ServiceBus en berichtenentiteiten beheren. De tool biedt geavanceerde functies zoals import/export functionaliteit of de mogelijkheid om onderwerp, wachtrijen, abonnementen, relay services, meldinghubs en evenementenhubs te testen. 

## <a name="next-steps"></a>Volgende stappen
Nu u de basisbeginselen van wachtrijen voor servicebus hebt geleerd, raadpleegt u [Wachtrijen, onderwerpen en abonnementen][Queues, topics, and subscriptions] voor meer informatie.

Ga voor meer informatie ook naar het [PHP Developer Center.](https://azure.microsoft.com/develop/php/)

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[require_once]: https://php.net/require_once


