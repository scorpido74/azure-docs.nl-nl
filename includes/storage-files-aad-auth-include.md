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
ms.openlocfilehash: 167d50e5c7f3049f46fd8540630e47044240809f
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536575"
---
[Azure Files](../articles/storage/files/storage-files-introduction.md) ondersteunt verificatie op basis van identiteit via Server Message Block (SMB) via [on-premises Active Directory Domain Services (AD DS)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) (preview) en [Azure Active Directory Domain Services (Azure AD DS)](../articles/active-directory-domain-services/overview.md). In dit artikel wordt aandacht gevraagd voor de manier waarop Azure-bestandsshares domeinservices kunnen gebruiken, on-premises of in Azure, ter ondersteuning van identiteitsgebaseerde toegang tot Azure-bestandsshares via SMB. Als u toegang op basis van identiteit inschakelt voor uw Azure-bestandsshares, u bestaande bestandsservers vervangen door Azure-bestandsshares zonder uw bestaande directoryservice te vervangen, waardoor naadloze gebruikerstoegang tot shares behouden blijven. 

Azure Files verleent autorisatie voor gebruikerstoegang tot zowel de share- als de map-/bestandsniveaus. Toestemmingstoewijzing op share-level kan worden uitgevoerd op Azure Active Directory-gebruikers of -groepen die worden beheerd via het [RBAC-model (Role-based Access Control).](../articles/role-based-access-control/overview.md) Met RBAC moeten de referenties die u gebruikt voor bestandstoegang beschikbaar zijn of gesynchroniseerd met Azure AD. U ingebouwde RBAC-rollen zoals Storage File Data SMB Share Reader toewijzen aan gebruikers of groepen in Azure AD om leestoegang te verlenen tot een Azure-bestandsshare.

Op map-/bestandsniveau ondersteunt Azure Files het behouden, overnemen en afdwingen van [Windows DACLs,](https://docs.microsoft.com/windows/win32/secauthz/access-control-lists) net als alle Windows-bestandsservers. U ervoor kiezen om Windows DACLs te bewaren bij het kopiëren van gegevens over SMB tussen uw bestaande bestandsshare en uw Azure-bestandsshares. Of u nu van plan bent autorisatie af te dwingen of niet, u Azure-bestandsshares gebruiken om een back-up te maken van ACL's samen met uw gegevens. 
