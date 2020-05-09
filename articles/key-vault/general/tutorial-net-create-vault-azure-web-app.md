---
title: Zelf studie-Azure Key Vault gebruiken met een Azure webapp in .NET | Microsoft Docs
description: In deze zelf studie configureert u een ASP.NET-kern toepassing voor het lezen van een geheim in uw sleutel kluis.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: mbaldwin
ms.openlocfilehash: 962b738d7e5f91076d17c19daad01f8dbaf92341
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/07/2020
ms.locfileid: "82888839"
---
# <a name="tutorial-use-a-managed-identity-to-connect-key-vault-to-an-azure-web-app-with-net"></a>Zelf studie: een beheerde identiteit gebruiken om Key Vault te verbinden met een Azure-web-app met .NET

Azure Key Vault biedt een manier voor het veilig opslaan van referenties en andere geheimen, maar uw code moet worden geverifieerd bij Key Vault om ze op te halen. Het [Overzicht van beheerde identiteiten voor Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md) helpt bij het oplossen van dit probleem door Azure-services een automatisch beheerde identiteit in Azure AD te geven. U kunt deze identiteit gebruiken voor verificatie bij elke service die ondersteuning biedt voor Azure AD-verificatie, met inbegrip van Key Vault, zonder dat u referenties in uw code hoeft weer te geven.

In deze zelf studie wordt een beheerde identiteit gebruikt voor het verifiëren van een Azure-web-app met een Azure Key Vault. Hoewel de stappen de [Azure Key Vault v4-client bibliotheek voor .net](/dotnet/api/overview/azure/key-vault?view=azure-dotnet) en de [Azure cli](/cli/azure/get-started-with-azure-cli)gebruiken, zijn dezelfde basis principes van toepassing wanneer u de ontwikkelings taal van uw keuze, Azure PowerShell en/of de Azure Portal gebruikt.

## <a name="prerequisites"></a>Vereisten

Dit zijn de vereisten voor het voltooien van deze snelstart:

* Een Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* De [.net Core 3,1 SDK of hoger](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* [Azure cli](/cli/azure/install-azure-cli?view=azure-cli-latest) of [Azure PowerShell](/powershell/azure/overview)

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. Maak een resource groep om zowel uw sleutel kluis als uw web-app te huis te maken met de opdracht [AZ Group Create](/cli/azure/group?view=azure-cli-latest#az-group-create) :

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```

## <a name="set-up-your-key-vault"></a>Uw sleutel kluis instellen

U gaat nu een sleutel kluis maken en deze in een geheim plaatsen, zodat u deze later in deze zelf studie kunt gebruiken.

Als u een sleutel kluis wilt maken, gebruikt u de opdracht [AZ Key kluis Create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) :

> [!Important]
> Elke sleutel kluis moet een unieke naam hebben. Vervang de naam van uw sleutel kluis <door de naam van de> kluis in de volgende voor beelden.

```azurecli-interactive
az keyvault create --name "<your-keyvault-name>" -g "myResourceGroup"
```

Noteer de geretourneerde `vaultUri`waarde, die de notatie ' https://<your-sleutel kluis-naam>. Vault.Azure.net/' bevat. Deze wordt gebruikt in de stap voor [het bijwerken van de code](#update-the-code) .

U kunt nu een geheim in uw sleutel kluis plaatsen met de opdracht [AZ sleutel kluis Secret set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set) . Stel de naam van het geheim in op ' MySecret ' en de waarde op ' geslaagd! '.

```azurecli-interactive
az keyvault secret set --vault-name "<your-keyvault-name>" --name "MySecret" --value "Success!"
```

## <a name="create-a-net-web-app"></a>Een .NET-web-app maken

### <a name="create-a-local-app"></a>Een lokale app maken

Maak in een terminalvenster op uw computer een map met de naam `akvwebapp` en wijzig de huidige map in die map.

```bash
mkdir akvwebapp
cd akvwebapp
```

Maak nu een nieuwe .NET core-app met de [nieuwe web](/dotnet/core/tools/dotnet-new) -opdracht DotNet:

```bash
dotnet new web
```

Voer de toepassing lokaal uit zodat u kunt zien hoe deze eruit ziet wanneer u de toepassing implementeert naar Azure. 

```bash
dotnet run
```

Open een webbrowser en navigeer naar de app op `http://localhost:5000`.

U ziet het **Hallo wereld** -bericht van de voor beeld-app die op de pagina wordt weer gegeven.

### <a name="initialize-the-git-repository"></a>De Git-opslag plaats initialiseren

Druk in uw terminalvenster op **Ctrl + C** om de webserver af te sluiten.  Initialiseer een Git-opslagplaats voor het .NET Core-project.

```bash
git init
git add .
git commit -m "first commit"
```

### <a name="configure-a-deployment-user"></a>Een implementatiegebruiker configureren

FTP en lokale Git kunnen worden geïmplementeerd in een Azure-web-app met behulp van een *implementatie gebruiker*. Wanneer u de implementatie gebruiker hebt geconfigureerd, kunt u deze gebruiken voor al uw Azure-implementaties. De gebruikers naam en het wacht woord voor implementatie op account niveau verschillen van de referenties van uw Azure-abonnement. 

Als u de implementatie gebruiker wilt configureren, voert u de opdracht [AZ webapp Deployment User set](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) uit. Kies een gebruikers naam en wacht woord die voldoen aan de volgende richt lijnen: 

- De gebruikers naam moet uniek zijn binnen Azure en voor lokale Git-pushes mag het @-teken niet bevatten. 
- Het wacht woord moet ten minste acht tekens lang zijn, met twee van de volgende drie elementen: letters, cijfers en symbolen. 

```azurecli-interactive
az webapp deployment user set --user-name "<username>" --password "<password>"
```

De JSON-uitvoer toont het wacht `null`woord als. Als er een `'Conflict'. Details: 409`-fout optreedt, wijzigt u de gebruikersnaam. Als er een `'Bad Request'. Details: 400`-fout optreedt, kiest u een sterker wachtwoord. 

Registreer de gebruikers naam en het wacht woord die u wilt gebruiken om uw web-apps te implementeren.

### <a name="create-an-app-service-plan"></a>Een app service-plan maken

Maak een App Service plan met de opdracht Azure CLI [AZ appservice plan Create](/cli/azure/appservice/plan?view=azure-cli-latest) . In het volgende voor beeld wordt een App Service `myAppServicePlan` plan gemaakt met de naam in de prijs categorie **gratis** :

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

Maak een [Azure-Web](../../app-service/containers/app-service-linux-intro.md) -app `myAppServicePlan` in het app service plan. 

> [!Important]
> Net als bij Key Vault moet een Azure-web-app een unieke naam hebben. Vervang \<uw-webapp-naam\> door de naam van uw web-app in de volgende voor beelden.


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


De URL van de externe Git wordt weergegeven in de eigenschap `deploymentLocalGitUrl`, met de indeling `https://<username>@<your-webapp-name>.scm.azurewebsites.net/<your-webapp-name>.git`. Sla deze URL op, zoals u deze later nodig hebt.

Blader naar de app die u net hebt gemaakt. Vervang _ &lt;de naam van uw-webapp>_ door de naam van uw app.

```bash
https://<your-webapp-name>.azurewebsites.net
```

De standaard webpagina voor een nieuwe Azure-web-app wordt weer geven.

### <a name="deploy-your-local-app"></a>Uw lokale app implementeren

Voeg in het lokale terminal venster een externe Azure-server toe aan uw lokale Git-opslag plaats, waarbij u * \<deploymentLocalGitUrl* vervangt door het maken van een stap>met de URL van de externe Git die u hebt opgeslagen in de stap [een externe web-app maken](#create-a-remote-web-app) .

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Push naar de externe Azure-instantie om uw app te implementeren met de volgende opdracht. Wanneer met git Credential Manager om referenties wordt gevraagd, gebruikt u de referenties die u hebt gemaakt in de [gebruikers stap een implementatie configureren](#configure-a-deployment-user) .

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

Blader naar de geïmplementeerde toepassing (of vernieuw deze) met behulp van uw webbrowser.

```bash
http://<your-webapp-name>.azurewebsites.net
```

U ziet de Hallo wereld! bericht dat u eerder hebt gezien `http://localhost:5000`tijdens het bezoek.

## <a name="create-and-assign-a-managed-identity"></a>Een beheerde identiteit maken en toewijzen

In azure CLI voert u de opdracht [AZ webapp-Identity Assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) uit om de identiteit voor deze toepassing te maken:

```azurecli-interactive
az webapp identity assign --name "<your-webapp-name>" --resource-group "myResourceGroup"
```

Met deze bewerking wordt het JSON-fragment geretourneerd:

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

Als u uw web-app **toestemming wilt geven** om bewerkingen uit te voeren en **weer te geven** op uw sleutel kluis, geeft u de PrincipalID door aan de Azure cli [AZ instellen-beleids](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) opdracht:

```azurecli-interactive
az keyvault set-policy --name "<your-keyvault-name>" --object-id "<principalId>" --secret-permissions get list
```


## <a name="modify-the-app-to-access-your-key-vault"></a>Wijzig de app om toegang te krijgen tot uw sleutel kluis

### <a name="install-the-packages"></a>De pakketten installeren

Installeer in het Terminal venster de Azure Key Vault-client bibliotheek voor .NET-pakketten:

```console
dotnet add package Azure.Identity
dotnet add package Azure.Security.KeyVault.Secrets
```

### <a name="update-the-code"></a>De code bijwerken

Zoek en open het Startup.cs-bestand in uw akvwebapp-project. 

Voeg deze twee regels toe aan de koptekst:

```csharp
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
```

Voeg deze drie regels toe vóór `app.UseEndpoints` de aanroep, waarbij u de URI bijwerkt zodat deze overeenkomt met de `vaultUri` sleutel kluis.

```csharp
var client = new SecretClient(new Uri("https://<your-unique-key-vault-name>.vault.azure.net/"), new DefaultAzureCredential());

KeyVaultSecret secret = client.GetSecret("mySecret");

string secretValue = secret.Value;
```

De te lezen `await context.Response.WriteAsync("Hello World!");` regel bijwerken:

```csharp
await context.Response.WriteAsync(secretValue);
```

Zorg ervoor dat u uw wijzigingen opslaat voordat u verdergaat met de volgende stap.

### <a name="redeploy-your-web-app"></a>Uw web-app opnieuw implementeren

Als u uw code hebt bijgewerkt, kunt u deze opnieuw implementeren in azure met de volgende Git-opdrachten:

```bash
git add .
git commit -m "Updated web app to access my key vault"
git push azure master
```

## <a name="visit-your-completed-web-app"></a>Ga naar uw voltooide web-app

```bash
http://<your-webapp-name>.azurewebsites.net
```

Wanneer u **Hallo wereld**hebt gezien, ziet u nu de waarde van het geheim dat wordt weer gegeven: **geslaagd!**

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [beheerde identiteiten voor Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md)
- Meer informatie over [beheerde identiteiten voor app service](../../app-service/overview-managed-identity.md?tabs=dotnet)
- Zie de [Azure Key Vault-client bibliotheek voor .net API-naslag informatie](/dotnet/api/overview/azure/key-vault?view=azure-dotnet)
- Zie de [Azure Key Vault-client bibliotheek voor .net-bron code](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault)
- Zie de [v4-Azure Key Vault-client bibliotheek voor .net NuGet-pakket](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)


