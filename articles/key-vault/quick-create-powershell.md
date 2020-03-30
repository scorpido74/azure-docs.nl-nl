---
title: 'Snelstart: stel & een geheim op te halen uit Key Vault met PowerShell'
description: In deze quickstart leert u hoe u geheimen uit een Azure Key Vault maakt, ophaalt en verwijdert met Azure PowerShell.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/08/2019
ms.author: mbaldwin
ms.openlocfilehash: 627d74f48c0f2b3da8665cd255102f36869477c2
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79472755"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-powershell"></a>Snelstart: Een geheim uit Azure Key Vault instellen en ophalen met PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure Key Vault is een cloudservice die werkt als een beveiligd geheimenarchief. U kunt veilig sleutels, wachtwoorden, certificaten en andere geheime informatie opslaan. Voor meer informatie over Key Vault u het [overzicht](key-vault-overview.md)bekijken. In deze snelstart gebruikt u PowerShell om een sleutelkluis te maken. Vervolgens slaat u een geheim op in de zojuist gemaakte kluis.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

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

Hoewel we in deze snelstart 'Contoso KeyVault2' als naam voor de sleutelkluis gebruiken, moet u een unieke naam opgeven.

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

![Uitvoer nadat de opdracht voor het maken van de sleutelkluis is voltooid](./media/quick-create-powershell/output-after-creating-keyvault.png)

## <a name="adding-a-secret-to-key-vault"></a>Een geheim toevoegen aan Key Vault

Als u een geheim wilt toevoegen aan de kluis, hoeft u maar een paar stappen uit te voeren. In dit geval voegt u een wachtwoord toe dat door een toepassing kan worden gebruikt. Het wachtwoord heeft de naam **ExamplePassword** en slaat daarin de waarde van **hVFkk965BuUv** op.

Converteer eerst de waarde van **hVFkk965BuUv** naar een beveiligde tekenreeks door het volgende te typen:

```azurepowershell-interactive
$secretvalue = ConvertTo-SecureString 'hVFkk965BuUv' -AsPlainText -Force
```

Typ vervolgens de onderstaande PowerShell-opdrachten om een geheim te maken in Key Vault met de naam **ExamplePassword** en de waarde **hVFkk965BuUv**:

```azurepowershell-interactive
$secret = Set-AzKeyVaultSecret -VaultName 'Contoso-Vault2' -Name 'ExamplePassword' -SecretValue $secretvalue
```

Als u de waarde in het geheim als tekst zonder opmaak wilt weergeven:

```azurepowershell-interactive
(Get-AzKeyVaultSecret -vaultName "Contoso-Vault2" -name "ExamplePassword").SecretValueText
```

U hebt nu een sleutelkluis gemaakt, een geheim opgeslagen en dat vervolgens opgehaald.

## <a name="clean-up-resources"></a>Resources opschonen

 Andere snelstartgidsen en zelfstudies in deze verzameling zijn gebaseerd op deze snelstartgids. Als u van plan bent om verder te gaan met andere snelstarts en zelfstudies, kunt u deze resources intact laten.

U kunt de opdracht [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) gebruiken om de resourcegroep, sleutelkluis en alle gerelateerde resources te verwijderen wanneer u deze niet meer nodig hebt.

```azurepowershell-interactive
Remove-AzResourceGroup -Name ContosoResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart heb je een Key Vault gemaakt en er een geheim in opgeslagen. Ga verder naar de onderstaande artikelen voor meer informatie over Key Vault en hoe u deze integreren met uw toepassingen.

- Lees een [overzicht van Azure Key Vault](key-vault-overview.md)
- Zie de referentie voor de [azure PowerShell Key Vault-cmdlets](/powershell/module/az.keyvault/?view=azps-2.6.0#key_vault)
- Meer informatie over [sleutels, geheimen en certificaten](about-keys-secrets-and-certificates.md)
- Aanbevolen [procedures voor Azure Key Vault bekijken](key-vault-best-practices.md)
