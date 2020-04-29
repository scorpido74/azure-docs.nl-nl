---
title: 'Zelf studie: een virtuele Linux-machine en een ASP.NET-console toepassing gebruiken om geheimen op te slaan in Azure Key Vault | Microsoft Docs'
description: In deze zelf studie leert u hoe u een ASP.NET Core-toepassing kunt configureren voor het lezen van een geheim van Azure sleutel kluis.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 50810dd1fbf2d97989df47b537ef5c574be059d2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81423150"
---
# <a name="tutorial-use-a-linux-vm-and-a-net-app-to-store-secrets-in-azure-key-vault"></a>Zelf studie: een virtuele Linux-machine en een .NET-app gebruiken om geheimen op te slaan in Azure Key Vault

Azure Key Vault helpt u bij het beveiligen van geheimen zoals API-sleutels en database verbindings reeksen die nodig zijn voor toegang tot uw toepassingen, services en IT-resources.

In deze zelf studie stelt u een .NET-console toepassing in om informatie uit Azure Key Vault te lezen met behulp van beheerde identiteiten voor Azure-resources. Procedures voor:

> [!div class="checklist"]
> * Een sleutelkluis maken
> * Een geheim opslaan in Key Vault
> * Een virtuele machine van Azure Linux maken
> * Een [Beheerde identiteit](../../active-directory/managed-identities-azure-resources/overview.md) inschakelen voor de virtuele machine
> * Verleen de vereiste machtigingen voor de console toepassing om gegevens te lezen van Key Vault
> * Een geheim ophalen uit Key Vault

Lees voor meer informatie over de [basis concepten van sleutel kluis](../general/basic-concepts.md).

## <a name="prerequisites"></a>Vereisten

* [Git](https://git-scm.com/downloads).
* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.
* [Azure CLI 2,0 of hoger](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) of Azure Cloud shell.

[!INCLUDE [Azure Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="understand-managed-service-identity"></a>Informatie over Managed Service Identity

Azure Key Vault kunnen referenties veilig opslaan zodat ze zich niet in uw code bevinden, maar om ze op te halen, moet u zich verifiëren bij Azure Key Vault. En om u te verifiëren bij Key Vault, hebt u een referentie nodig. Dat is een klassiek bootstrap-probleem. Met Azure en Azure Active Directory (Azure AD) kan Managed Service Identity (MSI) een Boots trap-identiteit bieden waardoor het veel eenvoudiger is om aan de slag te gaan.

Wanneer u MSI inschakelt voor een Azure-service, zoals Virtual Machines, App Service of functions, maakt Azure een service-principal voor het exemplaar van de service in Azure Active Directory. Azure stuurt de referenties voor de service-principal naar het exemplaar van de service.

![MSI](../media/MSI.png)

Vervolgens roept uw ​​code een lokale metagegevensservice aan die beschikbaar is op de Azure-resource om een ​​toegangstoken te verkrijgen.
Uw code gebruikt het toegangstoken dat wordt verkregen van het lokale MSI_ENDPOINT zich te authenticeren bij een Azure Key Vault-service.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Als u zich met behulp van Azure CLI wilt aanmelden bij Azure, voert u het volgende in:

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een resource groep met behulp `az group create` van de opdracht. Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

Maak een resource groep op de locatie vs-West. Kies een naam voor de resource groep en vervang `YourResourceGroupName` in het volgende voor beeld:

```azurecli-interactive
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

U gebruikt deze resourcegroep in de hele zelfstudie.

## <a name="create-a-key-vault"></a>Een sleutelkluis maken

Vervolgens maakt u een sleutel kluis in de resource groep. Geef de volgende informatie op:

* Sleutel kluis naam: een teken reeks van 3 tot 24 tekens die alleen cijfers, letters en afbreek streepjes (0-9, a-z, A-Z en \- ) kan bevatten.
* Naam van de resourcegroep
* Locatie: **VS-West**

```azurecli-interactive
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

Op dit moment wordt alleen uw Azure-account gemachtigd voor het uitvoeren van bewerkingen op deze nieuwe kluis.

## <a name="add-a-secret-to-the-key-vault"></a>Een geheim toevoegen aan de sleutelkluis

U kunt nu een geheim toevoegen. In een praktijk scenario kunt u een SQL-connection string of andere informatie die u nodig hebt om veilig te bewaren, opslaan, maar beschikbaar maken voor uw toepassing.

Voor deze zelf studie typt u de volgende opdrachten om een geheim te maken in de sleutel kluis. Het geheim wordt **AppSecret** genoemd en de waarde ervan is **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-linux-virtual-machine"></a>Een virtuele Linux-machine maken

Maak een virtuele machine met `az vm create` de opdracht.

In het volgende voorbeeld wordt een virtuele machine met de naam **myVM** gemaakt en voegt u een gebruikersaccount met de naam **azureuser** toe. De `--generate-ssh-keys` para meter die wordt gebruikt om automatisch een SSH-sleutel te genereren en te plaatsen in de standaard locatie van de sleutel (**~/.ssh**). Als u een specifieke set sleutels wilt gebruiken, gebruikt u de optie `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Het maken van de virtuele machine en de ondersteunende resources duurt enkele minuten. In de volgende voorbeeld uitvoer ziet u dat de bewerking VM maken is voltooid.

```output
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-00-00-00-00-00",
  "powerState": "VM running",
  "privateIpAddress": "XX.XX.XX.XX",
  "publicIpAddress": "XX.XX.XXX.XXX",
  "resourceGroup": "myResourceGroup"
}
```

Noteer uw `publicIpAddress` in de uitvoer van uw VM. Dit adres wordt in latere stappen gebruikt voor toegang tot de VM.

## <a name="assign-an-identity-to-the-vm"></a>Een identiteit toewijzen aan de virtuele machine

In deze stap wijzen we een door het systeem toegewezen identiteit toe aan de virtuele machine met de volgende opdracht:

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

De uitvoer van de opdracht zou moeten zijn:

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

Noteer de waarde van `systemAssignedIdentity`. U gebruikt deze in de volgende stap.

## <a name="give-the-vm-identity-permission-to-key-vault"></a>VM-identiteit machtigen voor Key Vault

U krijgt nu Key Vault-machtigingen voor de identiteit die u hebt gemaakt. Voer de volgende opdracht uit:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>Aanmelden bij de nieuwe virtuele machine

Meld u nu via een terminal aan bij de virtuele machine.

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-net-core-on-linux"></a>.NET Core installeren op Linux

Op uw virtuele Linux-machine:

Registreer de product code van micro soft als vertrouwd door de volgende opdrachten uit te voeren:

   ```console
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
   ```

Gewenste versie van het hostbesturingssysteem instellen op basis van het besturings systeem:

```console
   # Ubuntu 17.10
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-artful-prod artful main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
   
   # Ubuntu 17.04
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-zesty-prod zesty main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
   
   # Ubuntu 16.04 / Linux Mint 18
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-xenial-prod xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
   
   # Ubuntu 14.04 / Linux Mint 17
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-trusty-prod trusty main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
```

Installeer .NET en controleer de versie:

   ```console
   sudo apt-get install dotnet-sdk-2.1.4
   dotnet --version
   ```

## <a name="create-and-run-a-sample-net-app"></a>Een voor beeld-.NET-app maken en uitvoeren

Voer de volgende opdrachten uit. Als het goed is, ziet u "Hallo wereld" afgedrukt op de-console.

```console
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

## <a name="edit-the-console-app-to-fetch-your-secret"></a>De console-app bewerken om uw geheim op te halen

Open Program.cs-bestand en voeg deze pakketten toe:

   ```csharp
   using System;
   using System.IO;
   using System.Net;
   using System.Text;
   using Newtonsoft.Json;
   using Newtonsoft.Json.Linq;
   ```

Het is een proces in twee stappen om het klassen bestand te wijzigen, zodat de app toegang heeft tot het geheim in de sleutel kluis.

1. Haal een token op uit het lokale MSI-eind punt op de virtuele machine die op zijn beurt een token ophaalt uit Azure Active Directory.
1. Geef het token door om uw geheim te Key Vault en op te halen.

   Bewerk het klassen bestand zodat het de volgende code bevat:

   ```csharp
    class Program
       {
           static void Main(string[] args)
           {
               // Step 1: Get a token from local (URI) Managed Service Identity endpoint which in turn fetches it from Azure Active Directory
               var token = GetToken();

               // Step 2: Fetch the secret value from Key Vault
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
               WebRequest kvRequest = WebRequest.Create("https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01");
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

Nu hebt u geleerd hoe u bewerkingen kunt uitvoeren met Azure Key Vault in een .NET-toepassing die wordt uitgevoerd op een virtuele machine van Azure Linux.

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de resourcegroep, de virtuele machine en alle gerelateerde resources wanneer u ze niet meer nodig hebt. Om dit te doen, selecteert u de resourcegroep voor de VM en selecteert u **Verwijderen**.

Verwijder de sleutelkluis met behulp van de opdracht `az keyvault delete`:

```azurecli-interactive
az keyvault delete --name
                   [--resource group]
                   [--subscription]
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [REST-API van Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
