---
title: Gegevensbewerkingen autoriseren
titleSuffix: Azure Storage
description: Meer informatie over de verschillende manieren om toegang te verlenen tot Azure Storage, waaronder Azure Active Directory, gedeelde sleutel autorisatie of Shared Access signatures (SAS).
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 783e8666e2602f9251d81e976a27fbce099defa2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75460528"
---
# <a name="authorizing-access-to-data-in-azure-storage"></a>Toegang tot gegevens in Azure Storage autoriseren

Telkens wanneer u toegang hebt tot gegevens in uw opslag account, stuurt uw client een aanvraag via HTTP/HTTPS om Azure Storage. Elke aanvraag bij een beveiligde resource moet worden geautoriseerd, zodat wordt gegarandeerd dat de client beschikt over de vereiste machtigingen voor toegang tot de gegevens.

In de volgende tabel worden de opties beschreven die Azure Storage biedt om toegang tot resources te verlenen:

|  |Gedeelde sleutel (sleutel van het opslag account)  |Shared Access Signature (SAS)  |Azure Active Directory (Azure AD)  |Anonieme open bare Lees toegang  |
|---------|---------|---------|---------|---------|
|Azure Blobs     |[Ondersteund](/rest/api/storageservices/authorize-with-shared-key/)         |[Ondersteund](storage-sas-overview.md)         |[Ondersteund](storage-auth-aad.md)         |[Ondersteund](../blobs/storage-manage-access-to-resources.md)         |
|Azure Files (SMB)     |[Ondersteund](/rest/api/storageservices/authorize-with-shared-key/)         |Niet ondersteund         |[Ondersteund, alleen met AAD Domain Services](../files/storage-files-active-directory-overview.md)         |Niet ondersteund         |
|Azure Files (REST)     |[Ondersteund](/rest/api/storageservices/authorize-with-shared-key/)         |[Ondersteund](storage-sas-overview.md)         |Niet ondersteund         |Niet ondersteund         |
|Azure-wachtrijen     |[Ondersteund](/rest/api/storageservices/authorize-with-shared-key/)         |[Ondersteund](storage-sas-overview.md)         |[Ondersteund](storage-auth-aad.md)         |Niet ondersteund         |
|Azure-tabellen     |[Ondersteund](/rest/api/storageservices/authorize-with-shared-key/)         |[Ondersteund](storage-sas-overview.md)         |Niet ondersteund         |Niet ondersteund         |

Elke autorisatie optie wordt hieronder beschreven:

- **Integratie van Azure Active Directory (Azure AD)** voor blobs en wacht rijen. Azure AD biedt op rollen gebaseerd toegangs beheer (RBAC) voor nauw keurige controle over de toegang van een client tot bronnen in een opslag account. Zie [toegang tot Azure-blobs en-wacht rijen toestaan met Azure Active Directory](storage-auth-aad.md)voor meer informatie over Azure AD-integratie voor blobs en wacht rijen.

- **Azure AD Domain Services (DS) Integration (preview)** voor bestanden. Azure Files ondersteunt autorisatie op basis van een identiteit via Server Message Block (SMB) via Azure AD DS. U kunt RBAC gebruiken voor een nauw keurige controle over de toegang van een client tot Azure Files resources in een opslag account. Voor meer informatie over Azure AD-integratie voor bestanden met Domain Services, Zie [overzicht van de verificatie ondersteuning voor Azure Files Azure Active Directory Domain Service (Aad DS) voor SMB-toegang (preview)](../files/storage-files-active-directory-overview.md).

- **Gedeelde sleutel autorisatie** voor blobs, bestanden, wacht rijen en tabellen. Een client die een gedeelde sleutel gebruikt, geeft een header door elke aanvraag die is ondertekend met de toegangs sleutel voor het opslag account. Zie [autoriseren met gedeelde sleutel](/rest/api/storageservices/authorize-with-shared-key/)voor meer informatie.
- **Shared Access signatures** voor blobs, bestanden, wacht rijen en tabellen. Shared Access signatures (SAS) bieden beperkte gedelegeerde toegang tot resources in een opslag account. Door beperkingen toe te voegen aan het tijds interval waarvoor de hand tekening geldig is of op machtigingen die door deze worden verleend, biedt het beheer van de toegang flexibiliteit. Zie [using Shared Access signatures (SAS) (Engelstalig)](storage-sas-overview.md)voor meer informatie.
- **Anonieme open bare Lees toegang** voor containers en blobs. Autorisatie is niet vereist. Zie [Anonieme leestoegang tot containers en blobs beheren](../blobs/storage-manage-access-to-resources.md) voor meer informatie.  

Standaard zijn alle resources in Azure Storage beveiligd en zijn ze alleen beschikbaar voor de eigenaar van het account. Hoewel u een van de hierboven beschreven verificatie strategieën kunt gebruiken om clients toegang te verlenen tot resources in uw opslag account, raadt micro soft aan om Azure AD zo mogelijk te gebruiken voor maximale beveiliging en gebruiks gemak.

## <a name="next-steps"></a>Volgende stappen

- [Toegang tot Azure-blobs en-wacht rijen toestaan met Azure Active Directory](storage-auth-aad.md)
- [Autoriseren met gedeelde sleutel](/rest/api/storageservices/authorize-with-shared-key/)
- [Beperkte toegang verlenen tot Azure Storage-resources met behulp van Shared Access signatures (SAS)](storage-sas-overview.md)
