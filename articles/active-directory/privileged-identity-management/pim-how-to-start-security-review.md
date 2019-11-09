---
title: Een toegangs beoordeling van Azure AD-rollen maken in PIM-Azure AD | Microsoft Docs
description: Meer informatie over het maken van een toegangs beoordeling van Azure AD-rollen in Azure AD Privileged Identity Management (PIM).
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
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847117"
---
# <a name="create-an-access-review-of-azure-ad-roles-in-privileged-identity-management"></a>Een toegangs beoordeling maken van Azure AD-rollen in Privileged Identity Management

Om het risico te verminderen dat is gekoppeld aan verouderde roltoewijzingen, moet u de toegang regel matig controleren. U kunt Azure AD Privileged Identity Management (PIM) gebruiken om toegangs beoordelingen te maken voor beschermde Azure AD-rollen. U kunt ook terugkerende toegangs beoordelingen configureren die automatisch worden uitgevoerd.

In dit artikel wordt beschreven hoe u een of meer toegangs beoordelingen maakt voor beschermde Azure AD-rollen.

## <a name="prerequisites"></a>Vereisten

[Beheerder van geprivilegieerde rol](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>Toegangs beoordelingen openen

1. Meld u aan bij [Azure Portal](https://portal.azure.com/) met een gebruiker die lid is van de beheerdersrol privileged Role.

1. Open **Azure AD privileged Identity Management**.

1. Selecteer in het menu links de optie **Azure AD-rollen** en selecteer vervolgens **toegangs beoordelingen**.

1. Selecteer onder beheren de optie **toegangs beoordelingen**.

    ![Azure AD-rollen: lijst met toegangs beoordelingen waarin de status van alle beoordelingen wordt weer gegeven](./media/pim-how-to-start-security-review/access-reviews.png)

[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]

## <a name="start-the-access-review"></a>De toegangs beoordeling starten

Nadat u de instellingen voor een toegangs beoordeling hebt opgegeven, selecteert u **starten**. De toegangs beoordeling wordt weer gegeven in de lijst met een indicator van de status.

![Lijst met toegangs beoordelingen waarin de status van de gestarte beoordelingen wordt weer gegeven](./media/pim-how-to-start-security-review/access-reviews-list.png)

Standaard verzendt Azure AD een e-mail naar revisoren kort nadat de controle is gestart. Als u ervoor kiest om Azure AD de e-mail niet te laten verzenden, moet u de revisoren informeren dat een toegangs beoordeling wacht totdat deze is voltooid. U kunt de instructies weer geven voor het controleren van de [toegang tot Azure AD-rollen](pim-how-to-perform-security-review.md).

## <a name="manage-the-access-review"></a>De toegangs beoordeling beheren

U kunt de voortgang volgen zodra de revisoren hun beoordelingen hebben voltooid op de pagina **overzicht** van de toegangs beoordeling. Er worden geen toegangs rechten gewijzigd in de map totdat de [controle is voltooid](pim-how-to-complete-review.md).

![Overzichts pagina toegangs beoordelingen met details van de beoordeling](./media/pim-how-to-start-security-review/access-review-overview.png)

Als dit een eenmalige controle is, volgt u de stappen in een [toegangs beoordeling van Azure AD-rollen](pim-how-to-complete-review.md) om de resultaten te bekijken en toe te passen nadat de toegangs beoordeling is voltooid of de beheerder de toegangs beoordeling heeft gestopt.  

Als u een reeks toegangs beoordelingen wilt beheren, gaat u naar de toegangs beoordeling en gaat u naar de geplande Beoordelingen. vervolgens kunt u de eind datum bewerken of revisoren toevoegen/verwijderen dienovereenkomstig.

Op basis van uw selecties tijdens de **voltooiings instellingen**wordt automatisch Toep assen na de eind datum van de beoordeling of wanneer u de controle hand matig stopt. De status van de beoordeling wordt gewijzigd van **voltooid** met behulp van tussenliggende statussen, zoals **Toep assen** en tot slot op de status **toegepast**. U wordt gewend om geweigerde gebruikers, indien van toepassing, te zien, indien aanwezig, die in een paar minuten worden verwijderd uit rollen.

## <a name="next-steps"></a>Volgende stappen

- [Toegang tot Azure AD-rollen controleren](pim-how-to-perform-security-review.md)
- [Een toegangs beoordeling van Azure AD-rollen volt ooien](pim-how-to-complete-review.md)
- [Een toegangs beoordeling van Azure-resource rollen maken](pim-resource-roles-start-access-review.md)
