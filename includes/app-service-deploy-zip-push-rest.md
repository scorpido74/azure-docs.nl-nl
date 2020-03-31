---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 08/12/2019
ms.author: cephalin
ms.openlocfilehash: 92e39f128e90ba83a919388e217f0edc86f81770
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75769660"
---
## <a name="deploy-zip-file-with-rest-apis"></a><a name="rest"></a>ZIP-bestand implementeren met REST API's 

U de [REST-API's van](https://github.com/projectkudu/kudu/wiki/REST-API) de implementatieservice gebruiken om het .zip-bestand te implementeren in uw app in Azure. Als u wilt implementeren, stuurt u een POST-verzoek naar https://<app_name>.scm.azurewebsites.net/api/zipdeploy. Het POST-verzoek moet het .zip-bestand in de berichttekst bevatten. De implementatiereferenties voor uw app moet u opgegeven in de aanvraag met behulp van HTTP-basisverificatie. Zie de verwijzing naar de [implementatie van .zip push](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file). 

Voor de HTTP BASIC-verificatie hebt u uw implementatiereferenties van de App Service nodig. Zie Referenties op [gebruikersniveau instellen en opnieuw instellen](../articles/app-service/deploy-configure-credentials.md#userscope)als u wilt zien hoe u uw implementatiereferenties instelt.

### <a name="with-curl"></a>Met cURL

In het volgende voorbeeld wordt het gereedschap cURL gebruikt om een .zip-bestand te implementeren. Vervang de `<deployment_user>`tijdelijke `<zip_file_path>`aanduidingen , en `<app_name>`. Wanneer u wordt gevraagd door cURL, typt u het wachtwoord in.

```bash
curl -X POST -u <deployment_user> --data-binary @"<zip_file_path>" https://<app_name>.scm.azurewebsites.net/api/zipdeploy
```

Met deze aanvraag wordt push-implementatie geactiveerd vanuit het geüploade .zip-bestand. U de huidige en eerdere `https://<app_name>.scm.azurewebsites.net/api/deployments` implementaties bekijken met behulp van het eindpunt, zoals wordt weergegeven in het volgende cURL-voorbeeld. Nogmaals, `<app_name>` vervang met de naam `<deployment_user>` van uw app en met de gebruikersnaam van uw implementatiereferenties.

```bash
curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/deployments
```

### <a name="with-powershell"></a>Met PowerShell

In het volgende voorbeeld wordt [gebruikgenomen van Publish-AzWebapp](/powershell/module/az.websites/publish-azwebapp) om het .zip-bestand te uploaden. Vervang de `<group-name>`tijdelijke `<app-name>`aanduidingen , en `<zip-file-path>`.

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <zip-file-path>
```

Met deze aanvraag wordt push-implementatie geactiveerd vanuit het geüploade .zip-bestand. 

Voer de volgende opdrachten uit om de huidige en eerdere implementaties te controleren. Nogmaals, vervang `<deployment-user>` `<deployment-password>`de `<app-name>` , , en tijdelijke aanduidingen.

```bash
$username = "<deployment-user>"
$password = "<deployment-password>"
$apiUrl = "https://<app-name>.scm.azurewebsites.net/api/deployments"
$base64AuthInfo = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $username, $password)))
$userAgent = "powershell/1.0"
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -UserAgent $userAgent -Method GET
```
