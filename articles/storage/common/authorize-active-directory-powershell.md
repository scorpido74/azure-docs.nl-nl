---
title: Power shell-opdrachten uitvoeren met Azure AD-referenties voor toegang tot BLOB-of wachtrij gegevens
titleSuffix: Azure Storage
description: Power shell ondersteunt aanmelden met Azure AD-referenties om opdrachten uit te voeren op Azure Storage Blob-en Queue-gegevens. Er wordt een toegangs token voor de sessie gegeven en gebruikt om aanroepende bewerkingen te autoriseren. De machtigingen zijn afhankelijk van de RBAC-rol die is toegewezen aan de Azure AD-beveiligings-principal.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/30/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 373b939ac63f31ccaf6a9f01fac92920e19074ed
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75553447"
---
# <a name="run-powershell-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>Power shell-opdrachten uitvoeren met Azure AD-referenties voor toegang tot BLOB-of wachtrij gegevens

Azure Storage biedt uitbrei dingen voor Power shell waarmee u zich kunt aanmelden en script opdrachten kunt uitvoeren met de referenties voor Azure Active Directory (Azure AD). Wanneer u zich aanmeldt bij Power shell met Azure AD-referenties, wordt een OAuth 2,0-toegangs token geretourneerd. Dit token wordt automatisch door Power shell gebruikt voor het autoriseren van volgende gegevens bewerkingen op BLOB-of wachtrij opslag. Voor ondersteunde bewerkingen hoeft u geen account sleutel of SAS-token meer door te geven met de opdracht.

U kunt machtigingen toewijzen aan Blob-en wachtrij gegevens aan een Azure AD-beveiligingsprincipal via op rollen gebaseerd toegangs beheer (RBAC). Zie [Manage access rights to Azure Storage Data with RBAC](storage-auth-aad-rbac.md)(Engelstalig) voor meer informatie over RBAC-rollen in azure Storage.

## <a name="supported-operations"></a>Ondersteunde bewerkingen

De Azure Storage-extensies worden ondersteund voor bewerkingen op Blob-en wachtrij gegevens. Welke bewerkingen u kunt aanroepen, is afhankelijk van de machtigingen die zijn verleend aan de Azure AD-beveiligings-principal waarmee u zich aanmeldt bij Power shell. Machtigingen voor het Azure Storage van containers of wacht rijen worden toegewezen via RBAC. Als u bijvoorbeeld de rol **BLOB data Reader** hebt toegewezen, kunt u script opdrachten uitvoeren die gegevens uit een container of wachtrij lezen. Als u de rol **BLOB data contributor** hebt toegewezen, kunt u script opdrachten uitvoeren die een container of wachtrij lezen, schrijven of verwijderen, of de gegevens die ze bevatten.

Zie [opslag bewerkingen aanroepen met OAuth-tokens](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens)voor meer informatie over de vereiste machtigingen voor elke Azure Storage bewerking in een container of wachtrij.  

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>Power shell-opdrachten aanroepen met Azure AD-referenties

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Als u Azure PowerShell wilt gebruiken om u aan te melden en volgende bewerkingen uit te voeren op Azure Storage met behulp van Azure AD-referenties, maakt u een opslag context om te verwijzen naar het opslag account en neemt u de para meter `-UseConnectedAccount` op.

In het volgende voor beeld ziet u hoe u een container maakt in een nieuw opslag account op basis van Azure PowerShell met uw Azure AD-referenties. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen punt haken te vervangen door uw eigen waarden:

1. Meld u aan bij uw Azure-account met de opdracht [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) :

    ```powershell
    Connect-AzAccount
    ```

    Zie [Aanmelden met Azure PowerShell](/powershell/azure/authenticate-azureps)voor meer informatie over het aanmelden bij Azure met Power shell.

1. Maak een Azure-resource groep door [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)aan te roepen. 

    ```powershell
    $resourceGroup = "sample-resource-group-ps"
    $location = "eastus"
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

1. Maak een opslag account door [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount)aan te roepen.

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
      -Name "<storage-account>" `
      -SkuName Standard_LRS `
      -Location $location `
    ```

1. Haal de context van het opslag account op waarmee het nieuwe opslag account wordt opgegeven door [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext)aan te roepen. Wanneer u op een opslag account werkt, kunt u naar de context verwijzen in plaats van herhaaldelijk de referenties door te geven. Neem de para meter `-UseConnectedAccount` op voor het aanroepen van volgende gegevens bewerkingen met uw Azure AD-referenties:

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. Voordat u de container maakt, moet u de rol voor [blobgegevens](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) van de opslag toewijzen aan uzelf. Hoewel u de eigenaar van het account bent, hebt u expliciete machtigingen nodig om gegevens bewerkingen uit te voeren op het opslag account. Zie voor meer informatie over het toewijzen van RBAC-rollen [toegang verlenen aan Azure Blob en gegevens wachtrij met RBAC in het Azure Portal](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > RBAC-roltoewijzingen kunnen enkele minuten duren voordat deze wordt door gegeven.

1. Maak een container door [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer)aan te roepen. Omdat deze aanroep de context gebruikt die in de vorige stappen is gemaakt, wordt de container gemaakt met behulp van uw Azure AD-referenties.

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>Volgende stappen

- [Power shell gebruiken om een RBAC-rol toe te wijzen voor toegang tot Blob-en wachtrij gegevens](storage-auth-aad-rbac-powershell.md)
- [Toegang tot Blob-en wachtrij gegevens toestaan met beheerde identiteiten voor Azure-resources](storage-auth-aad-msi.md)
