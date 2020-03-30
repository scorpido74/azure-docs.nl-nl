---
title: Wachtrijopslag gebruiken vanuit PHP - Azure Storage
description: Meer informatie over het gebruik van de Azure Queue-opslagservice om wachtrijen te maken en te verwijderen en berichten in te voegen, op te halen en te verwijderen. Voorbeelden zijn geschreven in PHP.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/11/2018
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: 692c943e48c08771b5f1c60b66412270081cf0e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72302964"
---
# <a name="how-to-use-queue-storage-from-php"></a>Queue Storage gebruiken met PHP

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

In deze handleiding ziet u hoe u veelvoorkomende scenario's uitvoeren met behulp van de Azure Queue-opslagservice. De voorbeelden worden geschreven via klassen uit de [Azure Storage Client Library voor PHP.][download] De gedekte scenario's omvatten het invoegen, gluren, opvragen en verwijderen van wachtrijberichten, evenals het maken en verwijderen van wachtrijen.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>Een PHP-toepassing maken

De enige vereiste voor het maken van een PHP-toepassing die toegang heeft tot Azure Queue-opslag is de verwijzing naar klassen in de [Azure Storage Client Library voor PHP][download] vanuit uw code. U kunt elk ontwikkelprogramma gebruiken om uw toepassing te maken, waaronder Kladblok.

In deze handleiding gebruikt u de wachtrijopslagservicefuncties die lokaal kunnen worden aangeroepen binnen een PHP-toepassing of in code die wordt uitgevoerd in een webtoepassing in Azure.

## <a name="get-the-azure-client-libraries"></a>De Azure-clientbibliotheken opgebruiken

### <a name="install-via-composer"></a>Installeren via componist

1. Maak een bestand met de naam **composer.json** in de hoofdmap van uw project en voeg er de volgende code aan toe:
   
    ```json
    {
      "require": {
        "microsoft/azure-storage-queue": "*"
      }
    }
    ```
2. Download **[composer.phar][composer-phar]** in uw projectroot.
3. Een opdrachtprompt openen en de volgende opdracht uitvoeren in de projecthoofdmap
   
    ```
    php composer.phar install
    ```

U ook naar de [Azure Storage PHP-clientbibliotheek][download] op GitHub gaan om de broncode te klonen.

## <a name="configure-your-application-to-access-queue-storage"></a>Uw toepassing configureren om toegang te krijgen tot wachtrijopslag

Als u de API's voor Azure Queue-opslag wilt gebruiken, moet u het:

1. Verwijs naar het autoloaderbestand met behulp van de [require_once-instructie.]
2. Verwijs naar alle klassen die u zou kunnen gebruiken.

In het volgende voorbeeld ziet u hoe u het bestand voor autoloader opneemt en verwijst naar de klasse **QueueRestProxy.**

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Queue\QueueRestProxy;
```

In de volgende voorbeelden `require_once` wordt de instructie altijd weergegeven, maar alleen naar de klassen die nodig zijn voor het voorbeeld dat moet worden uitgevoerd, wordt verwezen.

## <a name="set-up-an-azure-storage-connection"></a>Een Azure-opslagverbinding instellen

Als u een Azure Queue-opslagclient wilt instantiëren, moet u eerst over een geldige verbindingstekenreeks beschikken. De indeling voor de verbindingstekenreeks voor wachtrijservice is als volgt.

Voor toegang tot een live service:

```php
DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
```

Voor toegang tot de emulatoropslag:

```php
UseDevelopmentStorage=true
```

Als u een Azure Queue-serviceclient wilt maken, moet u de klasse **QueueRestProxy** gebruiken. U een van de volgende technieken gebruiken:

* Geef de verbindingstekenreeks er rechtstreeks aan door.
* Gebruik omgevingsvariabelen in uw web-app om de verbindingstekenreeks op te slaan. Zie [het document met configuratie-instellingen voor azure-web-apps](../../app-service/configure-common.md) voor het configureren van verbindingstekenreeksen.
In de voorbeelden die hier worden beschreven, wordt de verbindingsreeks rechtstreeks doorgegeven.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";
$queueClient = QueueRestProxy::createQueueService($connectionString);
```

## <a name="create-a-queue"></a>Een wachtrij maken

Met een object **QueueRestProxy** u een wachtrij maken met de methode **createQueue.** Bij het maken van een wachtrij u opties instellen in de wachtrij, maar dit is niet vereist. (In het onderstaande voorbeeld ziet u hoe u metagegevens instelt in een wachtrij.)

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
> U moet niet vertrouwen op de gevoeligheid van de aanvraag voor metagegevenssleutels. Alle toetsen worden gelezen van de service in kleine letters.
> 
> 

## <a name="add-a-message-to-a-queue"></a>Een bericht toevoegen aan een wachtrij

Als u een bericht aan een wachtrij wilt toevoegen, gebruikt u **QueueRestProxy->makenMessage**. De methode neemt de naam van de wachtrij, de berichttekst en de berichtopties (die optioneel zijn).

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

U een bericht (of berichten) aan de voorkant van een wachtrij bekijken zonder het uit de wachtrij te verwijderen door **QueueRestProxy->peekMessages**te bellen. Standaard retourneert de **methode peekMessage** één bericht, maar u die waarde wijzigen met de methode **PeekMessagesOptions->setNumberMessages.**

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

Uw code verwijdert een bericht uit een wachtrij in twee stappen. Eerst belt u **QueueRestProxy->listMessages**, waardoor het bericht onzichtbaar is voor elke andere code die uit de wachtrij wordt gelezen. Standaard blijft het bericht onzichtbaar gedurende 30 seconden. (Als het bericht in deze periode niet wordt verwijderd, wordt het weer zichtbaar in de wachtrij.) Als u wilt eindigen met het verwijderen van het bericht uit de wachtrij, moet u **QueueRestProxy->deleteMessage**aanroepen. Dit proces in twee stappen van het verwijderen van een bericht zorgt ervoor dat wanneer uw code een bericht niet verwerkt als gevolg van hardware- of softwarefouten, een ander exemplaar van uw code hetzelfde bericht kan ontvangen en het opnieuw kan proberen. Uw coderoept **deleteMessage** direct nadat het bericht is verwerkt.

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

U de inhoud van een bericht in de wachtrij wijzigen door **QueueRestProxy->updateMessage te**bellen. Als het bericht een werktaak vertegenwoordigt, kunt u deze functie gebruiken om de status van de werktaak bij te werken. Met de volgende code wordt het wachtrijbericht bijgewerkt met nieuwe inhoud en wordt de time-out voor zichtbaarheid ingesteld om nog eens 60 seconden uit te breiden. Dit slaat de stand van het werk op dat aan het bericht is gekoppeld en geeft de client nog een minuut om aan het bericht te blijven werken. U kunt deze techniek gebruiken om uit meerdere stappen bestaande werkstromen in berichten in de wachtrij te volgen zonder dat u helemaal opnieuw hoeft te beginnen als een verwerkingsstap vanwege een hardware- of softwarefout is mislukt. Doorgaans houdt u ook het aantal nieuwe pogingen bij en als het bericht meer dan *n* keer opnieuw is geprobeerd, verwijdert u het. Dit biedt bescherming tegen berichten die een toepassingsfout activeren telkens wanneer ze worden verwerkt.

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

## <a name="additional-options-for-de-queuing-messages"></a>Extra opties voor het ontgenereren van berichten

Er zijn twee manieren waarop u het ophalen van berichten uit een wachtrij aanpassen. Ten eerste kunt u berichten batchgewijs (maximaal 32) ophalen. Ten tweede u een langere of kortere time-out voor zichtbaarheid instellen, zodat uw code meer of minder tijd heeft om elk bericht volledig te verwerken. In het volgende codevoorbeeld wordt de methode **getMessages** gebruikt om 16 berichten in één gesprek te ontvangen. Vervolgens verwerkt het elk bericht met behulp van een **voor** lus. De time-out voor onzichtbaarheid wordt ingesteld op vijf minuten voor elk bericht.

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

## <a name="get-queue-length"></a>Wachtrijlengte opdoen

U kunt een schatting ophalen van het aantal berichten in de wachtrij. De methode **QueueRestProxy->getQueueMetadata** vraagt de wachtrijservice om metagegevens over de wachtrij terug te sturen. Als u de methode **getApproximateMessageCount** aanroept op het geretourneerde object, wordt geteld hoeveel berichten zich in een wachtrij bevinden. Het aantal is slechts bij benadering omdat berichten kunnen worden toegevoegd of verwijderd nadat de wachtrijservice op uw verzoek reageert.

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

Als u een wachtrij en alle berichten in deze wachtrij wilt verwijderen, roept u de methode **QueueRestProxy->deleteQueue** aan.

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

Volg de basisbeginselen van Azure Queue-opslag nu u de basisbeginselen van Azure Queue-opslag hebt geleerd, voordat u meer te weten komt over complexere opslagtaken:

* Ga naar de [API-verwijzing voor Azure Storage PHP-clientbibliotheek](https://azure.github.io/azure-storage-php/)
* Zie het [voorbeeld Geavanceerde wachtrij](https://github.com/Azure/azure-storage-php/blob/master/samples/QueueSamples.php).

Zie voor meer informatie ook het [PHP Developer Center.](https://azure.microsoft.com/develop/php/)

[download]: https://github.com/Azure/azure-storage-php
[require_once]: https://www.php.net/manual/en/function.require-once.php
[Azure Portal]: https://portal.azure.com
[composer-phar]: https://getcomposer.org/composer.phar
