---
title: 'Zelfstudie: Azure Key Vault gebruiken met een Azure-web-app in .NET | Microsoft Docs'
description: In deze zelfstudie gaat u een ASP.NET Core-toepassing configureren voor het lezen van een geheim in uw sleutelkluis.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: mbaldwin
ms.openlocfilehash: a198456412c3146db2bc3e2a2483377e387f452d
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/02/2020
ms.locfileid: "87513329"
---
# <a name="tutorial-use-a-managed-identity-to-connect-key-vault-to-an-azure-web-app-with-net"></a>Zelfstudie: Een beheerde identiteit gebruiken om Key Vault te koppelen met een Azure-web-app met .NET

Azure Key Vault biedt een manier voor het veilig opslaan van referenties en andere geheimen, maar uw code moet worden geverifieerd bij Key Vault om ze op te halen. Het [Overzicht van beheerde identiteiten voor Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md) helpt bij het oplossen van dit probleem door Azure-services een automatisch beheerde identiteit in Azure AD te geven. U kunt deze identiteit gebruiken voor verificatie bij alle services die ondersteuning bieden voor Azure AD-verificatie, inclusief Key Vault, zonder dat u referenties in uw code hoeft weer te geven.

In deze zelfstudie wordt een beheerde identiteit gebruikt voor het verifiëren van een Azure-web-app met een Azure Key Vault. Hoewel in de stappen wordt gebruikgemaakt van de [Azure Key Vault v4-clientbibliotheek voor .NET](/dotnet/api/overview/azure/key-vault?view=azure-dotnet) en de [Azure CLI](/cli/azure/get-started-with-azure-cli), gelden dezelfde basisprincipes wanneer u de ontwikkelingstaal van uw keuze, Azure PowerShell en/of de Azure-portal gebruikt.

## <a name="prerequisites"></a>Vereisten

Dit zijn de vereisten voor het voltooien van deze snelstart:

* Een Azure-abonnement (u kunt [een gratis abonnement maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)).
* De [.NET Core 3.1 SDK of hoger](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) of [Azure PowerShell](/powershell/azure/)

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. Maak een resourcegroep om zowel uw sleutelkluis als uw web-app in te bewaren met de opdracht [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create):

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```

## <a name="set-up-your-key-vault"></a>Uw sleutelkluis instellen

U gaat nu een sleutelkluis maken en hier een geheim in plaatsen, zodat u deze later in deze zelfstudie kunt gebruiken.

Als u een sleutelkluis wilt maken, gebruikt u de opdracht [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create):

> [!Important]
> Elke sleutelkluis moet een unieke naam hebben. Vervang <your-keyvault-name> door de naam van uw sleutelkluis in de volgende voorbeelden.

```azurecli-interactive
az keyvault create --name "<your-keyvault-name>" -g "myResourceGroup"
```

Noteer de geretourneerde `vaultUri`, die de notatie "https://&lt;uw-sleutelkluis-naam&gt;.vault.azure.net/" heeft. Deze wordt gebruikt in de stap [De code bijwerken](#update-the-code).

[!INCLUDE [Create a secret](../../../includes/key-vault-create-secret.md)]

## <a name="create-a-net-web-app"></a>Een .NET-web-app maken

### <a name="create-a-local-app"></a>Een lokale app maken

Maak in een terminalvenster op uw computer een map met de naam `akvwebapp` en wijzig de huidige map in die map.

```bash
mkdir akvwebapp
cd akvwebapp
```

Maak nu een nieuwe .NET Core-app met de opdracht [dotnet new web](/dotnet/core/tools/dotnet-new):

```bash
dotnet new web
```

Voer de toepassing lokaal uit zodat u kunt zien hoe deze eruit ziet wanneer u de toepassing implementeert naar Azure. 

```bash
dotnet run
```

Open een webbrowser en navigeer naar de app op `http://localhost:5000`.

Het bericht **Hallo wereld** uit de voorbeeld-app wordt weergegeven op de pagina.

### <a name="initialize-the-git-repository"></a>De Git-opslagplaats initialiseren

Druk in uw terminalvenster op **Ctrl + C** om de webserver af te sluiten.  Initialiseer een Git-opslagplaats voor het .NET Core-project.

```bash
git init
git add .
git commit -m "first commit"
```

### <a name="configure-a-deployment-user"></a>Een implementatiegebruiker configureren

FTP en lokale Git kunnen worden geïmplementeerd in een Azure-web-app met behulp van een *implementatiegebruikers*. Zodra u deze implementatiegebruiker hebt gemaakt, kunt u deze voor al uw Azure-implementaties gebruiken. Uw gebruikersnaam en wachtwoord voor implementatie op accountniveau verschillen van de referenties voor uw Azure-abonnement. 

Als u de implementatiegebruiker wilt configureren, voert u de opdracht [az webapp deployment user set](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) uit. Kies een gebruikersnaam en wachtwoord die voldoen aan de volgende richtlijnen: 

- De gebruikersnaam moet uniek zijn binnen Azure en voor lokale Git-pushes en mag het symbool @ niet bevatten. 
- Het wachtwoord moet ten minste acht tekens lang zijn en minimaal twee van de volgende drie typen elementen bevatten: letters, cijfers en symbolen. 

```azurecli-interactive
az webapp deployment user set --user-name "<username>" --password "<password>"
```

De JSON-uitvoer toont het wachtwoord als `null`. Als er een `'Conflict'. Details: 409`-fout optreedt, wijzigt u de gebruikersnaam. Als er een `'Bad Request'. Details: 400`-fout optreedt, kiest u een sterker wachtwoord. 

Noteer uw gebruikersnaam en wachtwoord om te gebruiken bij het implementeren van uw web-apps.

### <a name="create-an-app-service-plan"></a>Een App Service-plan maken

Maak een App Service-plan met de Azure CLI-opdracht [az appservice plan create](/cli/azure/appservice/plan?view=azure-cli-latest). In het volgende voorbeeld wordt een App Service-plan gemaakt met de naam `myAppServicePlan` en de prijscategorie **Gratis**:

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

Wanneer het App Service-plan is gemaakt, toont de Azure CLI soortgelijke informatie als in het volgende voorbeeld:

<pre>
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "West Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
</pre>


### <a name="create-a-remote-web-app"></a>Een externe web-app maken

Maak een [Azure-web-app](../../app-service/containers/app-service-linux-intro.md) in het App Service-plan `myAppServicePlan`. 

> [!Important]
> Net als bij Key Vault moet een Azure-web-app een unieke naam hebben. Vervang \<your-webapp-name\> in de volgende voorbeelden door de naam van uw web-app.


```azurecli-interactive
az webapp create --resource-group "myResourceGroup" --plan "myAppServicePlan" --name "<your-webapp-name>" --deployment-local-git
```

Wanneer de web-app is gemaakt, toont de Azure CLI soortgelijke uitvoer als in het volgende voorbeeld:

<pre>
Local git is configured with url of 'https://&lt;username&gt;@&lt;your-webapp-name&gt;.scm.azurewebsites.net/&lt;ayour-webapp-name&gt;.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "clientCertExclusionPaths": null,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;your-webapp-name&gt;.azurewebsites.net",
  "deploymentLocalGitUrl": "https://&lt;username&gt;@&lt;your-webapp-name&gt;.scm.azurewebsites.net/&lt;your-webapp-name&gt;.git",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>


De URL van de externe Git wordt weergegeven in de eigenschap `deploymentLocalGitUrl`, met de indeling `https://<username>@<your-webapp-name>.scm.azurewebsites.net/<your-webapp-name>.git`. Sla deze URL op, want u hebt deze later nodig.

Blader naar de app die u net hebt gemaakt. Vervang _&lt;your-webapp-name>_ door de naam van uw app.

```bash
https://<your-webapp-name>.azurewebsites.net
```

De standaard webpagina voor een nieuwe Azure-web-app wordt weergeven.

### <a name="deploy-your-local-app"></a>Uw lokale app implementeren

Voeg in het lokale terminalvenster een externe Azure-server toe aan uw lokale Git-opslagplaats en vervang *\<deploymentLocalGitUrl-from-create-step>* met de URL van de externe Git die u hebt opgeslagen in de stap [Een externe web-app maken](#create-a-remote-web-app).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Push naar de externe Azure-instantie om uw app te implementeren met de volgende opdracht. Wanneer Git Credential Manager u vraagt om referenties, gebruikt u de referenties die u hebt gemaakt in de stap [Een implementatiegebruiker configureren](#configure-a-deployment-user).

```bash
git push azure master
```

Het kan enkele minuten duren voor deze opdracht is uitgevoerd. De opdracht geeft informatie weer die lijkt op het volgende voorbeeld:
<pre>
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 285 bytes | 95.00 KiB/s, done.
Total 3 (delta 2), reused 0 (delta 0), pack-reused 0
remote: Deploy Async
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'd6b54472f7'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
remote:
remote: Oryx Version      : 0.2.20200114.13, Commit: 204922f30f8e8d41f5241b8c218425ef89106d1d, ReleaseTagName: 20200114.13
remote: Build Operation ID: |imoMY2y77/s=.40ca2a87_
remote: Repository Commit : d6b54472f7e8e9fd885ffafaa64522e74cf370e1
.
.
.
remote: Deployment successful.
remote: Deployment Logs : 'https://&lt;your-webapp-name&gt;.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/d6b54472f7e8e9fd885ffafaa64522e74cf370e1/log'
To https://&lt;your-webapp-name&gt;.scm.azurewebsites.net:443/&lt;your-webapp-name&gt;.git
   d87e6ca..d6b5447  master -> master
</pre>

Blader naar (of vernieuw) de geïmplementeerde toepassing via uw webbrowser.

```bash
http://<your-webapp-name>.azurewebsites.net
```

U ziet het bericht 'Hallo wereld!' dat u eerder hebt gezien op `http://localhost:5000`.

## <a name="create-and-assign-a-managed-identity"></a>Een beheerde identiteit maken en toewijzen

Voer in Azure CLI de opdracht[az webapp-identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) uit om de identiteit voor deze toepassing te maken:

```azurecli-interactive
az webapp identity assign --name "<your-webapp-name>" --resource-group "myResourceGroup"
```

Met deze bewerking wordt dit JSON-fragment geretourneerd:

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

Als u uw web-app toestemming wilt geven om bewerkingen voor **ophalen** en **weergeven** uit te voeren op uw sleutelkluis, geeft u de principal-id door aan de Azure CLI-opdracht [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy):

```azurecli-interactive
az keyvault set-policy --name "<your-keyvault-name>" --object-id "<principalId>" --secret-permissions get list
```


## <a name="modify-the-app-to-access-your-key-vault"></a>De app wijzigen om toegang te krijgen tot uw sleutelkluis

### <a name="install-the-packages"></a>De pakketten installeren

Installeer vanuit het terminalvenster de pakketten voor de Azure Key Vault-clientbibliotheek voor .NET:

```console
dotnet add package Azure.Identity
dotnet add package Azure.Security.KeyVault.Secrets
```

### <a name="update-the-code"></a>De code bijwerken

Zoek en open het bestand Startup.cs in uw akvwebapp-project. 

Voeg deze twee regels toe aan de header:

```csharp
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
using Azure.Core;
```

Voeg deze regels toe vóór de `app.UseEndpoints`-aanroep, waarbij u de URI bijwerkt volgens de `vaultUri` van uw sleutelkluis. De onderstaande code maakt gebruik van ['DefaultAzureCredential()'](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet) voor verificatie naar de sleutelkluis, die een token van de beheerde identiteit van de toepassing gebruikt voor verificatie. Er wordt ook exponentieel uitstel gebruikt voor nieuwe pogingen wanneer de sleutelkluis wordt vertraagd.

```csharp
SecretClientOptions options = new SecretClientOptions()
    {
        Retry =
        {
            Delay= TimeSpan.FromSeconds(2),
            MaxDelay = TimeSpan.FromSeconds(16),
            MaxRetries = 5,
            Mode = RetryMode.Exponential
         }
    };
var client = new SecretClient(new Uri("https://<your-unique-key-vault-name>.vault.azure.net/"), new DefaultAzureCredential(),options);

KeyVaultSecret secret = client.GetSecret("mySecret");

string secretValue = secret.Value;
```

Werk de regel `await context.Response.WriteAsync("Hello World!");` bij naar:

```csharp
await context.Response.WriteAsync(secretValue);
```

Zorg ervoor dat u uw wijzigingen opslaat voordat u verdergaat met de volgende stap.

### <a name="redeploy-your-web-app"></a>Uw web-app opnieuw implementeren

Als u uw code hebt bijgewerkt, kunt u deze opnieuw implementeren in Azure met de volgende Git-opdrachten:

```bash
git add .
git commit -m "Updated web app to access my key vault"
git push azure master
```

## <a name="visit-your-completed-web-app"></a>Ga naar uw voltooide web-app

```bash
http://<your-webapp-name>.azurewebsites.net
```

Waar eerder **Hallo wereld** stond, zou nu de waarde van uw geheim moeten staan: **Geslaagd!**

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [beheerde identiteiten voor Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md)
- Meer informatie over [beheerde identiteiten voor App Service](../../app-service/overview-managed-identity.md?tabs=dotnet)
- Raadpleeg de documentatie voor de [API voor de Azure Key Vault-clientbibliotheek voor .NET](/dotnet/api/overview/azure/key-vault?view=azure-dotnet)
- Zie de [broncode voor de Azure Key Vault-clientbibliotheek voor .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault)
- Zie het [NuGet-pakket voor v4 Azure Key Vault-clientbibliotheek voor .NET](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)


