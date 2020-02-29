---
title: Uw app uitvoeren vanuit een ZIP-pakket
description: Implementeer het ZIP-pakket van uw app met behulp van atomisch. Verbeter de voorspel baarheid en betrouw baarheid van het gedrag van uw app tijdens het ZIP-implementatie proces.
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: 316ada7700a5cf45ee90f515336039702bab48c0
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920719"
---
# <a name="run-your-app-in-azure-app-service-directly-from-a-zip-package"></a>Uw app rechtstreeks vanuit een ZIP-pakket uitvoeren in Azure App Service

In [Azure app service](overview.md)kunt u uw apps rechtstreeks vanuit een zip-pakket bestand voor implementatie uitvoeren. In dit artikel wordt beschreven hoe u deze functionaliteit in uw app inschakelt.

Alle andere implementatie methoden in App Service hebben een gemeen schappelijke waarde: uw bestanden worden geïmplementeerd in *D:\home\site\wwwroot* in uw app (of */Home/site/wwwroot* voor Linux-apps). Aangezien dezelfde map tijdens runtime wordt gebruikt door uw app, is het mogelijk dat de implementatie mislukt omdat er conflicten zijn opgetreden in de bestands vergrendeling, waardoor de app onvoorspelbaar werkt omdat sommige bestanden nog niet zijn bijgewerkt.

Als u daarentegen rechtstreeks vanuit een pakket uitvoert, worden de bestanden in het pakket niet gekopieerd naar de map *wwwroot* . In plaats daarvan wordt het ZIP-pakket zelf direct gekoppeld als de alleen-lezen *wwwroot* -Directory. Er zijn verschillende voor delen die rechtstreeks vanuit een pakket kunnen worden uitgevoerd:

- Elimineert conflicten tussen bestands vergrendelingen tussen implementatie en runtime.
- Hiermee wordt gegarandeerd dat er op elk gewenst moment alleen apps met volledige implementatie worden uitgevoerd.
- Kan worden geïmplementeerd in een productie-app (met opnieuw opstarten).
- Verbetert de prestaties van Azure Resource Manager implementaties.
- Kan koude begin tijden verminderen, met name voor Java script-functies met grote NPM-pakket structuren.

> [!NOTE]
> Op dit moment worden alleen ZIP-pakket bestanden ondersteund.

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

## <a name="enable-running-from-package"></a>Uitvoeren vanaf pakket inschakelen

Met de instelling app-`WEBSITE_RUN_FROM_PACKAGE` kan vanuit een pakket worden uitgevoerd. Als u deze wilt instellen, voert u de volgende opdracht uit met Azure CLI.

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_RUN_FROM_PACKAGE="1"
```

met `WEBSITE_RUN_FROM_PACKAGE="1"` kunt u uw app uitvoeren vanuit een pakket dat lokaal is voor uw app. U kunt ook [uitvoeren vanuit een extern pakket](#run-from-external-url-instead).

## <a name="run-the-package"></a>Het pakket uitvoeren

De eenvoudigste manier om een pakket in uw App Service uit te voeren, is met de Azure CLI [AZ webapp Deployment source config-](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) opdracht. Bijvoorbeeld:

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src <filename>.zip
```

Omdat de instelling van de app `WEBSITE_RUN_FROM_PACKAGE` is ingesteld, wordt met deze opdracht niet de inhoud van het pakket uitgepakt naar de map *D:\home\site\wwwroot* van uw app. In plaats daarvan uploadt het ZIP-bestand naar *D:\home\data\SitePackages*en maakt het een *pakket naam. txt* in dezelfde map. Dit bevat onder andere het zip-pakket dat tijdens runtime moet worden geladen. Als u uw ZIP-pakket op een andere manier uploadt (zoals [FTP](deploy-ftp.md)), moet u de *D:\home\data\SitePackages* -map en het bestand *packagenaam. txt* hand matig maken.

Met deze opdracht wordt ook de app opnieuw gestart. Omdat `WEBSITE_RUN_FROM_PACKAGE` is ingesteld, App Service het geüploade pakket koppelen als de alleen-lezen *wwwroot* -map en wordt de app rechtstreeks vanuit die gekoppelde map uitgevoerd.

## <a name="run-from-external-url-instead"></a>Uitvoeren vanaf externe URL

U kunt ook een pakket uitvoeren vanuit een externe URL, zoals Azure Blob Storage. U kunt de [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) gebruiken om pakket bestanden te uploaden naar uw Blob Storage-account. U moet een persoonlijke opslag container met een [Shared Access Signature (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) gebruiken om de app service runtime in staat te stellen om veilig toegang te krijgen tot het pakket. 

Wanneer u uw bestand uploadt naar Blob-opslag en een SAS-URL voor het bestand hebt, stelt u de `WEBSITE_RUN_FROM_PACKAGE` app-instelling in op de URL. In het volgende voor beeld wordt dit gedaan met behulp van Azure CLI:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="https://myblobstorage.blob.core.windows.net/content/SampleCoreMVCApp.zip?st=2018-02-13T09%3A48%3A00Z&se=2044-06-14T09%3A48%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=bNrVrEFzRHQB17GFJ7boEanetyJ9DGwBSV8OM3Mdh%2FM%3D"
```

Als u een bijgewerkt pakket met dezelfde naam naar de Blob-opslag publiceert, moet u de app opnieuw opstarten zodat het bijgewerkte pakket in App Service wordt geladen.

### <a name="use-key-vault-references"></a>Key Vault verwijzingen gebruiken

Voor extra beveiliging kunt u Key Vault verwijzingen gebruiken in combi natie met uw externe URL. Hierdoor blijft de URL op rest versleuteld en kunt u gebruikmaken van Key Vault voor geheim beheer en draaiing. Het is raadzaam om Azure Blob-opslag te gebruiken, zodat u de bijbehorende SAS-sleutel eenvoudig kunt draaien. Azure Blob-opslag wordt versleuteld op rest, waardoor uw toepassings gegevens veilig blijven wanneer deze niet is geïmplementeerd op App Service.

1. Maak een Azure Key Vault.

    ```azurecli
    az keyvault create --name "Contoso-Vault" --resource-group <group-name> --location eastus
    ```

1. Voeg uw externe URL toe als geheim in Key Vault.

    ```azurecli
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<insert-your-URL>"
    ```

1. Maak de `WEBSITE_RUN_FROM_PACKAGE` app-instelling en stel de waarde in als een Key Vault verwijzing naar de externe URL.

    ```azurecli
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"
    ```

Raadpleeg de volgende artikelen voor meer informatie.

- [Key Vault verwijzingen voor App Service](app-service-key-vault-references.md)
- [Azure Storage versleuteling voor Data-at-rest](../storage/common/storage-service-encryption.md)

## <a name="troubleshooting"></a>Problemen oplossen

- Het rechtstreeks uitvoeren vanuit een pakket maakt `wwwroot` alleen-lezen. Uw app krijgt een fout melding als wordt geprobeerd om bestanden naar deze map te schrijven.
- TAR-en GZIP-indelingen worden niet ondersteund.
- Deze functie is niet compatibel met de [lokale cache](overview-local-cache.md).
- Gebruik de lokale zip-optie (`WEBSITE_RUN_FROM_PACKAGE`= 1) voor verbeterde prestaties van koud start.

## <a name="more-resources"></a>Meer bronnen

- [Continue implementatie voor Azure App Service](deploy-continuous-deployment.md)
- [Code implementeren met een ZIP-of WAR-bestand](deploy-zip.md)
