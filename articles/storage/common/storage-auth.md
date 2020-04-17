---
title: Gegevensbewerkingen autoriseren
titleSuffix: Azure Storage
description: Meer informatie over de verschillende manieren om toegang tot Azure Storage te autoriseren, waaronder Azure Active Directory, Shared Key-autorisatie of SAS (Shared Access Signatures).
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 8db6140789a9e4f46e368b913cacacd21609ccd9
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81529719"
---
# <a name="authorizing-access-to-data-in-azure-storage"></a>Toegang tot gegevens in Azure Storage toestaan

Elke keer dat u toegang krijgt tot gegevens in uw opslagaccount, dient uw client via HTTP/HTTPS een verzoek in naar Azure Storage. Elke aanvraag bij een beveiligde resource moet worden geautoriseerd, zodat wordt gegarandeerd dat de client beschikt over de vereiste machtigingen voor toegang tot de gegevens.

In de volgende tabel worden de opties beschreven die Azure Storage biedt voor het toestaan van toegang tot bronnen:

|  |Gedeelde sleutel (opslagaccountsleutel)  |Shared Access Signature (SAS)  |Azure Active Directory (Azure AD)  |On-premises Active Directory Domain Services (voorbeeld) |Anonieme openbare leestoegang  |
|---------|---------|---------|---------|---------|---------|
|Azure-blobs     |[Ondersteund](/rest/api/storageservices/authorize-with-shared-key/)         |[Ondersteund](storage-sas-overview.md)         |[Ondersteund](storage-auth-aad.md)         |Niet ondersteund|[Ondersteund](../blobs/storage-manage-access-to-resources.md)         |
|Azure-bestanden (SMB)     |[Ondersteund](/rest/api/storageservices/authorize-with-shared-key/)         |Niet ondersteund         |[Ondersteund, alleen met AAD Domain Services](../files/storage-files-active-directory-overview.md)         |[Ondersteund, moeten referenties worden gesynchroniseerd met Azure AD](../files/storage-files-active-directory-overview.md)|Niet ondersteund         |
|Azure-bestanden (REST)     |[Ondersteund](/rest/api/storageservices/authorize-with-shared-key/)         |[Ondersteund](storage-sas-overview.md)         |Niet ondersteund         |Niet ondersteund |Niet ondersteund         |
|Azure-wachtrijen     |[Ondersteund](/rest/api/storageservices/authorize-with-shared-key/)         |[Ondersteund](storage-sas-overview.md)         |[Ondersteund](storage-auth-aad.md)         |Niet ondersteund | Niet ondersteund         |
|Azure-tabellen     |[Ondersteund](/rest/api/storageservices/authorize-with-shared-key/)         |[Ondersteund](storage-sas-overview.md)         |Niet ondersteund         |Niet ondersteund| Niet ondersteund         |

Elke autorisatieoptie wordt hieronder kort beschreven:

- **Azure Active Directory -integratie (Azure AD)** voor blobs en wachtrijen. Azure AD biedt rbac (role-based access control) voor controle over de toegang van een client tot bronnen in een opslagaccount. Zie [Toegang tot Azure blobs en wachtrijen autoriseren met Azure Active Directory](storage-auth-aad.md)voor meer informatie over de AD-integratie van Azure voor blobs en wachtrijen.

- **Azure Active Directory Domain Services (Azure AD DS)-verificatie** voor Azure Files. Azure Files ondersteunt autorisatie op basis van identiteit via Server Message Block (SMB) via Azure AD DS. U RBAC gebruiken voor fijnmazige controle over de toegang van een client tot Azure Files-bronnen in een opslagaccount. Zie het [overzicht](../files/storage-files-active-directory-overview.md)voor meer informatie over Azure Files-verificatie met domeinservices.

- **On-premises Active Directory Domain Services (AD DS) of on-premises AD DS-verificatie (preview)** voor Azure Files. Azure Files ondersteunt autorisatie op basis van identiteit via SMB via AD DS. Uw AD DS-omgeving kan worden gehost in on-premises machines of in Azure VM's. SMB-toegang tot bestanden wordt ondersteund met AD DS-referenties van domeinaangesloten machines, on-premises of in Azure. U een combinatie van RBAC gebruiken voor toegangscontrole op shareniveau en NTFS-DACLs voor de handhaving van machtigingsrechten voor directory/bestandsniveau. Zie het [overzicht](../files/storage-files-active-directory-overview.md)voor meer informatie over Azure Files-verificatie met domeinservices.

- **Beheersleutelautorisatie** voor blobs, bestanden, wachtrijen en tabellen. Een client met Gedeelde sleutel passeert een koptekst bij elk verzoek dat is ondertekend met de toegangssleutel voor het opslagaccount. Zie [Autoriseren met gedeelde sleutel voor](/rest/api/storageservices/authorize-with-shared-key/)meer informatie.
- **Gedeelde toegangshandtekeningen** voor blobs, bestanden, wachtrijen en tabellen. SAS (Shared Access Signatures) bieden beperkte gedelegeerde toegang tot bronnen in een opslagaccount. Het toevoegen van beperkingen aan het tijdsinterval waarvoor de handtekening geldig is of op machtigingen die het verleent, biedt flexibiliteit bij het beheren van toegang. Zie [Gedeelde toegangshandtekeningen (SAS) gebruiken voor](storage-sas-overview.md)meer informatie.
- **Anoniem openbaar lezen toegang** voor containers en blobs. Toestemming is niet vereist. Zie [Anonieme leestoegang tot containers en blobs beheren](../blobs/storage-manage-access-to-resources.md)voor meer informatie.  

Standaard zijn alle resources in Azure Storage beveiligd en alleen beschikbaar voor de eigenaar van het account. Hoewel u een van de hierboven beschreven autorisatiestrategieën gebruiken om clients toegang te verlenen tot bronnen in uw opslagaccount, raadt Microsoft aan Azure AD te gebruiken wanneer mogelijk voor maximale beveiliging en gebruiksgemak.

## <a name="next-steps"></a>Volgende stappen

- [Toegang tot Azure-blobs en wachtrijen autoriseren met Azure Active Directory](storage-auth-aad.md)
- [Autoriseren met gedeelde sleutel](/rest/api/storageservices/authorize-with-shared-key/)
- [Beperkte toegang verlenen tot Azure Storage-bronnen met behulp van gedeelde toegangshandtekeningen (SAS)](storage-sas-overview.md)
