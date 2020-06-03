---
title: 'Zelfstudie: Azure Key Vault gebruiken met een virtuele Windows-machine in Python | Microsoft Docs'
description: In deze zelfstudie gaat u een ASP.NET Core-toepassing configureren voor het lezen van een geheim in uw sleutelkluis.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 05/11/2020
ms.author: sudbalas
ms.custom: mvc
ms.openlocfilehash: 00e2f573a85d6926c8941a3ed05eeab1163b9ea1
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121096"
---
# <a name="tutorial-use-azure-key-vault-with-a-windows-virtual-machine-in-python"></a>Zelfstudie: Azure Key Vault gebruiken met een Windows-VM in Python

Azure Key Vault helpt u bij het beveiligen van geheimen zoals API-sleutels, de databaseverbindingsreeksen die nodig zijn voor toegang tot uw toepassingen, services en IT-resources.

In deze zelfstudie leert u hoe u ervoor zorgt dat een consoletoepassing informatie ophaalt uit Azure Key Vault. Hiervoor moet u beheerde identiteiten voor Azure-resources gebruiken. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een sleutelkluis maken.
> * Voeg een geheim toe aan de sleutelkluis.
> * Een geheim ophalen uit de sleutelkluis.
> * Een virtuele Azure-machine maken.
> * Schakel een beheerde identiteit in.
> * Wijs machtigingen toe aan de VM-identiteit.

Lees voordat u verdergaat eerst [Basisconcepten van Key Vault](basic-concepts.md). 

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan.

## <a name="prerequisites"></a>Vereisten

Voor Windows, Mac en Linux:
  * [Git](https://git-scm.com/downloads)
  * Voor deze zelfstudie moet u de Azure CLI lokaal uitvoeren. Azure CLI versie 2.0.4 of hoger moet geïnstalleerd zijn. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli).

## <a name="about-managed-service-identity"></a>Informatie over Managed Service Identity

In Azure Key Vault worden referenties veilig opgeslagen zodat ze niet in uw code worden weergegeven. U moet u echter verifiëren bij Azure Key Vault om uw sleutels op te halen. En om u te verifiëren bij Key Vault, hebt u een referentie nodig. Dit is een klassiek bootstrap-dilemma. In MSI (Managed Service Identity) is dit probleem opgelost doordat er een _bootstrap-identiteit_ wordt geboden dat het proces vereenvoudigt.

Wanneer u MSI inschakelt voor een Azure-service (bijvoorbeeld Azure Virtual Machines, Azure App Service of Azure Functions), wordt in Azure een [service-principal](basic-concepts.md) gemaakt. Dit gebeurt voor het exemplaar van de service in Azure Active Directory (Azure AD). De referenties voor de service-principal worden in dit exemplaar geplaatst. 

![MSI](../media/MSI.png)

Vervolgens wordt met de ​​code een lokale metagegevensservice aangeroepen die beschikbaar is in de Azure-resource om een toegangstoken te verkrijgen. Uw code gebruikt het toegangstoken dat wordt verkregen van het lokale MSI-eindpunt om zich te authenticeren bij een Azure Key Vault-service. 

## <a name="log-in-to-azure"></a>Meld u aan bij Azure.

Als u zich bij Azure wilt aanmelden met de Azure CLI, voert u het volgende in:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

Maak een resourcegroep met de opdracht [az group create](/cli/azure/group#az-group-create). 

Selecteer de naam van een resourcegroep en vul de tijdelijke aanduiding in. In het volgende voorbeeld wordt een resourcegroep gemaakt in de regio VS - west:

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

U gebruikt de nieuwe resourcegroep in de hele zelfstudie.

## <a name="create-a-key-vault"></a>Een sleutelkluis maken

Geef de volgende informatie op om een sleutelkluis te maken in de resourcegroep die u in de vorige stap hebt gemaakt:

* Naam van de sleutelkluis: een tekenreeks van 3 tot en met 24 tekens, die alleen cijfers (0-9), letters (a-z, A-Z) en afbreekstreepjes (-) mag bevatten
* Naam van de resourcegroep
* Locatie: **VS - west**

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
Uw Azure-account is nu als enige gemachtigd om bewerkingen op deze nieuwe sleutelkluis uit te voeren.

## <a name="add-a-secret-to-the-key-vault"></a>Een geheim toevoegen aan de sleutelkluis

We gaan een geheim toevoegen om te laten zien hoe dit werkt. Het geheim kan een SQL-verbindingsreeks zijn of andere informatie die u zowel veilig als beschikbaar wilt houden op de toepassing.

Typ de volgende opdracht om een geheim te maken in de sleutelkluis met de naam **AppGeheim**:

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Met dit geheim wordt de waarde **MijnGeheim** opgeslagen.

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken
Gebruik een van de volgende manieren om een virtuele machine te maken:

* [De Azure CLI](../../virtual-machines/windows/quick-create-cli.md)
* [PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
* [Azure Portal](../../virtual-machines/windows/quick-create-portal.md)

## <a name="assign-an-identity-to-the-vm"></a>Een identiteit toewijzen aan de virtuele machine
In deze stap maakt u een door het systeem toegewezen identiteit voor de virtuele machine door de volgende opdracht uit te voeren in de Azure CLI:

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Bekijk de door het systeem toegewezen identiteit die wordt weergegeven in de volgende code. De uitvoer van de vorige opdracht ziet er als volgt uit: 

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="assign-permissions-to-the-vm-identity"></a>Machtigingen toewijzen aan de VM-identiteit
U kunt nu de eerder gemaakte identiteitsmachtigingen toewijzen aan uw sleutelkluis door de volgende opdracht uit te voeren:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-on-to-the-virtual-machine"></a>Aanmelden bij de virtuele machine

Als u zich wilt aanmelden bij de virtuele machine, volgt u de instructies in [Verbinding maken met en aanmelden bij een virtuele Azure-machine met Windows](../../virtual-machines/windows/connect-logon.md).

## <a name="create-and-run-a-sample-python-app"></a>Een Python-voorbeeld-app maken en uitvoeren

In het volgende gedeelte vindt u een voorbeeldbestand genaamd *Sample.py*. In dit voorbeeld wordt gebruikgemaakt van een bibliotheek voor [aanvragen](https://2.python-requests.org/en/master/) om HTTP GET-aanroepen uit te voeren.

## <a name="edit-samplepy"></a>Sample.py bewerken

Nadat u *Sample.py* hebt gemaakt, opent u het bestand en kopieert u de code in dit gedeelte. 

De code beschrijft een proces dat uit twee stappen bestaat:
1. Haal een token uit het lokale MSI-eindpunt op de virtuele machine op.  
  Hiermee wordt ook een token uit Azure AD opgehaald.
1. Geef het token door aan de sleutelkluis en haal vervolgens het geheim op. 

```python
    # importing the requests library 
    import requests 

    # Step 1: Fetch an access token from a Managed Identity enabled azure resource.
    # Resources with an MSI configured recieve an AAD access token by using the Azure Instance Metadata Service (IMDS)
    # IMDS provides an endpoint accessible to all IaaS VMs using a non-routable well-known IP Address
    # To learn more about IMDS and MSI Authentication see the following link: https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service
    # Note that the resource here is https://vault.azure.net for public cloud and api-version is 2018-02-01
    MSI_ENDPOINT = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net"
    r = requests.get(MSI_ENDPOINT, headers = {"Metadata" : "true"}) 
      
    # extracting data in json format 
    # This request gets an access_token from Azure AD by using the local MSI endpoint.
    data = r.json() 
    
    # Step 2: Pass the access_token received from previous HTTP GET call to your key vault.
    KeyVaultURL = "https://{YOUR KEY VAULT NAME}.vault.azure.net/secrets/{YOUR SECRET NAME}?api-version=2016-10-01"
    kvSecret = requests.get(url = KeyVaultURL, headers = {"Authorization": "Bearer " + data["access_token"]})
    
    print(kvSecret.json()["value"])
```

U kunt de geheime waarde weergeven door de volgende code uit te voeren: 

```console
python Sample.py
```

In de voorgaande code ziet u hoe u bewerkingen met Azure Key Vault uitvoert op een virtuele Windows-machine. 

## <a name="clean-up-resources"></a>Resources opschonen

Als ze niet meer nodig zijn, verwijdert u de virtuele machine en de sleutelkluis.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [REST-API van Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
