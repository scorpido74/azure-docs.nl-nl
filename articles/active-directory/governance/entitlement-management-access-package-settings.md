---
title: Koppeling delen om een toegangspakket aan te vragen in Azure AD-rechtenbeheer - Azure Active Directory
description: Meer informatie over het delen van koppelingen om een toegangspakket aan te vragen in Azure Active Directory-rechtenbeheer.
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
ms.openlocfilehash: ea90032b1f0cfe598ffdb3d35448a996f3111036
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968765"
---
# <a name="share-link-to-request-an-access-package-in-azure-ad-entitlement-management"></a>Koppeling delen om een toegangspakket aan te vragen in Azure AD-rechtenbeheer

De meeste gebruikers in uw directory kunnen zich aanmelden bij de My Access-portal en automatisch een lijst met toegangspakketten zien die ze kunnen aanvragen. Voor externe zakelijke partnergebruikers die nog niet in uw directory staan, moet u hen echter een koppeling sturen die ze kunnen gebruiken om een toegangspakket aan te vragen. 

Zolang de catalogus voor het toegangspakket is [ingeschakeld voor externe gebruikers](entitlement-management-catalog-create.md) en u een beleid hebt voor de map van de externe [gebruiker,](entitlement-management-access-package-request-policy.md)kan de externe gebruiker de koppeling Mijn access-portal gebruiken om het toegangspakket aan te vragen.

## <a name="share-link-to-request-an-access-package"></a>Koppeling delen om een toegangspakket aan te vragen

**Vereiste rol:** Globale beheerder, gebruikersbeheerder, cataloguseigenaar of Access-pakketbeheer

1. Klik in de Azure-portal op **Azure Active Directory** en klik vervolgens op **Identiteitsbeheer**.

1. Klik in het linkermenu op **Access-pakketten** en open het toegangspakket.

1. Kopieer op de pagina Overzicht de **koppeling Mijn Access-portal**.

    ![Overzicht van toegangspakketten - Koppeling mijn Access-portal](./media/entitlement-management-shared/my-access-portal-link.png)

    Het is belangrijk dat u de volledige My Access-portalkoppeling kopieert wanneer u deze naar een interne zakenpartner stuurt. Dit zorgt ervoor dat de partner toegang krijgt tot de portal van uw directory om hun verzoek in te dienen. De koppeling `myaccess`begint met , bevat een directory hint, en eindigt met een toegangspakket-ID.  (Voor de Amerikaanse regering is `myaccess.microsoft.us`het domein in de link My Access-portal .)

    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. E-mail of stuur de link naar uw externe zakenpartner. Ze kunnen de link delen met hun gebruikers om het toegangspakket aan te vragen.

## <a name="next-steps"></a>Volgende stappen

- [Toegang tot een toegangspakket aanvragen](entitlement-management-request-access.md)
- [Toegangsaanvragen goedkeuren of weigeren](entitlement-management-request-approve.md)