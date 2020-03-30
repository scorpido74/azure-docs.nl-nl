---
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/25/2018
ms.author: spelluru
ms.openlocfilehash: 5e3c4622131528fc2c40a1510aeea3092018d182
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77474170"
---
### <a name="install-via-composer"></a>Installeren via componist
1. Maak een bestand met de naam **composer.json** in de hoofdmap van uw project en voeg er de volgende code aan toe:
   
    ```json
    {
      "require": {
        "microsoft/windowsazure": "*"
      }
    }
    ```
2. Download **[composer.phar][composer-phar]** in uw projectroot.
3. Een opdrachtprompt openen en de volgende opdracht uitvoeren in de projecthoofdmap
   
    ```
    php composer.phar install
    ```

U ook naar de [Azure Storage PHP-clientbibliotheek][php-sdk-github] op GitHub gaan om de broncode te klonen.

[php-sdk-github]: https://github.com/Azure/azure-storage-php
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: https://github.com/Azure/azure-sdk-for-php
[composer-phar]: http://getcomposer.org/composer.phar
