---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b9d0d2b97472eb3264f5e4600fddbfc7d3250918
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/28/2019
ms.locfileid: "67704181"
---
## <a name="use-the-docker-cli-to-authenticate-the-private-container-registry"></a>De docker-CLI gebruiken om het persoonlijke container register te verifiëren

U kunt zich op verschillende manieren verifiëren met het persoonlijke container register voor Cognitive Services containers, maar de aanbevolen methode van de opdracht regel is het gebruik van de [docker-cli](https://docs.docker.com/engine/reference/commandline/cli/).

Gebruik de [opdracht`docker login`](https://docs.docker.com/engine/reference/commandline/login/), zoals in het volgende voor beeld wordt weer gegeven, om u aan te melden bij `containerpreview.azurecr.io`, het persoonlijke container register voor Cognitive Services containers. Vervang *\<gebruikersnaam\>* door de gebruikers naam\<en het *wacht woord\>* door het wacht woord dat is opgegeven in de referenties die u hebt ontvangen van het Cognitive Services team van Azure.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Als u uw referenties in een tekst bestand hebt beveiligd, kunt u de inhoud van het tekst bestand samen voegen met behulp van de `cat` opdracht tot de `docker login` opdracht, zoals in het volgende voor beeld wordt weer gegeven. Vervang *\<passwordFile\>* door het pad en de naam van het tekst bestand dat het wacht woord bevat en *\<username\>* met de gebruikers naam die is opgegeven in uw referenties.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```
