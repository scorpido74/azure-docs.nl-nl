---
title: Include-bestand
description: Include-bestand
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/14/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 4e699707db02de07f3d1ebb7d1fa8d0575a10aa3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67836933"
---
FTP en lokale Git kunnen worden geïmplementeerd in een Azure-web-app met behulp van een *implementatie gebruiker*. Wanneer u de implementatie gebruiker hebt geconfigureerd, kunt u deze gebruiken voor al uw Azure-implementaties. De gebruikers naam en het wacht woord voor implementatie op account niveau verschillen van de referenties van uw Azure-abonnement. 

Als u de implementatie gebruiker wilt configureren, voert u de opdracht [AZ webapp Deployment User set](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) uit in azure Cloud shell. Vervang \<gebruikers> naam \<en wacht woord> door een gebruikers naam en wacht woord voor de implementatie gebruiker. 

- De gebruikers naam moet uniek zijn binnen Azure en voor lokale Git-pushes mag het symbool ' @ ' niet bevatten. 
- Het wacht woord moet ten minste acht tekens lang zijn, met twee van de volgende drie elementen: letters, cijfers en symbolen. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

De JSON-uitvoer toont het wacht `null`woord als. Als er een `'Conflict'. Details: 409`-fout optreedt, wijzigt u de gebruikersnaam. Als er een `'Bad Request'. Details: 400`-fout optreedt, kiest u een sterker wachtwoord. 

Registreer de gebruikers naam en het wacht woord die u wilt gebruiken om uw web-apps te implementeren.
