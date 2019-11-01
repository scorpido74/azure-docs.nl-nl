---
title: 'Zelfstudie: een virtuele Linux-machine en een Python-toepassing gebruiken voor het opslaan van geheimen in Azure Key Vault | Microsoft Docs'
description: In deze zelfstudie configureert u een Python-toepassing om een geheim vanuit Azure Key Vault te lezen.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 15650de776b481d1635b58f2b8ecf2bf2921d12f
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/31/2019
ms.locfileid: "73242413"
---
# <a name="tutorial-use-a-linux-vm-and-a-python-app-to-store-secrets-in-azure-key-vault"></a>Zelf studie: een virtuele Linux-machine en een python-app gebruiken om geheimen op te slaan in Azure Key Vault

Azure Key Vault helpt u bij het beveiligen van geheimen zoals de API-sleutels en databaseverbindingsreeksen die nodig zijn voor toegang tot uw toepassingen, services en IT-resources.

In deze zelfstudie voert u de stappen uit om een Azure-webtoepassing gegevens te laten lezen uit Azure Key Vault met behulp van beheerde identiteiten voor Azure-resources. In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Een sleutelkluis maken
> * Een geheim opslaan in de sleutelkluis
> * Een virtuele Linux-machine maken
> * Een [Beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md) inschakelen voor de virtuele machine
> * De vereiste machtigingen verlenen aan de consoletoepassing om gegevens te lezen uit de sleutelkluis
> * Een geheim ophalen uit de sleutelkluis

Lees voordat u verdergaat de [Basisconcepten over Key Vault](basic-concepts.md).

## <a name="prerequisites"></a>Vereisten

* [Git](https://git-scm.com/downloads).
* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.
* [Azure CLI versie 2.0.4 of hoger](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) of Azure Cloud Shell.

[!INCLUDE [Azure Cloud Shell](../../includes/cloud-shell-try-it.md)]

## <a name="understand-managed-service-identity"></a>Informatie over Managed Service Identity

In Azure Key Vault kunnen referenties veilig worden opgeslagen zodat ze zich niet in uw code bevinden. Om deze op te halen, moet u zich verifiëren bij Azure Key Vault. En om u te verifiëren bij Key Vault, hebt u een referentie nodig. Dat is een klassiek bootstrap-probleem. Via Azure en Azure Active Directory (Azure Active Directory) biedt Managed Service Identity (MSI) een 'bootstrap-identiteit' die het eenvoudiger maakt om dingen aan de gang te krijgen.

Wanneer u MSI inschakelt voor een Azure-service, zoals Virtual Machines, App Service of Functions, wordt in Azure een Service-principal gemaakt voor het exemplaar van de service in Azure Active Directory. Azure stuurt de referenties voor de service-principal naar het exemplaar van de service.

![MSI](media/MSI.png)

Vervolgens wordt met de ​​code een lokale metagegevensservice aangeroepen die beschikbaar is in de Azure-resource, om een ​​toegangstoken te verkrijgen. Uw code gebruikt het toegangstoken dat wordt verkregen van het lokale MSI-eindpunt om zich te authenticeren bij een Azure Key Vault-service.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Als u zich met behulp van Azure CLI wilt aanmelden bij Azure, voert u het volgende in:

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

Maak een resourcegroep met behulp van de opdracht `az group create` in de locatie US - west met de volgende code. Vervang `YourResourceGroupName` door een naam van uw keuze.

```azurecli-interactive
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

U gebruikt deze resourcegroep in de hele zelfstudie.

## <a name="create-a-key-vault"></a>Een sleutelkluis maken

Vervolgens maakt u een sleutelkluis in de resourcegroep die u in de vorige stap hebt gemaakt. Geef de volgende informatie op:

* Naam sleutel kluis: de naam moet een teken reeks van 3-24 tekens zijn en moet alleen 0-9, a-z, A-Z en afbreek streepjes (-) bevatten.
* Naam van de resourcegroep.
* Locatie: **US - west**.

```azurecli-interactive
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

Vanaf dit punt is uw Azure-account nu als enige gemachtigd om bewerkingen op deze nieuwe kluis uit te voeren.

## <a name="add-a-secret-to-the-key-vault"></a>Een geheim toevoegen aan de sleutelkluis

We gaan een geheim toevoegen om te laten zien hoe dit werkt. Dit kan een SQL-verbindingsreeks zijn of enige andere gegevens die u zowel veilig als beschikbaar wilt houden op de toepassing.

Typ de volgende opdrachten om een geheim te maken in de sleutelkluis met de naam *AppGeheim*. Met dit geheim wordt de waarde **MijnGeheim** opgeslagen.

```azurecli-interactive
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-linux-virtual-machine"></a>Een virtuele Linux-machine maken

Maak een VM met de opdracht `az vm create`.

In het volgende voorbeeld wordt een virtuele machine met de naam **myVM** gemaakt en voegt u een gebruikersaccount met de naam **azureuser** toe. Door de parameter `--generate-ssh-keys` wordt automatisch een SSH-sleutel gegenereerd en in de standaardsleutellocatie ( **~/.ssh**) geplaatst. Als u een specifieke set sleutels wilt gebruiken, gebruikt u de optie `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Het maken van de virtuele machine en de ondersteunende resources duurt enkele minuten. In het volgende voorbeeld van uitvoer ziet u dat het maken van de virtuele machine is geslaagd:

```azurecli
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

Noteer uw eigen `publicIpAddress` in de uitvoer van uw virtuele machine. Dit adres wordt in latere stappen gebruikt voor toegang tot de VM.

## <a name="assign-an-identity-to-the-vm"></a>Een identiteit toewijzen aan de virtuele machine

In deze stap wijzen we een door het systeem toegewezen identiteit toe aan de virtuele machine met de volgende opdracht:

```azurecli-interactive
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

De uitvoer van de opdracht is als volgt.

```azurecli
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

Noteer de waarde van `systemAssignedIdentity`. U gebruikt deze in de volgende stap.

## <a name="give-the-vm-identity-permission-to-key-vault"></a>VM-identiteit machtigen voor Key Vault

U krijgt nu Key Vault-machtigingen voor de identiteit die u hebt gemaakt. Voer de volgende opdracht uit:

```azurecli-interactive
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>Aanmelden bij de nieuwe virtuele machine

Meld u nu via een terminal aan bij de virtuele machine.

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-python-library-on-the-vm"></a>Python-bibliotheek installeren op de virtuele machine

Download en installeer de Python-bibliotheek [Aanvragen](https://pypi.org/project/requests/2.7.0/) voor HTTP GET-aanroepen.

## <a name="create-edit-and-run-the-sample-python-app"></a>De Python-voorbeeld-app maken, bewerken en uitvoeren

Maak een Python-bestand met de naam **Sample.py**.

Open Sample.py en bewerk het bestand zodat het de volgende code bevat:

```python
# importing the requests library
import requests

# Step 1: Fetch an access token from an MSI-enabled Azure resource      
# Note that the resource here is https://vault.azure.net for the public cloud, and api-version is 2018-02-01
MSI_ENDPOINT = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net"
r = requests.get(MSI_ENDPOINT, headers = {"Metadata" : "true"})

# Extracting data in JSON format 
# This request gets an access token from Azure Active Directory by using the local MSI endpoint
data = r.json()

# Step 2: Pass the access token received from the previous HTTP GET call to the key vault
KeyVaultURL = "https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01"
kvSecret = requests.get(url = KeyVaultURL, headers = {"Authorization": "Bearer " + data["access_token"]})

print(kvSecret.json()["value"])
```

De bovenstaande code voert een proces in twee stappen uit:

   1. Haalt een token uit het lokale MSI-eindpunt op de virtuele machine op. Het eindpunt haalt vervolgens een token op uit Azure Active Directory.
   1. Geeft het token door aan de sleutelkluis en haalt het geheim op.

Voer de volgende opdracht uit. U ziet de geheime waarde.

```console
python Sample.py
```

In deze zelfstudie hebt u geleerd hoe u Azure Key Vault gebruikt met een Python-app die wordt uitgevoerd op een Linux-machine.

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de resourcegroep, de virtuele machine en alle gerelateerde resources wanneer u ze niet meer nodig hebt. Om dit te doen, selecteert u de resourcegroep voor de VM en selecteert u **Verwijderen**.

Verwijder de sleutelkluis met behulp van de opdracht `az keyvault delete`:

```azurecli-interactive
az keyvault delete --name
                   [--resource-group]
                   [--subscription]
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [REST-API van Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
