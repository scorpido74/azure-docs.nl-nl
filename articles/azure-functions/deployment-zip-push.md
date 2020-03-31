---
title: Zip-push-implementatie voor Azure-functies
description: Gebruik de .zip-bestandsimplementatiefaciliteiten van de Kudu-implementatieservice om uw Azure-functies te publiceren.
ms.topic: conceptual
ms.date: 08/12/2018
ms.openlocfilehash: 6bda0859ca4741fe74f572b204e40130c56c46fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75769661"
---
# <a name="zip-deployment-for-azure-functions"></a>Zip-implementatie voor Azure Functions

In dit artikel wordt beschreven hoe u projectbestanden van uw functie-app naar Azure implementeren vanuit een .zip-bestand (gecomprimeerd). U leert hoe u een push-implementatie doen, zowel door Azure CLI als met behulp van de REST API's. [Azure Functions Core Tools](functions-run-local.md) gebruikt deze implementatie-API's ook bij het publiceren van een lokaal project naar Azure.

Azure Functions heeft het volledige scala aan opties voor continue implementatie en integratie die worden geleverd door Azure App Service. Zie [Continue implementatie voor Azure-functies voor](functions-continuous-deployment.md)meer informatie.

Om de ontwikkeling te versnellen, u het gemakkelijker vinden om uw functie-app projectbestanden rechtstreeks vanuit een .zip-bestand te implementeren. De .zip deployment API neemt de inhoud van een .zip-bestand op en haalt de inhoud uit in de `wwwroot` map van uw functie-app. Deze .zip-bestandsimplementatie maakt gebruik van dezelfde Kudu-service die continue integratiegebaseerde implementaties mogelijk maakt, waaronder:

+ Verwijdering van bestanden die overbleven van eerdere implementaties.
+ Implementatieaanpassingen, inclusief het uitvoeren van implementatiescripts.
+ Implementatielogboeken.
+ Synchronisatiefunctie wordt geactiveerd in een [functie-app Voor het abonnement verbruik.](functions-scale.md)

Zie de verwijzing [naar de implementatie .zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

## <a name="deployment-zip-file-requirements"></a>Vereisten voor implementatie .zip-bestanden

Het .zip-bestand dat u gebruikt voor push-implementatie moet alle bestanden bevatten die nodig zijn om uw functie uit te voeren.

>[!IMPORTANT]
> Wanneer u .zip-implementatie gebruikt, worden alle bestanden van een bestaande implementatie die niet in het .zip-bestand worden gevonden, uit uw functie-app verwijderd.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Een functie-app bevat alle bestanden en `wwwroot` mappen in de map. Een .zip-bestandsimplementatie bevat `wwwroot` de inhoud van de map, maar niet de map zelf. Wanneer u een C#-klassebibliotheekproject implementeert, moet u de `bin` gecompileerde bibliotheekbestanden en -afhankelijkheden opnemen in een submap in uw .zip-pakket.

## <a name="download-your-function-app-files"></a>Uw functie-app-bestanden downloaden

Wanneer u zich ontwikkelt op een lokale computer, u eenvoudig een .zip-bestand maken van de projectmap voor functie-apps op uw ontwikkelingscomputer.

Het is echter mogelijk dat u uw functies hebt gemaakt met behulp van de editor in de Azure-portal. U een bestaand functie-app-project op een van de volgende manieren downloaden:

+ **Vanuit de Azure-portal:**

  1. Meld u aan bij de [Azure-portal](https://portal.azure.com)en ga vervolgens naar uw functie-app.

  2. Selecteer op het tabblad **Overzicht** de optie **App-inhoud downloaden**. Selecteer uw downloadopties en selecteer **Download**.

      ![Het functie-app-project downloaden](./media/deployment-zip-push/download-project.png)

     Het gedownloade .zip-bestand is in de juiste indeling om opnieuw te worden gepubliceerd naar uw functie-app met behulp van .zip push-implementatie. De portal download kan ook de bestanden toevoegen die nodig zijn om uw functie-app rechtstreeks in Visual Studio te openen.

+ **Rest API's gebruiken:**

    Gebruik de volgende implementatie-GET-API `<function_app>` om de bestanden van uw project te downloaden: 

        https://<function_app>.scm.azurewebsites.net/api/zip/site/wwwroot/

    Inclusief `/site/wwwroot/` zorgt ervoor dat uw zip-bestand alleen de projectbestanden van de functie-app bevat en niet de hele site. Als u nog niet bent aangemeld bij Azure, wordt u gevraagd dit te doen.  

Je ook een .zip-bestand downloaden uit een GitHub-repository. Wanneer u een GitHub-repository downloadt als een .zip-bestand, voegt GitHub een extra mapniveau toe voor de branch. Dit extra mapniveau betekent dat u het .zip-bestand niet rechtstreeks implementeren omdat u het hebt gedownload van GitHub. Als u een GitHub-repository gebruikt om uw functie-app te behouden, moet u [continue integratie](functions-continuous-deployment.md) gebruiken om uw app te implementeren.  

## <a name="deploy-by-using-azure-cli"></a><a name="cli"></a>Implementeren met behulp van Azure CLI

U Azure CLI gebruiken om een push-implementatie te activeren. Push een .zip-bestand naar uw functie-app met behulp van de opdracht [az-functieapp-implementatiebron config-zip.](/cli/azure/functionapp/deployment/source#az-functionapp-deployment-source-config-zip) Als u deze opdracht wilt gebruiken, moet u Azure CLI-versie 2.0.21 of hoger gebruiken. Als u wilt zien welke Azure `az --version` CLI-versie u gebruikt, gebruikt u de opdracht.

Vervang in de volgende `<zip_file_path>` opdracht de tijdelijke aanduiding door het pad naar de locatie van het .zip-bestand. Vervang ook `<app_name>` de unieke naam van uw `<resource_group>` functie-app en vervang de naam van uw resourcegroep.

```azurecli-interactive
az functionapp deployment source config-zip -g <resource_group> -n \
<app_name> --src <zip_file_path>
```

Met deze opdracht worden projectbestanden van het gedownloade .zip-bestand geïmplementeerd in uw functie-app in Azure. Vervolgens wordt de app opnieuw opgestart. Als u de lijst met implementaties voor deze functie-app wilt bekijken, moet u de API's van DE REST gebruiken.

Wanneer u Azure CLI op uw `<zip_file_path>` lokale computer gebruikt, is dit het pad naar het zip-bestand op uw computer. U Azure CLI ook uitvoeren in [Azure Cloud Shell.](../cloud-shell/overview.md) Wanneer u Cloud Shell gebruikt, moet u eerst uw implementatie .zip-bestand uploaden naar het Azure Files-account dat is gekoppeld aan uw Cloud Shell. In dat `<zip_file_path>` geval wordt de opslaglocatie gebruikt die uw Cloud Shell-account gebruikt. Zie [Persist-bestanden in Azure Cloud Shell](../cloud-shell/persisting-shell-storage.md)voor meer informatie.

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]

## <a name="run-functions-from-the-deployment-package"></a>Functies uitvoeren vanuit het implementatiepakket

U er ook voor kiezen om uw functies rechtstreeks uit het implementatiepakketbestand uit te voeren. Met deze methode wordt de implementatiestap van het `wwwroot` kopiëren van bestanden van het pakket naar de map van uw functie-app overgeslagen. In plaats daarvan wordt het pakketbestand gemonteerd door de `wwwroot` runtime Functies en wordt de inhoud van de map alleen-lezen.  

Zip-implementatie integreert met deze functie, die u `WEBSITE_RUN_FROM_PACKAGE` inschakelen `1`door de instelling van de functie-app in te stellen op een waarde van. Zie [Uw functies uitvoeren vanuit een implementatiepakketbestand](run-functions-from-deployment-package.md)voor meer informatie.

[!INCLUDE [app-service-deploy-zip-push-custom](../../includes/app-service-deploy-zip-push-custom.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Doorlopende implementatie voor Azure Functions](functions-continuous-deployment.md)

[.zip push deployment reference topic]: https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file
