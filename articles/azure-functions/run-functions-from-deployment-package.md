---
title: Uw Azure Functions uitvoeren vanuit een pakket
description: Laat de Azure Functions runtime uw functies uitvoeren door een implementatie pakket bestand te koppelen dat de project bestanden van de functie-app bevat.
ms.topic: conceptual
ms.date: 07/15/2019
ms.openlocfilehash: 3ae287939f22469b03f0e10f184f067274464905
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79087032"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>Uw Azure Functions uitvoeren vanuit een pakket bestand

In azure kunt u uw functies rechtstreeks uitvoeren vanuit een implementatie pakket bestand in uw functie-app. De andere optie is het implementeren van uw bestanden in de map `d:\home\site\wwwroot` van uw functie-app.

In dit artikel worden de voor delen beschreven van het uitvoeren van uw functies vanuit een pakket. Ook wordt uitgelegd hoe u deze functie inschakelt in uw functie-app.

> [!IMPORTANT]
> Wanneer u uw functies in een [Premium-abonnement](functions-scale.md#premium-plan)implementeert voor een Linux-functie-app, moet u altijd uitvoeren vanuit het pakket bestand en [uw app publiceren met behulp van de Azure functions core tools](functions-run-local.md#project-file-deployment).

## <a name="benefits-of-running-from-a-package-file"></a>Voor delen van het uitvoeren vanuit een pakket bestand
  
Er zijn verschillende voor delen voor het uitvoeren van een pakket bestand:

+ Hiermee vermindert u het risico dat problemen met het kopiëren van bestanden worden vergrendeld.
+ Kan worden geïmplementeerd in een productie-app (met opnieuw opstarten).
+ U kunt zeker zijn van de bestanden die in uw app worden uitgevoerd.
+ Verbetert de prestaties van [Azure Resource Manager implementaties](functions-infrastructure-as-code.md).
+ Kan koude begin tijden verminderen, met name voor Java script-functies met grote NPM-pakket structuren.

Zie [deze aankondiging](https://github.com/Azure/app-service-announcements/issues/84)voor meer informatie.

## <a name="enabling-functions-to-run-from-a-package"></a>Functies inschakelen om uit te voeren vanuit een pakket

Als u wilt dat uw functie-app vanuit een pakket wordt uitgevoerd, voegt u alleen een `WEBSITE_RUN_FROM_PACKAGE`-instelling toe aan de instellingen van uw functie-app. De instelling `WEBSITE_RUN_FROM_PACKAGE` kan een van de volgende waarden hebben:

| Waarde  | Beschrijving  |
|---------|---------|
| **`1`**  | Aanbevolen voor functie-apps die worden uitgevoerd in Windows. Voer uit vanuit een pakket bestand in de map `d:\home\data\SitePackages` van uw functie-app. Als deze optie niet wordt [geïmplementeerd met zip Deploy](#integration-with-zip-deployment), moet de map ook een bestand bevatten met de naam `packagename.txt`. Dit bestand bevat alleen de naam van het pakket bestand in de map, zonder spaties. |
|**`<URL>`**  | Locatie van een specifiek pakket bestand dat u wilt uitvoeren. Bij het gebruik van Blob Storage moet u een persoonlijke container met een [Shared Access Signature (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) gebruiken om de runtime van de functies in te scha kelen voor toegang tot het pakket. U kunt de [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) gebruiken om pakket bestanden te uploaden naar uw Blob Storage-account. Wanneer u een URL opgeeft, moet u ook [Triggers synchroniseren](functions-deployment-technologies.md#trigger-syncing) nadat u een bijgewerkt pakket hebt gepubliceerd. |

> [!CAUTION]
> Bij het uitvoeren van een functie-app in Windows levert de optie externe URL slechterere prestaties bij koude start. Wanneer u de functie-app in Windows implementeert, moet u `WEBSITE_RUN_FROM_PACKAGE` instellen op `1` en publiceren met de zip-implementatie.

Hieronder ziet u een functie-app die is geconfigureerd om te worden uitgevoerd vanuit een zip-bestand dat wordt gehost in Azure Blob Storage:

![App-instelling WEBSITE_RUN_FROM_ZIP](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> Momenteel worden alleen zip-pakket bestanden ondersteund.

## <a name="integration-with-zip-deployment"></a>Integratie met zip-implementatie

Een [zip-implementatie][Zip deployment for Azure Functions] is een functie van Azure app service waarmee u uw functie-app-project kunt implementeren in de `wwwroot` Directory. Het project wordt verpakt als een zip-implementatie bestand. Dezelfde Api's kunnen worden gebruikt om uw pakket te implementeren in de map `d:\home\data\SitePackages`. Met de `WEBSITE_RUN_FROM_PACKAGE` app-instellings waarde van `1`kopieert de zip-implementatie-Api's uw pakket naar de map `d:\home\data\SitePackages` in plaats van de bestanden uit te pakken naar `d:\home\site\wwwroot`. Ook wordt het `packagename.txt`-bestand gemaakt. Na het opnieuw opstarten wordt het pakket gekoppeld aan `wwwroot` als een alleen-lezen bestands systeem. Zie voor meer informatie over de implementatie van zip-implementatie [voor Azure functions](deployment-zip-push.md).

## <a name="adding-the-website_run_from_package-setting"></a>De instelling WEBSITE_RUN_FROM_PACKAGE toevoegen

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

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

- [Key Vault verwijzingen voor App Service](../app-service/app-service-key-vault-references.md)
- [Azure Storage versleuteling voor Data-at-rest](../storage/common/storage-service-encryption.md)

## <a name="troubleshooting"></a>Problemen oplossen

- Uitvoeren vanaf pakket maakt `wwwroot` alleen-lezen, dus er wordt een fout bericht weer gegeven wanneer u bestanden naar deze map schrijft.
- Tar-en gzip-indelingen worden niet ondersteund.
- Deze functie is niet samen met de lokale cache.
- Gebruik de lokale zip-optie (`WEBSITE_RUN_FROM_PACKAGE`= 1) voor verbeterde prestaties van koud start.
- Uitvoeren vanaf pakket is niet compatibel met de optie voor het aanpassen van de implementatie (`SCM_DO_BUILD_DURING_DEPLOYMENT=true`). de stap build wordt tijdens de implementatie genegeerd.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Doorlopende implementatie voor Azure Functions](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md
