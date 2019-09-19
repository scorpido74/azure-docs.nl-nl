---
title: bestand opnemen
description: bestand opnemen
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/25/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: c2b409f0eefe5efa389432cbb007cc08e0c6ae1e
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71078240"
---
Azure biedt de volgende ingebouwde RBAC-rollen voor het verlenen van toegang tot Blob-en wachtrij gegevens met behulp van Azure AD en OAuth:

- [Eigenaar van gegevens](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner)van de opslag-blob: Gebruik om het eigendom in te stellen en POSIX Access Control te beheren voor Azure Data Lake Storage Gen2. Zie [toegangs beheer in azure data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md)voor meer informatie.
- [Inzender voor Storage BLOB-gegevens](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Gebruiken om machtigingen voor lezen/schrijven/verwijderen te verlenen aan Blob Storage-resources.
- [Gegevens lezer](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader)van de opslag-blob: Gebruiken om alleen-lezen-machtigingen te verlenen aan Blob Storage-resources.
- [Inzender voor opslag wachtrij gegevens](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Gebruiken om machtigingen voor lezen/schrijven/verwijderen te verlenen aan Azure-wacht rijen.
- [Gegevens lezer van de opslag wachtrij](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): Gebruiken om alleen-lezen-machtigingen te verlenen aan Azure-wacht rijen.
- [Processor voor gegevens berichten van de opslag wachtrij](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): Gebruiken om machtigingen voor weer geven, ophalen en verwijderen te verlenen aan berichten in Azure Storage wachtrijen.
- [Afzender gegevens bericht van opslag wachtrij](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): Gebruik om machtigingen toe te kennen voor berichten in Azure Storage wacht rijen.

> [!NOTE]
> RBAC-roltoewijzingen kunnen tot vijf minuten duren voordat het kan worden door gegeven.
>
> Alleen rollen die expliciet zijn gedefinieerd voor toegang tot gegevens, hebben een beveiligingsprincipal toegestaan om toegang te krijgen tot BLOB-of wachtrij gegevens. Rollen zoals een **eigenaar**, **bijdrager**en **Inzender voor opslag accounts** geven een beveiligingsprincipal een beveiligings-principal toe om een opslag account te beheren, maar bieden geen toegang tot de BLOB-of wachtrij gegevens binnen dat account.
