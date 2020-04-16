---
title: Zelfstudie - Gebruik een virtuele Linux-machine en een ASP.NET-consoletoepassing om geheimen op te slaan in Azure Key Vault | Microsoft Documenten
description: In deze zelfstudie leert u hoe u een ASP.NET Core-toepassing configureert om een geheim uit Azure Key-kluis te lezen.
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423150"
---
# <a name="tutorial-use-a-linux-vm-and-a-net-app-to-store-secrets-in-azure-key-vault"></a>Zelfstudie: Gebruik een Linux-vm en een .NET-app om geheimen op te slaan in Azure Key Vault

Azure Key Vault helpt u om geheimen te beschermen, zoals API-sleutels en databaseverbindingstekenreeksen die nodig zijn om toegang te krijgen tot uw toepassingen, services en IT-resources.

In deze zelfstudie stelt u een .NET-consoletoepassing in om informatie uit Azure Key Vault te lezen met beheerde identiteiten voor Azure-bronnen. Procedures voor:

> [!div class="checklist"]
> * Een sleutelkluis maken
> * Een geheim opslaan in Key Vault
> * Een Azure Linux virtuele machine maken
> * Een [Beheerde identiteit](../../active-directory/managed-identities-azure-resources/overview.md) inschakelen voor de virtuele machine
> * De vereiste machtigingen voor de consoletoepassing verlenen om gegevens uit Key Vault te lezen
> * Een geheim ophalen uit Key Vault

Voordat we verder gaan, lees over [de belangrijkste kluis basisconcepten](../general/basic-concepts.md).

## <a name="prerequisites"></a>Vereisten

* [Git.](https://git-scm.com/downloads)
* Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.
* [Azure CLI 2.0 of hoger](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) of Azure Cloud Shell.

[!INCLUDE [Azure Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="understand-managed-service-identity"></a>Informatie over Managed Service Identity

Azure Key Vault kan referenties veilig opslaan, zodat ze niet in uw code staan, maar om ze op te halen, moet u zich verifiëren naar Azure Key Vault. En om u te verifiëren bij Key Vault, hebt u een referentie nodig. Dat is een klassiek bootstrap-probleem. Met Azure en Azure Active Directory (Azure AD) kan Managed Service Identity (MSI) een bootstrap-identiteit bieden die het veel eenvoudiger maakt om dingen op gang te brengen.

Wanneer u MSI inschakelt voor een Azure-service zoals Virtual Machines, App Service of Functions, maakt Azure een serviceprincipal voor het exemplaar van de service in Azure Active Directory. Azure stuurt de referenties voor de service-principal naar het exemplaar van de service.

![MSI](../media/MSI.png)

Vervolgens roept uw ​​code een lokale metagegevensservice aan die beschikbaar is op de Azure-resource om een ​​toegangstoken te verkrijgen.
Uw code gebruikt het toegangstoken dat wordt verkregen van het lokale MSI_ENDPOINT zich te authenticeren bij een Azure Key Vault-service.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Als u zich met behulp van Azure CLI wilt aanmelden bij Azure, voert u het volgende in:

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een resourcegroep `az group create` met de opdracht. Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

Maak een resourcegroep op de locatie West-VS. Kies een naam voor uw `YourResourceGroupName` resourcegroep en vervang in het volgende voorbeeld:

```azurecli-interactive
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

U gebruikt deze resourcegroep in de hele zelfstudie.

## <a name="create-a-key-vault"></a>Een sleutelkluis maken

Maak vervolgens een sleutelkluis in uw resourcegroep. Geef de volgende informatie op:

* Naam sleutelkluis: een tekenreeks van 3 tot 24 tekens die alleen cijfers, letters en koppeltekens kunnen bevatten \- (0-9, a-z, A-Z en ).
* Naam van de resourcegroep
* Locatie: **West US**

```azurecli-interactive
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

Op dit moment is alleen uw Azure-account gemachtigd om bewerkingen uit te voeren op deze nieuwe kluis.

## <a name="add-a-secret-to-the-key-vault"></a>Een geheim toevoegen aan de sleutelkluis

Voeg er een geheim aan toe. In een scenario in de echte wereld slaat u mogelijk een SQL-verbindingstekenreeks of andere informatie op die u veilig moet bewaren, maar die u beschikbaar stelt aan uw toepassing.

Typ voor deze zelfstudie de volgende opdrachten om een geheim in de sleutelkluis te maken. Het geheim heet **AppSecret** en de waarde ervan is **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-linux-virtual-machine"></a>Een virtuele Linux-machine maken

Maak een VM `az vm create` met de opdracht.

In het volgende voorbeeld wordt een virtuele machine met de naam **myVM** gemaakt en voegt u een gebruikersaccount met de naam **azureuser** toe. De `--generate-ssh-keys` parameter die ons gebruikte om automatisch een SSH-toets te genereren en deze op de standaardsleutellocatie te plaatsen **(~/.ssh).** Als u een specifieke set sleutels wilt gebruiken, gebruikt u de optie `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Het maken van de virtuele machine en de ondersteunende resources duurt enkele minuten. In de volgende voorbeelduitvoerwordt weergegeven dat de bewerking voor het maken van vm's is geslaagd.

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

Noteer de waarde van `systemAssignedIdentity`. Je gebruikt het in de volgende stap.

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

Op uw Linux VM:

Registreer de Microsoft-productcode als vertrouwd door de volgende opdrachten uit te voeren:

   ```console
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
   ```

De gewenste versiehostpakketfeed instellen op basis van het besturingssysteem:

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

## <a name="create-and-run-a-sample-net-app"></a>Een voorbeeld .NET-app maken en uitvoeren

Voer de volgende opdrachten uit. U zou "Hello World" op de console moeten zien.

```console
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

## <a name="edit-the-console-app-to-fetch-your-secret"></a>De console-app bewerken om uw geheim op te halen

Open Program.cs bestand en voeg deze pakketten toe:

   ```csharp
   using System;
   using System.IO;
   using System.Net;
   using System.Text;
   using Newtonsoft.Json;
   using Newtonsoft.Json.Linq;
   ```

Het is een proces in twee stappen om het klassenbestand te wijzigen om de app toegang te geven tot het geheim in de sleutelkluis.

1. Haal een token op uit het lokale MSI-eindpunt op de VM dat op zijn beurt een token uit Azure Active Directory haalt.
1. Geef het token door aan Key Vault en haal je geheim op.

   Bewerk het klassenbestand met de volgende code:

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

Nu hebt u geleerd hoe u bewerkingen uitvoeren met Azure Key Vault in een .NET-toepassing die wordt uitgevoerd op een virtuele Azure Linux-machine.

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
