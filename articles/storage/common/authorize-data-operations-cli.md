---
title: Toegang tot blob- of wachtrijgegevens autoriseren met Azure CLI
titleSuffix: Azure Storage
description: Geef op hoe u gegevensbewerkingen autoriseren tegen blob- of wachtrijgegevens met de Azure CLI. U gegevensbewerkingen autoriseren met Azure AD-referenties, met de accounttoegangssleutel of met een SAS-token (Shared Access Signature).
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/26/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: c7091592f8806b6f6655315ae1faace286c2c1f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78207691"
---
# <a name="authorize-access-to-blob-or-queue-data-with-azure-cli"></a>Toegang tot blob- of wachtrijgegevens autoriseren met Azure CLI

Azure Storage biedt extensies voor Azure CLI waarmee u opgeven hoe u bewerkingen op blob- of wachtrijgegevens wilt autoriseren. U gegevensbewerkingen op de volgende manieren autoriseren:

- Met een Azure Active Directory (Azure AD) beveiligingsprincipal. Microsoft raadt aan azure AD-referenties te gebruiken voor superieure beveiliging en gebruiksgemak.
- Met de accounttoegangssleutel of een SAS-token (Shared Access Signature) hebt u een token voor gedeelde toegang.

## <a name="specify-how-data-operations-are-authorized"></a>Opgeven hoe gegevensbewerkingen zijn geautoriseerd

Azure CLI-opdrachten voor het lezen en schrijven `--auth-mode` van blob- en wachtrijgegevens bevatten de optionele parameter. Geef deze parameter op om aan te geven hoe een gegevensbewerking moet worden geautoriseerd:

- Stel `--auth-mode` de `login` parameter in om in te loggen met een Azure AD-beveiligingsprincipal (aanbevolen).
- Stel `--auth-mode` de parameter `key` in op de verouderde waarde om te proberen de accounttoegangssleutel op te halen die moet worden gebruikt voor autorisatie. Als u de `--auth-mode` parameter weglaat, probeert de Azure CLI ook de toegangssleutel op te halen.

Als u `--auth-mode` de parameter wilt gebruiken, controleert u of u Azure CLI-versie 2.0.46 of hoger hebt geïnstalleerd. Voer `az --version` uit om de geïnstalleerde versie te controleren.

> [!IMPORTANT]
> Als u de `--auth-mode` parameter weglaat `key`of instelt op , probeert de Azure CLI de accounttoegangssleutel voor autorisatie te gebruiken. In dit geval raadt Microsoft u aan de toegangssleutel op de opdracht of in de **AZURE_STORAGE_KEY** omgevingsvariabele op te geven. Zie de sectie met de titel [Omgevingsvariabelen instellen voor autorisatieparameters voor](#set-environment-variables-for-authorization-parameters)meer informatie over omgevingsvariabelen.
>
> Als u de toegangssleutel niet opgeeft, probeert de Azure CLI de Azure Storage-bronprovider aan te roepen om deze voor elke bewerking op te halen. Het uitvoeren van veel gegevensbewerkingen waarvoor een oproep naar de resourceprovider vereist is, kan leiden tot beperking. Zie [Schaalbaarheids- en prestatiedoelen voor de Azure Storage-bronprovider voor](scalability-targets-resource-provider.md)meer informatie over limieten voor resourceprovider.

## <a name="authorize-with-azure-ad-credentials"></a>Autoriseren met Azure AD-referenties

Wanneer u zich aanmeldt bij Azure CLI met Azure AD-referenties, wordt een OAuth 2.0-toegangstoken geretourneerd. Dat token wordt automatisch gebruikt door Azure CLI om volgende gegevensbewerkingen te autoriseren tegen Blob- of Queue-opslag. Voor ondersteunde bewerkingen hoeft u niet langer een accountsleutel of SAS-token met de opdracht door te geven.

U machtigingen toewijzen aan blob- en wachtrijgegevens aan een Azure AD-beveiligingsprincipal via RBAC (Role-based Access Control). Zie [Toegangsrechten voor Azure Storage beheren met RBAC](storage-auth-aad-rbac.md)voor meer informatie over RBAC-rollen in Azure Storage.

### <a name="permissions-for-calling-data-operations"></a>Machtigingen voor het aanroepen van gegevensbewerkingen

De Azure Storage-extensies worden ondersteund voor bewerkingen op blob- en wachtrijgegevens. Welke bewerkingen u aanroepen, is afhankelijk van de machtigingen die zijn verleend aan de Azure AD-beveiligingsprincipal waarmee u zich aanmeldt bij Azure CLI. Machtigingen voor Azure Storage-containers of wachtrijen worden toegewezen via RBAC. Als u bijvoorbeeld de rol **Kblob-gegevenslezer** toegewezen krijgt, u scriptopdrachten uitvoeren die gegevens uit een container of wachtrij lezen. Als u de rol **Blob-gegevensinzender toegewezen krijgt,** u scriptopdrachten uitvoeren die een container of wachtrij of de gegevens die deze bevatten, lezen, schrijven of verwijderen.

Zie [Opslagbewerkingen bellen met OAuth-tokens](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens)voor meer informatie over de machtigingen die nodig zijn voor elke Azure Storage-bewerking in een container of wachtrij.  

### <a name="example-authorize-an-operation-to-create-a-container-with-azure-ad-credentials"></a>Voorbeeld: Een bewerking autoriseren om een container met Azure AD-referenties te maken

In het volgende voorbeeld ziet u hoe u een container maakt vanuit Azure CLI met uw Azure AD-referenties. Als u de container wilt maken, moet u zich aanmelden bij de Azure CLI en hebt u een brongroep en een opslagaccount nodig. Zie [Snelstart: Blobs maken, downloaden en aanbieden met Azure CLI](../blobs/storage-quickstart-blobs-cli.md)voor meer informatie over het maken van deze bronnen.

1. Voordat u de container maakt, wijst u de rol [Opslagblob-gegevensbijdrage raan](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) toe aan uzelf. Hoewel u de eigenaar van het account bent, hebt u expliciete machtigingen nodig om gegevensbewerkingen uit te voeren tegen het opslagaccount. Zie [Toegang verlenen tot Azure blob- en wachtrijgegevens met RBAC in de Azure-portal](storage-auth-aad-rbac.md)voor meer informatie over het toewijzen van RBAC-rollen.

    > [!IMPORTANT]
    > RBAC-roltoewijzingen kunnen enkele minuten duren voordat ze worden gepropageerd.

1. Roep de opdracht voor het `--auth-mode` maken `login` [van az-opslagcontainers](/cli/azure/storage/container#az-storage-container-create) aan met de parameter die is ingesteld om de container te maken met uw Azure AD-referenties. Vergeet niet om plaatsaanduidingswaarden in hoekhaakjes te vervangen door uw eigen waarden:

    ```azurecli
    az storage container create \
        --account-name <storage-account> \
        --name sample-container \
        --auth-mode login
    ```

## <a name="authorize-with-the-account-access-key"></a>Autoriseren met de accounttoegangssleutel

Als u over de accountsleutel beschikt, u een Azure Storage-gegevensbewerking aanroepen. Over het algemeen is het gebruik van de accountsleutel minder veilig. Als de accountsleutel is gecompromitteerd, kunnen alle gegevens in uw account worden gecompromitteerd.

In het volgende voorbeeld ziet u hoe u een container maakt met de accounttoegangssleutel. Geef de accountsleutel op `--auth-mode` en `key` geef de parameter de waarde op:

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --account-key <key>
    --auth-mode key
```

## <a name="authorize-with-a-sas-token"></a>Autoriseren met een SAS-token

Als u in het bezit bent van een SAS-token, u gegevensbewerkingen bellen die zijn toegestaan door de SAS. In het volgende voorbeeld ziet u hoe u een container maakt met een SAS-token:

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --sas-token <token>
```

## <a name="set-environment-variables-for-authorization-parameters"></a>Omgevingsvariabelen instellen voor autorisatieparameters

U autorisatieparameters opgeven in omgevingsvariabelen om te voorkomen dat deze bij elke aanroep worden betrokken bij een Azure Storage-gegevensbewerking. In de volgende tabel worden de beschikbare omgevingsvariabelen beschreven.

| Omgevingsvariabele                  | Beschrijving                                                                                                                                                                                                                                                                                                                                                                     |
|---------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    AZURE_STORAGE_ACCOUNT              |    De naam van het opslagaccount. Deze variabele moet worden gebruikt in combinatie met de opslagaccountsleutel of een SAS-token. Als geen van beide aanwezig is, probeert de Azure CLI de toegangssleutel voor het opslagaccount op te halen met behulp van het geverifieerde Azure AD-account. Als een groot aantal opdrachten tegelijk wordt uitgevoerd, kan de beperkingslimiet voor azure-opslagproviderworden bereikt. Zie [Schaalbaarheids- en prestatiedoelen voor de Azure Storage-bronprovider voor](scalability-targets-resource-provider.md)meer informatie over limieten voor resourceprovider.             |
|    AZURE_STORAGE_KEY                  |    De opslagaccountsleutel. Deze variabele moet worden gebruikt in combinatie met de naam van het opslagaccount.                                                                                                                                                                                                                                                                          |
|    AZURE_STORAGE_CONNECTION_STRING    |    Een verbindingstekenreeks die de opslagaccountsleutel of een SAS-token bevat. Deze variabele moet worden gebruikt in combinatie met de naam van het opslagaccount.                                                                                                                                                                                                                       |
|    AZURE_STORAGE_SAS_TOKEN            |    Een SAS-token (Shared Access Signature) hebt een token voor gedeelde toegang. Deze variabele moet worden gebruikt in combinatie met de naam van het opslagaccount.                                                                                                                                                                                                                                                            |
|    AZURE_STORAGE_AUTH_MODE            |    De autorisatiemodus waarmee de opdracht moet worden uitgevoerd. Toegestane waarden `login` zijn (aanbevolen) `key`of . Als u `login`dit opgeeft, gebruikt de Azure CLI uw Azure AD-referenties om de gegevensbewerking te autoriseren. Als u de `key` verouderde modus opgeeft, probeert de Azure CLI de accounttoegangssleutel op te vragen en de opdracht met de sleutel te autoriseren.    |

## <a name="next-steps"></a>Volgende stappen

- [Azure CLI gebruiken om een RBAC-rol toe te wijzen voor toegang tot blob- en wachtrijgegevens](storage-auth-aad-rbac-cli.md)
- [Toegang tot blob- en wachtrijgegevens met beheerde identiteiten voor Azure-bronnen autoriseren](storage-auth-aad-msi.md)
