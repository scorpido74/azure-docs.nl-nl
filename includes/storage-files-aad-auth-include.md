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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77565079"
---
[Azure Files](../articles/storage/files/storage-files-introduction.md) ondersteunt verificatie op basis van identiteit via Server Message Block (SMB) via [Active Directory (AD)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) (preview) en [Azure Active Directory Domain Services (Azure AD DS)](../articles/active-directory-domain-services/overview.md) (GA). In dit artikel wordt aandacht uitgeschreven voor de manier waarop Azure Files gebruik kan maken van domeinservices, on-premises of in Azure, om identiteitsgebaseerde toegang tot Azure Files via SMB te ondersteunen. Hiermee u uw bestaande bestandsservers eenvoudig vervangen door Azure Files en uw bestaande directoryservice blijven gebruiken, waardoor naadloze gebruikerstoegang tot shares behouden blijft. 

Azure Files dwingt autorisatie af voor de gebruiker toegang tot zowel het aandeel als het map-/bestandsniveau. Toestemmingstoewijzing op shareniveau kan worden toegewezen aan Azure AD-gebruikers of -groepen die worden beheerd via het typische [RBAC-model (Role-based Access Control).](../articles/role-based-access-control/overview.md) Met RBAC moeten de referenties die u gebruikt voor bestandstoegang beschikbaar zijn of gesynchroniseerd met Azure AD. U ingebouwde RBAC-rollen zoals Storage File Data SMB Share Reader toewijzen aan gebruikers of groepen in Azure AD om leestoegang te verlenen tot een Azure-bestandsshare.

Op map-/bestandsniveau ondersteunt Azure Files het behouden, overnemen en afdwingen van [Windows DACLs,](https://docs.microsoft.com/windows/win32/secauthz/access-control-lists) net als alle Windows-bestandsservers. Als u gegevens over SMB kopieert vanuit een bestandsshare naar Azure-bestanden, of omgekeerd, u ervoor kiezen om Windows-DACLs te behouden. Of u nu van plan bent autorisatie af te dwingen of niet, u Azure Files gebruiken om samen met uw gegevens back-ups te maken. 
