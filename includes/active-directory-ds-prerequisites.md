---
title: bestand opnemen
description: bestand met vereisten toevoegen
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 66b5f8e2-e08d-43c8-b460-6204aecda8f7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.custom: include file
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: maheshu
ms.openlocfilehash: f7b1b294e9500ef9e0aadd24cfe3cd4e61fddda1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68426875"
---
> [!IMPORTANT]
> **Schakel wacht woord-hash-synchronisatie in op Azure AD Domain Services, voordat u de taken in dit artikel voltooit.**
>
> Volg de onderstaande instructies, afhankelijk van het type gebruikers in uw Azure AD-adres lijst. Volg beide instructies als u een combi natie van alleen Cloud-en gesynchroniseerde gebruikers accounts in uw Azure AD-adres lijst hebt. U kunt de volgende bewerkingen mogelijk niet uitvoeren als u een B2B-gast account wilt gebruiken (bijvoorbeeld uw Gmail of MSA van een andere ID-provider die we toestaan) omdat het wacht woord voor deze gebruikers niet is gesynchroniseerd met het beheerde domein, aangezien gast accounts in de Directory zijn. De volledige informatie over deze accounts met inbegrip van hun wacht woorden zou zich buiten Azure AD bevinden en omdat deze informatie niet in azure AD wordt gebruikt, wordt deze zelfs niet gesynchroniseerd met het beheerde domein. 
> - [Instructies voor Cloud gebruikers accounts](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)
> - [Instructies voor gebruikers accounts die zijn gesynchroniseerd vanuit een on-premises Directory](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant.md)
