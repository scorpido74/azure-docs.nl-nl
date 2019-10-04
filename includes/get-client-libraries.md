---
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/25/2018
ms.author: spelluru
ms.openlocfilehash: 3cd5f59a3dcf3afcd26d16874e7dc77ca0a7e844
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71841693"
---
### <a name="install-via-composer"></a>Installeren via Composer
1. Maak een bestand met de naam **Composer. json** in de hoofdmap van het project en voeg de volgende code toe:
   
    ```json
    {
      "require": {
        "microsoft/azure-storage": "*"
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
