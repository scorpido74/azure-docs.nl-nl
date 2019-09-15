---
title: 'Quick Start: een geheim instellen en ophalen van Azure Key Vault met behulp van een node web app | Microsoft Docs'
description: In deze Quick Start kunt u een geheim van Azure Key Vault instellen en ophalen met behulp van een node web app
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: quickstart
ms.date: 09/03/2010
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 02b9c439a932a4b35700871e68bdad7f03451110
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003500"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-by-using-a-node-web-app"></a>Quickstart: Een geheim van Azure Key Vault instellen en ophalen met behulp van een knoop punt web-app 

In deze Quick start ziet u hoe u een geheim opslaat in Azure Key Vault en hoe u het kunt ophalen met behulp van een web-app. Met Key Vault kunt u de informatie beveiligen. Als u de geheime waarde wilt zien, moet u deze snelstartgids uitvoeren op Azure. In de snelstart wordt gebruikgemaakt van Node.js en beheerde identiteiten voor Azure-resources. In deze zelfstudie leert u procedures om het volgende te doen:

* Een sleutelkluis maken.
* Een geheim opslaan in de sleutelkluis.
* Een geheim ophalen uit de sleutelkluis.
* Een Azure-webtoepassing maken.
* Schakel een [Beheerde identiteit](../active-directory/managed-service-identity/overview.md) in voor de web-app.
* De vereiste machtigingen verlenen aan de webtoepassing om gegevens te lezen uit de sleutelkluis.

Voordat u doorgaat, moet u ervoor zorgen dat u bekend bent met de [basis concepten voor Key Vault](basic-concepts.md).

> [!NOTE]
> Key Vault is een centrale opslagplaats voor het opslaan van geheimen via een programma. Maar hiervoor moeten toepassingen en gebruikers eerst worden geverifieerd bij Key Vault, wat betekent dat ze een geheim moeten presenteren. Als u de aanbevolen procedures voor beveiliging wilt volgen, moet dit eerste geheim periodiek worden gerouleerd. 
>
> Met [beheerde service-identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md) krijgen toepassingen die in Azure worden uitgevoerd, een identiteit die automatisch door Azure wordt beheerd. Dit helpt bij het oplossen van het *probleem van het introduceren van geheimen* zodat gebruikers en toepassingen aanbevolen procedures kunnen volgen en zich geen zorgen hoeven maken over het rouleren van het eerste geheim.

## <a name="prerequisites"></a>Vereisten

* [Node.js](https://nodejs.org/en/)
* [Git](https://www.git-scm.com/)
* [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 2.0.4 of hoger. Voor deze Quick start moet u de Azure CLI lokaal uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](https://review.docs.microsoft.com/en-us/cli/azure/install-azure-cli?branch=master&view=azure-cli-latest).
* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="log-in-to-azure"></a>Meld u aan bij Azure.

Als u zich bij Azure wilt aanmelden met de Azure CLI, voert u het volgende in:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een resourcegroep met de opdracht [az group create](/cli/azure/group#az-group-create). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

Selecteer de naam van een resourcegroep en vul de tijdelijke aanduiding in.
In het volgende voor beeld wordt een resource groep gemaakt in de locatie VS-Oost.

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

De resourcegroep die u net hebt gemaakt, wordt overal in dit artikel gebruikt.

## <a name="create-a-key-vault"></a>Een sleutelkluis maken

Vervolgens maakt u een sleutel kluis met behulp van de resource groep die u in de vorige stap hebt gemaakt. Hoewel in dit artikel ' ContosoKeyVault ' als naam wordt gebruikt, moet u een unieke naam gebruiken. Geef de volgende informatie op:

* De naam van de sleutel kluis.
* Naam van de resourcegroep. De naam moet een tekenreeks zijn met 3-24 tekens en mag alleen 0-9, a-z, A-Z en een streepje (-) bevatten.
* Locatie: **US - oost**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "East US"
```

Vanaf dit punt is uw Azure-account nu als enige gemachtigd om bewerkingen op deze nieuwe kluis uit te voeren.

## <a name="add-a-secret-to-the-key-vault"></a>Een geheim toevoegen aan de sleutelkluis

We gaan een geheim toevoegen om te laten zien hoe dit werkt. U wilt bijvoorbeeld een SQL-verbindingsreeks opslaan of andere gegevens die u veilig wilt bewaren, maar wel beschikbaar wilt stellen aan uw toepassing. In deze zelfstudie gebruiken we het geheim **AppSecret** en slaan we de waarde van **MySecret** op in het geheim.

Typ de volgende opdrachten om een geheim te maken in de sleutelkluis met de naam **AppGeheim**. Met dit geheim wordt de waarde **MijnGeheim** opgeslagen.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Als u de waarde in het geheim als tekst zonder opmaak wilt weergeven:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Met deze opdracht vraagt u de geheime gegevens op, inclusief de URI. Wanneer u deze stappen hebt uitgevoerd, beschikt u over een URI voor een geheim in een sleutelkluis. Noteer deze informatie. U hebt deze in een latere stap nodig.

## <a name="clone-the-repo"></a>De opslagplaats klonen

Kloon de opslagplaats om een lokale kopie te maken waarin u de bron kunt bewerken. Voer de volgende opdracht uit:

```
git clone https://github.com/Azure-Samples/key-vault-node-quickstart.git
```

## <a name="install-dependencies"></a>Afhankelijkheden installeren

Voer de volgende opdrachten uit om afhankelijkheden te installeren:

```
cd key-vault-node-quickstart
npm install
```

In dit project worden twee knooppunt modules gebruikt: [MS-rest-Azure](https://www.npmjs.com/package/ms-rest-azure) en [Azure-sleutel kluis](https://www.npmjs.com/package/azure-keyvault).

## <a name="publish-the-web-app-to-azure"></a>De web-app publiceren in Azure

Een [Azure app service](https://azure.microsoft.com/services/app-service/) -abonnement maken. In dit plan kunt u meerdere web-apps opslaan.

    ```
    az appservice plan create --name myAppServicePlan --resource-group myResourceGroup
    ```
Maak vervolgens een web-app. Vervang `<app_name>` in het volgende voor beeld door een globaal unieke app-naam (geldige tekens zijn a-z, 0-9 en-). De runtime wordt ingesteld op NODE|6.9. Als u alle ondersteunde Runtimes wilt weer `az webapp list-runtimes`geven, voert u uit.

    ```
    # Bash
    az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "NODE|6.9" --deployment-local-git
    ```
Wanneer de web-app is gemaakt, toont de Azure CLI soortgelijke uitvoer als in het volgende voorbeeld:

    ```
    {
      "availabilityState": "Normal",
      "clientAffinityEnabled": true,
      "clientCertEnabled": false,
      "cloningInfo": null,
      "containerSize": 0,
      "dailyMemoryTimeQuota": 0,
      "defaultHostName": "<app_name>.azurewebsites.net",
      "enabled": true,
      "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git"
      < JSON data removed for brevity. >
    }
    ```
Blader naar de zojuist gemaakte web-app en u ziet dat deze werkt. Vervang `<app_name>` door een unieke app-naam.

    ```
    http://<app name>.azurewebsites.net
    ```
Met de voor gaande opdracht wordt ook een app met git-functionaliteit gemaakt waarmee u naar Azure kunt implementeren vanuit uw lokale Git-opslag plaats. De lokale Git-opslag plaats is geconfigureerd met deze URL `https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git`:.

Nadat u de voor gaande opdracht hebt voltooid, kunt u een externe Azure-toepassing toevoegen aan uw lokale Git-opslag plaats. Vervang `<url>` door de URL van het git-opslag plaats.

    ```
    git remote add azure <url>
    ```

## <a name="enable-a-managed-identity-for-the-web-app"></a>Een beheerde identiteit voor de web-app inschakelen

Azure Key Vault biedt een manier voor het veilig opslaan van referenties en andere sleutels en geheimen, maar uw code moet worden geverifieerd voor Key Vault om ze op te halen. In [Overzicht van beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md) wordt het oplossen van dit probleem eenvoudiger gemaakt door Azure-services een automatisch beheerde identiteit in Azure Active Directory (Azure AD) te geven. U kunt deze identiteit gebruiken voor verificatie bij alle services die ondersteuning bieden voor Azure AD-verificatie, inclusief Key Vault, zonder dat u referenties in uw code hoeft te hebben.

Voer de opdracht identity assign uit om de identiteit voor deze toepassing te maken:

```azurecli
az webapp identity assign --name <app_name> --resource-group "<YourResourceGroupName>"
```

U kunt dit ook doen door naar de portal te gaan en de instelling **Identity/System assigned** op **Aan** te zetten in de eigenschappen van de webtoepassing.

### <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>Machtigingen toewijzen aan uw toepassing voor het lezen van geheimen uit Key Vault

Noteer de uitvoer van de vorige opdracht. Deze moet de volgende indeling hebben:
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
Voer vervolgens de volgende opdracht uit met behulp van de naam van de sleutel kluis en de waarde van **principalId**:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get set
```

## <a name="deploy-the-node-app-to-azure-and-retrieve-the-secret-value"></a>De node-app implementeren in Azure en de geheime waarde ophalen

Voer de volgende opdracht uit om de app te implementeren in Azure:

```
git push azure master
```

Wanneer u vervolgens naar `https://<app_name>.azurewebsites.net`bladert, kunt u de geheime waarde zien. Zorg ervoor dat u de naam `<YourKeyVaultName>` hebt vervangen door de naam van uw kluis.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een Key Vault gemaakt en een geheim opgeslagen. Ga verder met de volgende artikelen voor meer informatie over Key Vault en hoe u deze integreert met uw toepassingen.

- Een [overzicht van Azure Key Vault](key-vault-overview.md) lezen
- Raadpleeg de [Azure Key Vault hand leiding voor ontwikkel aars](key-vault-developers-guide.md)
- Meer informatie over [sleutels, geheimen en certificaten](about-keys-secrets-and-certificates.md)
- [Azure Key Vault aanbevolen procedures](key-vault-best-practices.md) controleren