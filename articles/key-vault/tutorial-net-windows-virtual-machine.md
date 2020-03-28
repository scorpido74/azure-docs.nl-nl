---
title: Zelfstudie - Azure Key Vault gebruiken met een virtuele Windows-machine in .NET | Microsoft Documenten
description: In deze zelfstudie configureert u een ASP.NET kerntoepassing om een geheim uit uw sleutelkluis te lezen.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 1bf280ac8b9b01189c306f33e8fcc232a5cec8b6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79472670"
---
# <a name="tutorial-use-azure-key-vault-with-a-windows-virtual-machine-in-net"></a>Zelfstudie: Azure Key Vault gebruiken met een virtuele Windows-machine in .NET

Azure Key Vault helpt u om geheimen te beschermen, zoals API-sleutels, de databaseverbindingstekenreeksen die u nodig hebt om toegang te krijgen tot uw toepassingen, services en IT-resources.

In deze zelfstudie leert u hoe u een consoletoepassing krijgt om informatie uit Azure Key Vault te lezen. Hiervoor gebruikt u beheerde identiteiten voor Azure-bronnen. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Maak een resourcegroep.
> * Een sleutelkluis maken.
> * Voeg een geheim toe aan de sleutelkluis.
> * Een geheim ophalen uit de sleutelkluis.
> * Een virtuele Azure-machine maken.
> * Schakel een [beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md) in voor de virtuele machine.
> * Machtigingen toewijzen aan de VM-identiteit.

Lees voordat u begint [de basisconcepten van Key Vault.](basic-concepts.md) 

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)aan.

## <a name="prerequisites"></a>Vereisten

Voor Windows, Mac en Linux:
  * [Git](https://git-scm.com/downloads)
  * Voor deze zelfstudie moet u de Azure CLI lokaal uitvoeren. U moet de Azure CLI-versie 2.0.4 of hoger hebben geïnstalleerd. Voer `az --version` uit om de versie te bekijken. Als u de CLI wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli).

## <a name="about-managed-service-identity"></a>Informatie over Managed Service Identity

Azure Key Vault slaat referenties veilig op, zodat ze niet worden weergegeven in uw code. U moet u echter verifiëren bij Azure Key Vault om uw sleutels op te halen. En om u te verifiëren bij Key Vault, hebt u een referentie nodig. Dit is een klassiek bootstrap-dilemma. In MSI (Managed Service Identity) is dit probleem opgelost doordat er een _bootstrap-identiteit_ wordt geboden dat het proces vereenvoudigt.

Wanneer u MSI inschakelt voor een Azure-service, zoals Azure Virtual Machines, Azure App Service of Azure Functions, maakt Azure een [serviceprincipal.](basic-concepts.md) MSI doet dit voor het exemplaar van de service in Azure Active Directory (Azure AD) en injecteert de servicehoofdreferenties in dat exemplaar. 

![MSI](media/MSI.png)

Als u vervolgens een toegangstoken wilt ontvangen, roept uw code een lokale metagegevensservice aan die beschikbaar is op de Azure-bron. Als u wilt verifiëren voor een Azure Key Vault-service, gebruikt uw code het toegangstoken dat wordt opgehaald van het lokale MSI-eindpunt. 

## <a name="create-resources-and-assign-permissions"></a>Resources maken en machtigingen toewijzen

Voordat u begint met coderen, moet u een aantal bronnen maken, een geheim in uw sleutelkluis plaatsen en machtigingen toewijzen.

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Als u zich met behulp van Azure CLI wilt aanmelden bij Azure, voert u het volgende in:

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Een resourcegroep maken

Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. Maak een resourcegroep met de opdracht [az group create](/cli/azure/group#az-group-create). 

In dit voorbeeld wordt een resourcegroep op de locatie West-VS gezoend:

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Uw nieuw gemaakte resourcegroep wordt tijdens deze zelfstudie gebruikt.

### <a name="create-a-key-vault-and-populate-it-with-a-secret"></a>Maak een sleutelkluis en bevul deze met een geheim

Maak een sleutelkluis in uw resourcegroep door de opdracht [AZ Keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) met de volgende informatie op te geven:

* Naam sleutelgewelf: een tekenreeks van 3 tot 24 tekens die alleen getallen (0-9), letters (a-z, A-Z) en koppeltekens (-) kunnen bevatten
* Naam van de resourcegroep
* Locatie: **West US**

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
Op dit moment is uw Azure-account de enige die gemachtigd is om bewerkingen uit te voeren op deze nieuwe sleutelkluis.

Voeg nu een geheim toe aan je sleutelkluis met de geheime set opdracht [az keyvault](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set)


Voer de volgende opdracht in om een geheim te maken in de sleutelkluis met de naam **AppSecret:**

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Met dit geheim wordt de waarde **MijnGeheim** opgeslagen.

### <a name="create-a-virtual-machine"></a>Een virtuele machine maken
Maak een virtuele machine met behulp van een van de volgende methoden:

* [De Azure CLI](../virtual-machines/windows/quick-create-cli.md)
* [Powershell](../virtual-machines/windows/quick-create-powershell.md)
* [Azure Portal](../virtual-machines/windows/quick-create-portal.md)

### <a name="assign-an-identity-to-the-vm"></a>Een identiteit toewijzen aan de virtuele machine
Maak een systeemtoegewezen identiteit voor de virtuele machine met de opdracht [AZ VM-identiteit toewijzen:](/cli/azure/vm/identity?view=azure-cli-latest#az-vm-identity-assign)

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Let op de door het systeem toegewezen identiteit die wordt weergegeven in de volgende code. De uitvoer van de vorige opdracht zou zijn: 

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

### <a name="assign-permissions-to-the-vm-identity"></a>Machtigingen toewijzen aan de VM-identiteit
Wijs de eerder gemaakte identiteitsmachtigingen toe aan uw sleutelkluis met de opdracht [setbeleid van AZ Keyvault:](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy)

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

### <a name="sign-in-to-the-virtual-machine"></a>Inloggen bij de virtuele machine

Als u zich wilt aanmelden bij de virtuele machine, volgt u de instructies in [Verbinding maken en meldt u zich aan bij een virtuele Azure-machine met Windows](../virtual-machines/windows/connect-logon.md).

## <a name="set-up-the-console-app"></a>De console-app instellen

Maak een console-app en installeer `dotnet` de vereiste pakketten met behulp van de opdracht.

### <a name="install-net-core"></a>.NET Core installeren

Als u .NET Core wilt installeren, gaat u naar de pagina [.NET-downloads.](https://www.microsoft.com/net/download)

### <a name="create-and-run-a-sample-net-app"></a>Een voorbeeld .NET-app maken en uitvoeren

Open een opdrachtprompt.

U 'Hello World' op de console afdrukken door de volgende opdrachten uit te voeren:

```console
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

### <a name="install-the-packages"></a>De pakketten installeren

Installeer in het consolevenster de .NET-pakketten die nodig zijn voor deze quickstart:

```console
dotnet add package System.IO;
dotnet add package System.Net;
dotnet add package System.Text;
dotnet add package Newtonsoft.Json;
dotnet add package Newtonsoft.Json.Linq;
```

## <a name="edit-the-console-app"></a>De console-app bewerken

Open het *Program.cs* bestand en voeg deze pakketten toe:

```csharp
using System;
using System.IO;
using System.Net;
using System.Text;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```

Bewerk het klassenbestand om de code in het volgende proces in drie stappen te bevatten:

1. Haal een token uit het lokale MSI-eindpunt op de virtuele machine op. Als u dit doet, wordt ook een token opgehaald uit Azure AD.
2. Geef het token door aan je sleutelkluis en haal je geheim. 
3. Voeg de naam van de kluis en de geheime naam toe aan het verzoek.

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

Wanneer ze niet meer nodig zijn, verwijdert u de virtuele machine en uw sleutelkluis.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [REST-API van Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
