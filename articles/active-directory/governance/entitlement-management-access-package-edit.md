---
title: Toegangspakket verbergen of verwijderen in beheer van rechten - Azure AD
description: Meer informatie over het verbergen of verwijderen van een toegangspakket in Azure Active Directory-rechtenbeheer.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: cf410db5a483fc4f5d9efc1a6bd5c6202694ef4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261994"
---
# <a name="hide-or-delete-an-access-package-in-azure-ad-entitlement-management"></a>Een toegangspakket verbergen of verwijderen in Azure AD-rechtenbeheer

Toegangspakketten zijn standaard vindbaar. Dit betekent dat als een beleid een gebruiker toestaat om het toegangspakket aan te vragen, hij of zij automatisch het toegangspakket ziet dat wordt vermeld in hun My Access-portal. U de **instelling Verborgen** echter wijzigen, zodat het toegangspakket niet wordt vermeld in de My Access-portal van de gebruiker.

In dit artikel wordt beschreven hoe u een toegangspakket verbergen of verwijderen.

## <a name="change-the-hidden-setting"></a>De instelling Verborgen wijzigen

Volg deze stappen om de instelling **Verborgen** voor een toegangspakket te wijzigen.

**Vereiste rol:** Globale beheerder, gebruikersbeheerder, cataloguseigenaar of Access-pakketbeheer

1. Klik in de Azure-portal op **Azure Active Directory** en klik vervolgens op **Identiteitsbeheer**.

1. Klik in het linkermenu op **Access-pakketten** en open het toegangspakket.

1. Klik op de pagina Overzicht op **Bewerken**.

1. Stel de **instelling Verborgen** in.

    Als het toegangspakket is ingesteld op **Nee,** wordt het toegangspakket weergegeven in de Mijn Access-portal van de gebruiker.

    Als dit is ingesteld op **Ja,** wordt het toegangspakket niet vermeld in de My Access-portal van de gebruiker. De enige manier waarop een gebruiker het toegangspakket kan bekijken, is als hij de directe **My Access-portalkoppeling** naar het toegangspakket heeft. Zie [Koppeling delen om een toegangspakket aan te vragen](entitlement-management-access-package-settings.md)voor meer informatie.

## <a name="delete-an-access-package"></a>Een toegangspakket verwijderen

Een toegangspakket kan alleen worden verwijderd als er geen actieve gebruikerstoewijzingen zijn. Volg deze stappen om een toegangspakket te verwijderen.

**Vereiste rol:** Globale beheerder, gebruikersbeheerder, cataloguseigenaar of Access-pakketbeheer

1. Klik in de Azure-portal op **Azure Active Directory** en klik vervolgens op **Identiteitsbeheer**.

1. Klik in het linkermenu op **Access-pakketten** en open het toegangspakket.

1. Klik in het linkermenu op **Toewijzingen** en verwijder de toegang voor alle gebruikers.

1. Klik in het linkermenu op **Overzicht** en klik vervolgens op **Verwijderen**.

1. Klik in het verwijderbericht dat wordt weergegeven op **Ja**.

## <a name="next-steps"></a>Volgende stappen

- [Toewijzingen voor een toegangspakket weergeven, toevoegen en verwijderen](entitlement-management-access-package-assignments.md)
- [Rapporten en logboeken weergeven](entitlement-management-reports.md)
