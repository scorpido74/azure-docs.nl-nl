---
title: Queue Storage gebruiken via PHP-Azure Storage
description: Informatie over het gebruik van de Azure Queue Storage-service voor het maken en verwijderen van wacht rijen en het invoegen, ophalen en verwijderen van berichten. Voor beelden zijn geschreven in PHP.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/11/2018
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: 692c943e48c08771b5f1c60b66412270081cf0e6
ms.sourcegitcommit: bd4198a3f2a028f0ce0a63e5f479242f6a98cc04
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/14/2019
ms.locfileid: "72302964"
---
# <a name="how-to-use-queue-storage-from-php"></a>Queue Storage gebruiken met PHP

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

In deze hand leiding wordt beschreven hoe u algemene scenario's uitvoert met behulp van de Azure Queue Storage-service. De voor beelden zijn geschreven via klassen uit de [Azure Storage-client bibliotheek voor php][download]. De gedekte scenario's zijn het invoegen, inspecteren, ophalen en verwijderen van wachtrij berichten, en het maken en verwijderen van wacht rijen.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>Een PHP-toepassing maken

De enige vereiste voor het maken van een PHP-toepassing die toegang heeft tot Azure Queue-opslag is het verwijzen van klassen in de [Azure Storage-client bibliotheek voor php][download] vanuit uw code. U kunt elk ontwikkelprogramma gebruiken om uw toepassing te maken, waaronder Kladblok.

In deze hand leiding gebruikt u de functies van Queue Storage-service die lokaal kunnen worden aangeroepen binnen een PHP-toepassing, of in code die wordt uitgevoerd in een webtoepassing in Azure.

## <a name="get-the-azure-client-libraries"></a>De Azure-client bibliotheken ophalen

### <a name="install-via-composer"></a>Installeren via Composer

1. Maak een bestand met de naam **Composer. json** in de hoofdmap van het project en voeg de volgende code toe:
   
    ```json
    {
      "require": {
        "microsoft/azure-storage-queue": "*"
      }
    }
    ```
2. Down load **[Composer. Phar][composer-phar]** in de hoofdmap van het project.
3. Open een opdracht prompt en voer de volgende opdracht uit in de project root
   
    ```
    php composer.phar install
    ```

Ga ook naar de [Azure Storage php-client bibliotheek][download] op github om de bron code te klonen.

## <a name="configure-your-application-to-access-queue-storage"></a>Uw toepassing configureren voor toegang tot de wachtrij opslag

Als u de Api's voor Azure Queue Storage wilt gebruiken, moet u het volgende doen:

1. Refereer met de [require_once] -instructie naar het autoloader-bestand.
2. Verwijs naar klassen die u mogelijk gebruikt.

In het volgende voor beeld ziet u hoe u het autoloader-bestand opneemt en naar de klasse **QueueRestProxy** verwijst.

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Queue\QueueRestProxy;
```

In de volgende voor beelden wordt de instructie `require_once` altijd weer gegeven, maar alleen de klassen die nodig zijn om het voor beeld uit te voeren, worden verwezen.

## <a name="set-up-an-azure-storage-connection"></a>Een Azure Storage-verbinding instellen

Als u een Azure Queue Storage-client wilt instantiëren, moet u eerst een geldig connection string hebben. De indeling voor de wachtrij service connection string is als volgt.

Voor toegang tot een Live-service:

```php
DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
```

Voor toegang tot de emulator-opslag:

```php
UseDevelopmentStorage=true
```

Als u een Azure Queue-service-client wilt maken, moet u de **QueueRestProxy** -klasse gebruiken. U kunt een van de volgende technieken gebruiken:

* Geef het connection string rechtstreeks door aan het bestand.
* Gebruik omgevings variabelen in uw web-app om de connection string op te slaan. Zie [configuratie-instellingen document van Azure web app](../../app-service/configure-common.md) voor het configureren van verbindings reeksen.
In de voorbeelden die hier worden beschreven, wordt de verbindingsreeks rechtstreeks doorgegeven.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";
$queueClient = QueueRestProxy::createQueueService($connectionString);
```

## <a name="create-a-queue"></a>Een wachtrij maken

Met een **QueueRestProxy** -object kunt u een wachtrij maken met behulp van de methode **createQueue** . Bij het maken van een wachtrij kunt u opties instellen in de wachtrij, maar dit is niet vereist. (In het onderstaande voor beeld ziet u hoe u meta gegevens in een wachtrij kunt instellen.)

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\CreateQueueOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// OPTIONAL: Set queue metadata.
$createQueueOptions = new CreateQueueOptions();
$createQueueOptions->addMetaData("key1", "value1");
$createQueueOptions->addMetaData("key2", "value2");

try    {
    // Create queue.
    $queueClient->createQueue("myqueue", $createQueueOptions);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [!NOTE]
> U moet niet vertrouwen op hoofdletter gevoeligheid voor meta gegevens sleutels. Alle sleutels worden in kleine letters in de service gelezen.
> 
> 

## <a name="add-a-message-to-a-queue"></a>Een bericht aan een wachtrij toevoegen

Gebruik **QueueRestProxy-> createMessage**om een bericht toe te voegen aan een wachtrij. De-methode heeft de naam van de wachtrij, de bericht tekst en bericht opties (optioneel).

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\CreateMessageOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Create message.
    $queueClient->createMessage("myqueue", "Hello World!");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="peek-at-the-next-message"></a>Bekijken van het volgende bericht

U kunt een bericht (of berichten) aan de voor kant van een wachtrij weer geven zonder het uit de wachtrij te verwijderen door het aanroepen van **QueueRestProxy-> peekMessages**. De methode **peekMessage** retourneert standaard één bericht, maar u kunt deze waarde wijzigen met behulp van de **PeekMessagesOptions-> methode setNumberOfMessages** .

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\PeekMessagesOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// OPTIONAL: Set peek message options.
$message_options = new PeekMessagesOptions();
$message_options->setNumberOfMessages(1); // Default value is 1.

try    {
    $peekMessagesResult = $queueClient->peekMessages("myqueue", $message_options);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$messages = $peekMessagesResult->getQueueMessages();

// View messages.
$messageCount = count($messages);
if($messageCount <= 0){
    echo "There are no messages.<br />";
}
else{
    foreach($messages as $message)    {
        echo "Peeked message:<br />";
        echo "Message Id: ".$message->getMessageId()."<br />";
        echo "Date: ".date_format($message->getInsertionDate(), 'Y-m-d')."<br />";
        echo "Message text: ".$message->getMessageText()."<br /><br />";
    }
}
```

## <a name="de-queue-the-next-message"></a>Het volgende bericht uit de wachtrij verwijderen

Uw code verwijdert een bericht uit een wachtrij in twee stappen. Eerst roept u **QueueRestProxy-> listMessages**aan, waardoor het bericht onzichtbaar wordt voor alle andere code die vanuit de wachtrij wordt gelezen. Standaard blijft het bericht onzichtbaar gedurende 30 seconden. (Als het bericht niet binnen deze tijds periode wordt verwijderd, wordt het opnieuw weer gegeven in de wachtrij.) Om het verwijderen van het bericht uit de wachtrij te volt ooien, moet u **QueueRestProxy-> deleteMessage**aanroepen. Dit proces met twee stappen voor het verwijderen van een bericht zorgt ervoor dat wanneer uw code een bericht niet kan verwerken als gevolg van een hardware-of software fout, een ander exemplaar van uw code hetzelfde bericht kan ophalen en het opnieuw proberen. Uw code aanroepen **deleteMessage** direct nadat het bericht is verwerkt.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// Get message.
$listMessagesResult = $queueClient->listMessages("myqueue");
$messages = $listMessagesResult->getQueueMessages();
$message = $messages[0];

/* ---------------------
    Process message.
   --------------------- */

// Get message ID and pop receipt.
$messageId = $message->getMessageId();
$popReceipt = $message->getPopReceipt();

try    {
    // Delete message.
    $queueClient->deleteMessage("myqueue", $messageId, $popReceipt);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="change-the-contents-of-a-queued-message"></a>De inhoud van een bericht in de wachtrij wijzigen

U kunt de inhoud van een bericht in de wachtrij wijzigen door **QueueRestProxy-> updateMessage**aan te roepen. Als het bericht een werktaak vertegenwoordigt, kunt u deze functie gebruiken om de status van de werktaak bij te werken. Met de volgende code wordt het bericht in de wachtrij bijgewerkt met nieuwe inhoud en wordt de time-out voor de zicht baarheid ingesteld op een waarde van 60 seconden. Hiermee wordt de status van het werk dat is gekoppeld aan het bericht opgeslagen en wordt de client nog een minuut om te blijven werken met het bericht. U kunt deze techniek gebruiken om uit meerdere stappen bestaande werkstromen in berichten in de wachtrij te volgen zonder dat u helemaal opnieuw hoeft te beginnen als een verwerkingsstap vanwege een hardware- of softwarefout is mislukt. Doorgaans houdt u ook het aantal nieuwe pogingen bij en als het bericht meer dan *n* keer opnieuw is geprobeerd, verwijdert u het. Dit biedt bescherming tegen berichten die een toepassingsfout activeren telkens wanneer ze worden verwerkt.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Get message.
$listMessagesResult = $queueClient->listMessages("myqueue");
$messages = $listMessagesResult->getQueueMessages();
$message = $messages[0];

// Define new message properties.
$new_message_text = "New message text.";
$new_visibility_timeout = 5; // Measured in seconds.

// Get message ID and pop receipt.
$messageId = $message->getMessageId();
$popReceipt = $message->getPopReceipt();

try    {
    // Update message.
    $queueClient->updateMessage("myqueue",
                                $messageId,
                                $popReceipt,
                                $new_message_text,
                                $new_visibility_timeout);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="additional-options-for-de-queuing-messages"></a>Aanvullende opties voor de wachtrij berichten

Er zijn twee manieren waarop u het ophalen van berichten uit een wachtrij kunt aanpassen. Ten eerste kunt u berichten batchgewijs (maximaal 32) ophalen. Ten tweede kunt u een langere of korte time-out voor de zicht baarheid instellen, waardoor uw code meer of minder tijd nodig is om elk bericht volledig te verwerken. In het volgende code voorbeeld wordt de methode **getMessages** gebruikt om 16 berichten in één aanroep op te halen. Vervolgens wordt elk bericht verwerkt met behulp **van een for** -lus. De time-out voor onzichtbaarheid wordt ingesteld op vijf minuten voor elk bericht.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\ListMessagesOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// Set list message options.
$message_options = new ListMessagesOptions();
$message_options->setVisibilityTimeoutInSeconds(300);
$message_options->setNumberOfMessages(16);

// Get messages.
try{
    $listMessagesResult = $queueClient->listMessages("myqueue",
                                                     $message_options);
    $messages = $listMessagesResult->getQueueMessages();

    foreach($messages as $message){

        /* ---------------------
            Process message.
        --------------------- */

        // Get message Id and pop receipt.
        $messageId = $message->getMessageId();
        $popReceipt = $message->getPopReceipt();

        // Delete message.
        $queueClient->deleteMessage("myqueue", $messageId, $popReceipt);
    }
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="get-queue-length"></a>Wachtrij lengte ophalen

U kunt een schatting ophalen van het aantal berichten in de wachtrij. De **QueueRestProxy-> getQueueMetadata-** methode vraagt de wachtrij service om meta gegevens over de wachtrij te retour neren. Het aanroepen van de methode **getApproximateMessageCount** voor het geretourneerde object biedt een telling van het aantal berichten in een wachtrij. De telling is alleen geschatte omdat berichten kunnen worden toegevoegd of verwijderd nadat de wachtrij service op uw aanvraag reageert.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Get queue metadata.
    $queue_metadata = $queueClient->getQueueMetadata("myqueue");
    $approx_msg_count = $queue_metadata->getApproximateMessageCount();
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

echo $approx_msg_count;
```

## <a name="delete-a-queue"></a>Een wachtrij verwijderen

Als u een wachtrij en alle berichten erin wilt verwijderen, roept u de **QueueRestProxy-> delete Queue-** methode aan.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Delete queue.
    $queueClient->deleteQueue("myqueue");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>Volgende stappen

Nu u de basis principes van Azure Queue Storage hebt geleerd, volgt u deze koppelingen voor meer informatie over complexere opslag taken:

* Ga naar de [API-verwijzing voor Azure Storage php-client bibliotheek](https://azure.github.io/azure-storage-php/)
* Bekijk het [voor beeld van een geavanceerde wachtrij](https://github.com/Azure/azure-storage-php/blob/master/samples/QueueSamples.php).

Zie ook het [PHP-ontwikkelaars centrum](https://azure.microsoft.com/develop/php/)voor meer informatie.

[download]: https://github.com/Azure/azure-storage-php
[require_once]: https://www.php.net/manual/en/function.require-once.php
[Azure Portal]: https://portal.azure.com
[composer-phar]: https://getcomposer.org/composer.phar
