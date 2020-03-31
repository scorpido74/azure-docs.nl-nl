---
title: Werk- of schoolaccount sluiten in een onbeheerde Azure AD-map
description: Uw werk- of schoolaccount sluiten in een niet-beheerde Azure Active Directory.
services: active-directory
author: rolyon
manager: mtillman
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 05/20/2019
ms.author: rolyon
ms.reviewer: ''
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c101c0ef7932151e675c5c514ac558e6e0f94b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73815725"
---
# <a name="close-your-work-or-school-account-in-an-unmanaged-directory"></a>Uw werk- of schoolaccount sluiten in een niet-beheerde map

Als u een gebruiker bent in een azure ad-organisatie (unmanaged Azure Active Directory) en u geen apps meer uit die organisatie hoeft te gebruiken of ermee hoeft te werken, u uw account op elk gewenst moment sluiten. Een niet-beheerde map heeft geen globale beheerder. Gebruikers in een niet-beheerde map kunnen hun accounts zelf sluiten, zonder contact op te nemen met een beheerder.

Gebruikers in een niet-beheerde map worden vaak gemaakt tijdens selfservice-aanmelding. Een voorbeeld hiervan is een informatiemedewerker in een organisatie die zich aanmeldt voor een gratis service. Zie Aanmelden voor selfservice voor Azure Active Directory voor meer informatie over [selfservice-aanmelding.](directory-self-service-signup.md)

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="before-you-begin"></a>Voordat u begint

Voordat je je account sluiten, moet je de volgende objecten bevestigen:

* Zorg ervoor dat u een gebruiker bent van een niet-beheerde Azure AD-map. Je je account niet sluiten als je deel uitmaakt van een beheerde directory. Als u deel uitmaakt van een beheerde directory en uw account wilt sluiten, moet u contact opnemen met uw beheerder. Zie [De gebruiker verwijderen uit niet-beheerde tenant](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)voor informatie over het bepalen of u tot een niet-beheerde map behoort.

* Sla alle gegevens op die u wilt bewaren. Zie Logboeken openen en exporteren [voor niet-beheerde tenants voor](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)informatie over het indienen van een exportaanvraag.

> [!WARNING]
> Het sluiten van uw account is onomkeerbaar. Wanneer u uw account sluit, worden alle persoonlijke gegevens verwijderd. Je hebt geen toegang meer tot je account en gegevens die aan je account zijn gekoppeld.

## <a name="close-your-account"></a>Uw account sluiten

Voer de volgende stappen uit om een onbeheerd werk- of schoolaccount te sluiten:

1. Meld u aan om uw account te [sluiten,](https://go.microsoft.com/fwlink/?linkid=873123)met behulp van het account dat u wilt sluiten.

1. Selecteer Account **sluiten**in **Mijn gegevensaanvragen**.

    ![Mijn gegevensaanvragen - Account sluiten](./media/users-close-account/close-account.png)

1. Bekijk het bevestigingsbericht en selecteer **Ja**.

    ![Mijn gegevensaanvragen - Sluiten bevestigen](./media/users-close-account/confirm-close.png)

## <a name="next-steps"></a>Volgende stappen

- [Wat is selfservice-aanmelding voor Azure Active Directory?](directory-self-service-signup.md)
- [De gebruiker uit de onbeheerde tenant verwijderen](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)
- [Door het systeem gegenereerde logboeken voor onbeheerde tenants openen en exporteren](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)
