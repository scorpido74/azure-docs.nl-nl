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
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279621"
---
Azure biedt de volgende ingebouwde RBAC-rollen voor het verlenen van toegang tot Blob-en wachtrij gegevens met behulp van Azure AD en OAuth:

- [Eigenaar](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner)van de gegevens van de opslag-blob: gebruiken om het eigendom in te stellen en POSIX Access Control te beheren voor Azure data Lake Storage Gen2. Zie [toegangs beheer in azure data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md)voor meer informatie.
- [Inzender voor gegevens](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor)van de opslag-blob: gebruiken om machtigingen voor lezen/schrijven/verwijderen te verlenen aan Blob Storage-resources.
- [Gegevens lezer](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader)van de opslag-blob: gebruik deze om alleen-lezen-machtigingen te verlenen aan Blob Storage-resources.
- [Inzender voor gegevens van de opslag wachtrij](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): gebruiken om machtigingen voor lezen/schrijven/verwijderen te verlenen aan Azure-wacht rijen.
- [Gegevens lezer van de opslag wachtrij](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): gebruik dit om alleen-lezen-machtigingen te verlenen aan Azure-wacht rijen.
- [Processor voor gegevens berichten van de opslag wachtrij](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): gebruik dit om machtigingen voor weer geven, ophalen en verwijderen te verlenen aan berichten in azure Storage wachtrijen.
- [Gegevens bericht van de opslag wachtrij](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): gebruik om machtigingen toe te kennen voor berichten in azure Storage wacht rijen.

> [!NOTE]
> RBAC-roltoewijzingen kunnen tot vijf minuten duren voordat het kan worden door gegeven.
>
> Alleen rollen die expliciet zijn gedefinieerd voor toegang tot gegevens, hebben een beveiligingsprincipal toegestaan om toegang te krijgen tot BLOB-of wachtrij gegevens. Rollen zoals een **eigenaar**, **bijdrager**en **Inzender voor opslag accounts** geven een beveiligingsprincipal een beveiligings-principal toe om een opslag account te beheren, maar bieden geen toegang tot de BLOB-of wachtrij gegevens binnen dat account.
>
> Toegang tot BLOB-of wachtrij gegevens in de Azure Portal kan worden geautoriseerd met behulp van uw Azure AD-account of de toegangs sleutel voor het opslag account. Zie [de Azure Portal gebruiken om toegang te krijgen tot BLOB-of wachtrij gegevens](../articles/storage/common/storage-access-blobs-queues-portal.md)voor meer informatie.
