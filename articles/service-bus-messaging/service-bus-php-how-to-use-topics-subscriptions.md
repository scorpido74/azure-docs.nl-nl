---
title: Azure Service Bus-onderwerpen gebruiken met PHP
description: In deze zelfstudie leert u hoe u Azure Service Bus-onderwerpen en -abonnementen gebruikt vanuit een PHP-toepassing.
services: service-bus-messaging
documentationcenter: php
author: axisc
manager: timlt
editor: spelluru
ms.assetid: faaa4bbd-f6ef-42ff-aca7-fc4353976449
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: quickstart
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 92f25f4bdac4942478c93f717c81eadd2c2f5b4a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76760671"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-php"></a>Quickstart: Service Bus-onderwerpen en abonnementen gebruiken met PHP

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

In dit artikel ziet u hoe u servicebus-onderwerpen en abonnementen gebruiken. De voorbeelden zijn geschreven in PHP en gebruiken de [Azure SDK voor PHP.](https://github.com/Azure/azure-sdk-for-php) De behandelde scenario's omvatten:

- Onderwerpen en abonnementen maken 
- Abonnementsfilters maken 
- Berichten naar een onderwerp verzenden 
- Berichten ontvangen van een abonnement
- Onderwerpen en abonnementen verwijderen

## <a name="prerequisites"></a>Vereisten
1. Een Azure-abonnement. U hebt een Azure-account nodig om deze zelfstudie te voltooien. U uw [Visual Studio- of MSDN-abonneevoordelen](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) activeren of u aanmelden voor een [gratis account.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
2. Volg stappen in de [Quickstart: gebruik de Azure-portal om een servicebusonderwerp en abonnementen op het onderwerp te maken](service-bus-quickstart-topics-subscriptions-portal.md) om een **naamruimte** voor servicebus te maken en de **verbindingstekenreeks op te**halen.

    > [!NOTE]
    > Je maakt een **topic** en een **abonnement op** het onderwerp door **PHP** in deze quickstart te gebruiken. 

## <a name="create-a-php-application"></a>Een PHP-toepassing maken
De enige vereiste voor het maken van een PHP-toepassing die toegang heeft tot de Azure Blob-service is om referentieklassen in de [Azure SDK voor PHP](https://github.com/Azure/azure-sdk-for-php) vanuit uw code te gebruiken. U alle ontwikkeltools gebruiken om uw toepassing of Kladblok te maken.

> [!NOTE]
> Uw PHP-installatie moet ook de [OpenSSL-extensie](https://php.net/openssl) hebben geïnstalleerd en ingeschakeld.
> 
> 

In dit artikel wordt beschreven hoe u servicefuncties gebruikt die lokaal binnen een PHP-toepassing kunnen worden aangeroepen of in code die wordt uitgevoerd binnen een Azure-webrol, werknemersrol of website.

## <a name="get-the-azure-client-libraries"></a>De Azure-clientbibliotheken oppakken

### <a name="install-via-composer"></a>Installeren via componist
1. Maak een bestand met de naam **composer.json** in de hoofdmap van uw project en voeg er de volgende code aan toe:
   
    ```json
    {
      "require": {
        "microsoft/windowsazure": "*"
      }
    }
    ```
2. Download **[composer.phar][composer-phar]** in uw projectwortel.
3. Een opdrachtprompt openen en de volgende opdracht uitvoeren in de projecthoofdmap
   
    ```
    php composer.phar install
    ```

## <a name="configure-your-application-to-use-service-bus"></a>Uw toepassing configureren om Service Bus te gebruiken
Ga als u de API's van de ServiceBus gebruiken:

1. Verwijs naar het autoloaderbestand met de [require_once-instructie.][require-once]
2. Verwijs naar alle klassen die u zou kunnen gebruiken.

In het volgende voorbeeld ziet u hoe u het bestand voor autoloader opneemt en verwijst naar de klasse **ServiceBusService.**

> [!NOTE]
> In dit voorbeeld (en andere voorbeelden in dit artikel) wordt ervan uitgegaan dat u de PHP-clientbibliotheken voor Azure via Composer hebt geïnstalleerd. Als u de bibliotheken handmatig of als EEN PEAR-pakket hebt geïnstalleerd, moet u verwijzen naar het **windowsAzure.php-autoloaderbestand.**
> 
> 

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

In de volgende voorbeelden `require_once` wordt de instructie altijd weergegeven, maar alleen naar de klassen die nodig zijn voor het voorbeeld om uit te voeren, wordt verwezen.

## <a name="set-up-a-service-bus-connection"></a>Een ServiceBus-verbinding instellen
Als u een Service Bus-client wilt instantiëren, moet u eerst een geldige verbindingstekenreeks in deze indeling hebben:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Waar `Endpoint` is meestal van `https://[yourNamespace].servicebus.windows.net`het formaat .

Als u een Azure-serviceclient `ServicesBuilder` wilt maken, moet u de klasse gebruiken. U kunt:

* Geef de verbindingstekenreeks er rechtstreeks aan door.
* Gebruik de **CCM (CloudConfigurationManager)** om meerdere externe bronnen voor de verbindingstekenreeks te controleren:
  * Standaard wordt het geleverd met ondersteuning voor een externe bron - omgevingsvariabelen.
  * U kunt nieuwe bronnen toevoegen door de klasse `ConnectionStringSource` uit te breiden.

In de voorbeelden die hier worden beschreven, wordt de verbindingsreeks rechtstreeks doorgegeven.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-topic"></a>Een onderwerp maken
U beheerbewerkingen uitvoeren voor `ServiceBusRestProxy` Service Bus-onderwerpen via de klas. Een `ServiceBusRestProxy` object wordt gebouwd `ServicesBuilder::createServiceBusService` via de fabrieksmethode met een geschikte verbindingstekenreeks die de tokenmachtigingen inkapselt om het te beheren.

In het volgende voorbeeld ziet u `ServiceBusRestProxy` hoe `ServiceBusRestProxy->createTopic` u een `mytopic` gesprek `MySBNamespace` kunt instantiëren en aanroepen om een onderwerp te maken dat binnen een naamruimte wordt genoemd:

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\TopicInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Create topic.
    $topicInfo = new TopicInfo("mytopic");
    $serviceBusRestProxy->createTopic($topicInfo);
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
> U `listTopics` de `ServiceBusRestProxy` methode voor objecten gebruiken om te controleren of er al een onderwerp met een opgegeven naam bestaat binnen een servicenaamruimte.
> 
> 

## <a name="create-a-subscription"></a>Een abonnement maken
Onderwerpabonnementen worden ook `ServiceBusRestProxy->createSubscription` gemaakt met de methode. Abonnementen hebben een naam en kunnen een optioneel filter hebben waarmee de verzameling berichten wordt beperkt die aan de virtuele wachtrij van het abonnement wordt doorgegeven.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Een abonnement maken met het standaardfilter (MatchAll)
Als er geen filter is opgegeven wanneer een nieuw abonnement wordt gemaakt, wordt het **Filter MatchAll** (standaard) gebruikt. Wanneer het filter **MatchAll** wordt gebruikt, worden alle berichten die naar het onderwerp worden gepubliceerd, in de virtuele wachtrij van het abonnement geplaatst. In het volgende voorbeeld `mysubscription` wordt een abonnement gemaakt met de naam en wordt het standaard **filter MatchAll** gebruikt.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\SubscriptionInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Create subscription.
    $subscriptionInfo = new SubscriptionInfo("mysubscription");
    $serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);
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

### <a name="create-subscriptions-with-filters"></a>Abonnementen met filters maken
U kunt ook filters instellen waarmee u kunt opgeven welke berichten die naar een onderwerp worden verzonden, moeten worden weergegeven in een bepaald onderwerpabonnement. Het meest flexibele type filter dat wordt ondersteund door abonnementen is het [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter), dat een subset van SQL92 implementeert. SQL-filters worden uitgevoerd voor de eigenschappen van de berichten die worden gepubliceerd naar het onderwerp. Zie [SqlFilter.SqlExpression Eigenschap sqlfilter.SqlExpression][sqlfilter]voor meer informatie over SqlFilters.

> [!NOTE]
> Elke regel op een abonnement verwerkt binnenkomende berichten onafhankelijk van elkaar en voegt hun resultaatberichten toe aan het abonnement. Bovendien heeft elk nieuw abonnement een **standaardregelobject** met een filter dat alle berichten van het onderwerp aan het abonnement toevoegt. Als u alleen berichten wilt ontvangen die overeenkomen met uw filter, moet u de standaardregel verwijderen. U de standaardregel `ServiceBusRestProxy->deleteRule` verwijderen met behulp van de methode.
> 
> 

In het volgende voorbeeld `HighMessages` wordt een abonnement gemaakt met de naam `MessageNumber` **SqlFilter** dat alleen berichten selecteert met een aangepaste eigenschap groter dan 3. Zie [Berichten naar een onderwerp verzenden](#send-messages-to-a-topic) voor informatie over het toevoegen van aangepaste eigenschappen aan berichten.

```php
$subscriptionInfo = new SubscriptionInfo("HighMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');

$ruleInfo = new RuleInfo("HighMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber > 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "HighMessages", $ruleInfo);
```

Deze code vereist het gebruik van `WindowsAzure\ServiceBus\Models\SubscriptionInfo`een extra naamruimte: .

In het volgende voorbeeld wordt `LowMessages` ook `SqlFilter` een abonnement gemaakt met de `MessageNumber` naam: een abonnement dat alleen berichten selecteert met een eigenschap die kleiner is dan of gelijk is aan 3.

```php
$subscriptionInfo = new SubscriptionInfo("LowMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

$ruleInfo = new RuleInfo("LowMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber <= 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);
```

Nu, wanneer een bericht `mytopic` wordt verzonden naar het onderwerp, `mysubscription` wordt het altijd geleverd aan `HighMessages` ontvangers `LowMessages` die zijn geabonneerd op het abonnement en selectief worden geleverd aan ontvangers die zijn geabonneerd op de en abonnementen (afhankelijk van de inhoud van het bericht).

## <a name="send-messages-to-a-topic"></a>Berichten verzenden naar een onderwerp
Als u een bericht wilt verzenden naar `ServiceBusRestProxy->sendTopicMessage` een servicebusonderwerp, roept uw toepassing de methode aan. In de volgende code ziet u `mytopic` hoe u `MySBNamespace` een bericht verzendt naar het onderwerp dat eerder is gemaakt in de servicenaamruimte.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\BrokeredMessage;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message");

    // Send message.
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
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

De berichten die worden verzonden naar Service Bus-onderwerpen, zijn exemplaren van de klasse [BrokeredMessage][BrokeredMessage]. [BrokeredMessage-objecten][BrokeredMessage] hebben een set standaardeigenschappen en -methoden, evenals eigenschappen die kunnen worden gebruikt om aangepaste toepassingsspecifieke eigenschappen vast te houden. In het volgende voorbeeld ziet u hoe `mytopic` u vijf testberichten verzendt naar het onderwerp dat eerder is gemaakt. De `setProperty` methode wordt gebruikt om`MessageNumber`een aangepaste eigenschap ( ) toe te voegen aan elk bericht. De `MessageNumber` waarde van de eigenschap varieert per bericht (u deze waarde gebruiken om te bepalen welke abonnementen het ontvangen, zoals wordt weergegeven in de sectie [Een abonnement maken):](#create-a-subscription)

```php
for($i = 0; $i < 5; $i++){
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message ".$i);

    // Set custom property.
    $message->setProperty("MessageNumber", $i);

    // Send message.
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
}
```

Service Bus-onderwerpen ondersteunen een maximale grootte van 256 kB in de [Standard-laag](service-bus-premium-messaging.md) en 1 MB in de [Premium-laag](service-bus-premium-messaging.md). De koptekst, die de standaard- en aangepaste toepassingseigenschappen bevat, kan maximaal 64 kB groot zijn. Er is geen limiet voor het aantal berichten in een onderwerp, maar er is een limiet voor de totale grootte van de berichten in een onderwerp. Deze bovengrens over de grootte van het onderwerp is 5 GB. Zie [ServiceBus-quota voor][Service Bus quotas]meer informatie over quota.

## <a name="receive-messages-from-a-subscription"></a>Berichten ontvangen van een abonnement
De beste manier om berichten van een `ServiceBusRestProxy->receiveSubscriptionMessage` abonnement te ontvangen, is door een methode te gebruiken. Berichten kunnen worden ontvangen in twee verschillende modi: [ *ReceiveAndDelete* en *PeekLock*](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode). **PeekLock** is de standaardmodus.

Wanneer u de [ReceiveAndDelete](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode)-modus gebruikt, wordt het ontvangen in één bewerking uitgevoerd; dat wil zeggen als Service Bus een leesaanvraag voor een bericht in een abonnement ontvangt, wordt voor het bericht aangegeven dat het is verbruikt en wordt het bericht naar de toepassing geretourneerd. [ReceiveAndDelete](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) *-modus is het eenvoudigste model en werkt het beste voor scenario's waarin een toepassing kan tolereren dat een bericht niet wordt verwerkt wanneer er een fout optreedt. Neem bijvoorbeeld een scenario waarin de consument de ontvangstaanvraag uitgeeft en het systeem vervolgens vastloopt voordat de aanvraag wordt verwerkt. Omdat Service Bus het bericht heeft gemarkeerd als wordt verbruikt, heeft de toepassing, wanneer de toepassing opnieuw wordt opgestart en opnieuw berichten begint te consumeren, het bericht gemist dat voorafgaand aan de crash is verbruikt.

In de standaard [PeekLock-modus](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) wordt het ontvangen van een bericht een tweetrapsbewerking, waardoor het mogelijk is om toepassingen te ondersteunen die ontbrekende berichten niet kunnen tolereren. Als Service Bus een aanvraag ontvangt, wordt het volgende te verbruiken bericht gevonden, wordt het bericht vergrendeld om te voorkomen dat andere consumenten het ontvangen en wordt het bericht vervolgens naar de toepassing geretourneerd. Nadat de toepassing is voltooid verwerking van het bericht (of slaat het betrouwbaar voor toekomstige verwerking), `ServiceBusRestProxy->deleteMessage`het voltooit de tweede fase van het ontvangstproces door het doorgeven van het ontvangen bericht aan . Wanneer Service Bus `deleteMessage` de oproep ziet, markeert het bericht als verbruikt en verwijdert u het bericht uit de wachtrij.

In het volgende voorbeeld ziet u hoe u een bericht ontvangt en verwerkt met de [PeekLock-modus](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) (de standaardmodus). 

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Set receive mode to PeekLock (default is ReceiveAndDelete)
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();

    // Get message.
    $message = $serviceBusRestProxy->receiveSubscriptionMessage("mytopic", "mysubscription", $options);

    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";

    /*---------------------------
        Process message here.
    ----------------------------*/

    // Delete message. Not necessary if peek lock is not set.
    echo "Deleting message...<br />";
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

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>How to: omgaan met toepassingscrashes en onleesbare berichten
Service Bus biedt functionaliteit om netjes te herstellen bij fouten in uw toepassing of problemen bij het verwerken van een bericht. Als een ontvanger toepassing niet in staat is om het `unlockMessage` bericht te verwerken om `deleteMessage` wat voor reden dan is, dan kan het de methode op het ontvangen bericht (in plaats van de methode) aanroepen. Het zorgt ervoor dat Service Bus het bericht in de wachtrij ontgrendelt en beschikbaar maakt om opnieuw te worden ontvangen, hetzij door dezelfde verbruikende toepassing of door een andere verbruikende toepassing.

Er is ook een time-out gekoppeld aan een bericht dat is vergrendeld in de wachtrij, en als de toepassing het bericht niet verwerkt voordat de time-out van de vergrendeling verloopt (bijvoorbeeld als de toepassing vastloopt), ontgrendelt Service Bus het bericht automatisch en maakt het beschikbaar om opnieuw te ontvangen.

In het geval dat de toepassing crasht `deleteMessage` na het verwerken van het bericht, maar voordat het verzoek wordt uitgegeven, wordt het bericht opnieuw geleverd aan de toepassing wanneer het opnieuw wordt opgestart. Dit type verwerking wordt vaak *ten minste eenmaal* verwerkt genoemd; dat wil zeggen, elk bericht wordt minstens één keer verwerkt, maar in bepaalde situaties kan hetzelfde bericht opnieuw worden bezorgd. Als het scenario dubbele verwerking niet kan tolereren, moeten toepassingsontwikkelaars extra logica toevoegen aan toepassingen om dubbele berichtbezorging te verwerken. Het wordt vaak `getMessageId` bereikt met behulp van de methode van het bericht, die constant blijft over de levering pogingen.

## <a name="delete-topics-and-subscriptions"></a>Onderwerpen en abonnementen verwijderen
Als u een onderwerp of `ServiceBusRestProxy->deleteTopic` een `ServiceBusRestProxy->deleteSubscripton` abonnement wilt verwijderen, gebruikt u de methoden of de methoden. Als een onderwerp wordt verwijderd, worden ook alle abonnementen verwijderd die zijn geregistreerd bij het onderwerp.

In het volgende voorbeeld ziet `mytopic` u hoe u een onderwerp met de naam en de geregistreerde abonnementen verwijdert.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\ServiceBus\ServiceBusService;
use WindowsAzure\ServiceBus\ServiceBusSettings;
use WindowsAzure\Common\ServiceException;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Delete topic.
    $serviceBusRestProxy->deleteTopic("mytopic");
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

Door de `deleteSubscription` methode te gebruiken, u een abonnement onafhankelijk verwijderen:

```php
$serviceBusRestProxy->deleteSubscription("mytopic", "mysubscription");
```

> [!NOTE]
> U servicebusbronnen beheren met [Service Bus Explorer.](https://github.com/paolosalvatori/ServiceBusExplorer/) Met de Service Bus Explorer kunnen gebruikers eenvoudig verbinding maken met een naamruimte van een ServiceBus en berichtenentiteiten beheren. De tool biedt geavanceerde functies zoals import/export functionaliteit of de mogelijkheid om onderwerp, wachtrijen, abonnementen, relay services, meldinghubs en evenementenhubs te testen. 

## <a name="next-steps"></a>Volgende stappen
Zie [Wachtrijen, onderwerpen en abonnementen][Queues, topics, and subscriptions]voor meer informatie.

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[sqlfilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter
[require-once]: https://php.net/require_once
[Service Bus quotas]: service-bus-quotas.md
