---
title: Voer Azure CLI-opdrachten uit met Azure AD-referenties om toegang te krijgen tot BLOB-of wachtrij gegevens
titleSuffix: Azure Storage
description: Azure CLI-ondersteuning voor aanmelding met Azure AD-referenties voor het uitvoeren van opdrachten op Azure Storage Blob-en Queue-gegevens. Er wordt een toegangs token voor de sessie gegeven en gebruikt om aanroepende bewerkingen te autoriseren. De machtigingen zijn afhankelijk van de RBAC-rol die is toegewezen aan de Azure AD-beveiligings-principal.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/30/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: bfbb466b7679e841c9c0154dbe28de14574d4282
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75553460"
---
# <a name="run-azure-cli-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>Voer Azure CLI-opdrachten uit met Azure AD-referenties om toegang te krijgen tot BLOB-of wachtrij gegevens

Azure Storage biedt uitbrei dingen voor Azure CLI waarmee u zich kunt aanmelden en script opdrachten kunt uitvoeren met de referenties voor Azure Active Directory (Azure AD). Wanneer u zich aanmeldt bij Azure CLI met Azure AD-referenties, wordt een OAuth 2,0-toegangs token geretourneerd. Dit token wordt automatisch door Azure CLI gebruikt voor het autoriseren van volgende gegevens bewerkingen op BLOB-of wachtrij opslag. Voor ondersteunde bewerkingen hoeft u geen account sleutel of SAS-token meer door te geven met de opdracht.

U kunt machtigingen toewijzen aan Blob-en wachtrij gegevens aan een Azure AD-beveiligingsprincipal via op rollen gebaseerd toegangs beheer (RBAC). Zie [Manage access rights to Azure Storage Data with RBAC](storage-auth-aad-rbac.md)(Engelstalig) voor meer informatie over RBAC-rollen in azure Storage.

## <a name="supported-operations"></a>Ondersteunde bewerkingen

De Azure Storage-extensies worden ondersteund voor bewerkingen op Blob-en wachtrij gegevens. Welke bewerkingen u kunt aanroepen, is afhankelijk van de machtigingen die zijn verleend aan de Azure AD-beveiligings-principal waarmee u zich aanmeldt bij Azure CLI. Machtigingen voor het Azure Storage van containers of wacht rijen worden toegewezen via RBAC. Als u bijvoorbeeld de rol **BLOB data Reader** hebt toegewezen, kunt u script opdrachten uitvoeren die gegevens uit een container of wachtrij lezen. Als u de rol **BLOB data contributor** hebt toegewezen, kunt u script opdrachten uitvoeren die een container of wachtrij lezen, schrijven of verwijderen, of de gegevens die ze bevatten.

Zie [opslag bewerkingen aanroepen met OAuth-tokens](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens)voor meer informatie over de vereiste machtigingen voor elke Azure Storage bewerking in een container of wachtrij.  

## <a name="call-azure-cli-commands-using-azure-ad-credentials"></a>Azure CLI-opdrachten aanroepen met Azure AD-referenties

Azure CLI ondersteunt de para meter `--auth-mode` voor Blob-en wachtrij gegevens bewerkingen:

- Stel de `--auth-mode`-para meter in op `login` om u aan te melden met een Azure AD-beveiligings-principal.
- Stel de para meter `--auth-mode` in op de verouderde `key` waarde om te proberen een query uit te voeren voor een account sleutel als er geen verificatie parameters voor het account worden opgegeven.

In het volgende voor beeld ziet u hoe u een container maakt in een nieuw opslag account vanuit Azure CLI met behulp van uw Azure AD-referenties. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen punt haken te vervangen door uw eigen waarden:

1. Zorg ervoor dat u Azure CLI-versie 2.0.46 of hoger hebt geïnstalleerd. Voer `az --version` uit om de geïnstalleerde versie te controleren.

1. Voer `az login` uit en verifieer de verificatie in het browser venster:

    ```azurecli
    az login
    ```

1. Geef het gewenste abonnement op. Maak een resourcegroep met [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create). Maak een opslag account in die resource groep met [AZ Storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create):

    ```azurecli
    az account set --subscription <subscription-id>

    az group create \
        --name sample-resource-group-cli \
        --location eastus

    az storage account create \
        --name <storage-account> \
        --resource-group sample-resource-group-cli \
        --location eastus \
        --sku Standard_LRS \
        --encryption-services blob
    ```

1. Voordat u de container maakt, moet u de rol voor [blobgegevens](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) van de opslag toewijzen aan uzelf. Hoewel u de eigenaar van het account bent, hebt u expliciete machtigingen nodig om gegevens bewerkingen uit te voeren op het opslag account. Zie voor meer informatie over het toewijzen van RBAC-rollen [toegang verlenen aan Azure Blob en gegevens wachtrij met RBAC in het Azure Portal](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > RBAC-roltoewijzingen kunnen enkele minuten duren voordat deze wordt door gegeven.

1. Roep de opdracht [AZ storage container Create](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) aan met de para meter `--auth-mode` ingesteld op `login` om de container te maken met uw Azure AD-referenties:

    ```azurecli
    az storage container create \ 
        --account-name <storage-account> \ 
        --name sample-container \
        --auth-mode login
    ```

De omgevings variabele die aan de para meter `--auth-mode` is gekoppeld, is `AZURE_STORAGE_AUTH_MODE`. U kunt de juiste waarde opgeven in de omgevings variabele om te voor komen dat deze wordt opgenomen bij elke aanroep van een Azure Storage gegevens bewerking.

## <a name="next-steps"></a>Volgende stappen

- [Azure CLI gebruiken om een RBAC-rol toe te wijzen voor toegang tot Blob-en wachtrij gegevens](storage-auth-aad-rbac-cli.md)
- [Toegang tot Blob-en wachtrij gegevens toestaan met beheerde identiteiten voor Azure-resources](storage-auth-aad-msi.md)
