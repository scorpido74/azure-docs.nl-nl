---
title: bestand opnemen
description: bestand met vereisten opnemen
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
> **Schakel wachtwoordhashsynchronisatie in op Azure AD Domain Services voordat u de taken in dit artikel voltooit.**
>
> Volg de onderstaande instructies, afhankelijk van het type gebruikers in uw Azure AD-map. Vul beide sets instructies in als u een mix van alleen cloud- en gesynchroniseerde gebruikersaccounts in uw Azure AD-map hebt. U mogelijk niet de volgende bewerkingen uitvoeren in het geval u een B2B Guest-account probeert te gebruiken (bijvoorbeeld uw gmail of MSA van een andere identiteitsprovider die we toestaan), omdat we het wachtwoord voor deze gebruikers niet hebben gesynchroniseerd met beheerd domein, omdat dit gastaccounts in de directory. De volledige informatie over deze accounts, inclusief hun wachtwoorden, zou buiten Azure AD liggen en omdat deze informatie niet in Azure AD staat, wordt deze niet eens gesynchroniseerd met het beheerde domein. 
> - [Instructies voor gebruikersaccounts met alleen cloud](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)
> - [Instructies voor gebruikersaccounts die zijn gesynchroniseerd vanuit een on-premises map](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant.md)
