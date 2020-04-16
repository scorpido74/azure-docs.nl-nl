---
title: 'Snelstart: een sleutel instellen en ophalen uit Azure Key Vault'
description: Snel aan de slag met het instellen en ophalen van een sleutel van Azure Key Vault met Azure PowerShell
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.date: 03/30/2020
ms.author: mbaldwin
ms.openlocfilehash: c407c10327a80de6b3df18a3db3978468c9f8da0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424186"
---
# <a name="quickstart-set-and-retrieve-a-key-from-azure-key-vault-using-azure-powershell"></a>Snelstart: een sleutel instellen en ophalen uit Azure Key Vault met Azure PowerShell

In deze quickstart maakt u een sleutelkluis in Azure Key Vault met Azure PowerShell. Azure Key Vault is een cloudservice die werkt als een beveiligd geheimenarchief. U kunt veilig sleutels, wachtwoorden, certificaten en andere geheime informatie opslaan. U kunt het [Overzicht](../general/overview.md) raadplegen voor meer informatie over Key Vault. Azure PowerShell wordt gebruikt voor het maken en beheren van Azure-resources met opdrachten of scripts. Zodra dat u dat hebt voltooid, zult u een sleutel opslaan.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om PowerShell lokaal te installeren en gebruiken, is versie 1.0.0 of hoger van de Azure PowerShell-module vereist voor deze zelfstudie. Typ `$PSVersionTable.PSVersion` om de versie te bekijken. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Login-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een Azure-resourcegroep met behulp van de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. 

```azurepowershell-interactive
New-AzResourceGroup -Name ContosoResourceGroup -Location EastUS
```

## <a name="create-a-key-vault"></a>Een sleutelkluis maken

Vervolgens maakt u een sleutelkluis. Om deze stap te kunnen uitvoeren, hebt u enkele gegevens nodig:

Hoewel we "Contoso KeyVault2" gebruiken als de naam voor onze Key Vault tijdens deze quickstart, moet je een unieke naam gebruiken.

- **Kluisnaam** Contoso-Vault2.
- **Naam van resourcegroep** ContosoResourceGroup.
- **Locatie** Oost-VS.

```azurepowershell-interactive
New-AzKeyVault -Name 'Contoso-Vault2' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US'
```

De uitvoer van deze cmdlet toont eigenschappen van de nieuw gemaakte sleutelkluis. Let op de onderstaande twee eigenschappen:

* **Kluisnaam**: in het voorbeeld is dat **Contoso-Vault2**. U gebruikt deze naam voor andere Sleutelkluis-cmdlets.
* **Vault URI**: in dit voorbeeld is dat https://Contoso-Vault2.vault.azure.net/. Toepassingen die via de REST API gebruikmaken van uw kluis, moeten deze URI gebruiken.

Nadat de kluis is gemaakt, is uw Azure-account het enige account dat iets met deze nieuwe kluis mag doen.

![Uitvoer nadat de opdracht voor het maken van de sleutelkluis is voltooid](../media/quick-create-powershell/output-after-creating-keyvault.png)

## <a name="add-a-key-to-key-vault"></a>Een sleutel toevoegen aan Key Vault

Om een sleutel aan de kluis toe te voegen, hoeft u slechts een paar extra stappen te nemen. Deze sleutel kan worden gebruikt door een toepassing. 

Typ de onderstaande opdrachten om een zogenaamde **ExampleKey** te maken:

```azurepowershell-interactive
Add-AzKeyVaultKey -VaultName 'Contoso-Vault2' -Name 'ExampleKey' -Destination 'Software'
```

U nu verwijzen naar deze sleutel die u aan Azure Key Vault hebt toegevoegd met behulp van de URI. Gebruik **https://Contoso-Vault2.vault.azure.net/keys/ExampleKey** om de huidige versie te krijgen. 

Ga als u de eerder opgeslagen sleutel weergeven:

```azurepowershell-interactive
Get-AzKeyVaultKey -VaultName 'Contoso-Vault2' -KeyName 'ExampleKey'
```

Nu hebt u een Key Vault gemaakt, een sleutel opgeslagen en deze opgehaald.

## <a name="clean-up-resources"></a>Resources opschonen

Andere snelstartgidsen en zelfstudies in deze verzameling zijn gebaseerd op deze snelstartgids. Als u van plan bent om verder te gaan met volgende snelstarts en zelfstudies, kunt u deze resources intact laten.
Wanneer dit niet meer nodig is, u de opdracht [Verwijderen-AzResourceGroep](/powershell/module/az.resources/remove-azresourcegroup) gebruiken om de resourcegroep en alle gerelateerde resources te verwijderen. U kunt de resources als volgt verwijderen:

```azurepowershell-interactive
Remove-AzResourceGroup -Name ContosoResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Key Vault gemaakt en er een certificaat in opgeslagen. Ga verder naar de onderstaande artikelen voor meer informatie over Key Vault en hoe u deze integreren met uw toepassingen.

- Lees een [overzicht van Azure Key Vault](../general/overview.md)
- Zie de referentie voor de [azure PowerShell Key Vault-cmdlets](/powershell/module/az.keyvault/)
- Aanbevolen [procedures voor Azure Key Vault bekijken](../general/best-practices.md)
