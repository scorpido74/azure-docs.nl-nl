---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 84cd8ed79281b005407b5a857398b5669635c072
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68320492"
---
Als u een HTTP-proxy moet configureren voor het maken van uitgaande aanvragen, gebruikt u de volgende twee argumenten:

| Name | Gegevenstype | Beschrijving |
|--|--|--|
|HTTP_PROXY|tekenreeks|De proxy voor het gebruik van bijvoorbeeld`http://proxy:8888`<br>`<proxy-url>`|
|HTTP_PROXY_CREDS|tekenreeks|Alle referenties die nodig zijn om te verifiëren tegen de proxy, bijvoorbeeld gebruikersnaam:wachtwoord.|
|`<proxy-user>`|tekenreeks|De gebruiker voor de proxy.|
|`<proxy-password>`|tekenreeks|Het wachtwoord `<proxy-user>` dat is gekoppeld aan de proxy.|
||||


```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
HTTP_PROXY=<proxy-url> \
HTTP_PROXY_CREDS=<proxy-user>:<proxy-password> \
```
