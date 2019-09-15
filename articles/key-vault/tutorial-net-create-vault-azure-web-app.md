---
title: 'Zelfstudie: Azure Key Vault gebruiken met een Azure-web-app in .NET | Microsoft Docs'
description: In deze zelf studie configureert u een ASP.NET-kern toepassing voor het lezen van een geheim in uw sleutel kluis.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 84256d79ec543d038b4d3d3f3dc6901bbd003871
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003364"
---
# <a name="tutorial-use-azure-key-vault-with-an-azure-web-app-in-net"></a>Zelfstudie: Azure Key Vault gebruiken met een Azure-web-app in .NET

Met Azure Key Vault kunt u geheimen beveiligen, zoals API-sleutels en databaseverbindingsreeksen. Het biedt u toegang tot uw toepassingen, services en IT-resources.

In deze zelfstudie leert u hoe u een Azure-webtoepassing maakt waarmee gegevens uit een Azure-sleutelkluis kunnen worden gelezen. Tijdens dit proces wordt gebruikgemaakt van beheerde identiteiten voor Azure-resources. Zie [Azure App Service](../app-service/overview.md) voor meer informatie over Azure-webtoepassingen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een sleutelkluis maken.
> * Voeg een geheim toe aan de sleutelkluis.
> * Een geheim ophalen uit de sleutelkluis.
> * Een Azure-web-app maken.
> * Een beheerde identiteit inschakelen voor de web-app.
> * Machtiging voor de web-app toewijzen.
> * Voer de web-app uit op Azure.

Lees [Key Vault basis concepten](basic-concepts.md)voordat u begint. 

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Vereisten

* Voor Windows: [.net Core 2,1 SDK of hoger](https://www.microsoft.com/net/download/windows)
* Voor Mac: [Visual Studio voor Mac](https://visualstudio.microsoft.com/vs/mac/)
* Voor Windows, Mac en Linux:
  * [Git](https://git-scm.com/downloads)
  * Voor deze zelfstudie moet u de Azure CLI lokaal uitvoeren. U moet de Azure CLI-versie 2.0.4 of hoger hebben geïnstalleerd. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](https://docs.microsoft.com/cli/azure/install-azure-cli).
  * [.NET Core](https://www.microsoft.com/net/download/dotnet-core/2.1)

## <a name="about-managed-service-identity"></a>Informatie over Managed Service Identity

Azure Key Vault referenties veilig opgeslagen, zodat ze niet in uw code worden weer gegeven. U moet u echter verifiëren bij Azure Key Vault om uw sleutels op te halen. En om u te verifiëren bij Key Vault, hebt u een referentie nodig. Dit is een klassiek bootstrap-dilemma. In MSI (Managed Service Identity) is dit probleem opgelost doordat er een _bootstrap-identiteit_ wordt geboden dat het proces vereenvoudigt.

Wanneer u MSI inschakelt voor een Azure-service, zoals Azure Virtual Machines, Azure App Service of Azure Functions, maakt Azure een [Service-Principal](basic-concepts.md). MSI doet dit voor het exemplaar van de service in Azure Active Directory (Azure AD) en activeert de referenties van de Service-Principal in dat exemplaar.

![MSI-diagram](media/MSI.png)

Om een toegangs token op te halen, roept uw code een lokale meta gegevens service aan die beschikbaar is op de Azure-resource. Uw code gebruikt het toegangstoken dat wordt verkregen van het lokale MSI-eindpunt om zich te authenticeren bij een Azure Key Vault-service.

## <a name="log-in-to-azure"></a>Meld u aan bij Azure.

Als u zich bij Azure wilt aanmelden met de Azure CLI, voert u het volgende in:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

Maak een resourcegroep met de opdracht [az group create](/cli/azure/group#az-group-create).

Selecteer vervolgens de naam van een resource groep en vul de tijdelijke aanduiding in. In het volgende voorbeeld wordt een resourcegroep gemaakt in de regio US - west:

   ```azurecli
   # To list locations: az account list-locations --output table
   az group create --name "<YourResourceGroupName>" --location "West US"
   ```

U gebruikt deze resourcegroep in de hele zelfstudie.

## <a name="create-a-key-vault"></a>Een sleutelkluis maken

Als u een sleutelkluis in de resourcegroep wilt maken, biedt u de volgende informatie:

* Sleutel kluis naam: een teken reeks van 3 tot 24 tekens die alleen getallen (0-9), letters (a-z, A-Z) en afbreek streepjes (-) kan bevatten
* Resourcegroepnaam
* Locatie: **US - west**

Voer in de Azure CLI de volgende opdracht in:

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

Op dit moment is uw Azure-account de enige die gemachtigd is voor het uitvoeren van bewerkingen op deze nieuwe kluis.

## <a name="add-a-secret-to-the-key-vault"></a>Een geheim toevoegen aan de sleutelkluis

U kunt nu een geheim toevoegen. Dit kan een SQL-verbindingsreeks zijn of enige andere gegevens die u zowel veilig als beschikbaar wilt houden op de toepassing.

Als u een geheim wilt maken in de sleutel kluis met de naam **AppSecret**, voert u de volgende opdracht in: 

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Met dit geheim wordt de waarde **MijnGeheim** opgeslagen.

Als u de waarde in het geheim als tekst zonder opmaak wilt weer geven, voert u de volgende opdracht in:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Met deze opdracht wordt de geheime informatie, inclusief de URI, weer gegeven. 

Wanneer u deze stappen hebt uitgevoerd, beschikt u over een URI voor een geheim in een sleutelkluis. Noteer deze informatie voor later gebruik in deze zelf studie. 

## <a name="create-a-net-core-web-app"></a>Een .NET Core-web-app maken

Als u een .NET core-web-app wilt maken en deze wilt publiceren naar Azure, volgt u de instructies in [een ASP.net core web-app maken in azure](../app-service/app-service-web-get-started-dotnet.md). 

U kunt ook deze video bekijken:

>[!VIDEO https://www.youtube.com/embed/EdiiEH7P-bU]

## <a name="open-and-edit-the-solution"></a>De oplossing openen en bewerken

1. Ga naar het bestand **Pages** > **about.cshtml.cs** .

1. Installeer deze NuGet-pakketten:
   - [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)
   - [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)

1. Importeer de volgende code in het *about.cshtml.cs* -bestand:

   ```csharp
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.KeyVault.Models;
    using Microsoft.Azure.Services.AppAuthentication;
   ```

   De code in de AboutModel-klasse moet er als volgt uitzien:

   ```csharp
    public class AboutModel : PageModel
    {
        public string Message { get; set; }

        public async Task OnGetAsync()
        {
            Message = "Your application description page.";
            int retries = 0;
            bool retry = false;
            try
            {
                /* The next four lines of code show you how to use AppAuthentication library to fetch secrets from your key vault */
                AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
                KeyVaultClient keyVaultClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
                var secret = await keyVaultClient.GetSecretAsync("https://<YourKeyVaultName>.vault.azure.net/secrets/AppSecret")
                        .ConfigureAwait(false);
                Message = secret.Value;
            }
            /* If you have throttling errors see this tutorial https://docs.microsoft.com/azure/key-vault/tutorial-net-create-vault-azure-web-app */
            /// <exception cref="KeyVaultErrorException">
            /// Thrown when the operation returned an invalid status code
            /// </exception>
            catch (KeyVaultErrorException keyVaultException)
            {
                Message = keyVaultException.Message;
            }
        }

        // This method implements exponential backoff if there are 429 errors from Azure Key Vault
        private static long getWaitTime(int retryCount)
        {
            long waitTime = ((long)Math.Pow(2, retryCount) * 100L);
            return waitTime;
        }

        // This method fetches a token from Azure Active Directory, which can then be provided to Azure Key Vault to authenticate
        public async Task<string> GetAccessTokenAsync()
        {
            var azureServiceTokenProvider = new AzureServiceTokenProvider();
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
            return accessToken;
        }
    }
    ```

## <a name="run-the-web-app"></a>De web-app uitvoeren

1. Selecteer in het hoofd menu van Visual Studio 2019 **debug** > **Start**, met of zonder fout opsporing. 
1. Ga in de browser naar de pagina **over** .  
    De waarde voor **AppGeheim** wordt weergegeven.

## <a name="enable-a-managed-identity"></a>Een beheerde identiteit inschakelen

Azure Key Vault biedt een manier voor het veilig opslaan van referenties en andere geheimen, maar uw code moet worden geverifieerd bij Key Vault om ze op te halen. Het [Overzicht van beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md) helpt bij het oplossen van dit probleem door Azure-services een automatisch beheerde identiteit in Azure AD te geven. U kunt deze identiteit gebruiken voor verificatie bij elke service die ondersteuning biedt voor Azure AD-verificatie, met inbegrip van Key Vault, zonder dat u referenties in uw code hoeft weer te geven.

Voer de volgende opdracht uit voor het maken van de identiteit voor deze toepassing in de Azure CLI:

```azurecli
az webapp identity assign --name "<YourAppName>" --resource-group "<YourResourceGroupName>"
```

Vervang \<YourAppName > door de naam van de gepubliceerde app op Azure.  
    Als de naam van uw gepubliceerde app bijvoorbeeld **MyAwesomeapp.azurewebsites.net**is, vervangt \<u YourAppName > door **MyAwesomeapp**.

Noteer de `PrincipalId` wanneer u de toepassing publiceert in Azure. De uitvoer van de opdracht in stap 1 moet de volgende indeling hebben:

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

>[!NOTE]
>De opdracht in deze procedure is het equivalent van het naar de [Azure Portal](https://portal.azure.com) gaan en de instelling van de **identiteit/het systeem** in de eigenschappen **van** de webtoepassing wijzigt in.

## <a name="assign-permissions-to-your-app"></a>Machtigingen toewijzen aan uw app

Vervang \<YourKeyVaultName > door de naam van uw sleutel kluis en vervang \<PrincipalId > door de waarde van de **PrincipalId** in de volgende opdracht:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list
```

Met deze opdracht geeft u de identiteit (MSI) van de app service-machtiging voor het **ophalen** en **weer geven** van bewerkingen op uw sleutel kluis.

## <a name="publish-the-web-app-to-azure"></a>De web-app publiceren in Azure

Publiceer opnieuw uw web-app naar Azure om te controleren of uw Live Web-App de geheime waarde kan ophalen.

1. In Visual Studio selecteert u het project **key-vault-dotnet-core-quickstart**.
2. Selecteer **Publiceren** > **Start**.
3. Selecteer **Maken**.

Wanneer u de toepassing uitvoert, ziet u, als het goed is, dat de geheime waarde wordt opgehaald.

Nu hebt u een web-app in .NET gemaakt waarin de geheimen van uw sleutel kluis worden opgeslagen en opgehaald.

## <a name="clean-up-resources"></a>Resources opschonen
Als ze niet meer nodig zijn, kunt u de virtuele machine en de sleutel kluis verwijderen.

## <a name="next-steps"></a>Volgende stappen

>[!div class="nextstepaction"]
>[Gids voor Azure Key Vault-ontwikkelaars](key-vault-developers-guide.md)
