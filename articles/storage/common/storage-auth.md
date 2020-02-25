---
title: Gegevensbewerkingen autoriseren
titleSuffix: Azure Storage
description: Meer informatie over de verschillende manieren om toegang te verlenen tot Azure Storage, waaronder Azure Active Directory, gedeelde sleutel autorisatie of Shared Access signatures (SAS).
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 7deb773fae6ba56b6f601983ffd2b07d887e1480
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565756"
---
# <a name="authorizing-access-to-data-in-azure-storage"></a>Toegang tot gegevens in Azure Storage autoriseren

Telkens wanneer u toegang hebt tot gegevens in uw opslag account, stuurt uw client een aanvraag via HTTP/HTTPS om Azure Storage. Elke aanvraag voor een beveiligde resource moet worden gemachtigd, zodat de-service ervoor zorgt dat de-client over de vereiste machtigingen beschikt om toegang tot de gegevens te krijgen.

In de volgende tabel worden de opties beschreven die Azure Storage biedt om toegang tot resources te verlenen:

|  |Gedeelde sleutel (sleutel van het opslag account)  |Shared Access Signature (SAS)  |Azure Active Directory (Azure AD)  |Active Directory (preview-versie)|Anonieme open bare Lees toegang  |
|---------|---------|---------|---------|---------|---------|
|Azure-blobs     |[Ondersteund](/rest/api/storageservices/authorize-with-shared-key/)         |[Ondersteund](storage-sas-overview.md)         |[Ondersteund](storage-auth-aad.md)         |Niet ondersteund|[Ondersteund](../blobs/storage-manage-access-to-resources.md)         |
|Azure Files (SMB)     |[Ondersteund](/rest/api/storageservices/authorize-with-shared-key/)         |Niet ondersteund         |[Ondersteund, alleen met AAD Domain Services](../files/storage-files-active-directory-overview.md)         |[Ondersteund, referenties moeten worden gesynchroniseerd met Azure AD](../files/storage-files-active-directory-overview.md)|Niet ondersteund         |
|Azure Files (REST)     |[Ondersteund](/rest/api/storageservices/authorize-with-shared-key/)         |[Ondersteund](storage-sas-overview.md)         |Niet ondersteund         |Niet ondersteund |Niet ondersteund         |
|Azure-wachtrijen     |[Ondersteund](/rest/api/storageservices/authorize-with-shared-key/)         |[Ondersteund](storage-sas-overview.md)         |[Ondersteund](storage-auth-aad.md)         |Niet ondersteund | Niet ondersteund         |
|Azure-tabellen     |[Ondersteund](/rest/api/storageservices/authorize-with-shared-key/)         |[Ondersteund](storage-sas-overview.md)         |Niet ondersteund         |Niet ondersteund| Niet ondersteund         |

Elke autorisatie optie wordt hieronder beschreven:

- **Integratie van Azure Active Directory (Azure AD)** voor blobs en wacht rijen. Azure AD biedt op rollen gebaseerd toegangs beheer (RBAC) voor nauw keurige controle over de toegang van een client tot bronnen in een opslag account. Zie [toegang tot Azure-blobs en-wacht rijen toestaan met Azure Active Directory](storage-auth-aad.md)voor meer informatie over Azure AD-integratie voor blobs en wacht rijen.

- **Azure Active Directory Domain Services-verificatie (Azure AD DS)** voor bestanden. Azure Files ondersteunt autorisatie op basis van een identiteit via Server Message Block (SMB) via Azure AD DS. U kunt RBAC gebruiken voor een nauw keurige controle over de toegang van een client tot Azure Files resources in een opslag account. Raadpleeg ons [overzicht](../files/storage-files-active-directory-overview.md)voor meer informatie over het Azure files-verificatie met behulp van Domain Services.

- **Active Directory (AD)-verificatie (preview)** voor bestanden. Azure Files ondersteunt autorisatie op basis van een identiteit via SMB via AD. Uw AD-domein service kan worden gehost op on-premises machines of in azure-Vm's. SMB-toegang tot bestanden wordt ondersteund met behulp van AD-referenties van computers die lid zijn van een domein, on-premises of in Azure. U kunt RBAC gebruiken voor toegangs beheer op share niveau en NTFS-DACL'S voor het afdwingen van machtigingen op Directory-en bestands niveau. Raadpleeg ons [overzicht](../files/storage-files-active-directory-overview.md)voor meer informatie over het Azure files-verificatie met behulp van Domain Services.

- **Gedeelde sleutel autorisatie** voor blobs, bestanden, wacht rijen en tabellen. Een client die een gedeelde sleutel gebruikt, geeft een header door elke aanvraag die is ondertekend met de toegangs sleutel voor het opslag account. Zie [autoriseren met gedeelde sleutel](/rest/api/storageservices/authorize-with-shared-key/)voor meer informatie.
- **Shared Access signatures** voor blobs, bestanden, wacht rijen en tabellen. Shared Access signatures (SAS) bieden beperkte gedelegeerde toegang tot resources in een opslag account. Door beperkingen toe te voegen aan het tijds interval waarvoor de hand tekening geldig is of op machtigingen die door deze worden verleend, biedt het beheer van de toegang flexibiliteit. Zie [using Shared Access signatures (SAS) (Engelstalig)](storage-sas-overview.md)voor meer informatie.
- **Anonieme open bare Lees toegang** voor containers en blobs. Autorisatie is niet vereist. Zie [Anonieme leestoegang tot containers en blobs beheren](../blobs/storage-manage-access-to-resources.md) voor meer informatie.  

Standaard zijn alle resources in Azure Storage beveiligd en zijn ze alleen beschikbaar voor de eigenaar van het account. Hoewel u een van de hierboven beschreven verificatie strategieën kunt gebruiken om clients toegang te verlenen tot resources in uw opslag account, raadt micro soft aan om Azure AD zo mogelijk te gebruiken voor maximale beveiliging en gebruiks gemak.

## <a name="next-steps"></a>Volgende stappen

- [Toegang tot Azure-blobs en-wacht rijen toestaan met Azure Active Directory](storage-auth-aad.md)
- [Autoriseren met gedeelde sleutel](/rest/api/storageservices/authorize-with-shared-key/)
- [Beperkte toegang verlenen tot Azure Storage-resources met behulp van Shared Access signatures (SAS)](storage-sas-overview.md)
