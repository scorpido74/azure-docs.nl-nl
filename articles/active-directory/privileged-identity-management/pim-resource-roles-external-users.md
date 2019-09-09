---
title: Gasten uitnodigen en Azure-resource rollen toewijzen in PIM-Azure Active Directory | Microsoft Docs
description: Meer informatie over het uitnodigen van externe gast gebruikers en het toewijzen van Azure-resource rollen in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3e01b58a2a2fc6f93ae5ccc15e200a0cea69a0c
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804226"
---
# <a name="invite-guest-users-and-assign-azure-resource-roles-in-pim"></a>Gast gebruikers uitnodigen en Azure-resource rollen toewijzen in PIM

Business-to-Business (B2B) van Azure Active Directory (Azure AD) is een set mogelijkheden binnen Azure AD waarmee organisaties met een wille keurig account kunnen samen werken met externe gast gebruikers (gasten) en leveranciers. Wanneer u B2B combineert met Azure AD Privileged Identity Management (PIM), kunt u de vereisten voor naleving en governance blijven Toep assen op gasten. U kunt bijvoorbeeld de volgende PIM-functies gebruiken voor Azure-identiteits taken met gasten:

- Toegang tot specifieke Azure-resources toewijzen
- Just-in-time-toegang inschakelen
- De duur en eind datum van de toewijzing opgeven
- MFA vereisen voor actieve toewijzing of activering
- Toegangs beoordelingen uitvoeren
- Waarschuwingen en controle Logboeken gebruiken

In dit artikel wordt beschreven hoe u een gast kunt uitnodigen voor uw organisatie en hoe u de toegang tot Azure-resources kunt beheren met Privileged Identity Management.

## <a name="when-would-you-invite-guests"></a>Wanneer wilt u gasten uitnodigen?

Hier volgen enkele voor beelden van scenario's waarin u gasten kunt uitnodigen voor uw organisatie:

- Een externe selfservice leverancier toestaan die alleen een e-mail account heeft voor toegang tot uw Azure-resources voor een project.
- Een externe partner toestaan in een grote organisatie die gebruikmaakt van on-premises Active Directory Federation Services om toegang te krijgen tot uw onkosten toepassing.
- Ondersteunings medewerkers die niet in uw organisatie (zoals micro soft support) zijn, toestaan om tijdelijk toegang te krijgen tot uw Azure-resource om problemen op te lossen.

## <a name="how-does-collaboration-using-b2b-guests-work"></a>Hoe werkt samen werking met B2B-gasten?

Wanneer u B2B-samen werking gebruikt, kunt u een externe gebruiker als gast uitnodigen voor uw organisatie. De gast lijkt zich in uw organisatie te bevindt, maar er zijn geen referenties gekoppeld aan de gast. Wanneer een gast moet worden geverifieerd, moeten deze worden geverifieerd in hun eigen organisatie en niet in uw organisatie. Dit betekent dat als de gast geen toegang meer heeft tot hun thuis organisatie, ook de toegang tot uw organisatie verloren gaat. Als de gast bijvoorbeeld hun organisatie verlaat, verliezen ze automatisch de toegang tot resources die u met hen hebt gedeeld in azure AD zonder dat u iets hoeft te doen. Zie [Wat is gast gebruikers toegang in azure Active Directory B2B?](../b2b/what-is-b2b.md)voor meer informatie over B2B.

![Diagram waarin wordt getoond hoe een gast gebruiker in uw Directory wordt weer gegeven, maar wordt geverifieerd in de basismap](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-guest-collaboration-settings"></a>Instellingen voor samen werking van gast controleren

Om ervoor te zorgen dat u gasten kunt uitnodigen voor uw organisatie, moet u de instellingen voor de samen werking van uw gast controleren.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. Klik op **Azure Active Directory** > **gebruikers instellingen**.

1. Klik op **externe instellingen voor samen werking beheren**.

    ![Pagina instellingen voor externe samen werking met instellingen voor beperkingen voor machtigingen, uitnodigen en samen werking](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. Zorg ervoor dat de **beheerders en gebruikers in de rol gast uitnodigen de schakel optie voor uitnodigingen kunnen uitnodigen** is ingesteld op **Ja**.

## <a name="invite-a-guest-and-assign-a-role"></a>Een gast uitnodigen en een rol toewijzen

Met behulp van PIM kunt u een gast uitnodigen en deze in aanmerking komen voor een Azure-resource functie, net als een lid van een gebruiker.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/) met een gebruiker die lid is van de rol [privileged Role Administrator](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) of [user Administrator](../users-groups-roles/directory-assign-admin-roles.md#user-administrator) .

1. Open **Azure AD privileged Identity Management**.

1. Klik op **Azure-resources**.

1. Gebruik het **resource filter** om de lijst met beheerde resources te filteren.

1. Klik op de resource die u wilt beheren, zoals een resource, resource groep, abonnement of beheer groep.

    Stel het bereik in op alleen de vereisten van de gast.

1. Klik onder beheren op **functies** om de lijst met rollen voor Azure-resources weer te geven.

    ![De lijst met Azure-resources rollen met het aantal gebruikers dat actief en in aanmerking komt](./media/pim-resource-roles-external-users/resources-roles.png)

1. Klik op de minimale rol die de gebruiker nodig heeft.

    ![Geselecteerde functie pagina waarop de huidige leden van de rol worden weer gegeven](./media/pim-resource-roles-external-users/selected-role.png)

1. Klik op de pagina rol op **lid toevoegen** om het deel venster nieuwe toewijzing te openen.

1. Klik op **een lid of groep selecteren**.

    ![Nieuwe toewijzing: een lid of groeps paneel selecteren gebruikers en groepen weer geven samen met een optie voor uitnodigen](./media/pim-resource-roles-external-users/select-member-group.png)

1. Als u een gast wilt uitnodigen, klikt u op **uitnodigen**.

    ![Een gast pagina uitnodigen met vakken om een e-mail adres in te voeren en een persoonlijk bericht op te geven](./media/pim-resource-roles-external-users/invite-guest.png)

1. Nadat u een gast hebt geselecteerd, klikt u op **uitnodigen**.

    De gast moet worden toegevoegd als een geselecteerd lid.

1. Klik in het deel venster **een lid of groep selecteren** op **selecteren**.

1. Selecteer in het deel venster **lidmaatschaps instellingen** het toewijzings type en de duur.

    ![Pagina nieuwe toewijzings instellingen voor lidmaatschap met opties om het toewijzings type, de begin datum en de eind datum op te geven](./media/pim-resource-roles-external-users/membership-settings.png)

1. Klik op **gereed** en vervolgens op **toevoegen**om de toewijzing te volt ooien.

    De roltoewijzing van de gast wordt weer gegeven in de lijst met rollen.

    ![Pagina rollen die de gast aanbieden als in aanmerking komend](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-a-guest"></a>Rol activeren als gast

Als externe gebruiker moet u eerst de uitnodiging voor uw Azure AD-organisatie accepteren en mogelijk uw rol activeren.

1. Open het e-mail bericht met uw uitnodiging. Het e-mail bericht ziet er ongeveer als volgt uit.

    ![Uitnodiging voor e-mail met mapnaam, persoonlijk bericht en een koppeling aan de slag](./media/pim-resource-roles-external-users/email-invite.png)

1. Klik op de koppeling **aan de slag** in het e-mail bericht.

1. Nadat u de machtigingen hebt bekeken, klikt u op **accepteren**.

    ![De pagina Machtigingen controleren in een browser met een lijst met machtigingen die de organisatie wil laten beoordelen](./media/pim-resource-roles-external-users/invite-accept.png)

1. U wordt mogelijk gevraagd om een gebruiksrecht overeenkomst te accepteren en op te geven of u aangemeld wilt blijven.

    De Azure Portal wordt geopend. Als u alleen in aanmerking komt voor een rol, hebt u geen toegang tot resources.

1. Als u uw rol wilt activeren, opent u het e-mail bericht met de koppeling rol activeren. Het e-mail bericht ziet er ongeveer als volgt uit.

    ![E-mail bericht van PIM dat aangeeft dat u in aanmerking komt voor een rol met de koppeling rol activeren](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. Klik op **rol activeren** om uw in aanmerking komende rollen te openen in Pim.

    ![Pagina mijn rollen in PIM die uw in aanmerking komende rollen weergeeft](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. Klik onder actie op de koppeling **activeren** .

    Afhankelijk van de rolinstellingen moet u bepaalde informatie opgeven om de rol te activeren.

1. Zodra u de instellingen voor de rol hebt opgegeven, klikt u op **activeren** om de rol te activeren.

    ![Het bereik en de opties van de pagina vermelding activeren om de begin tijd, duur en reden op te geven](./media/pim-resource-roles-external-users/activate-role.png)

    Tenzij de beheerder vereist is om uw aanvraag goed te keuren, moet u toegang hebben tot de opgegeven resources.

## <a name="view-activity-for-a-guest"></a>Activiteit voor een gast weer geven

Net als bij een lid van een gebruiker kunt u audit logboeken bekijken om bij te houden wat gasten doen.

1. Als beheerder opent u PIM en selecteert u de resource die is gedeeld.

1. Klik op **resource audit** om de activiteit voor die resource weer te geven. Hieronder ziet u een voor beeld van de activiteit voor een resource groep.

    ![Azure-resources-resource audit pagina met een lijst met de tijd, aanvrager en actie](./media/pim-resource-roles-external-users/audit-resource.png)

1. Als u de activiteit voor de gast wilt weer geven, klikt u op **Azure Active Directory** > **gebruikers** > gast naam.

1. Klik op **audit logboeken** om de audit logboeken voor de organisatie weer te geven. Als dat nodig is, kunt u filters opgeven.

    ![Map audit logboeken datum, doel, geïnitieerd door en activiteit](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>Volgende stappen

- [Azure AD-beheerders rollen toewijzen in PIM](pim-how-to-add-role-to-user.md)
- [Wat is toegang tot gast gebruikers in Azure Active Directory B2B?](../b2b/what-is-b2b.md)
