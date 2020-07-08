---
title: bestand opnemen
description: bestand opnemen
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/06/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: b49d757067d8ee888bee52f3931c8d48d786c044
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86024883"
---
Azure biedt de volgende ingebouwde RBAC-rollen voor het verlenen van toegang tot Blob-en wachtrij gegevens met behulp van Azure AD en OAuth:

- [Eigenaar](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner)van de gegevens van de opslag-blob: gebruiken om het eigendom in te stellen en POSIX Access Control te beheren voor Azure data Lake Storage Gen2. Zie [toegangs beheer in azure data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md)voor meer informatie.
- [Inzender voor gegevens](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor)van de opslag-blob: gebruiken om machtigingen voor lezen/schrijven/verwijderen te verlenen aan Blob Storage-resources.
- [Gegevens lezer](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader)van de opslag-blob: gebruik deze om alleen-lezen-machtigingen te verlenen aan Blob Storage-resources.
- Delegering van de [opslag-BLOB](../articles/role-based-access-control/built-in-roles.md#storage-blob-delegator): een sleutel voor gebruikers overdracht ophalen die moet worden gebruikt voor het maken van een gedeelde toegangs handtekening die is ondertekend met Azure AD-referenties voor een container of BLOB.
- [Inzender voor gegevens van de opslag wachtrij](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): gebruiken om machtigingen voor lezen/schrijven/verwijderen te verlenen aan Azure-wacht rijen.
- [Gegevens lezer van de opslag wachtrij](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): gebruik dit om alleen-lezen-machtigingen te verlenen aan Azure-wacht rijen.
- [Processor voor gegevens berichten van de opslag wachtrij](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): gebruik dit om machtigingen voor weer geven, ophalen en verwijderen te verlenen aan berichten in azure Storage wachtrijen.
- [Gegevens bericht van de opslag wachtrij](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): gebruik om machtigingen toe te kennen voor berichten in azure Storage wacht rijen.

Alleen rollen die expliciet zijn gedefinieerd voor toegang tot gegevens, hebben een beveiligingsprincipal toegestaan om toegang te krijgen tot BLOB-of wachtrij gegevens. Ingebouwde rollen, zoals **eigenaar**, **Inzender**en **Inzender voor opslag accounts** , hebben een beveiligingsprincipal toe om een opslag account te beheren, maar bieden geen toegang tot de BLOB-of wachtrij gegevens binnen dat account via Azure AD. Als een rol echter de **micro soft. Storage/Storage accounts/listkeys ophalen/Action**bevat, kan een gebruiker aan wie de rol is toegewezen, toegang krijgen tot gegevens in het opslag account via de verificatie van de gedeelde sleutel met de toegangs sleutels voor het account. Zie [de Azure Portal gebruiken om toegang te krijgen tot BLOB-of wachtrij gegevens](../articles/storage/common/storage-access-blobs-queues-portal.md)voor meer informatie.

Zie de sectie **opslag** in [Azure ingebouwde rollen voor Azure RBAC](../articles/role-based-access-control/built-in-roles.md#storage)voor meer informatie over ingebouwde RBAC-rollen voor Azure Storage voor de gegevens Services en de beheer service. Zie voor meer informatie over de verschillende soorten rollen die machtigingen bieden in azure [klassieke abonnements beheerders rollen, Azure RBAC-rollen en Azure AD-rollen](../articles/role-based-access-control/rbac-and-directory-admin-roles.md).

> [!IMPORTANT]
> RBAC-roltoewijzingen kunnen tot vijf minuten duren voordat het kan worden door gegeven.
