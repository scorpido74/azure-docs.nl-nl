---
title: 'Zelfstudie: Azure Key Vault gebruiken met een virtuele machine in Python | Microsoft Docs'
description: In deze zelfstudie gaat u een ASP.NET Core-toepassing configureren voor het lezen van een geheim in uw sleutelkluis.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 07/20/2020
ms.author: mbaldwin
ms.custom: mvc, tracking-python
ms.openlocfilehash: 453307b304c4cb1899b1de31117c944ac66fcddb
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87093562"
---
# <a name="tutorial-use-azure-key-vault-with-a-virtual-machine-in-python"></a>Zelfstudie: Azure Key Vault gebruiken met een virtuele machine in Python

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

## <a name="log-in-to-azure"></a>Meld u aan bij Azure.

Als u zich bij Azure wilt aanmelden met de Azure CLI, voert u het volgende in:

```azurecli
az login
```

### <a name="create-a-resource-group-and-key-vault"></a>Een resourcegroep en sleutelkluis maken

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

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
