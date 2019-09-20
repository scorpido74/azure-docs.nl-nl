---
title: Service Bus-onderwerpen gebruiken met PHP | Microsoft Docs
description: Meer informatie over het gebruik van Service Bus-onderwerpen met PHP in Azure.
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
ms.topic: article
ms.date: 04/15/2019
ms.author: aschhab
ms.openlocfilehash: eba2c0aeb37f2bc2283e7afb108bb4578981120e
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71147222"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-php"></a>Service Bus-onderwerpen en-abonnementen gebruiken met PHP

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

In dit artikel wordt beschreven hoe u Service Bus-onderwerpen en-abonnementen gebruikt. De voor beelden zijn geschreven in PHP en gebruiken de [Azure SDK voor php](https://github.com/Azure/azure-sdk-for-php). De volgende scenario's zijn van toepassing:

- Onderwerpen en abonnementen maken 
- Abonnements filters maken 
- Berichten verzenden naar een onderwerp 
- Berichten ontvangen van een abonnement
- Onderwerpen en abonnementen verwijderen

## <a name="prerequisites"></a>Vereisten
1. Een Azure-abonnement. U hebt een Azure-account nodig om deze zelfstudie te voltooien. U kunt de [voor delen van uw Visual Studio-of MSDN-abonnee](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) activeren of zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Volg de stappen in [de Snelstartgids: Gebruik de Azure Portal om een service bus onderwerp en abonnementen aan het onderwerp](service-bus-quickstart-topics-subscriptions-portal.md) te maken om een service bus **naam ruimte** te maken en de **Connection String**op te halen.

    > [!NOTE]
    > U maakt een **onderwerp** en een **abonnement** op het onderwerp met behulp van **php** in deze Quick Start. 

## <a name="create-a-php-application"></a>Een PHP-toepassing maken
De enige vereiste voor het maken van een PHP-toepassing die toegang heeft tot de Azure-Blob service, is door te verwijzen naar klassen in de [Azure SDK voor php](https://github.com/Azure/azure-sdk-for-php) vanuit uw code. U kunt alle ontwikkel hulpprogramma's gebruiken om uw toepassing of Klad blok te maken.

> [!NOTE]
> Voor de PHP-installatie moet ook de [openssl-extensie](https://php.net/openssl) zijn geïnstalleerd en ingeschakeld.
> 
> 

In dit artikel wordt beschreven hoe u Service functies gebruikt die in een PHP-toepassing lokaal kunnen worden aangeroepen, of in code die wordt uitgevoerd binnen een Azure-webrole,-werk functie of-website.

## <a name="get-the-azure-client-libraries"></a>De Azure-client bibliotheken ophalen

### <a name="install-via-composer"></a>Installeren via Composer
1. Maak een bestand met de naam **Composer. json** in de hoofdmap van het project en voeg de volgende code toe:
   
    ```json
    {
      "require": {
        "microsoft/windowsazure": "*"
      }
    }
    ```
2. Down load **[Composer. Phar] [Composer-Phar]** in de hoofdmap van het project.
3. Open een opdracht prompt en voer de volgende opdracht uit in de project root
   
    ```
    php composer.phar install
    ```

## <a name="configure-your-application-to-use-service-bus"></a>Uw toepassing configureren voor het gebruik van Service Bus
De Service Bus-Api's gebruiken:

1. Verwijs naar het autoloader-bestand met behulp van de [require_once][require-once] -instructie.
2. Verwijs naar alle klassen die u kunt gebruiken.

In het volgende voor beeld ziet u hoe u het autoloader-bestand opneemt en naar de klasse **ServiceBusService** verwijst.

> [!NOTE]
> In dit voor beeld (en andere voor beelden in dit artikel) wordt ervan uitgegaan dat u de PHP-client bibliotheken voor Azure hebt geïnstalleerd via Composer. Als u de bibliotheken hand matig of als een peer-pakket hebt geïnstalleerd, moet u verwijzen naar het autoloader-bestand **WindowsAzure. php** .
> 
> 

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

In de volgende voor beelden wordt `require_once` de instructie altijd weer gegeven, maar alleen de klassen waarnaar het voor beeld moet worden uitgevoerd, worden verwezen.

## <a name="set-up-a-service-bus-connection"></a>Een Service Bus verbinding instellen
Als u een Service Bus-client wilt instantiëren, moet u eerst een geldig connection string in deze indeling hebben:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Waar `Endpoint` bevindt zich doorgaans `https://[yourNamespace].servicebus.windows.net`in de indeling.

Als u een Azure-serviceclient wilt maken, moet u `ServicesBuilder` de-klasse gebruiken. U kunt:

* Geef het connection string rechtstreeks door aan het bestand.
* Gebruik de **CloudConfigurationManager (CCM)** om meerdere externe bronnen voor de Connection String te controleren:
  * Standaard wordt er ondersteuning geboden voor één externe bron-omgevings variabelen.
  * U kunt nieuwe bronnen toevoegen door de klasse `ConnectionStringSource` uit te breiden.

In de voorbeelden die hier worden beschreven, wordt de verbindingsreeks rechtstreeks doorgegeven.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-topic"></a>Een onderwerp maken
U kunt beheer bewerkingen uitvoeren voor service bus onderwerpen via de `ServiceBusRestProxy` -klasse. Een `ServiceBusRestProxy` object wordt samengesteld via de `ServicesBuilder::createServiceBusService` fabrieks methode met een geschikte Connection String die de token machtigingen voor het beheer van de sleutel bevat.

In het volgende voor beeld ziet u hoe `ServiceBusRestProxy` u een `ServiceBusRestProxy->createTopic` aanroept om een onderwerp `mytopic` te maken `MySBNamespace` met de naam in een naam ruimte:

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
> U kunt de `listTopics` methode voor `ServiceBusRestProxy` objecten gebruiken om te controleren of een onderwerp met een opgegeven naam al bestaat in een service naam ruimte.
> 
> 

## <a name="create-a-subscription"></a>Een abonnement maken
Er worden ook abonnementen van het onderwerp `ServiceBusRestProxy->createSubscription` gemaakt met de-methode. Abonnementen hebben een naam en kunnen een optioneel filter hebben waarmee de verzameling berichten wordt beperkt die aan de virtuele wachtrij van het abonnement wordt doorgegeven.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Een abonnement maken met het standaardfilter (MatchAll)
Als er geen filter is opgegeven bij het maken van een nieuw abonnement, wordt het **MatchAll** -filter (standaard) gebruikt. Wanneer het filter **MatchAll** wordt gebruikt, worden alle berichten die naar het onderwerp worden gepubliceerd, in de virtuele wachtrij van het abonnement geplaatst. In het volgende voor beeld wordt een `mysubscription` abonnement gemaakt met de naam en wordt het standaard **MatchAll** -filter gebruikt.

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
U kunt ook filters instellen waarmee u kunt opgeven welke berichten die naar een onderwerp worden verzonden, moeten worden weergegeven in een bepaald onderwerpabonnement. Het meest flexibele type filter dat wordt ondersteund door abonnementen is de [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter), waarmee een SUBSET van SQL92 wordt geïmplementeerd. SQL-filters worden uitgevoerd voor de eigenschappen van de berichten die worden gepubliceerd naar het onderwerp. Zie de [eigenschap SqlFilter. SqlExpression][sqlfilter]voor meer informatie over SqlFilters.

> [!NOTE]
> Met elke regel op een abonnement worden inkomende berichten onafhankelijk verwerkt en worden de bijbehorende resultaat berichten toegevoegd aan het abonnement. Daarnaast heeft elk nieuw abonnement een standaard **regel** object met een filter waarmee alle berichten van het onderwerp aan het abonnement worden toegevoegd. Als u alleen berichten wilt ontvangen die overeenkomen met uw filter, moet u de standaard regel verwijderen. U kunt de standaard regel verwijderen met behulp `ServiceBusRestProxy->deleteRule` van de-methode.
> 
> 

In het volgende voor beeld wordt een `HighMessages` abonnement gemaakt met de naam met een **SqlFilter** die alleen berichten `MessageNumber` selecteert die een aangepaste eigenschap hebben die groter is dan 3. Zie [berichten verzenden naar een onderwerp](#send-messages-to-a-topic) voor informatie over het toevoegen van aangepaste eigenschappen aan berichten.

```php
$subscriptionInfo = new SubscriptionInfo("HighMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');

$ruleInfo = new RuleInfo("HighMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber > 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "HighMessages", $ruleInfo);
```

Voor deze code is het gebruik van een extra naam `WindowsAzure\ServiceBus\Models\SubscriptionInfo`ruimte vereist:.

Op dezelfde manier wordt `LowMessages` `SqlFilter` met het volgende voor beeld een abonnement gemaakt met de naam die alleen berichten `MessageNumber` selecteert die een eigenschap hebben die kleiner is dan of gelijk is aan 3.

```php
$subscriptionInfo = new SubscriptionInfo("LowMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

$ruleInfo = new RuleInfo("LowMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber <= 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);
```

Wanneer een bericht wordt verzonden naar `mytopic` het onderwerp, wordt het altijd bezorgd bij ontvangers die zijn geabonneerd op het `mysubscription` abonnement en worden ze selectief geleverd aan ontvangers die zich hebben geabonneerd op `HighMessages` de `LowMessages` -en-abonnementen (afhankelijk van op de inhoud van het bericht).

## <a name="send-messages-to-a-topic"></a>Berichten verzenden naar een onderwerp
Uw toepassing roept de `ServiceBusRestProxy->sendTopicMessage` -methode aan om een bericht naar een service bus onderwerp te verzenden. De volgende code laat zien hoe u een bericht verzendt naar `mytopic` het onderwerp dat eerder in `MySBNamespace` de service naam ruimte is gemaakt.

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

De berichten die worden verzonden naar Service Bus-onderwerpen, zijn exemplaren van de klasse [BrokeredMessage][BrokeredMessage]. [BrokeredMessage][BrokeredMessage] -objecten hebben een set standaard eigenschappen en-methoden en eigenschappen die kunnen worden gebruikt om aangepaste toepassingsspecifieke eigenschappen te bewaren. In het volgende voor beeld ziet u hoe u vijf test berichten `mytopic` verzendt naar het onderwerp dat eerder is gemaakt. De `setProperty` methode wordt gebruikt om een aangepaste eigenschap (`MessageNumber`) aan elk bericht toe te voegen. De `MessageNumber` waarde van de eigenschap varieert per bericht (u kunt deze waarde gebruiken om te bepalen welke abonnementen ze ontvangen, zoals wordt weer gegeven in de sectie [een abonnement maken](#create-a-subscription) ):

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

Service Bus-onderwerpen ondersteunen een maximale grootte van 256 kB in de [Standard-laag](service-bus-premium-messaging.md) en 1 MB in de [Premium-laag](service-bus-premium-messaging.md). De koptekst, die de standaard- en aangepaste toepassingseigenschappen bevat, kan maximaal 64 kB groot zijn. Er is geen limiet voor het aantal berichten in een onderwerp, maar er is een limiet voor de totale grootte van de berichten in een onderwerp. Deze bovengrens voor de grootte van het onderwerp is 5 GB. Zie [Service Bus quota's][Service Bus quotas]voor meer informatie over quota's.

## <a name="receive-messages-from-a-subscription"></a>Berichten ontvangen van een abonnement
De beste manier om berichten van een abonnement te ontvangen is met behulp van een `ServiceBusRestProxy->receiveSubscriptionMessage` methode. Berichten kunnen in twee verschillende modi worden ontvangen: [ *ReceiveAndDelete* en *PeekLock*](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode). **PeekLock** is de standaardmodus.

Wanneer u de [ReceiveAndDelete](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode)-modus gebruikt, wordt het ontvangen in één bewerking uitgevoerd; dat wil zeggen als Service Bus een leesaanvraag voor een bericht in een abonnement ontvangt, wordt voor het bericht aangegeven dat het is verbruikt en wordt het bericht naar de toepassing geretourneerd. De [ReceiveAndDelete](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) *-modus is het eenvoudigste model en werkt het beste voor scenario's waarin een toepassing niet de verwerking van een bericht kan verdragen wanneer zich een fout voordoet. Neem bijvoorbeeld een scenario waarin de consument de ontvangstaanvraag uitgeeft en het systeem vervolgens vastloopt voordat de aanvraag wordt verwerkt. Omdat Service Bus het bericht heeft gemarkeerd als verbruikt, wordt het bericht dat voor het vastlopen is verbruikt, gemist wanneer de toepassing opnieuw wordt gestart en er opnieuw wordt verbruikt.

In de standaard [PeekLock](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) -modus wordt een bericht ontvangen van een bewerking met twee fasen, waardoor het mogelijk is om toepassingen te ondersteunen die geen ontbrekende berichten kunnen verdragen. Als Service Bus een aanvraag ontvangt, wordt het volgende te verbruiken bericht gevonden, wordt het bericht vergrendeld om te voorkomen dat andere consumenten het ontvangen en wordt het bericht vervolgens naar de toepassing geretourneerd. Nadat de toepassing klaar is met de verwerking van het bericht (of het op betrouw bare wijze opslaat voor toekomstige verwerking), wordt de tweede fase van het ontvangst proces voltooid `ServiceBusRestProxy->deleteMessage`door het ontvangen bericht door te geven aan. Als Service Bus de `deleteMessage` oproep ziet, wordt het bericht gemarkeerd als verbruikt en wordt het uit de wachtrij verwijderd.

In het volgende voor beeld ziet u hoe u een bericht ontvangt en verwerkt met behulp van de [PeekLock](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) -modus (de standaard modus). 

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

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Procedure: vastlopende toepassingen en onleesbare berichten verwerken
Service Bus biedt functionaliteit om netjes te herstellen bij fouten in uw toepassing of problemen bij het verwerken van een bericht. Als een ontvanger toepassing het bericht om de een of andere reden niet kan verwerken, kan de `unlockMessage` methode op het ontvangen bericht worden aangeroepen (in plaats van de `deleteMessage` methode). Hiermee wordt Service Bus het bericht in de wachtrij ontgrendeld en wordt het beschikbaar gemaakt om opnieuw te worden ontvangen, ofwel door dezelfde verbruiks toepassing of door een andere consumerende toepassing.

Er is ook een time-out gekoppeld aan een bericht dat in de wachtrij is vergrendeld. als de toepassing het bericht niet kan verwerken voordat de time-out van de vergren deling verloopt (bijvoorbeeld als de toepassing vastloopt), wordt Service Bus het bericht automatisch ontgrendeld en wordt het beschikbaar om opnieuw te worden ontvangen.

In het geval dat de toepassing vastloopt na het verwerken van het bericht `deleteMessage` , maar voordat de aanvraag is verzonden, wordt het bericht opnieuw aan de toepassing bezorgd wanneer het opnieuw wordt gestart. Dit type verwerking wordt vaak *ten minste eenmaal per* verwerking genoemd; dat wil zeggen dat elk bericht ten minste één keer wordt verwerkt, maar in bepaalde situaties kan hetzelfde bericht opnieuw worden bezorgd. Als het scenario dubbele verwerking niet kan verdragen, moeten ontwikkel aars van toepassingen extra logica toevoegen aan toepassingen voor het afhandelen van dubbele bericht bezorging. Dit wordt vaak bereikt met de `getMessageId` methode van het bericht, dat constant blijft tijdens bezorgings pogingen.

## <a name="delete-topics-and-subscriptions"></a>Onderwerpen en abonnementen verwijderen
Als u een onderwerp of een abonnement wilt verwijderen, `ServiceBusRestProxy->deleteTopic` gebruikt u `ServiceBusRestProxy->deleteSubscripton` respectievelijk de methoden of. Als een onderwerp wordt verwijderd, worden ook alle abonnementen verwijderd die zijn geregistreerd bij het onderwerp.

In het volgende voor beeld ziet u hoe u een `mytopic` onderwerp met de naam en de geregistreerde abonnementen verwijdert.

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

Met behulp `deleteSubscription` van de-methode kunt u een abonnement onafhankelijk verwijderen:

```php
$serviceBusRestProxy->deleteSubscription("mytopic", "mysubscription");
```

> [!NOTE]
> U kunt Service Bus-resources beheren met [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Met de Service Bus Explorer kunnen gebruikers verbinding maken met een Service Bus naam ruimte en de Messa ging-entiteiten op een eenvoudige manier beheren. Het hulp programma biedt geavanceerde functies zoals de functionaliteit voor importeren/exporteren of de mogelijkheid om onderwerp, wacht rijen, abonnementen, relay-Services, Notification hubs en Events hubs te testen. 

## <a name="next-steps"></a>Volgende stappen
Zie [wacht rijen, onderwerpen en abonnementen][Queues, topics, and subscriptions]voor meer informatie.

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[sqlfilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter
[require-once]: https://php.net/require_once
[Service Bus quotas]: service-bus-quotas.md
