---
title: Een toegangsbeoordeling van Azure AD-rollen maken in PIM - Azure AD | Microsoft Documenten
description: Meer informatie over het maken van een toegangscontrole van Azure AD-rollen in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/22/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f7e9ef503a9a3469ecbc835be8d9229fbd0167f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847117"
---
# <a name="create-an-access-review-of-azure-ad-roles-in-privileged-identity-management"></a>Een toegangscontrole van Azure AD-rollen maken in Privileged Identity Management

Als u het risico van verouderde roltoewijzingen wilt verminderen, moet u de toegang regelmatig controleren. U Azure AD Privileged Identity Management (PIM) gebruiken om toegangsbeoordelingen te maken voor geprivilegieerde Azure AD-rollen. U ook terugkerende toegangsbeoordelingen configureren die automatisch plaatsvinden.

In dit artikel wordt beschreven hoe u een of meer toegangsbeoordelingen maakt voor bevoorrechte Azure AD-rollen.

## <a name="prerequisites"></a>Vereisten

[Beheerder van een bevoorrechte rol](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>Open access-recensies

1. Meld u aan bij [azure portal](https://portal.azure.com/) met een gebruiker die lid is van de rol privileged role administrator.

1. Azure **AD Privileged Identity Management openen**.

1. Selecteer in het linkermenu **Azure AD-rollen** en selecteer **Access-recensies**.

1. Selecteer onder Beheren de optie **Toegangsbeoordelingen**.

    ![Azure AD-rollen - Lijst met toegangsbeoordelingen met de status van alle beoordelingen](./media/pim-how-to-start-security-review/access-reviews.png)

[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]

## <a name="start-the-access-review"></a>De toegangscontrole starten

Zodra u de instellingen voor een toegangscontrole hebt opgegeven, selecteert u **Start**. De toegangscontrole wordt weergegeven in uw lijst met een indicator van de status ervan.

![Lijst met beoordelingen openen met de status van gestarte beoordelingen](./media/pim-how-to-start-security-review/access-reviews-list.png)

Azure AD stuurt standaard een e-mail naar revisoren kort nadat de beoordeling is gestart. Als u ervoor kiest azure AD de e-mail niet te laten verzenden, moet u de revisoren informeren dat een toegangscontrole wacht voordat ze zijn voltooid. U ze de instructies weergeven voor het [controleren van de toegang tot Azure AD-rollen.](pim-how-to-perform-security-review.md)

## <a name="manage-the-access-review"></a>De toegangscontrole beheren

U de voortgang bijhouden terwijl de revisoren hun beoordelingen voltooien op de **overzichtspagina** van de toegangscontrole. Er worden geen toegangsrechten gewijzigd in de map totdat de [beoordeling is voltooid.](pim-how-to-complete-review.md)

![Overzichtspagina met beoordelingen van access met de details van de beoordeling](./media/pim-how-to-start-security-review/access-review-overview.png)

Als dit een eenmalige beoordeling is, volgt u na afloop van de toegangscontroleperiode of de beheerder de toegangscontrole, de stappen in [Een toegangscontrole van Azure AD-rollen voltooien](pim-how-to-complete-review.md) om de resultaten te bekijken en toe te passen.  

Als u een reeks toegangsbeoordelingen wilt beheren, navigeert u naar de toegangscontrole en vindt u aankomende gebeurtenissen in Geplande beoordelingen en bewerkt u de einddatum of voegt u revisoren dienovereenkomstig toe.

Op basis van uw selecties in **Instellingen na voltooiing**wordt automatisch toepassen uitgevoerd na de einddatum van de beoordeling of wanneer u de beoordeling handmatig stopt. De status van de beoordeling verandert van **Voltooid** via tussenliggende staten zoals **Toepassen** en ten slotte worden **vermeld toegepast**. U mag verwachten dat geweigerde gebruikers in een paar minuten uit rollen worden verwijderd.

## <a name="next-steps"></a>Volgende stappen

- [Toegang tot Azure AD-rollen controleren](pim-how-to-perform-security-review.md)
- [Een toegangscontrole van Azure AD-rollen voltooien](pim-how-to-complete-review.md)
- [Een toegangscontrole van Azure-bronrollen maken](pim-resource-roles-start-access-review.md)
