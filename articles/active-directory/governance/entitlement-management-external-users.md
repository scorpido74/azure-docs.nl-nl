---
title: Toegang regelen voor externe gebruikers in Azure AD-rechtenbeheer - Azure Active Directory
description: Meer informatie over de instellingen die u opgeven om de toegang voor externe gebruikers in Azure Active Directory-rechtenbeheer te regelen.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: af0f68ca9520c1715463212da80aaabed48f8269
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128685"
---
# <a name="govern-access-for-external-users-in-azure-ad-entitlement-management"></a>Toegang regelen voor externe gebruikers in Azure AD-rechtenbeheer

Azure AD-rechtenbeheer maakt gebruik van [Azure AD business-to-business (B2B)](../b2b/what-is-b2b.md) om samen te werken met mensen buiten uw organisatie in een andere directory. Met Azure AD B2B verifiëren externe gebruikers zich naar hun thuismap, maar hebben ze een weergave in uw map. Met de weergave in uw map kan de gebruiker toegang krijgen tot uw bronnen.

In dit artikel worden de instellingen beschreven die u opgeven om de toegang voor externe gebruikers te regelen.

## <a name="how-entitlement-management-can-help"></a>Hoe het beheer van rechten kan helpen

Wanneer u de [Azure AD B2B-uitnodigingservaring](../b2b/what-is-b2b.md) gebruikt, moet u al de e-mailadressen kennen van de externe gastgebruikers die u in uw bronmap wilt plaatsen en waarmee u wilt werken. Dit werkt geweldig als je werkt aan een kleiner of kortdurend project en je kent alle deelnemers al, maar dit is moeilijker te beheren als je veel gebruikers hebt waarmee je wilt werken of als de deelnemers in de loop van de tijd veranderen.  U werkt bijvoorbeeld mogelijk samen met een andere organisatie en hebt één aanspreekpunt met die organisatie, maar na verloop van tijd hebben ook extra gebruikers van die organisatie toegang nodig.

Met beheer van rechten u een beleid definiëren waarmee gebruikers van door u opgegeven organisaties zelf een toegangspakket kunnen aanvragen. U opgeven of goedkeuring vereist is en een vervaldatum voor de toegang. Als goedkeuring vereist is, u ook een of meer gebruikers van de externe organisatie uitnodigen voor uw directory en deze aanwijzen als fiatteurs, omdat ze waarschijnlijk weten welke externe gebruikers van hun organisatie toegang nodig hebben. Zodra u het toegangspakket hebt geconfigureerd, u de koppeling van het toegangspakket naar uw contactpersoon (sponsor) sturen bij de externe organisatie. Die contactpersoon kan delen met andere gebruikers in de externe organisatie en ze kunnen deze link gebruiken om het toegangspakket aan te vragen. Gebruikers van die organisatie die al zijn uitgenodigd in uw directory, kunnen die koppeling ook gebruiken.

Wanneer een aanvraag wordt goedgekeurd, biedt het beheer van de rechten de gebruiker de nodige toegang, waaronder het uitnodigen van de gebruiker als deze nog niet in uw directory staat. Azure AD maakt automatisch een B2B-gastaccount voor hen. Houd er rekening mee dat een beheerder eerder heeft beperkt welke organisaties zijn toegestaan voor samenwerking, door een [B2B-lijst toe te staan of te weigeren](../b2b/allow-deny-list.md) om uitnodigingen aan andere organisaties toe te staan of te blokkeren.  Als de gebruiker niet is toegestaan door de lijst toestaan of blokkeren, worden ze niet uitgenodigd.

Aangezien u niet wilt dat de toegang van de externe gebruiker eeuwig duurt, geeft u een vervaldatum op in het beleid, zoals 180 dagen. Als de toegang niet wordt verlengd, verwijdert het beheer van het recht na 180 dagen alle toegang die aan dat toegangspakket is gekoppeld. Als de gebruiker die is uitgenodigd via het beheer van het rechtenbeheer geen andere toewijzingen voor toegangspakketten heeft, wordt zijn gastaccount standaard gedurende 30 dagen geblokkeerd en vervolgens verwijderd. Dit voorkomt de verspreiding van onnodige rekeningen. Zoals beschreven in de volgende secties, zijn deze instellingen configureerbaar.

## <a name="how-access-works-for-external-users"></a>Hoe toegang werkt voor externe gebruikers

De volgende diagrammen en stappen geven een overzicht van hoe externe gebruikers toegang krijgen tot een toegangspakket.

![Diagram met de levenscyclus van externe gebruikers](./media/entitlement-management-external-users/external-users-lifecycle.png)

1. U [voegt een verbonden organisatie toe](entitlement-management-organization.md) voor de Azure AD-map of -domein waarmee u wilt samenwerken.

1. U maakt een toegangspakket in uw directory met een beleid [voor gebruikers die niet in uw map staan.](entitlement-management-access-package-create.md#for-users-not-in-your-directory)

1. U stuurt een [My Access-portalkoppeling](entitlement-management-access-package-settings.md) naar uw contactpersoon bij de externe organisatie die ze met hun gebruikers kunnen delen om het toegangspakket aan te vragen.

1. Een externe gebruiker **(Verzoeker A** in dit voorbeeld) gebruikt de koppeling Mijn Access-portal om toegang tot het toegangspakket aan te [vragen.](entitlement-management-request-access.md) Hoe de gebruiker zich aanmeldt, is afhankelijk van het verificatietype van de map of het domein dat is gedefinieerd in de verbonden organisatie.

1. Een goedkeurder [keurt het verzoek goed](entitlement-management-request-approve.md) (of het verzoek is automatisch goedgekeurd).

1. Het verzoek gaat in de [leverende staat](entitlement-management-process.md).

1. Met behulp van het B2B-uitnodigingsproces wordt in dit voorbeeld een gastgebruikersaccount gemaakt in uw directory **(Aanvrager of Een (Gast).** Als een [lijst met toestaan of een weigeringslijst](../b2b/allow-deny-list.md) is gedefinieerd, wordt de lijstinstelling toegepast.

1. De gastgebruiker krijgt toegang tot alle bronnen in het toegangspakket. Het kan enige tijd duren voordat wijzigingen worden aangebracht in Azure AD en in andere Microsoft Online Services of verbonden SaaS-toepassingen. Zie [Wanneer wijzigingen worden toegepast](entitlement-management-access-package-resources.md#when-changes-are-applied)voor meer informatie .

1. De externe gebruiker ontvangt een e-mail waarin staat dat zijn of haar toegang is [geleverd.](entitlement-management-process.md)

1. Als u toegang wilt krijgen tot de bronnen, kan de externe gebruiker op de koppeling in de e-mail klikken of rechtstreeks proberen toegang te krijgen tot een van de directorybronnen om het uitnodigingsproces te voltooien.

1. Afhankelijk van de beleidsinstellingen verloopt naarmate de tijd verstrijkt, de toewijzing van het toegangspakket voor de externe gebruiker en wordt de toegang van de externe gebruiker verwijderd.

1. Afhankelijk van de levenscyclus van externe gebruikersinstellingen wordt de externe gebruiker, wanneer de externe gebruiker geen toegangspakkettoewijzingen meer heeft, geblokkeerd om zich aan te melden en wordt het gastgebruikersaccount uit uw directory verwijderd.

## <a name="settings-for-external-users"></a>Instellingen voor externe gebruikers

Om ervoor te zorgen dat mensen buiten uw organisatie toegangspakketten kunnen aanvragen en toegang kunnen krijgen tot de bronnen in die toegangspakketten, zijn er enkele instellingen die u moet verifiëren dat ze correct zijn geconfigureerd.

### <a name="enable-catalog-for-external-users"></a>Catalogus inschakelen voor externe gebruikers

- Wanneer u een [nieuwe catalogus](entitlement-management-catalog-create.md)maakt, is het standaard ingeschakeld om externe gebruikers toegangspakketten in de catalogus te laten aanvragen. Controleer of **Ingeschakeld voor externe gebruikers** is ingesteld op **Ja**.

    ![Catalogusinstellingen bewerken](./media/entitlement-management-shared/catalog-edit.png)

### <a name="configure-your-azure-ad-b2b-external-collaboration-settings"></a>Uw azure AD B2B-instellingen voor externe samenwerking configureren

- Als gasten andere gasten in uw directory kunnen uitnodigen, betekent dit dat uitnodigingen voor gasten buiten het beheer van de rechten kunnen plaatsvinden. We raden aan om te stellen **Gasten kunnen uitnodigen** om **nee** alleen toe te staan voor goed geregelde uitnodigingen.
- Als u de lijst met B2B-toegestane gebruikt, moet u ervoor zorgen dat elk domein waarmee u wilt samenwerken met behulp van rechtenbeheer aan de lijst wordt toegevoegd. Als u de Lijst b2b-weigering gebruikt, moet u er ook voor zorgen dat elk domein waarmee u wilt samenwerken, niet aan de lijst wordt toegevoegd.
- Als u een beheerbeleid voor **alle gebruikers** maakt (Alle verbonden organisaties + nieuwe externe gebruikers), heeft elke B2B-weergave of weigering van lijstinstellingen die u hebt voorrang. Zorg er daarom voor dat u de domeinen die u in dit beleid wilt opnemen, opneemt in uw lijst met toegestane gegevens als u er een gebruikt, en sluit deze uit van uw weigeringslijst als u een weigeringslijst gebruikt.
- Als u een beheerbeleid voor rechten wilt maken dat **alle gebruikers** bevat (Alle verbonden organisaties + nieuwe externe gebruikers), moet u eerst verificatie van een eenmalige toegangscode voor uw directory inschakelen. Zie [Verificatie van eenmalige toegangscode (voorbeeld) e-mailen voor](../b2b/one-time-passcode.md#opting-in-to-the-preview)meer informatie.
- Zie [Externe samenwerking inschakelen en beheren wie gasten kan uitnodigen](../b2b/delegate-invitations.md)voor meer informatie over externe samenwerkingsinstellingen voor Azure AD B2B.

    ![Instellingen voor externe samenwerking in Azure AD](./media/entitlement-management-external-users/collaboration-settings.png)

### <a name="review-your-conditional-access-policies"></a>Uw beleid voor voorwaardelijke toegang controleren

- Zorg ervoor dat gasten worden uitgesloten van een beleid voor voorwaardelijke toegang waaraan nieuwe gastgebruikers niet kunnen voldoen, omdat ze hierdoor niet kunnen inloggen op uw directory. Gasten hebben bijvoorbeeld waarschijnlijk geen geregistreerd apparaat, bevinden zich niet op een bekende locatie en willen zich niet opnieuw registreren voor multi-factor authenticatie (MFA), dus als u deze vereisten toevoegt aan een beleid voor voorwaardelijke toegang, wordt ervoor geblokkeerd dat gasten geen recht meer kunnen gebruiken Management. Zie [Wat zijn voorwaarden in voorwaardelijke toegang voor Azure Active Directory?](../conditional-access/concept-conditional-access-conditions.md).

    ![Instellingen voor voorwaardelijke toegang voor Azure AD sluiten instellingen uit](./media/entitlement-management-external-users/conditional-access-exclude.png)

### <a name="review-your-sharepoint-online-external-sharing-settings"></a>Uw instellingen voor extern delen van SharePoint Online controleren

- Als u SharePoint Online-sites wilt opnemen in uw toegangspakketten voor externe gebruikers, moet u ervoor zorgen dat de instelling voor extern delen op organisatieniveau is ingesteld op **Iedereen** (gebruikers hoeven zich niet aan te melden) of **Nieuwe en bestaande gasten** (gasten moeten zich aanmelden of een verificatiecode opgeven). Zie [Extern delen in- of uitschakelen](https://docs.microsoft.com/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting)voor meer informatie.

- Als u extern delen buiten het beheer van de rechten wilt beperken, u de instelling voor extern delen instellen op **bestaande gasten.** Vervolgens kunnen alleen nieuwe gebruikers die worden uitgenodigd via het beheer van rechten toegang krijgen tot deze sites. Zie [Extern delen in- of uitschakelen](https://docs.microsoft.com/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting)voor meer informatie.

- Zorg ervoor dat de instellingen op siteniveau gasttoegang inschakelen (dezelfde optieselecties als eerder vermeld). Zie [Extern delen in- of uitschakelen voor een site voor](https://docs.microsoft.com/sharepoint/change-external-sharing-site)meer informatie.

### <a name="review-your-office-365-group-sharing-settings"></a>Uw instellingen voor het delen van Office 365-groepen controleren

- Als u Office 365-groepen wilt opnemen in uw toegangspakketten voor externe gebruikers, controleert u of de **gebruikers nieuwe gasten aan de organisatie kunnen toevoegen,** is ingesteld op **Aan** om gasttoegang toe te staan. Zie [Gasttoegang tot Office 365-groepen beheren](https://docs.microsoft.com/office365/admin/create-groups/manage-guest-access-in-groups?view=o365-worldwide#manage-groups-guest-access)voor meer informatie .

- Als u wilt dat externe gebruikers toegang hebben tot de SharePoint Online-site en -bronnen die zijn gekoppeld aan een Office 365-groep, moet u het externe delen van SharePoint Online inschakelen. Zie [Extern delen in- of uitschakelen](https://docs.microsoft.com/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting)voor meer informatie.

- Zie [Voorbeeld: Gastbeleid configureren voor groepen op directoryniveau voor](../users-groups-roles/groups-settings-cmdlets.md#example-configure-guest-policy-for-groups-at-the-directory-level)informatie over het instellen van het gastbeleid voor Office 365-groepen op directoryniveau.

### <a name="review-your-teams-sharing-settings"></a>Uw instellingen voor het delen van Teams controleren

- Als u Teams wilt opnemen in uw toegangspakketten voor externe gebruikers, controleert u of de **gasttoegang in Microsoft Teams** is ingesteld op **Aan** om gasttoegang toe te staan. Zie [Gasttoegang configureren in het Microsoft Teams-beheercentrum](/microsoftteams/set-up-guests#configure-guest-access-in-the-teams-admin-center)voor meer informatie.

## <a name="manage-the-lifecycle-of-external-users"></a>De levenscyclus van externe gebruikers beheren

U selecteren wat er gebeurt wanneer een externe gebruiker, die is uitgenodigd voor uw directory via een aanvraag voor toegangspakketten, geen toewijzingen voor toegangspakketten meer heeft. Dit kan gebeuren als de gebruiker al zijn toegangspakkettoewijzingen opgeeft of als de laatste toewijzing van het toegangspakket verloopt. Wanneer een externe gebruiker geen toegangspakkettoewijzingen meer heeft, worden deze standaard geblokkeerd om zich aan te melden bij uw directory. Na 30 dagen wordt hun gastgebruikersaccount uit uw directory verwijderd.

**Vereiste rol:** Globale beheerder of gebruikersbeheerder

1. Klik in de Azure-portal op **Azure Active Directory** en klik vervolgens op **Identiteitsbeheer**.

1. Klik in het linkermenu in de sectie **Beheer van rechten** op **Instellingen**.

1. Klik op **Bewerken**.

    ![Instellingen voor het beheren van de levenscyclus van externe gebruikers](./media/entitlement-management-external-users/settings-external-users.png)

1. Selecteer **in de** sectie De levenscyclus van externe gebruikers beheren de verschillende instellingen voor externe gebruikers.

1. Zodra een externe gebruiker zijn laatste toewijzing aan toegangspakketten verliest, als u wilt voorkomen dat deze persoon zich aanmeldt bij deze map, stelt u de **externe gebruiker Blokkeren in om zich aan te melden bij deze map** op **Ja**.

    > [!NOTE]
    > Als een gebruiker is geblokkeerd om zich aan te melden bij deze map, kan de gebruiker het toegangspakket niet opnieuw aanvragen of om extra toegang aanvragen in deze map. Configureer niet dat ze worden geblokkeerd als ze vervolgens toegang moeten vragen tot andere toegangspakketten.

1. Als een externe gebruiker zijn laatste toewijzing aan toegangspakketten verliest, als u zijn gastgebruikersaccount in deze directory wilt verwijderen, stelt u **Externe gebruiker verwijderen** in op **Ja**.

    > [!NOTE]
    > Het beheer van rechten verwijdert alleen accounts die zijn uitgenodigd via het beheer van rechten. Houd er ook rekening mee dat een gebruiker wordt geblokkeerd voor het aanmelden en verwijderd uit deze directory, zelfs als die gebruiker is toegevoegd aan bronnen in deze directory die geen toegang hebben tot pakkettoewijzingen. Als de gast in deze directory aanwezig was voordat hij toegangspakkettoewijzingen ontving, blijven deze gast aanwezig. Als de gast echter is uitgenodigd via een toewijzing voor toegangspakketten en na uitnodiging ook is toegewezen aan een OneDrive voor Bedrijven- of SharePoint Online-site, worden deze nog steeds verwijderd.

1. Als u het gastgebruikersaccount in deze map wilt verwijderen, u het aantal dagen instellen voordat het wordt verwijderd. Als u het gastgebruikersaccount wilt verwijderen zodra deze hun laatste toewijzing aan toegangspakketten verliest, stelt u **het aantal dagen in voordat u de externe gebruiker uit deze map verwijdert** op **0**.

1. Klik op **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

- [Een verbonden organisatie toevoegen](entitlement-management-organization.md)
- [Voor gebruikers die niet in uw map staan](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
- [Oplossen](entitlement-management-troubleshoot.md)
