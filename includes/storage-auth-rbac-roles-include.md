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
ms.openlocfilehash: d73bab76860abf3e9fa442dad44e1ddb66d147a3
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519547"
---
Azure biedt de volgende ingebouwde RBAC-rollen voor het toestaan van toegang tot blob- en wachtrijgegevens met Azure AD en OAuth:

- [Storage Blob Data Owner:](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner)Gebruik om eigendom in te stellen en POSIX-toegangscontrole voor Azure Data Lake Storage Gen2 te beheren. Zie [Toegangsbeheer in Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md)voor meer informatie.
- [Opslagblobgegevensinzender:](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor)gebruik om lees-/schrijf-/verwijdermachtigingen toe te kennen aan blob-opslagbronnen.
- [OpslagBlob-gegevenslezer:](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader)gebruik om alleen-lezen machtigingen toe te kennen aan opslagbronnen van Blob.
- [Opslagwachtrijgegevens inzender:](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor)gebruik om lees-/schrijf-/verwijdermachtigingen toe te kennen aan Azure-wachtrijen.
- [Gegevenslezer opslagwachtrij:](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader)gebruik om alleen-lezen machtigingen toe te kennen aan Azure-wachtrijen.
- [Beheer van gegevensbeheer voor opslagwachtrij:](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor)hiermee u toestaan dat machtigingen voor berichten in Azure Storage-wachtrijen worden toegestaan, op te halen en te verwijderen.
- [Afzender van gegevensin de opslagwachtrij:](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender)gebruik om machtigingen toe te voegen aan berichten in Azure Storage-wachtrijen.

Zie de sectie **Opslag** in azure ingebouwde rollen voor Azure RBAC voor gedetailleerde informatie over ingebouwde [RBAC-rollen](../articles/role-based-access-control/built-in-roles.md#storage)voor Azure Storage voor zowel de gegevensservices als de beheerservice. Zie daarnaast [klassieke abonnementsbeheerdersrollen, Azure RBAC-rollen en Azure AD-rollen](../articles/role-based-access-control/rbac-and-directory-admin-roles.md)voor informatie over de verschillende typen rollen die machtigingen in Azure bieden.

> [!NOTE]
> RBAC-roltoewijzingen kunnen maximaal vijf minuten duren voordat ze worden gepropageerd.
>
> Alleen rollen die expliciet zijn gedefinieerd voor gegevenstoegang, kunnen een beveiligingsprincipal toegang geven tot blob- of wachtrijgegevens. Met rollen zoals **Eigenaar,** **Inzender**en **opslagaccountbijdrager** kan een beveiligingsprincipal een opslagaccount beheren, maar bieden ze geen toegang tot de blob- of wachtrijgegevens in dat account.
>
> Toegang tot blob- of wachtrijgegevens in de Azure-portal kan worden geautoriseerd met uw Azure AD-account of de toegangssleutel voor het opslagaccount. Zie [De Azure-portal gebruiken om toegang te krijgen tot blob- of wachtrijgegevens](../articles/storage/common/storage-access-blobs-queues-portal.md)voor meer informatie.
