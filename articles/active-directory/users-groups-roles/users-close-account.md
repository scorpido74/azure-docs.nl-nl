---
title: Sluit uw werk-of school account in een niet-beheerde map-Azure Active Directory | Microsoft Docs
description: Uw werk-of school account sluiten in een onbeheerde Azure Active Directory.
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
ms.openlocfilehash: 0da4c6b1c1434dae564ab4876a3ab3f341a87097
ms.sourcegitcommit: a3a40ad60b8ecd8dbaf7f756091a419b1fe3208e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69891958"
---
# <a name="close-your-work-or-school-account-in-an-unmanaged-directory"></a>Uw werk-of school account sluiten in een niet-beheerde map

Als u een gebruiker in een niet-beheerde Azure Active Directory-organisatie (Azure AD) bent en u niet langer apps van die organisatie wilt gebruiken of een koppeling ermee wilt behouden, kunt u uw account op elk gewenst moment sluiten. Een onbeheerde map heeft geen globale beheerder. Gebruikers in een onbeheerde map kunnen hun eigen accounts ook op hun eigen account sluiten zonder dat ze contact hoeven op te nemen met een beheerder.

Gebruikers in een map die niet wordt beheerd, worden vaak gemaakt tijdens het aanmelden met een self-service. Een voor beeld hiervan is een informatie medewerker in een organisatie die zich aanmeldt voor een gratis service. Zie [Wat is self-service registratie voor Azure Active Directory?](directory-self-service-signup.md)voor meer informatie over selfservice registratie.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="before-you-begin"></a>Voordat u begint

Voordat u uw account kunt sluiten, moet u de volgende items bevestigen:

* Zorg ervoor dat u een gebruiker bent van een onbeheerde Azure AD-adres lijst. U kunt uw account niet sluiten als u deel uitmaakt van een beheerde map. Als u deel uitmaakt van een beheerde map en u uw account wilt sluiten, neemt u contact op met uw beheerder. Zie [de gebruiker verwijderen uit een niet-beheerde Tenant](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)voor informatie over hoe u kunt bepalen of u tot een niet-beheerde Directory behoort.

* Sla de gegevens op die u wilt bewaren. Zie door het systeem gegenereerde logboeken voor niet- [beheerde tenants openen en exporteren](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)voor meer informatie over het verzenden van een aanvraag voor exporteren.

> [!WARNING]
> Het sluiten van uw account is onomkeerbaar. Wanneer u uw account sluit, worden alle persoonlijke gegevens verwijderd. U hebt geen toegang meer tot uw account en gegevens die aan uw account zijn gekoppeld.

## <a name="close-your-account"></a>Je account sluiten

Voer de volgende stappen uit om een niet-beheerd werk-of school account te sluiten:

1. Meld u aan om [uw account te sluiten](https://go.microsoft.com/fwlink/?linkid=873123)met het account dat u wilt sluiten.

1. Selecteer bij **mijn gegevens aanvragen** **account sluiten**.

    ![Mijn gegevens aanvragen-account sluiten](./media/users-close-account/close-account.png)

1. Controleer het bevestigings bericht en selecteer vervolgens **Ja**.

    ![Mijn gegevens aanvragen-sluiten bevestigen](./media/users-close-account/confirm-close.png)

## <a name="next-steps"></a>Volgende stappen

- [Wat is een self-service registratie voor Azure Active Directory?](directory-self-service-signup.md)
- [De gebruiker uit een niet-beheerde Tenant verwijderen](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)
- [Door het systeem gegenereerde logboeken openen en exporteren voor onbeheerde tenants](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)
