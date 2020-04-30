---
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/25/2018
ms.author: spelluru
ms.openlocfilehash: 5e3c4622131528fc2c40a1510aeea3092018d182
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77474170"
---
### <a name="install-via-composer"></a>Installeren via Composer
1. Maak een bestand met de naam **Composer. json** in de hoofdmap van het project en voeg de volgende code toe:
   
    ```json
    {
      "require": {
        "microsoft/windowsazure": "*"
      }
    }
    ```
2. Down load **[Composer. Phar][composer-phar]** in de hoofdmap van het project.
3. Open een opdracht prompt en voer de volgende opdracht uit in de project root
   
    ```
    php composer.phar install
    ```

Ga ook naar de [Azure Storage php-client bibliotheek][php-sdk-github] op github om de bron code te klonen.

[php-sdk-github]: https://github.com/Azure/azure-storage-php
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: https://github.com/Azure/azure-sdk-for-php
[composer-phar]: http://getcomposer.org/composer.phar
