---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b9d0d2b97472eb3264f5e4600fddbfc7d3250918
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67704181"
---
## <a name="use-the-docker-cli-to-authenticate-the-private-container-registry"></a>De docker-CLI gebruiken om het persoonlijke container register te verifiëren

U kunt zich op verschillende manieren verifiëren met het persoonlijke container register voor Cognitive Services containers, maar de aanbevolen methode van de opdracht regel is het gebruik van de [docker-cli](https://docs.docker.com/engine/reference/commandline/cli/).

Gebruik de [ `docker login` opdracht](https://docs.docker.com/engine/reference/commandline/login/), zoals in het volgende voor beeld wordt weer gegeven, om u aan te melden bij `containerpreview.azurecr.io` , het persoonlijke container register voor Cognitive Services containers. Vervang door *\<username\>* de gebruikers naam en *\<password\>* het wacht woord dat is opgegeven in de referenties die u hebt ontvangen van het Azure Cognitive Services-team.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Als u uw referenties in een tekst bestand hebt beveiligd, kunt u de inhoud van het tekst bestand samen voegen met behulp van de `cat` opdracht, `docker login` zoals in het volgende voor beeld wordt weer gegeven. Vervang door *\<passwordFile\>* het pad en de naam van het tekst bestand dat het wacht woord bevat en *\<username\>* met de gebruikers naam die is opgegeven in uw referenties.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```
