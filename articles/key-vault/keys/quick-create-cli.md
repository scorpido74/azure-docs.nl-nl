---
title: 'Snelstartgids: een sleutel van Azure Key Vault instellen en ophalen'
description: Quick Start laat zien hoe u een sleutel van Azure Key Vault kunt instellen en ophalen met behulp van Azure CLI
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.date: 03/30/2020
ms.author: mbaldwin
ms.openlocfilehash: defc5317c127d771786989748e404285ca0c0584
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424207"
---
# <a name="quickstart-set-and-retrieve-a-key-from-azure-key-vault-using-azure-cli"></a>Snelstartgids: een sleutel van Azure Key Vault instellen en ophalen met behulp van Azure CLI

In deze Quick Start maakt u een sleutel kluis in Azure Key Vault met Azure CLI. Azure Key Vault is een cloudservice die werkt als een beveiligd geheimenarchief. U kunt veilig sleutels, wachtwoorden, certificaten en andere geheime informatie opslaan. U kunt het [Overzicht](../general/overview.md) raadplegen voor meer informatie over Key Vault. Azure CLI wordt gebruikt voor het maken en beheren van Azure-resources met behulp van opdrachten of scripts. Zodra u klaar bent, slaat u een sleutel op.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze snelstart versie 2.0.4 of hoger van Azure CLI uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren]( /cli/azure/install-azure-cli).

Als u zich wilt aanmelden bij Azure met behulp van de CLI, typt u:

```azurecli
az login
```

Zie [Aanmelden met Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) voor meer informatie over opties voor aanmelding via de CLI

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. In het volgende voorbeeld wordt een resourcegroep met de naam *ContosoResourceGroup* in de locatie *eastus* gemaakt.

```azurecli
az group create --name "ContosoResourceGroup" --location eastus
```

## <a name="create-a-key-vault"></a>Een sleutelkluis maken

Vervolgens maakt u een sleutelkluis in de resourcegroep die u in de vorige stap hebt gemaakt. U moet enkele gegevens verstrekken:

- Voor deze snelstart gebruiken we **Contoso-vault2**. U moet in de test een unieke naam opgeven.
- De naam van de resource groep **ContosoResourceGroup**.
- De locatie **VS - oost**.

```azurecli
az keyvault create --name "Contoso-Vault2" --resource-group "ContosoResourceGroup" --location eastus
```

De uitvoer van deze cmdlet toont eigenschappen van de nieuw gemaakte sleutelkluis. Let op de onderstaande twee eigenschappen:

- **Kluisnaam**: in het voorbeeld is dat **Contoso-Vault2**. U gebruikt deze naam voor andere Key Vault-opdrachten.
- **Vault URI**: In het voorbeeld is dit https://contoso-vault2.vault.azure.net/. Toepassingen die via de REST API gebruikmaken van uw kluis, moeten deze URI gebruiken.

Vanaf dit punt is uw Azure-account nu als enige gemachtigd om bewerkingen op deze nieuwe kluis uit te voeren.

## <a name="add-a-key-to-key-vault"></a>Een sleutel toevoegen aan Key Vault

Als u een sleutel wilt toevoegen aan de kluis, hoeft u alleen maar een paar extra stappen uit te voeren. Deze sleutel kan worden gebruikt door een toepassing. 

Typ de onderstaande opdrachten om een aangeroepen **ExampleKey** te maken:

```azurecli
az keyvault key create --vault-name "Contoso-Vault2" -n ExampleKey --protection software
```

U kunt nu verwijzen naar deze sleutel die u aan Azure Key Vault hebt toegevoegd met behulp van de bijbehorende URI. Gebruiken **https://Contoso-Vault2.vault.azure.net/keys/ExampleKey** om de huidige versie op te halen. 

Eerder opgeslagen sleutel weer geven:

```azurecli

az keyvault key show --name "ExampleKey" --vault-name "Contoso-Vault2"
```

U hebt nu een Key Vault gemaakt, een sleutel opgeslagen en deze opgehaald.

## <a name="clean-up-resources"></a>Resources opschonen

Andere snelstartgidsen en zelfstudies in deze verzameling zijn gebaseerd op deze snelstartgids. Als u van plan bent om verder te gaan met volgende snelstarts en zelfstudies, kunt u deze resources intact laten.
U kunt de opdracht [az group delete](/cli/azure/group) gebruiken om de resourcegroep en alle gerelateerde resources te verwijderen wanneer u ze niet meer nodig hebt. U kunt de resources als volgt verwijderen:

```azurecli
az group delete --name ContosoResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een Key Vault gemaakt en een sleutel in het bestand opgeslagen. Ga verder met de volgende artikelen voor meer informatie over Key Vault en hoe u deze integreert met uw toepassingen.

- Een [overzicht van Azure Key Vault](../general/overview.md) lezen
- Zie de naslag informatie voor de [Azure cli AZ](/cli/azure/keyvault?view=azure-cli-latest) -sleutel kluis-opdrachten
- [Azure Key Vault aanbevolen procedures](../general/best-practices.md) controleren
