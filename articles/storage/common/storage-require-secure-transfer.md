---
title: Veilige overdracht vereisen om veilige verbindingen te garanderen
titleSuffix: Azure Storage
description: Meer informatie over het vereisen van veilige overdracht voor aanvragen naar Azure Storage. Wanneer u een veilige overdracht voor een opslagaccount nodig hebt, worden aanvragen die afkomstig zijn van een onveilige verbinding geweigerd.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/21/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 125f4188ed3f12f366c619af9efe3aa203987c19
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870520"
---
# <a name="require-secure-transfer-to-ensure-secure-connections"></a>Veilige overdracht vereisen om veilige verbindingen te garanderen

U uw opslagaccount configureren om aanvragen van beveiligde verbindingen alleen te accepteren door de vereiste eigenschap **Secure transfer** voor het opslagaccount in te stellen. Wanneer u een veilige overdracht nodig hebt, worden aanvragen die afkomstig zijn van een onveilige verbinding afgewezen. Microsoft raadt u aan altijd een veilige overdracht voor al uw opslagaccounts te vereisen.

Wanneer een veilige overdracht vereist is, moet een aanroep naar een Azure Storage REST API-bewerking via HTTPS worden uitgevoerd. Elk verzoek dat via HTTP wordt ingediend, wordt afgewezen.

Verbinding maken met een Azure File-share via SMB zonder versleuteling mislukt wanneer beveiligde overdracht vereist is voor het opslagaccount. Voorbeelden van onveilige verbindingen zijn die gemaakt via SMB 2.1, SMB 3.0 zonder encryptie, of sommige versies van de Linux SMB-client.

Standaard is de vereiste eigenschap **Secure transfer** ingeschakeld wanneer u een opslagaccount maakt.

> [!NOTE]
> Omdat Azure Storage geen HTTPS ondersteunt voor aangepaste domeinnamen, wordt deze optie niet toegepast wanneer u een aangepaste domeinnaam gebruikt. En klassieke opslagaccounts worden niet ondersteund.

## <a name="require-secure-transfer-in-the-azure-portal"></a>Beveiligde overdracht vereisen in de Azure-portal

U de vereiste eigenschap **Secure transfer** inschakelen wanneer u een opslagaccount maakt in de [Azure-portal.](https://portal.azure.com) U het ook inschakelen voor bestaande opslagaccounts.

### <a name="require-secure-transfer-for-a-new-storage-account"></a>Veilige overdracht vereisen voor een nieuw opslagaccount

1. Open het deelvenster **Opslagaccount maken** in de Azure-portal.
1. Selecteer Onder **Beveiligde overdracht vereist**de optie **Ingeschakeld**.

   ![Het blad van het opslagaccount maken](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>Veilige overdracht vereisen voor een bestaand opslagaccount

1. Selecteer een bestaand opslagaccount in de Azure-portal.
1. Selecteer **Configuratie**in het menuvenster opslagaccount onder **INSTELLINGEN**.
1. Selecteer Onder **Beveiligde overdracht vereist**de optie **Ingeschakeld**.

   ![Menuvenster Opslagaccount](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="require-secure-transfer-from-code"></a>Veilige overdracht van code vereisen

Als u een veilige overdracht programmatisch wilt vereisen, stelt u de eigenschap _supportsHttpsTrafficOnly_ in op het opslagaccount. U deze eigenschap instellen met behulp van de REST API, clientbibliotheken of hulpprogramma's voor storage resource provider rest:

* [REST-API](/rest/api/storagerp/storageaccounts)
* [PowerShell](/powershell/module/az.storage/set-azstorageaccount)
* [CLI](/cli/azure/storage/account)
* [NodeJS](https://www.npmjs.com/package/azure-arm-storage/)
* [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage)
* [Python-SDK](https://pypi.org/project/azure-mgmt-storage)
* [Ruby SDK](https://rubygems.org/gems/azure_mgmt_storage)

## <a name="require-secure-transfer-with-powershell"></a>Veilige overdracht vereisen met PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Voor dit voorbeeld is de Azure PowerShell-module Az-versie 0.7 of hoger vereist. Voer `Get-Module -ListAvailable Az` uit om de versie te bekijken. Als u PowerShell wilt installeren of upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps).

Voer `Connect-AzAccount` uit om een verbinding op te zetten met Azure.

 Gebruik de volgende opdrachtregel om de instelling te controleren:

```powershell
Get-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}"
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : False
...

```

Gebruik de volgende opdrachtregel om de instelling in te schakelen:

```powershell
Set-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}" -EnableHttpsTrafficOnly $True
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : True
...

```

## <a name="require-secure-transfer-with-azure-cli"></a>Veilige overdracht vereisen met Azure CLI

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 Gebruik de volgende opdracht om de instelling te controleren:

```azurecli-interactive
az storage account show -g {ResourceGroupName} -n {StorageAccountName}
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": false,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

Gebruik de volgende opdracht om de instelling in te schakelen:

```azurecli-interactive
az storage account update -g {ResourceGroupName} -n {StorageAccountName} --https-only true
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": true,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

## <a name="next-steps"></a>Volgende stappen

[Beveiligingsaanbevelingen voor Blob-opslag](../blobs/security-recommendations.md)
