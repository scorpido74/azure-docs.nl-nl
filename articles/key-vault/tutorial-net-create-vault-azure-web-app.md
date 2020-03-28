---
title: 'Zelfstudie: Azure Key Vault gebruiken met een Azure-web-app in .NET | Microsoft Docs'
description: In deze zelfstudie configureert u een ASP.NET kerntoepassing om een geheim uit uw sleutelkluis te lezen.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: e636ab843a9801097bf770ca12c9d1e512750c91
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78198113"
---
# <a name="tutorial-use-azure-key-vault-with-an-azure-web-app-in-net"></a>Zelfstudie - Azure Key Vault gebruiken met een Azure-web-app in .NET

Met Azure Key Vault kunt u geheimen beveiligen, zoals API-sleutels en databaseverbindingsreeksen. Het biedt u toegang tot uw toepassingen, services en IT-resources.

In deze zelfstudie leert u hoe u een Azure-webtoepassing maakt waarmee gegevens uit een Azure-sleutelkluis kunnen worden gelezen. Tijdens dit proces wordt gebruikgemaakt van beheerde identiteiten voor Azure-resources. Zie [Azure App Service](../app-service/overview.md) voor meer informatie over Azure-webtoepassingen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een sleutelkluis maken.
> * Voeg een geheim toe aan de sleutelkluis.
> * Een geheim ophalen uit de sleutelkluis.
> * Maak een Azure-web-app.
> * Schakel een beheerde identiteit in voor de web-app.
> * Machtigingen toewijzen voor de web-app.
> * Voer de web-app uit op Azure.

Lees voordat u begint [de basisconcepten van Key Vault.](basic-concepts.md) 

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)aan.

## <a name="prerequisites"></a>Vereisten

* Voor Windows: [.NET Core 2.1 SDK of hoger](https://www.microsoft.com/net/download/windows)
* Voor Mac: [Visual Studio voor Mac](https://visualstudio.microsoft.com/vs/mac/)
* Voor Windows, Mac en Linux:
  * [Git](https://git-scm.com/downloads)
  * Voor deze zelfstudie moet u de Azure CLI lokaal uitvoeren. U moet de Azure CLI-versie 2.0.4 of hoger hebben geïnstalleerd. Voer `az --version` uit om de versie te bekijken. Als u de CLI wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](https://docs.microsoft.com/cli/azure/install-azure-cli).
  * [.NET Core](https://www.microsoft.com/net/download/dotnet-core/2.1)

## <a name="about-managed-service-identity"></a>Informatie over Managed Service Identity

Azure Key Vault slaat referenties veilig op, zodat ze niet worden weergegeven in uw code. U moet u echter verifiëren bij Azure Key Vault om uw sleutels op te halen. En om u te verifiëren bij Key Vault, hebt u een referentie nodig. Dit is een klassiek bootstrap-dilemma. In MSI (Managed Service Identity) is dit probleem opgelost doordat er een _bootstrap-identiteit_ wordt geboden dat het proces vereenvoudigt.

Wanneer u MSI inschakelt voor een Azure-service, zoals Azure Virtual Machines, Azure App Service of Azure Functions, maakt Azure een [serviceprincipal.](basic-concepts.md) MSI doet dit voor het exemplaar van de service in Azure Active Directory (Azure AD) en injecteert de servicehoofdreferenties in dat exemplaar.

![MSI-diagram](media/MSI.png)

Als u vervolgens een toegangstoken wilt ontvangen, roept uw code een lokale metagegevensservice aan die beschikbaar is op de Azure-bron. Uw code gebruikt het toegangstoken dat wordt verkregen van het lokale MSI-eindpunt om zich te authenticeren bij een Azure Key Vault-service.

## <a name="log-in-to-azure"></a>Meld u aan bij Azure.

Als u zich bij Azure wilt aanmelden met de Azure CLI, voert u het volgende in:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

Maak een resourcegroep met de opdracht [az group create](/cli/azure/group#az-group-create).

Selecteer vervolgens de naam van een resourcegroep en vul de tijdelijke aanduiding in. In het volgende voorbeeld wordt een resourcegroep gemaakt in de regio VS - west:

   ```azurecli
   # To list locations: az account list-locations --output table
   az group create --name "<YourResourceGroupName>" --location "West US"
   ```

U gebruikt deze resourcegroep in de hele zelfstudie.

## <a name="create-a-key-vault"></a>Een sleutelkluis maken

Als u een sleutelkluis in de resourcegroep wilt maken, biedt u de volgende informatie:

* Naam sleutelgewelf: een tekenreeks van 3 tot 24 tekens die alleen getallen (0-9), letters (a-z, A-Z) en koppeltekens (-) kunnen bevatten
* Naam van de resourcegroep
* Locatie: **West US**

Voer in De Azure CLI de volgende opdracht in:

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

Op dit moment is uw Azure-account de enige die gemachtigd is om bewerkingen uit te voeren op deze nieuwe kluis.

## <a name="add-a-secret-to-the-key-vault"></a>Een geheim toevoegen aan de sleutelkluis

U kunt nu een geheim toevoegen. Dit kan een SQL-verbindingsreeks zijn of enige andere gegevens die u zowel veilig als beschikbaar wilt houden op de toepassing.

Voer de volgende opdracht in om een geheim te maken in de sleutelkluis met de naam **AppSecret:** 

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Met dit geheim wordt de waarde **MijnGeheim** opgeslagen.

Als u de waarde in het geheim wilt weergeven als platte tekst, voert u de volgende opdracht in:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Met deze opdracht worden de geheime informatie weergegeven, waaronder de URI. 

Wanneer u deze stappen hebt uitgevoerd, beschikt u over een URI voor een geheim in een sleutelkluis. Noteer deze informatie voor later gebruik in deze zelfstudie. 

## <a name="create-a-net-core-web-app"></a>Een .NET Core-web-app maken

Als u een .NET Core-web-app wilt maken en deze wilt publiceren in Azure, volgt u de instructies in [Een ASP.NET Core-web-app maken in Azure](../app-service/app-service-web-get-started-dotnet.md). 

U kunt ook deze video bekijken:

>[!VIDEO https://www.youtube.com/embed/EdiiEH7P-bU]

## <a name="open-and-edit-the-solution"></a>De oplossing openen en bewerken

1. Ga naar het bestand **Pagina's** > **About.cshtml.cs.**

1. Installeer deze NuGet-pakketten:
   - [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)
   - [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)

1. Importeer de volgende code in het *About.cshtml.cs* bestand:

   ```csharp
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.KeyVault.Models;
    using Microsoft.Azure.Services.AppAuthentication;
   ```

   Uw code in de klasse AboutModel moet er als volgt uitzien:

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

1. Selecteer In het hoofdmenu van Visual Studio 2019 de optie **Foutopsporing** > **Starten**, met of zonder foutopsporing. 
1. Ga in de browser naar de pagina **Info.**  
    De waarde voor **AppGeheim** wordt weergegeven.

## <a name="enable-a-managed-identity"></a>Een beheerde identiteit inschakelen

Azure Key Vault biedt een manier voor het veilig opslaan van referenties en andere geheimen, maar uw code moet worden geverifieerd bij Key Vault om ze op te halen. Het [Overzicht van beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md) helpt bij het oplossen van dit probleem door Azure-services een automatisch beheerde identiteit in Azure AD te geven. U deze identiteit gebruiken om te verifiëren voor elke service die Azure AD-verificatie ondersteunt, inclusief Key Vault, zonder dat u referenties in uw code hoeft weer te geven.

Voer in Azure CLI de identiteit voor deze toepassing uit:

```azurecli
az webapp identity assign --name "<YourAppName>" --resource-group "<YourResourceGroupName>"
```

Vervang \<Uw AppName-> door de naam van de gepubliceerde app op Azure.  
    Als uw gepubliceerde app-naam **MyAwesomeapp.azurewebsites.net**bijvoorbeeld \<MyAwesomeapp.azurewebsites.net is, vervangt u YourAppName-> door **MyAwesomeapp**.

Noteer de `PrincipalId` wanneer u de toepassing publiceert in Azure. De uitvoer van de opdracht in stap 1 moet de volgende indeling hebben:

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

>[!NOTE]
>De opdracht in deze procedure is het equivalent van naar de [Azure-portal](https://portal.azure.com) gaan en de instelling **Identiteit / Systeem toegewezen** in **De** webtoepassingseigenschappen wijzigen.

## <a name="assign-permissions-to-your-app"></a>Machtigingen toewijzen aan uw app

Vervang \<YourKeyVaultName> door de naam van \<uw sleutelkluis en vervang PrincipalId> met de waarde van de **PrincipalId** in de volgende opdracht:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list
```

Deze opdracht geeft de identiteit (MSI) van de app-service toestemming om bewerkingen op uw sleutelkluis te **krijgen** en **weer te** geven.

## <a name="publish-the-web-app-to-azure"></a>De web-app publiceren in Azure

Publiceer uw web-app nogmaals naar Azure om te controleren of uw live web-app de geheime waarde kan ophalen.

1. In Visual Studio selecteert u het project **key-vault-dotnet-core-quickstart**.
2. Selecteer**Begin** **publiceren** > .
3. Selecteer **Maken**.

Wanneer u de toepassing uitvoert, ziet u, als het goed is, dat de geheime waarde wordt opgehaald.

Nu hebt u met succes een web-app in .NET gemaakt die de geheimen uit uw sleutelkluis opslaat en ophaalt.

## <a name="clean-up-resources"></a>Resources opschonen
Wanneer ze niet meer nodig zijn, u de virtuele machine en uw sleutelkluis verwijderen.

## <a name="next-steps"></a>Volgende stappen

>[!div class="nextstepaction"]
>[Gids voor Azure Key Vault-ontwikkelaars](key-vault-developers-guide.md)
