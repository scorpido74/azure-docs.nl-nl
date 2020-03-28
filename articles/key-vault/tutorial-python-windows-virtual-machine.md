---
title: Zelfstudie - Azure Key Vault gebruiken met een Virtuele Windows-machine in Python | Microsoft Documenten
description: In deze zelfstudie configureert u een ASP.NET kerntoepassing om een geheim uit uw sleutelkluis te lezen.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: c0bb391348548ecca595fd1a6472bafcb22ed4ee
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79472653"
---
# <a name="tutorial-use-azure-key-vault-with-a-windows-virtual-machine-in-python"></a>Zelfstudie: Azure Key Vault gebruiken met een Virtuele Windows-machine in Python

Azure Key Vault helpt u om geheimen te beschermen, zoals API-sleutels, de databaseverbindingstekenreeksen die u nodig hebt om toegang te krijgen tot uw toepassingen, services en IT-resources.

In deze zelfstudie leert u hoe u een consoletoepassing krijgt om informatie uit Azure Key Vault te lezen. Hiervoor gebruikt u beheerde identiteiten voor Azure-bronnen. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een sleutelkluis maken.
> * Voeg een geheim toe aan de sleutelkluis.
> * Een geheim ophalen uit de sleutelkluis.
> * Een virtuele Azure-machine maken.
> * Een beheerde identiteit inschakelen.
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

U gebruikt uw nieuw gemaakte resourcegroep tijdens deze zelfstudie.

## <a name="create-a-key-vault"></a>Een sleutelkluis maken

Als u een sleutelkluis wilt maken in de resourcegroep die u in de vorige stap hebt gemaakt, geeft u de volgende gegevens op:

* Naam sleutelgewelf: een tekenreeks van 3 tot 24 tekens die alleen getallen (0-9), letters (a-z, A-Z) en koppeltekens (-) kunnen bevatten
* Naam van de resourcegroep
* Locatie: **West US**

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
Op dit moment is uw Azure-account de enige die gemachtigd is om bewerkingen uit te voeren op deze nieuwe sleutelkluis.

## <a name="add-a-secret-to-the-key-vault"></a>Een geheim toevoegen aan de sleutelkluis

We gaan een geheim toevoegen om te laten zien hoe dit werkt. Het geheim kan een SQL-verbindingstekenreeks zijn of andere informatie die u nodig hebt om zowel veilig als beschikbaar voor uw toepassing te houden.

Voer de volgende opdracht in om een geheim te maken in de sleutelkluis met de naam **AppSecret:**

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Met dit geheim wordt de waarde **MijnGeheim** opgeslagen.

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken
U een virtuele machine maken met een van de volgende methoden:

* [De Azure CLI](../virtual-machines/windows/quick-create-cli.md)
* [Powershell](../virtual-machines/windows/quick-create-powershell.md)
* [Azure Portal](../virtual-machines/windows/quick-create-portal.md)

## <a name="assign-an-identity-to-the-vm"></a>Een identiteit toewijzen aan de virtuele machine
In deze stap maakt u een door het systeem toegewezen identiteit voor de virtuele machine door de volgende opdracht uit te voeren in de Azure CLI:

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

## <a name="assign-permissions-to-the-vm-identity"></a>Machtigingen toewijzen aan de VM-identiteit
Nu u de eerder gemaakte identiteitsmachtigingen toewijzen aan uw sleutelkluis door de volgende opdracht uit te voeren:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-on-to-the-virtual-machine"></a>Aanmelden bij de virtuele machine

Als u zich wilt aanmelden bij de virtuele machine, volgt u de instructies in [Verbinding maken en meldt u zich aan bij een virtuele Azure-machine met Windows](../virtual-machines/windows/connect-logon.md).

## <a name="create-and-run-a-sample-python-app"></a>Een voorbeeld python-app maken en uitvoeren

In de volgende sectie is een voorbeeldbestand met de naam *Sample.py*. Het maakt gebruik van een [aanvragen](https://2.python-requests.org/en/master/) bibliotheek om HTTP GET-oproepen te voeren.

## <a name="edit-samplepy"></a>Sample.py bewerken

Nadat u *Sample.py*hebt gemaakt, opent u het bestand en kopieert u de code in deze sectie. 

De code presenteert een proces in twee stappen:
1. Haal een token uit het lokale MSI-eindpunt op de virtuele machine op.  
  Als u dit doet, wordt ook een token opgehaald uit Azure AD.
1. Geef het token door aan je sleutelkluis en haal je geheim. 

```python
    # importing the requests library 
    import requests 

    # Step 1: Fetch an access token from a Managed Identity enabled azure resource.      
    # Note that the resource here is https://vault.azure.net for public cloud and api-version is 2018-02-01
    MSI_ENDPOINT = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net"
    r = requests.get(MSI_ENDPOINT, headers = {"Metadata" : "true"}) 
      
    # extracting data in json format 
    # This request gets an access_token from Azure AD by using the local MSI endpoint.
    data = r.json() 
    
    # Step 2: Pass the access_token received from previous HTTP GET call to your key vault.
    KeyVaultURL = "https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01"
    kvSecret = requests.get(url = KeyVaultURL, headers = {"Authorization": "Bearer " + data["access_token"]})
    
    print(kvSecret.json()["value"])
```

U de geheime waarde weergeven door de volgende code uit te voeren: 

```console
python Sample.py
```

In de voorgaande code ziet u hoe u bewerkingen met Azure Key Vault uitvoert op een virtuele Windows-machine. 

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer ze niet meer nodig zijn, verwijdert u de virtuele machine en uw sleutelkluis.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [REST-API van Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
