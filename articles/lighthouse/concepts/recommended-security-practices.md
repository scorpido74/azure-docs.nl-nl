---
title: Aanbevolen beveiligingsprocedures
description: Bij het gebruik van Azure delegated resource management is het belangrijk om rekening te houden met beveiliging en toegangscontrole.
ms.date: 03/24/2020
ms.topic: conceptual
ms.openlocfilehash: d9b806aaf988fedfde6ce468f3eff948aa8ce344
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246905"
---
# <a name="recommended-security-practices"></a>Aanbevolen beveiligingsprocedures

Bij het gebruik van [Azure delegated resource management](azure-delegated-resource-management.md)is het belangrijk om rekening te houden met beveiliging en toegangscontrole. Gebruikers in uw tenant hebben rechtstreeks toegang tot abonnementen en brongroepen van klanten, dus u wilt stappen ondernemen om de beveiliging van uw tenant te behouden. U wilt er ook voor zorgen dat u alleen de toegang toestaat die nodig is om de resources van uw klanten effectief te beheren. Dit onderwerp bevat aanbevelingen om u daarbij te helpen.

## <a name="require-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication vereisen

[Azure Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) (ook wel verificatie in twee stappen genoemd) voorkomt dat aanvallers toegang krijgen tot een account door meerdere verificatiestappen te vereisen. U moet multifactorverificatie vereisen voor alle gebruikers in de tenant van uw serviceprovider, inclusief gebruikers die toegang hebben tot klantbronnen.

We raden u aan uw klanten te vragen azure multi-factor-authenticatie ook in hun tenants te implementeren.

## <a name="assign-permissions-to-groups-using-the-principle-of-least-privilege"></a>Machtigingen toewijzen aan groepen met behulp van het principe van de minste bevoegdheden

Om het beheer eenvoudiger te maken, raden we u aan azure AD-gebruikersgroepen te gebruiken voor elke rol die nodig is om de resources van uw klanten te beheren. Hiermee u afzonderlijke gebruikers toevoegen of verwijderen aan de groep als dat nodig is, in plaats van machtigingen rechtstreeks aan die gebruiker toe te voegen of toe te voegen.

> [!IMPORTANT]
> Als u machtigingen wilt toevoegen voor een Azure AD-groep, moet het **type Groep** **beveiliging** zijn en geen **Office 365**. Deze optie wordt geselecteerd wanneer de groep wordt gemaakt. Zie [Een basisgroep maken en leden toevoegen met behulp van Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) voor meer informatie.

Wanneer u uw machtigingsstructuur maakt, moet u het principe van de minste bevoegdheden volgen, zodat gebruikers alleen de machtigingen hebben die nodig zijn om hun taak te voltooien, waardoor de kans op onbedoelde fouten wordt verminderd.

U bijvoorbeeld een structuur als deze gebruiken:

|Groepsnaam  |Type  |principalId  |Roldefinitie ophalen  |Functiedefinitie-ID  |
|---------|---------|---------|---------|---------|
|Architecten     |Gebruikersgroep         |\<principalId\>         |Inzender         |b24988ac-6180-42a0-ab88-20f7382dd24c  |
|Beoordeling     |Gebruikersgroep         |\<principalId\>         |Lezer         |acdd72a7-3385-48ef-bd42-f606fba81ae7  |
|VM-specialisten     |Gebruikersgroep         |\<principalId\>         |VM-bijdrager         |9980e02c-c2be-4d73-94e8-173b1dc7cf3c  |
|Automation     |Servicehoofdnaam (SPN)         |\<principalId\>         |Inzender         |b24988ac-6180-42a0-ab88-20f7382dd24c  |

Zodra u deze groepen hebt gemaakt, u gebruikers toewijzen als dat nodig is. Voeg alleen de gebruikers toe die echt toegang nodig hebben. Zorg ervoor dat u het groepslidmaatschap regelmatig controleert en gebruikers verwijdert die niet langer geschikt of noodzakelijk zijn om op te nemen.

Houd er rekening mee dat wanneer u [klanten aan boord neemt via een openbare serviceaanbieding,](../how-to/publish-managed-services-offers.md)elke groep (of gebruiker of serviceprincipal) die u opneemt, dezelfde machtigingen heeft voor elke klant die het abonnement koopt. Als u verschillende groepen wilt toewijzen om met elke klant te werken, moet u een afzonderlijk privéplan publiceren dat exclusief is voor elke klant of klanten aan boord afzonderlijk met Azure Resource Manager-sjablonen. U bijvoorbeeld een openbaar plan publiceren dat zeer beperkte toegang heeft en vervolgens rechtstreeks met de klant samenwerken om aan boord van hun resources te werken voor extra toegang met behulp van een aangepaste Azure Resource-sjabloon die extra toegang verleent als dat nodig is.


## <a name="next-steps"></a>Volgende stappen

- [Azure Multi-Factor Authentication implementeren](../../active-directory/authentication/howto-mfa-getstarted.md).
- Meer informatie over [cross-tenant management ervaringen](cross-tenant-management-experience.md).
