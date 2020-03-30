---
title: Code implementeren met een ZIP- of WAR-bestand
description: Meer informatie over het implementeren van uw app naar Azure App Service met een ZIP-bestand (of een WAR-bestand voor Java-ontwikkelaars).
ms.topic: article
ms.date: 08/12/2019
ms.reviewer: sisirap
ms.custom: seodec18
ms.openlocfilehash: 716f6813e37aec086a7d496e001fe2ca0f4aab57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75945143"
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-or-war-file"></a>Uw app implementeren op Azure App Service met een ZIP- of WAR-bestand

In dit artikel ziet u hoe u een ZIP-bestand of WAR-bestand gebruikt om uw web-app te implementeren in [Azure App Service.](overview.md) 

Deze ZIP-bestandsimplementatie maakt gebruik van dezelfde Kudu-service die continue integratiegebaseerde implementaties mogelijk maakt. Kudu ondersteunt de volgende functionaliteit voor zip-bestandsimplementatie: 

- Verwijdering van bestanden die overblijven van een eerdere implementatie.
- Optie om het standaardbuildproces in te schakelen, inclusief pakketherstel.
- Implementatieaanpassingen, inclusief het uitvoeren van implementatiescripts.  
- Implementatielogboeken. 
- Een limiet voor bestandsgrootte van 2048 MB.

Zie onze [Kudu-documentatie](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file) voor meer informatie.

Met de implementatie van het WAR-bestand wordt uw [WAR-bestand](https://wikipedia.org/wiki/WAR_(file_format)) geïmplementeerd in de App-service om uw Java-web-app uit te voeren. Zie [WAR-bestand implementeren](#deploy-war-file).

## <a name="prerequisites"></a>Vereisten

Als u de stappen in dit artikel wilt voltooien, [maakt u een App Service-app](/azure/app-service/)of gebruikt u een app die u hebt gemaakt voor een andere zelfstudie.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]
Het bovenstaande eindpunt werkt op dit moment niet voor Linux App Services. Overweeg in plaats daarvan FTP of de [ZIP-deploy-API te](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-faq#continuous-integration-and-deployment) gebruiken.

## <a name="deploy-zip-file-with-azure-cli"></a>ZIP-bestand implementeren met Azure CLI

Implementeer het geüploade ZIP-bestand naar uw web-app met behulp van de [opdracht az webapp-implementatiebron config-zip.](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip)  

In het volgende voorbeeld wordt het ZIP-bestand geïmplementeerd dat u hebt geüpload. Wanneer u een lokale installatie van Azure CLI gebruikt, `--src`geeft u het pad naar uw lokale ZIP-bestand op voor .

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src clouddrive/<filename>.zip
```

Met deze opdracht worden de bestanden en mappen uit het ZIP-bestand geïmplementeerd in de standaardmap voor de App Service-toepassing (`\home\site\wwwroot`) en wordt de app opnieuw opgestart.

Standaard gaat de implementatieengine ervan uit dat een ZIP-bestand klaar is om te draaien zoals het is en geen buildautomatisering uitvoert. Als u dezelfde buildautomatisering wilt inschakelen als `SCM_DO_BUILD_DURING_DEPLOYMENT` in een [Git-implementatie,](deploy-local-git.md)stelt u de app-instelling in door de volgende opdracht uit te voeren in de Cloud [Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings SCM_DO_BUILD_DURING_DEPLOYMENT=true
```

Zie onze [Kudu-documentatie](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url) voor meer informatie.

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="deploy-war-file"></a>WAR-bestand implementeren

Als u een WAR-bestand wilt implementeren `https://<app-name>.scm.azurewebsites.net/api/wardeploy`in App-service, stuurt u een POST-verzoek naar . Het WAR-bestand moet zijn opgenomen in de hoofdtekst van de POST-aanvraag. De implementatiereferenties voor uw app moet u opgegeven in de aanvraag met behulp van HTTP-basisverificatie.

Altijd `/api/wardeploy` gebruiken bij het implementeren van WAR-bestanden. Deze API breidt uw WAR-bestand uit en plaatst het op het gedeelde bestandsstation. het gebruik van andere implementatie-API's kan leiden tot inconsistent gedrag. 

Voor de HTTP BASIC-verificatie hebt u uw implementatiereferenties van de App Service nodig. Zie Referenties op [gebruikersniveau instellen en opnieuw instellen](deploy-configure-credentials.md#userscope)als u wilt zien hoe u uw implementatiereferenties instelt.

### <a name="with-curl"></a>Met cURL

In het volgende voorbeeld wordt het cURL-gereedschap gebruikt om een .war-bestand te implementeren. Vervang de `<username>`tijdelijke `<war-file-path>`aanduidingen , en `<app-name>`. Wanneer u wordt gevraagd door cURL, typt u het wachtwoord in.

```bash
curl -X POST -u <username> --data-binary @"<war-file-path>" https://<app-name>.scm.azurewebsites.net/api/wardeploy
```

### <a name="with-powershell"></a>Met PowerShell

In het volgende voorbeeld wordt [gebruik gemaakt van Publish-AzWebapp](/powershell/module/az.websites/publish-azwebapp) upload het .war-bestand. Vervang de `<group-name>`tijdelijke `<app-name>`aanduidingen , en `<war-file-path>`.

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <war-file-path>
```

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Volgende stappen

Probeer voor meer geavanceerde implementatiescenario's [implementeren in Azure met Git](deploy-local-git.md). Git-gebaseerde implementatie naar Azure maakt versiebeheer, pakketherstel, MSBuild en meer mogelijk.

## <a name="more-resources"></a>Meer bronnen

* [Kudu: Implementeren vanuit een zip-bestand](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Referenties voor Azure App-service-implementatie](deploy-ftp.md)
