---
title: PowerShell-opdrachten uitvoeren met Azure AD-referenties om toegang te krijgen tot blob- of wachtrijgegevens
titleSuffix: Azure Storage
description: PowerShell ondersteunt het aanmelden met Azure AD-referenties om opdrachten uit te voeren op azure storageblob- en wachtrijgegevens. Er wordt een toegangstoken voor de sessie verstrekt en wordt gebruikt om oproepbewerkingen te autoriseren. Machtigingen zijn afhankelijk van de RBAC-rol die is toegewezen aan de Ad-beveiligingsprincipal van Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/30/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 373b939ac63f31ccaf6a9f01fac92920e19074ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75553447"
---
# <a name="run-powershell-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>PowerShell-opdrachten uitvoeren met Azure AD-referenties om toegang te krijgen tot blob- of wachtrijgegevens

Azure Storage biedt extensies voor PowerShell waarmee u scriptopdrachten aanmelden en uitvoeren met Azure AD-referenties (Azure Active Directory). Wanneer u zich aanmeldt bij PowerShell met Azure AD-referenties, wordt een OAuth 2.0-toegangstoken geretourneerd. Dat token wordt automatisch gebruikt door PowerShell om latere gegevensbewerkingen te autoriseren tegen Blob- of Queue-opslag. Voor ondersteunde bewerkingen hoeft u niet langer een accountsleutel of SAS-token met de opdracht door te geven.

U machtigingen toewijzen aan blob- en wachtrijgegevens aan een Azure AD-beveiligingsprincipal via RBAC (Role-based Access Control). Zie [Toegangsrechten voor Azure Storage beheren met RBAC](storage-auth-aad-rbac.md)voor meer informatie over RBAC-rollen in Azure Storage.

## <a name="supported-operations"></a>Ondersteunde bewerkingen

De Azure Storage-extensies worden ondersteund voor bewerkingen op blob- en wachtrijgegevens. Welke bewerkingen u aanroepen, is afhankelijk van de machtigingen die zijn verleend aan de Azure AD-beveiligingsprincipal waarmee u zich aanmeldt bij PowerShell. Machtigingen voor Azure Storage-containers of wachtrijen worden toegewezen via RBAC. Als u bijvoorbeeld de rol **Kblob-gegevenslezer** hebt toegewezen, u scriptopdrachten uitvoeren die gegevens uit een container of wachtrij lezen. Als u de rol **Blob-gegevensinzender** hebt toegewezen, u scriptopdrachten uitvoeren die een container of wachtrij of de gegevens die deze bevatten, lezen, schrijven of verwijderen.

Zie [Opslagbewerkingen bellen met OAuth-tokens](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens)voor meer informatie over de machtigingen die nodig zijn voor elke Azure Storage-bewerking in een container of wachtrij.  

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>PowerShell-opdrachten aanroepen met Azure AD-referenties

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Als u Azure PowerShell wilt gebruiken om in te loggen en latere bewerkingen tegen Azure Storage `-UseConnectedAccount` uit te voeren met Azure AD-referenties, maakt u een opslagcontext om naar het opslagaccount te verwijzen en de parameter op te nemen.

In het volgende voorbeeld ziet u hoe u een container maakt in een nieuw opslagaccount van Azure PowerShell met uw Azure AD-referenties. Vergeet niet om plaatsaanduidingswaarden in hoekhaakjes te vervangen door uw eigen waarden:

1. Meld u aan bij uw Azure-account met de opdracht [Connect-AzAccount:](/powershell/module/az.accounts/connect-azaccount)

    ```powershell
    Connect-AzAccount
    ```

    Zie [Aanmelden bij Azure PowerShell](/powershell/azure/authenticate-azureps)voor meer informatie over het aanmelden bij Azure.

1. Maak een Azure-brongroep door [Nieuwe AzResourceGroep aan te](/powershell/module/az.resources/new-azresourcegroup)roepen . 

    ```powershell
    $resourceGroup = "sample-resource-group-ps"
    $location = "eastus"
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

1. Maak een opslagaccount aan door [nieuw-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount)te bellen .

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
      -Name "<storage-account>" `
      -SkuName Standard_LRS `
      -Location $location `
    ```

1. Download de context van het opslagaccount die het nieuwe opslagaccount opgeeft door [Nieuw-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext)aan te roepen. Wanneer u handelt op een opslagaccount, u verwijzen naar de context in plaats van herhaaldelijk de referenties door te geven. Voeg `-UseConnectedAccount` de parameter toe om eventuele volgende gegevensbewerkingen aan te roepen met uw Azure AD-referenties:

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. Voordat u de container maakt, wijst u de rol [Opslagblob-gegevensbijdrage raan](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) toe aan uzelf. Hoewel u de eigenaar van het account bent, hebt u expliciete machtigingen nodig om gegevensbewerkingen uit te voeren tegen het opslagaccount. Zie [Toegang verlenen tot Azure blob- en wachtrijgegevens met RBAC in de Azure-portal](storage-auth-aad-rbac.md)voor meer informatie over het toewijzen van RBAC-rollen.

    > [!IMPORTANT]
    > RBAC-roltoewijzingen kunnen enkele minuten duren voordat ze worden gepropageerd.

1. Maak een container door [Nieuw-AzStorageContainer aan](/powershell/module/az.storage/new-azstoragecontainer)te roepen. Omdat deze aanroep de context gebruikt die in de vorige stappen is gemaakt, wordt de container gemaakt met uw Azure AD-referenties.

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>Volgende stappen

- [PowerShell gebruiken om een RBAC-rol toe te wijzen voor toegang tot blob- en wachtrijgegevens](storage-auth-aad-rbac-powershell.md)
- [Toegang tot blob- en wachtrijgegevens met beheerde identiteiten voor Azure-bronnen autoriseren](storage-auth-aad-msi.md)
