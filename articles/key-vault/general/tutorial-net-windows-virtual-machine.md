---
title: Zelf studie-Azure Key Vault gebruiken met een virtuele Windows-machine in .NET | Microsoft Docs
description: In deze zelf studie configureert u een ASP.NET-kern toepassing voor het lezen van een geheim in uw sleutel kluis.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 6ba78a44af7beb9b5b79aa1a87e08f5a82589cce
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81422863"
---
# <a name="tutorial-use-azure-key-vault-with-a-windows-virtual-machine-in-net"></a>Zelf studie: Azure Key Vault gebruiken met een virtuele Windows-machine in .NET

Azure Key Vault helpt u bij het beveiligen van geheimen zoals API-sleutels, de database verbindings reeksen die u nodig hebt om toegang te krijgen tot uw toepassingen, services en IT-resources.

In deze zelf studie leert u hoe u een console toepassing kunt ophalen om informatie uit Azure Key Vault te lezen. Hiervoor gebruikt u beheerde identiteiten voor Azure-resources. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Maak een resourcegroep.
> * Een sleutelkluis maken.
> * Voeg een geheim toe aan de sleutelkluis.
> * Een geheim ophalen uit de sleutelkluis.
> * Een virtuele Azure-machine maken.
> * Schakel een [beheerde identiteit](../../active-directory/managed-identities-azure-resources/overview.md) in voor de virtuele machine.
> * Machtigingen toewijzen aan de VM-identiteit.

Lees [Key Vault basis concepten](basic-concepts.md)voordat u begint. 

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Vereisten

Voor Windows, Mac en Linux:
  * [Git](https://git-scm.com/downloads)
  * Voor deze zelfstudie moet u de Azure CLI lokaal uitvoeren. U moet de Azure CLI-versie 2.0.4 of hoger hebben geïnstalleerd. Voer `az --version` uit om de versie te bekijken. Als u de CLI wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli).

## <a name="about-managed-service-identity"></a>Informatie over Managed Service Identity

Azure Key Vault referenties veilig opgeslagen, zodat ze niet in uw code worden weer gegeven. U moet u echter verifiëren bij Azure Key Vault om uw sleutels op te halen. En om u te verifiëren bij Key Vault, hebt u een referentie nodig. Dit is een klassiek bootstrap-dilemma. In MSI (Managed Service Identity) is dit probleem opgelost doordat er een _bootstrap-identiteit_ wordt geboden dat het proces vereenvoudigt.

Wanneer u MSI inschakelt voor een Azure-service, zoals Azure Virtual Machines, Azure App Service of Azure Functions, maakt Azure een [Service-Principal](basic-concepts.md). MSI doet dit voor het exemplaar van de service in Azure Active Directory (Azure AD) en activeert de referenties van de Service-Principal in dat exemplaar. 

![MSI](../media/MSI.png)

Om een toegangs token op te halen, roept uw code een lokale meta gegevens service aan die beschikbaar is op de Azure-resource. Voor de verificatie bij een Azure Key Vault-service gebruikt uw code het toegangs token dat het ontvangt van het lokale MSI-eind punt. 

## <a name="create-resources-and-assign-permissions"></a>Resources maken en machtigingen toewijzen

Voordat u begint met het coderen van, moet u enkele resources maken, een geheim in uw sleutel kluis plaatsen en machtigingen toewijzen.

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Als u zich met behulp van Azure CLI wilt aanmelden bij Azure, voert u het volgende in:

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Een resourcegroep maken

Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. Maak een resourcegroep met de opdracht [az group create](/cli/azure/group#az-group-create). 

In dit voor beeld wordt een resource groep gemaakt in de locatie vs-West:

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

De zojuist gemaakte resource groep wordt in deze zelf studie gebruikt.

### <a name="create-a-key-vault-and-populate-it-with-a-secret"></a>Een sleutel kluis maken en deze vullen met een geheim

Maak een sleutel kluis in de resource groep door de opdracht [AZ sleutel kluis Create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) op te geven met de volgende informatie:

* Sleutel kluis naam: een teken reeks van 3 tot 24 tekens die alleen getallen (0-9), letters (a-z, A-Z) en afbreek streepjes (-) kan bevatten
* Naam van de resourcegroep
* Locatie: **VS-West**

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
Op dit moment is uw Azure-account de enige die gemachtigd is voor het uitvoeren van bewerkingen op deze nieuwe sleutel kluis.

Voeg nu een geheim toe aan uw sleutel kluis met behulp van de opdracht [AZ Key kluis Secret set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set)


Als u een geheim wilt maken in de sleutel kluis met de naam **AppSecret**, voert u de volgende opdracht in:

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Met dit geheim wordt de waarde **MijnGeheim** opgeslagen.

### <a name="create-a-virtual-machine"></a>Een virtuele machine maken
Maak een virtuele machine met behulp van een van de volgende methoden:

* [De Azure CLI](../../virtual-machines/windows/quick-create-cli.md)
* [PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
* [Azure Portal](../../virtual-machines/windows/quick-create-portal.md)

### <a name="assign-an-identity-to-the-vm"></a>Een identiteit toewijzen aan de virtuele machine
Maak een door het systeem toegewezen identiteit voor de virtuele machine met de opdracht [AZ VM Identity Assign](/cli/azure/vm/identity?view=azure-cli-latest#az-vm-identity-assign) :

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Let op de door het systeem toegewezen identiteit die in de volgende code wordt weer gegeven. De uitvoer van de voor gaande opdracht zou zijn: 

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

### <a name="assign-permissions-to-the-vm-identity"></a>Machtigingen toewijzen aan de VM-identiteit
Wijs de eerder gemaakte identiteits machtigingen toe aan uw sleutel kluis met de opdracht [AZ-set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) :

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

### <a name="sign-in-to-the-virtual-machine"></a>Meld u aan bij de virtuele machine

Volg de instructies in [verbinding maken en aanmelden bij een virtuele Azure-machine met Windows](../../virtual-machines/windows/connect-logon.md)om u aan te melden bij de virtuele machine.

## <a name="set-up-the-console-app"></a>De console-app instellen

Een console-app maken en de vereiste pakketten installeren met `dotnet` behulp van de opdracht.

### <a name="install-net-core"></a>.NET Core installeren

Als u .NET core wilt installeren, gaat u naar de pagina [.net-down loads](https://www.microsoft.com/net/download) .

### <a name="create-and-run-a-sample-net-app"></a>Een voor beeld-.NET-app maken en uitvoeren

Open een opdrachtprompt.

U kunt ' Hallo wereld ' afdrukken naar de-console door de volgende opdrachten uit te voeren:

```console
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

### <a name="install-the-packages"></a>De pakketten installeren

Installeer in het console venster de .NET-pakketten die vereist zijn voor deze Quick Start:

```console
dotnet add package System.IO;
dotnet add package System.Net;
dotnet add package System.Text;
dotnet add package Newtonsoft.Json;
dotnet add package Newtonsoft.Json.Linq;
```

## <a name="edit-the-console-app"></a>De console-app bewerken

Open het *Program.cs* -bestand en voeg deze pakketten toe:

```csharp
using System;
using System.IO;
using System.Net;
using System.Text;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```

Bewerk het klassen bestand zodat het de code bevat in het volgende proces met drie stappen:

1. Haal een token uit het lokale MSI-eindpunt op de virtuele machine op. Als u dit wel doet, wordt er ook een token uit Azure AD opgehaald.
2. Geef het token door aan de sleutel kluis en haal vervolgens uw geheim op. 
3. Voeg de kluis naam en geheime naam toe aan de aanvraag.

```csharp
 class Program
    {
        static void Main(string[] args)
        {
            // Step 1: Get a token from the local (URI) Managed Service Identity endpoint, which in turn fetches it from Azure AD
            var token = GetToken();

            // Step 2: Fetch the secret value from your key vault
            System.Console.WriteLine(FetchSecretValueFromKeyVault(token));
        }

        static string GetToken()
        {
            WebRequest request = WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net");
            request.Headers.Add("Metadata", "true");
            WebResponse response = request.GetResponse();
            return ParseWebResponse(response, "access_token");
        }
        
        static string FetchSecretValueFromKeyVault(string token)
        {
            //Step 3: Add the vault name and secret name to the request.
            WebRequest kvRequest = WebRequest.Create("https://<YourVaultName>.vault.azure.net/secrets/<YourSecretName>?api-version=2016-10-01");
            kvRequest.Headers.Add("Authorization", "Bearer "+  token);
            WebResponse kvResponse = kvRequest.GetResponse();
            return ParseWebResponse(kvResponse, "value");
        }

        private static string ParseWebResponse(WebResponse response, string tokenName)
        {
            string token = String.Empty;
            using (Stream stream = response.GetResponseStream())
            {
                StreamReader reader = new StreamReader(stream, Encoding.UTF8);
                String responseString = reader.ReadToEnd();

                JObject joResponse = JObject.Parse(responseString);    
                JValue ojObject = (JValue)joResponse[tokenName];             
                token = ojObject.Value.ToString();
            }
            return token;
        }
    }
```

In de voorgaande code ziet u hoe u bewerkingen met Azure Key Vault uitvoert op een virtuele Windows-machine.

## <a name="clean-up-resources"></a>Resources opschonen

Als ze niet meer nodig zijn, verwijdert u de virtuele machine en de sleutel kluis.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [REST-API van Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
