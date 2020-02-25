---
title: bestand opnemen
description: bestand opnemen
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/30/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: b248bbb526baf355faf2564358884fd83422b037
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565079"
---
[Azure files](../articles/storage/files/storage-files-introduction.md) ondersteunt verificatie op basis van identiteiten via Server Message Block (SMB) via [Active Directory (AD)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) (preview) en [Azure Active Directory Domain Services (Azure AD DS)](../articles/active-directory-domain-services/overview.md) (ga). In dit artikel wordt uitgelegd hoe Azure Files domein Services, on-premises of in azure, kunt gebruiken ter ondersteuning van op identiteit gebaseerde toegang tot Azure Files over SMB. Op deze manier kunt u uw bestaande bestands servers eenvoudig vervangen door Azure Files en uw bestaande adreslijst service blijven gebruiken, zodat u naadloze gebruikers toegang tot shares kunt behouden. 

Azure Files dwingt autorisatie af voor toegang van de gebruiker tot zowel de share als het Directory-of bestands niveau. Machtigings toewijzing op share niveau kan worden toegewezen aan Azure AD-gebruikers of-groepen die worden beheerd via het standaard model voor [op rollen gebaseerd toegangs beheer (RBAC)](../articles/role-based-access-control/overview.md) . Met RBAC moeten de referenties die u gebruikt voor toegang tot het bestand beschikbaar zijn of worden gesynchroniseerd met Azure AD. U kunt ingebouwde RBAC-rollen zoals opslag bestands gegevens van SMB-share aan gebruikers of groepen in azure AD toewijzen om Lees toegang te verlenen aan een Azure-bestands share.

Op Directory-of bestands niveau ondersteunt Azure Files het behoud, overnemen en afdwingen van Windows- [dacl's](https://docs.microsoft.com/windows/win32/secauthz/access-control-lists) , net als bij Windows-bestands servers. Als u gegevens via SMB kopieert van een bestands share naar Azure Files, of andersom, kunt u ervoor kiezen om Windows-DACL'S te blijven gebruiken. Of u van plan bent autorisatie af te dwingen, u kunt gebruikmaken van Azure Files voor back-up-Acl's samen met uw gegevens. 
