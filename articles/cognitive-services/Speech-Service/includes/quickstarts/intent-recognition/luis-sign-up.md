---
title: 'Snelstartgids: spraak, intenties en entiteiten herkennen, python-Speech-Service'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/27/2019
ms.author: erhopf
ms.openlocfilehash: aee5d6e1f6ed0519df7d1059b39f215d9f0d9091
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660479"
---
U moet een LUIS-account en een project maken met behulp van de preview-portal van LUIS als u de Snelstartgids voor de intentie herkenning wilt volt ooien. Voor deze Quick start is alleen een LUIS-abonnement vereist. Een spraak service-abonnement is niet vereist.

Het eerste wat u moet doen, is het maken van een LUIS-account en-app met behulp van de LUIS preview Portal. De LUIS-app die u maakt, maakt gebruik van een vooraf gebouwd domein voor Start Automation, dat intenties, entiteiten en voor beeld uitingen biedt. Wanneer u klaar bent, hebt u een LUIS-eind punt dat wordt uitgevoerd in de cloud die u kunt aanroepen met de spraak-SDK. 

Volg deze instructies om uw LUIS-app te maken: 

* <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-get-started-create-app" target="_blank">Snelstartgids: vooraf gebouwde domein-app bouwen</a>

Wanneer u klaar bent, hebt u drie dingen nodig: 

* Uw LUIS-sleutel
* Uw LUIS-regio
* Uw LUIS-App-ID

Hier vindt u deze informatie in de [Luis preview-Portal](https://preview.luis.ai/):

1. Selecteer **beheren**in de preview-portal van Luis en selecteer vervolgens **Azure-resources**. Op deze pagina vindt u uw LUIS-sleutel en-locatie (ook wel _regio_genoemd).  

   > [!div class="mx-imgBorder"]
   > ![LUIS-sleutel en-locatie](../../../media/luis/luis-key-region.png)

2. Nadat u uw sleutel en locatie hebt verkregen, hebt u de App-ID nodig. **Toepassings instellingen** selecteren: uw app-id is beschikbaar op deze pagina.

   > [!div class="mx-imgBorder"]
   > ![App-ID van LUIS](../../../media/luis/luis-app-id.png)