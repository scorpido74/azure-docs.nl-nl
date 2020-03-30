---
title: Azure-bronrollen toewijzen aan gasten in PIM - Azure AD | Microsoft Documenten
description: Meer informatie over het uitnodigen van externe gastgebruikers en het toewijzen van Azure resource rollen in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2efcf77d65fa2f9e203ed805cd7d78b9802ee3aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74021943"
---
# <a name="invite-guest-users-and-assign-azure-resource-roles-in-privileged-identity-management"></a>Gasten uitnodigen en Azure-bronrollen toewijzen in Privileged Identity Management

Azure Active Directory (Azure AD) gastgebruikers maken deel uit van de Business-to-business (B2B) samenwerkingsmogelijkheden binnen Azure AD, zodat u externe gastgebruikers en leveranciers beheren als gasten in Azure AD. Wanneer u B2B-samenwerking combineert met Azure AD Privileged Identity Management (PIM), u uw nalevings- en governancevereisten uitbreiden naar gasten. U bijvoorbeeld deze functies voor beheer van privileged identity management gebruiken voor Azure-identiteitstaken met gasten:

- Toegang toewijzen aan specifieke Azure-bronnen
- Just-in-time toegang inschakelen
- Toewijzingsduur en einddatum opgeven
- Meervoudige verificatie vereisen bij actieve toewijzing of activering
- Toegangsbeoordelingen uitvoeren
- Waarschuwingen en controlelogboeken gebruiken

In dit artikel wordt beschreven hoe u een gast uitnodigt voor uw organisatie en hun toegang tot Azure-bronnen beheert met behulp van Privileged Identity Management.

## <a name="when-would-you-invite-guests"></a>Wanneer zou je gasten uitnodigen?

Hier volgen enkele voorbeelden van wanneer u gasten uitnodigen voor uw organisatie:

- Sta een externe zelfstandige leverancier toe die alleen een e-mailaccount heeft om toegang te krijgen tot uw Azure-bronnen voor een project.
- Sta een externe partner toe in een grote organisatie die on-premises Active Directory Federation Services gebruikt om toegang te krijgen tot uw onkostentoepassing.
- Sta ondersteuningstechnici toe die niet in uw organisatie zijn (zoals Microsoft-ondersteuning) om tijdelijk toegang te krijgen tot uw Azure-bron om problemen op te lossen.

## <a name="how-does-collaboration-using-b2b-guests-work"></a>Hoe werkt samenwerking met B2B-gasten?

Wanneer u B2B-samenwerking gebruikt, u een externe gebruiker uitnodigen voor uw organisatie als gast. De gast kan worden beheerd als gebruiker in uw organisatie, maar een gast moet worden geverifieerd in zijn thuisorganisatie en niet in uw Azure AD-organisatie. Dit betekent dat als de gast geen toegang meer heeft tot zijn thuisorganisatie, hij of zij ook geen toegang meer heeft tot uw organisatie. Als de gast bijvoorbeeld zijn organisatie verlaat, verliest hij of zij automatisch de toegang tot resources die u met hem of haar hebt gedeeld in Azure AD zonder dat u iets hoeft te doen. Zie [Wat is gastgebruikerstoegang in Azure Active Directory B2B?](../b2b/what-is-b2b.md).

![Diagram met de manier waarop een gastgebruiker is geverifieerd in zijn thuismap](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-guest-collaboration-settings"></a>Instellingen voor gastsamenwerking controleren

Om ervoor te zorgen dat u gasten uitnodigen voor uw organisatie, moet u uw instellingen voor gastsamenwerking controleren.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. Selecteer Azure Active**Directory-gebruikersinstellingen** **Azure Active Directory** > .

1. Selecteer **Instellingen voor externe samenwerking beheren**.

    ![Pagina instellingen voor externe samenwerking met instellingen voor machtiging, uitnodiging en samenwerkingsbeperking](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. Controleer of de **beheerders en gebruikers in de gastuitnodigingsrol de** switch kunnen uitnodigen is ingesteld op **Ja**.

## <a name="invite-a-guest-and-assign-a-role"></a>Een gast uitnodigen en een rol toewijzen

Met Privileged Identity Management u een gast uitnodigen en deze in aanmerking laten komen voor een Azure-bronrol.

1. Meld u aan bij [azure portal](https://portal.azure.com/) met een gebruiker die lid is van de functie Privileged [Role Administrator](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) of [User Administrator.](../users-groups-roles/directory-assign-admin-roles.md#user-administrator)

1. Azure **AD Privileged Identity Management openen**.

1. Selecteer **Azure-resources**.

1. Gebruik het **filter Resource** om de lijst met beheerde resources te filteren.

1. Selecteer de resource die u wilt beheren, zoals een resource, resourcegroep, abonnement of beheergroep.

    U moet de scope alleen instellen op wat de gast nodig heeft.

1. Selecteer Onder Beheren de optie **Rollen** om de lijst met rollen voor Azure-resources weer te geven.

    ![Lijst met Azure-resources-rollen met het aantal gebruikers dat actief is en in aanmerking komt](./media/pim-resource-roles-external-users/resources-roles.png)

1. Selecteer de minimale rol die de gebruiker nodig heeft.

    ![Geselecteerde rolpagina met de huidige leden van die rol](./media/pim-resource-roles-external-users/selected-role.png)

1. Selecteer op de rolpagina **Lid toevoegen** om het deelvenster Nieuwe toewijzing te openen.

1. Klik **op Een lid of groep selecteren**.

    ![Nieuwe toewijzing - Selecteer een lid of groepsvenster met gebruikers en groepen samen met een optie Uitnodigen](./media/pim-resource-roles-external-users/select-member-group.png)

1. Als u een gast wilt uitnodigen, klikt u op **Uitnodigen**.

    ![Een gastpagina met vakken uitnodigen om een e-mailadres in te voeren en een persoonlijk bericht op te geven](./media/pim-resource-roles-external-users/invite-guest.png)

1. Nadat u een gast hebt geselecteerd, klikt u op **Uitnodigen**.

    De gast moet worden toegevoegd als geselecteerd lid.

1. Klik **in het deelvenster Een lid of groeps** selecteren op **Selecteren**.

1. Selecteer in het deelvenster **Lidmaatschapsinstellingen** het toewijzingstype en de duur.

    ![Nieuwe toewijzing - pagina Lidmaatschapsinstellingen met opties om toewijzingstype, begindatum en einddatum op te geven](./media/pim-resource-roles-external-users/membership-settings.png)

1. Als u de toewijzing wilt voltooien, selecteert u **Gereed** en **voegt u vervolgens Toe**.

    De toewijzing van gastrollen wordt weergegeven in uw takenlijst.

    ![Rolpagina waarin de gast als in aanmerking komen](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-a-guest"></a>Rol activeren als gast

Als u een externe gebruiker bent, moet u de uitnodiging accepteren om gast te zijn in de Azure AD-organisatie en mogelijk uw roltoewijzing activeren.

1. Open de e-mail met uw uitnodiging. De e-mail ziet er hetzelfde uit als het volgende.

    ![E-mailuitnodiging met naam van de map, persoonlijk bericht en een koppeling Aan de slag](./media/pim-resource-roles-external-users/email-invite.png)

1. Selecteer de koppeling **Aan de slag** in de e-mail.

1. Nadat u de machtigingen hebt bekeken, klikt u op **Accepteren**.

    ![De pagina Machtigingen controleren in een browser met een lijst met machtigingen die u moet controleren](./media/pim-resource-roles-external-users/invite-accept.png)

1. Mogelijk wordt u gevraagd een gebruiksvoorwaarden te accepteren en aan te geven of u wilt blijven aangemeld. Als u in de Azure-portal *in aanmerking komt* voor een rol, hebt u nog geen toegang tot bronnen.

1. Als u uw roltoewijzing wilt activeren, opent u de e-mail met uw functiekoppeling activeren. De e-mail ziet er hetzelfde uit als het volgende.

    ![E-mail waarin staat dat u in aanmerking komt voor een rol met een koppeling Activeren rol](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. Selecteer **Rol activeren** om uw in aanmerking komende rollen in Privileged Identity Management te openen.

    ![Pagina Mijn rollen in Privileged Identity Management met uw in aanmerking komende rollen](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. Selecteer onder Actie de koppeling **Activeren.**

    Afhankelijk van de rolinstellingen moet u bepaalde informatie opgeven om de rol te activeren.

1. Nadat u de instellingen voor de rol hebt opgegeven, klikt u op **Activeren** om de rol te activeren.

    ![Paginaaanbiedingsbereik en opties activeren om de begintijd, duur en reden op te geven](./media/pim-resource-roles-external-users/activate-role.png)

    Tenzij de beheerder uw aanvraag moet goedkeuren, moet u toegang hebben tot de opgegeven bronnen.

## <a name="view-activity-for-a-guest"></a>Activiteit voor een gast weergeven

U controlelogboeken bekijken om bij te houden wat gasten doen.

1. Open als beheerder Privileged Identity Management en selecteer de bron die is gedeeld.

1. Selecteer **Resourcecontrole** om de activiteit voor die resource weer te geven. Hieronder ziet u een voorbeeld van de activiteit voor een resourcegroep.

    ![Azure-bronnen - Pagina resourcecontrole met de tijd, aanvrager en actie](./media/pim-resource-roles-external-users/audit-resource.png)

1. Als u de activiteit voor de gast wilt weergeven, selecteert u de*gastnaam* **van Azure Active Directory** > **Users** > .

1. Selecteer **Controlelogboeken** om de controlelogboeken voor de organisatie te bekijken. Indien nodig u filters opgeven.

    ![Adreslijstlogboeken die datum, doel, geïnitieerd door en activiteit vermelden](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>Volgende stappen

- [Azure AD-beheerrollen toewijzen in Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Wat is toegang tot gastgebruikers in Azure AD B2B-samenwerking?](../b2b/what-is-b2b.md)
