---
title: bestand opnemen
description: bestand opnemen
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/14/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 4e699707db02de07f3d1ebb7d1fa8d0575a10aa3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67836933"
---
FTP en lokale Git kunnen worden geïmplementeerd in een Azure-webapp met behulp van een *implementatiegebruiker.* Zodra u uw implementatiegebruiker hebt geconfigureerd, u deze gebruiken voor al uw Azure-implementaties. De gebruikersnaam en het wachtwoord van uw accountimplementatie verschillen van uw Azure-abonnementsreferenties. 

Voer de opdracht voor de [gebruikersset van AZ Webapp Deployment in](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) Azure Cloud Shell uit om de implementatiegebruiker te configureren. Vervang \<gebruikersnaam \<> en wachtwoord> door een gebruikersnaam en wachtwoord van de implementatie. 

- De gebruikersnaam moet uniek zijn binnen Azure en voor lokale Git-pushes mag het symbool '@' niet bevatten. 
- Het wachtwoord moet ten minste acht tekens lang zijn, met twee van de volgende drie elementen: letters, cijfers en symbolen. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

De JSON-uitvoer geeft `null`het wachtwoord weer als . Als er een `'Conflict'. Details: 409`-fout optreedt, wijzigt u de gebruikersnaam. Als er een `'Bad Request'. Details: 400`-fout optreedt, kiest u een sterker wachtwoord. 

Neem uw gebruikersnaam en wachtwoord op om te gebruiken om uw web-apps te implementeren.
