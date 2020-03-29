---
title: Uw app uitvoeren vanuit een ZIP-pakket
description: Implementeer het ZIP-pakket van uw app met atomiciteit. Verbeter de voorspelbaarheid en betrouwbaarheid van het gedrag van uw app tijdens het ZIP-implementatieproces.
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: 5cc909d79b3f5ea2b4c6a3da12bc7250addbe00c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920719"
---
# <a name="run-your-app-in-azure-app-service-directly-from-a-zip-package"></a>Uw app rechtstreeks vanuit een ZIP-pakket uitvoeren in Azure App Service

In [Azure App Service](overview.md)u uw apps rechtstreeks uitvoeren vanuit een ZIP-pakketbestand voor implementatie. In dit artikel ziet u hoe u deze functionaliteit in uw app inschakelt.

Alle andere implementatiemethoden in App Service hebben iets gemeen: uw bestanden worden geïmplementeerd op *D:\home\site\wwwroot* in uw app (of */home/site/wwwroot* voor Linux-apps). Aangezien dezelfde map door uw app wordt gebruikt tijdens runtime, is het mogelijk dat de implementatie mislukt vanwege conflicten met bestandsvergrendeling en dat de app zich onvoorspelbaar gedraagt omdat sommige bestanden nog niet zijn bijgewerkt.

Wanneer u daarentegen rechtstreeks vanuit een pakket loopt, worden de bestanden in het pakket niet gekopieerd naar de *map wwwroot.* In plaats daarvan wordt het ZIP-pakket zelf direct gemonteerd als de alleen-lezen *wwwroot-directory.* Er zijn verschillende voordelen aan het uitvoeren van rechtstreeks uit een pakket:

- Elimineert bestandsvergrendelingsconflicten tussen implementatie en runtime.
- Zorgt ervoor dat alleen volledig geïmplementeerde apps op elk gewenst moment worden uitgevoerd.
- Kan worden geïmplementeerd in een productie-app (met opnieuw opstarten).
- Verbetert de prestaties van Azure Resource Manager-implementaties.
- Kan koude starttijden verminderen, met name voor JavaScript-functies met grote npm-pakketbomen.

> [!NOTE]
> Momenteel worden alleen ZIP-pakketbestanden ondersteund.

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

## <a name="enable-running-from-package"></a>Uitvoeren vanaf pakket inschakelen

Met `WEBSITE_RUN_FROM_PACKAGE` de app-instelling u vanuit een pakket worden uitgevoerd. Voer de volgende opdracht uit met Azure CLI om deze in te stellen.

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_RUN_FROM_PACKAGE="1"
```

`WEBSITE_RUN_FROM_PACKAGE="1"`hiermee u uw app uitvoeren vanuit een lokaal pakket naar uw app. U ook [vanaf een extern pakket worden uitgevoerd.](#run-from-external-url-instead)

## <a name="run-the-package"></a>Het pakket uitvoeren

De eenvoudigste manier om een pakket in uw App-service uit te voeren, is met de opdracht Azure CLI [az-webapp-implementatiebron config-zip.](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) Bijvoorbeeld:

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src <filename>.zip
```

Omdat `WEBSITE_RUN_FROM_PACKAGE` de app-instelling is ingesteld, haalt deze opdracht de pakketinhoud niet uit naar de map *D:\home\site\wwwroot* van uw app. In plaats daarvan wordt het ZIP-bestand as-is geüpload naar *D:\home\data\SitePackages*en wordt een *packagename.txt* gemaakt in dezelfde map, die de naam bevat van het ZIP-pakket dat tijdens runtime moet worden geladen. Als u uw ZIP-pakket op een andere manier uploadt (zoals [FTP),](deploy-ftp.md)moet u de map *D:\home\data\SitePackages* en het *pakketname.txt-bestand* handmatig maken.

De opdracht start ook de app opnieuw. Omdat `WEBSITE_RUN_FROM_PACKAGE` is ingesteld, App Service monteert het geüploade pakket als de alleen-lezen *wwwroot* directory en draait de app rechtstreeks vanuit die gemonteerde directory.

## <a name="run-from-external-url-instead"></a>In plaats daarvan uitvoeren vanaf externe URL

U een pakket ook uitvoeren vanuit een externe URL, zoals Azure Blob Storage. U de [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) gebruiken om pakketbestanden te uploaden naar uw Blob-opslagaccount. U moet een privéopslagcontainer met een [SAS (Shared Access Signature)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) gebruiken om de runtime van de App-service veilig toegang te geven tot het pakket. 

Zodra u uw bestand hebt geüpload naar blob-opslag `WEBSITE_RUN_FROM_PACKAGE` en een SAS-URL voor het bestand hebt, stelt u de app-instelling in op de URL. In het volgende voorbeeld wordt Azure CLI gebruikt:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="https://myblobstorage.blob.core.windows.net/content/SampleCoreMVCApp.zip?st=2018-02-13T09%3A48%3A00Z&se=2044-06-14T09%3A48%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=bNrVrEFzRHQB17GFJ7boEanetyJ9DGwBSV8OM3Mdh%2FM%3D"
```

Als u een bijgewerkt pakket met dezelfde naam publiceert naar Blob-opslag, moet u uw app opnieuw starten zodat het bijgewerkte pakket wordt geladen in App Service.

## <a name="troubleshooting"></a>Problemen oplossen

- Rechtstreeks vanuit een `wwwroot` pakket uitvoeren maakt alleen-lezen. Uw app ontvangt een foutmelding als deze bestanden naar deze map probeert te schrijven.
- TAR- en GZIP-formaten worden niet ondersteund.
- Deze functie is niet compatibel met [de lokale cache.](overview-local-cache.md)
- Voor verbeterde prestaties bij koude start gebruikt`WEBSITE_RUN_FROM_PACKAGE`u de lokale zip-optie (=1).

## <a name="more-resources"></a>Meer bronnen

- [Continue implementatie voor Azure App Service](deploy-continuous-deployment.md)
- [Code implementeren met een ZIP- of WAR-bestand](deploy-zip.md)
