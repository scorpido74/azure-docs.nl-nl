---
title: Zelf studie-Azure Key Vault gebruiken met een virtuele Windows-machine in python | Microsoft Docs
description: In deze zelf studie configureert u een ASP.NET-kern toepassing voor het lezen van een geheim in uw sleutel kluis.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 4078f2a005dcd324fa9c1d31cdca4b1f8337eecd
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988376"
---
# <a name="tutorial-use-azure-key-vault-with-a-windows-virtual-machine-in-python"></a>Zelf studie: Azure Key Vault gebruiken met een virtuele Windows-machine in python

Azure Key Vault helpt u bij het beveiligen van geheimen zoals API-sleutels, de database verbindings reeksen die u nodig hebt om toegang te krijgen tot uw toepassingen, services en IT-resources.

In deze zelf studie leert u hoe u een console toepassing kunt ophalen om informatie uit Azure Key Vault te lezen. Hiervoor gebruikt u beheerde identiteiten voor Azure-resources. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een sleutelkluis maken.
> * Voeg een geheim toe aan de sleutelkluis.
> * Een geheim ophalen uit de sleutelkluis.
> * Een virtuele Azure-machine maken.
> * Een beheerde identiteit in te scha kelen.
> * Machtigingen toewijzen aan de VM-identiteit.

Lees [Key Vault basis concepten](basic-concepts.md)voordat u begint. 

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Vereisten

Voor Windows, Mac en Linux:
  * [Git](https://git-scm.com/downloads)
  * Voor deze zelfstudie moet u de Azure CLI lokaal uitvoeren. U moet de Azure CLI-versie 2.0.4 of hoger hebben geïnstalleerd. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](https://review.docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="about-managed-service-identity"></a>Informatie over Managed Service Identity

Azure Key Vault referenties veilig opgeslagen, zodat ze niet in uw code worden weer gegeven. U moet u echter verifiëren bij Azure Key Vault om uw sleutels op te halen. En om u te verifiëren bij Key Vault, hebt u een referentie nodig. Dit is een klassiek bootstrap-dilemma. In MSI (Managed Service Identity) is dit probleem opgelost doordat er een _bootstrap-identiteit_ wordt geboden dat het proces vereenvoudigt.

Wanneer u MSI inschakelt voor een Azure-service, zoals Azure Virtual Machines, Azure App Service of Azure Functions, maakt Azure een [Service-Principal](basic-concepts.md). MSI doet dit voor het exemplaar van de service in Azure Active Directory (Azure AD) en activeert de referenties van de Service-Principal in dat exemplaar. 

![MSI](media/MSI.png)

Om een toegangs token op te halen, roept uw code een lokale meta gegevens service aan die beschikbaar is op de Azure-resource. Voor de verificatie bij een Azure Key Vault-service gebruikt uw code het toegangs token dat het ontvangt van het lokale MSI-eind punt. 

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

In deze zelf studie gebruikt u de zojuist gemaakte resource groep.

## <a name="create-a-key-vault"></a>Een sleutelkluis maken

Als u een sleutel kluis wilt maken in de resource groep die u in de vorige stap hebt gemaakt, geeft u de volgende informatie op:

* Sleutel kluis naam: een teken reeks van 3 tot 24 tekens die alleen getallen (0-9), letters (a-z, A-Z) en afbreek streepjes (-) kan bevatten
* Naam van de resourcegroep
* Locatie: **US - west**

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
Op dit moment is uw Azure-account de enige die gemachtigd is voor het uitvoeren van bewerkingen op deze nieuwe sleutel kluis.

## <a name="add-a-secret-to-the-key-vault"></a>Een geheim toevoegen aan de sleutelkluis

We gaan een geheim toevoegen om te laten zien hoe dit werkt. Het geheim kan een SQL-connection string zijn of enige andere informatie die u nodig hebt om zowel veilig als beschikbaar te stellen voor uw toepassing.

Als u een geheim wilt maken in de sleutel kluis met de naam **AppSecret**, voert u de volgende opdracht in:

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Met dit geheim wordt de waarde **MijnGeheim** opgeslagen.

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken
U kunt een virtuele machine maken met behulp van een van de volgende methoden:

* [De Azure CLI](../virtual-machines/windows/quick-create-cli.md)
* [PowerShell](../virtual-machines/windows/quick-create-powershell.md)
* [Azure Portal](../virtual-machines/windows/quick-create-portal.md)

## <a name="assign-an-identity-to-the-vm"></a>Een identiteit toewijzen aan de virtuele machine
In deze stap maakt u een door het systeem toegewezen identiteit voor de virtuele machine door de volgende opdracht uit te voeren in de Azure CLI:

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Let op de door het systeem toegewezen identiteit die in de volgende code wordt weer gegeven. De uitvoer van de voor gaande opdracht zou zijn: 

```azurecli
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="assign-permissions-to-the-vm-identity"></a>Machtigingen toewijzen aan de VM-identiteit
U kunt nu de eerder gemaakte identiteits machtigingen toewijzen aan uw sleutel kluis door de volgende opdracht uit te voeren:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-on-to-the-virtual-machine"></a>Aanmelden bij de virtuele machine

Als u zich wilt aanmelden bij de virtuele machine, volgt u de instructies in [verbinding maken en aanmelden bij een virtuele Azure-machine waarop Windows wordt uitgevoerd](../virtual-machines/windows/connect-logon.md).

## <a name="create-and-run-a-sample-python-app"></a>Een voor beeld van een python-app maken en uitvoeren

In de volgende sectie vindt u een voorbeeld bestand met de naam *sample.py*. Er wordt gebruikgemaakt van een bibliotheek [aanvragen](https://2.python-requests.org/en/master/) om http-aanroepen te maken.

## <a name="edit-samplepy"></a>Sample.py bewerken

Nadat u *sample.py*hebt gemaakt, opent u het bestand en kopieert u de code in deze sectie. 

De code geeft een proces met twee stappen:
1. Haal een token uit het lokale MSI-eindpunt op de virtuele machine op.  
  Als u dit wel doet, wordt er ook een token uit Azure AD opgehaald.
1. Geef het token door aan de sleutel kluis en haal vervolgens uw geheim op. 

```
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

U kunt de geheime waarde weer geven door de volgende code uit te voeren: 

```
python Sample.py
```

In de voorgaande code ziet u hoe u bewerkingen met Azure Key Vault uitvoert op een virtuele Windows-machine. 

## <a name="clean-up-resources"></a>Resources opschonen

Als ze niet meer nodig zijn, verwijdert u de virtuele machine en de sleutel kluis.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [REST-API van Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
