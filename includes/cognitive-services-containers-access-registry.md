---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b9d0d2b97472eb3264f5e4600fddbfc7d3250918
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67704181"
---
## <a name="use-the-docker-cli-to-authenticate-the-private-container-registry"></a>De Docker CLI gebruiken om het register van privécontainers te verifiëren

U op verschillende manieren verifiëren met het register van privécontainers voor Cognitive Services-containers, maar de aanbevolen methode van de opdrachtregel is het gebruik van de [Docker CLI.](https://docs.docker.com/engine/reference/commandline/cli/)

Gebruik [ `docker login` ](https://docs.docker.com/engine/reference/commandline/login/)de opdracht , zoals in het volgende `containerpreview.azurecr.io`voorbeeld wordt weergegeven, om in te loggen bij het privécontainerregister voor cognitive services-containers. Vervang * \<\> gebruikersnaam* door de gebruikersnaam en * \<het wachtwoord\> * door het wachtwoord dat is opgegeven in de referenties die u hebt ontvangen van het Azure Cognitive Services-team.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Als u uw referenties in een tekstbestand hebt beveiligd, u de inhoud van `cat` dat tekstbestand `docker login` met behulp van de opdracht aan de opdracht toevoegen, zoals in het volgende voorbeeld wordt weergegeven. Wachtwoord * \<vervangenBestand\> * door het pad en de naam van het tekstbestand dat het wachtwoord en * \<\> * de gebruikersnaam bevat, door de gebruikersnaam die in uw referenties is opgegeven.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```
