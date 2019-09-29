---
title: Toegang tot Azure Storage autoriseren | Microsoft Docs
description: Meer informatie over de verschillende manieren om toegang te verlenen tot Azure Storage, waaronder Azure Active Directory, gedeelde sleutel verificatie of hand tekeningen voor gedeelde toegang.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: c20f699a2d2270d11935b0216b1655390ece211c
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/29/2019
ms.locfileid: "71671063"
---
# <a name="authorizing-access-to-azure-storage"></a>Toegang tot Azure Storage autoriseren

Telkens wanneer u toegang hebt tot gegevens in uw opslag account, stuurt uw client een aanvraag via HTTP/HTTPS om Azure Storage. Elke aanvraag voor een beveiligde resource moet worden gemachtigd, zodat de-service ervoor zorgt dat de-client over de vereiste machtigingen beschikt om toegang tot de gegevens te krijgen.

In de volgende tabel worden de opties beschreven die Azure Storage biedt om toegang tot resources te verlenen:

|  |Gedeelde sleutel (sleutel van het opslag account)  |Shared Access Signature (SAS)  |Azure Active Directory (Azure AD)  |Anonieme open bare Lees toegang  |
|---------|---------|---------|---------|---------|
|Azure Blobs     |[Ondersteund](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[Ondersteund](storage-sas-overview.md)         |[Ondersteund](storage-auth-aad.md)         |[Ondersteund](../blobs/storage-manage-access-to-resources.md)         |
|Azure Files (SMB)     |[Ondersteund](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |Niet ondersteund         |[Ondersteund, alleen met AAD Domain Services](../files/storage-files-active-directory-overview.md)         |Niet ondersteund         |
|Azure Files (REST)     |[Ondersteund](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[Ondersteund](storage-sas-overview.md)         |Niet ondersteund         |Niet ondersteund         |
|Azure-wachtrijen     |[Ondersteund](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[Ondersteund](storage-sas-overview.md)         |[Ondersteund](storage-auth-aad.md)         |Niet ondersteund         |
|Azure-tabellen     |[Ondersteund](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[Ondersteund](storage-sas-overview.md)         |Niet ondersteund         |Niet ondersteund         |

Elke autorisatie optie wordt hieronder beschreven:

- **Integratie van Azure Active Directory (Azure AD)** voor blobs en wacht rijen. Azure AD biedt op rollen gebaseerd toegangs beheer (RBAC) voor nauw keurige controle over de toegang van een client tot bronnen in een opslag account. Zie voor meer informatie over Azure AD-integratie voor blobs en wacht rijen [verificatie aanvragen voor Azure Storage met behulp van Azure Active Directory](storage-auth-aad.md).

- **Azure AD Domain Services (DS) Integration (preview)** voor bestanden. Azure Files ondersteunt verificatie op basis van identiteiten via Server Message Block (SMB) via Azure AD DS. Dit biedt RBAC voor nauw keurige controle over de toegang van een client tot bronnen in een opslag account. Voor meer informatie over Azure AD-integratie voor bestanden met Domain Services, Zie [overzicht van de verificatie ondersteuning voor Azure Files Azure Active Directory Domain Service (Aad DS) voor SMB-toegang (preview)](../files/storage-files-active-directory-overview.md).

- **Gedeelde sleutel autorisatie** voor blobs, bestanden, wacht rijen en tabellen. Een client die een gedeelde sleutel gebruikt, geeft een header door elke aanvraag die is ondertekend met de toegangs sleutel voor het opslag account. Zie [autoriseren met gedeelde sleutel](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)voor meer informatie.
- **Shared Access signatures** voor blobs, bestanden, wacht rijen en tabellen. Shared Access signatures (SAS) bieden beperkte gedelegeerde toegang tot resources in een opslag account. Door beperkingen toe te voegen aan het tijds interval waarvoor de hand tekening geldig is of op machtigingen die door deze worden verleend, biedt het beheer van de toegang flexibiliteit. Zie [using Shared Access signatures (SAS) (Engelstalig)](storage-sas-overview.md)voor meer informatie.
- **Anonieme open bare Lees toegang** voor containers en blobs. Autorisatie is niet vereist. Zie [Anonieme leestoegang tot containers en blobs beheren](../blobs/storage-manage-access-to-resources.md) voor meer informatie.  

Standaard zijn alle resources in Azure Storage beveiligd en zijn ze alleen beschikbaar voor de eigenaar van het account. Hoewel u een van de hierboven beschreven verificatie strategieën kunt gebruiken om clients toegang te verlenen tot resources in uw opslag account, raadt micro soft aan om Azure AD zo mogelijk te gebruiken voor maximale beveiliging en gebruiks gemak. 
