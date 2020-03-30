---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines-windows
author: rothja
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/30/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: 2c7d312910c6d38c54b291da34bfb827246c7dad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "79504302"
---
## <a name="prepare-for-akv-integration"></a>Bereid je voor op AKV-integratie
Als u Azure Key Vault Integration wilt gebruiken om uw SQL Server VM te configureren, zijn er verschillende vereisten: 

1. [Azure Powershell installeren](#install)
2. [Een Azure Active Directory maken](#register)
3. [Een sleutelkluis maken](#createkeyvault)

In de volgende secties worden deze vereisten en de informatie beschreven die u moet verzamelen om later de PowerShell-cmdlets uit te voeren.

[!INCLUDE [updated-for-az](./updated-for-az.md)]

### <a name="install-azure-powershell"></a><a id="install"></a>Azure PowerShell installeren
Zorg ervoor dat u de nieuwste Azure PowerShell-module hebt geïnstalleerd. Zie [Azure PowerShell installeren en configureren](/powershell/azure/install-az-ps) voor meer informatie.

### <a name="register-an-application-in-your-azure-active-directory"></a><a id="register"></a>Een toepassing registreren in uw Azure Active Directory

Eerst moet u een [Azure Active Directory](https://azure.microsoft.com/trial/get-started-active-directory/) (AAD) in uw abonnement hebben. Onder de vele voordelen, dit u toestemming verlenen aan uw sleutel kluis voor bepaalde gebruikers en toepassingen.

Registreer vervolgens een aanvraag bij AAD. Dit geeft u een Service Principal-account dat toegang heeft tot uw sleutelkluis, die uw VM nodig heeft. In het azure key vault-artikel vindt u deze stappen in de sectie [Een toepassing registreren met Azure Active Directory](../articles/key-vault/key-vault-manage-with-cli2.md#registering-an-application-with-azure-active-directory) of ziet u de stappen met schermafbeeldingen in het gedeelte Een identiteit zoeken voor de **toepassingssectie** van [dit blogbericht](https://blogs.technet.com/b/kv/archive/2015/01/09/azure-key-vault-step-by-step.aspx). Voordat u deze stappen voltooit, moet u tijdens deze registratie de volgende gegevens verzamelen die later nodig zijn wanneer u Azure Key Vault Integration inschakelt op uw SQL VM.

* Nadat de toepassing is toegevoegd, zoekt u de **toepassings-id** (ook bekend als AAD ClientID of AppID) op het **registere-app-blad.**
    De toepassings-id wordt later toegewezen aan de parameter **$spName** (Service Principal name) in het PowerShell-script om Azure Key Vault Integration in te schakelen.

   ![Toepassings-id](./media/virtual-machines-sql-server-akv-prepare/aad-application-id.png)

* Kopieer tijdens deze stappen het geheim van uw sleutel, zoals in de volgende schermafbeelding wordt weergegeven. Dit sleutelgeheim wordt later toegewezen aan de **parameter $spSecret** (Service Principal secret) in het PowerShell-script.

   ![AAD geheim](./media/virtual-machines-sql-server-akv-prepare/aad-sp-secret.png)

* De toepassings-ID en het geheim worden ook gebruikt om een referentie in SQL Server te maken.

* U moet deze nieuwe toepassings-id (of client-id) autoriseren om de volgende toegangsmachtigingen te hebben: **get,** **wrapKey**, **unwrapKey**. Dit gebeurt met de [Cmdlet Set-AzKeyVaultAccessPolicy.](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) Zie overzicht [van Azure Key Vault](../articles/key-vault/key-vault-overview.md)voor meer informatie.

### <a name="create-a-key-vault"></a><a id="createkeyvault"></a>Een sleutelkluis maken
Om Azure Key Vault te gebruiken om de sleutels die u gebruikt voor versleuteling op te slaan in uw VM, hebt u toegang nodig tot een sleutelkluis. Als u uw sleutelkluis nog niet hebt ingesteld, maakt u er een door de stappen te volgen in het artikel Aan de [slag met Azure Key Vault.](../articles/key-vault/key-vault-overview.md) Voordat u deze stappen voltooit, is er een aantal informatie die u tijdens deze set moet verzamelen die later nodig is wanneer u Azure Key Vault Integration inschakelt op uw SQL VM.

    New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'

Wanneer u bij de stap Een sleutelkluis maken komt, let dan op de eigenschap returned **vaultUri,** de URL van de sleutelkluis. In het voorbeeld in die stap, hieronder weergegeven, is de naam van de sleutelkluis ContosoKeyVault, daarom zou de URL van de sleutelkluis zijn. https://contosokeyvault.vault.azure.net/

De URL van de sleutelkluis wordt later toegewezen aan de **parameter $akvURL** in het PowerShell-script om Azure Key Vault-integratie in te schakelen.

Nadat de sleutelkluis is gemaakt, moeten we een sleutel toevoegen aan de sleutelkluis, deze sleutel wordt doorverwezen wanneer we later een asymmetrische sleutelmaken in SQL Server maken.
