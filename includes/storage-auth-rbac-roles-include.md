---
title: bestand opnemen
description: bestand opnemen
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/17/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 4b10955a1d3b85acbcae109836bebc03ec04c72c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76279621"
---
Azure biedt de volgende ingebouwde RBAC-rollen voor het toestaan van toegang tot blob- en wachtrijgegevens met Azure AD en OAuth:

- [Storage Blob Data Owner:](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner)Gebruik om eigendom in te stellen en POSIX-toegangscontrole voor Azure Data Lake Storage Gen2 te beheren. Zie [Toegangsbeheer in Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md)voor meer informatie.
- [Opslagblobgegevensinzender:](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor)gebruik om lees-/schrijf-/verwijdermachtigingen toe te kennen aan blob-opslagbronnen.
- [OpslagBlob-gegevenslezer:](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader)gebruik om alleen-lezen machtigingen toe te kennen aan opslagbronnen van Blob.
- [Opslagwachtrijgegevens inzender:](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor)gebruik om lees-/schrijf-/verwijdermachtigingen toe te kennen aan Azure-wachtrijen.
- [Gegevenslezer opslagwachtrij:](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader)gebruik om alleen-lezen machtigingen toe te kennen aan Azure-wachtrijen.
- [Beheer van gegevensbeheer voor opslagwachtrij:](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor)hiermee u toestaan dat machtigingen voor berichten in Azure Storage-wachtrijen worden toegestaan, op te halen en te verwijderen.
- [Afzender van gegevensin de opslagwachtrij:](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender)gebruik om machtigingen toe te voegen aan berichten in Azure Storage-wachtrijen.

> [!NOTE]
> RBAC-roltoewijzingen kunnen maximaal vijf minuten duren voordat ze worden gepropageerd.
>
> Alleen rollen die expliciet zijn gedefinieerd voor gegevenstoegang, kunnen een beveiligingsprincipal toegang geven tot blob- of wachtrijgegevens. Met rollen zoals **Eigenaar,** **Inzender**en **opslagaccountbijdrager** kan een beveiligingsprincipal een opslagaccount beheren, maar bieden ze geen toegang tot de blob- of wachtrijgegevens in dat account.
>
> Toegang tot blob- of wachtrijgegevens in de Azure-portal kan worden geautoriseerd met uw Azure AD-account of de toegangssleutel voor het opslagaccount. Zie [De Azure-portal gebruiken om toegang te krijgen tot blob- of wachtrijgegevens](../articles/storage/common/storage-access-blobs-queues-portal.md)voor meer informatie.
