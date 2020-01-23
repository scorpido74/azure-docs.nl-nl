---
title: Een verbonden organisatie toevoegen in het beheer van rechten van Azure AD-Azure Active Directory
description: Meer informatie over het toestaan van personen buiten uw organisatie om toegangs pakketten aan te vragen, zodat u kunt samen werken aan projecten.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 01/22/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c1b6f5ebffa39d3b735e85df794e37329e3aa2e
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548897"
---
# <a name="add-a-connected-organization-in-azure-ad-entitlement-management"></a>Een verbonden organisatie toevoegen in het beheer van rechten van Azure AD

Met het beheer van rechten van Azure AD kunt u samen werken met mensen buiten uw organisatie. Als u regel matig samenwerkt met gebruikers in een extern Azure AD-adres lijst of-domein, kunt u ze als een verbonden organisatie toevoegen. In dit artikel wordt beschreven hoe u een verbonden organisatie toevoegt zodat u gebruikers buiten uw organisatie in staat kunt stellen om resources in uw directory aan te vragen.

## <a name="what-is-a-connected-organization"></a>Wat is een verbonden organisatie?

Een verbonden organisatie is een extern Azure AD-adres lijst of-domein waarmee u een relatie hebt.

Stel bijvoorbeeld dat u werkt met de Woodgrove Bank en u wilt samen werken met twee externe organisaties. Deze twee organisaties hebben verschillende configuraties:

- Grafisch ontwerp Institute maakt gebruik van Azure AD en hun gebruikers hebben een user principal name dat eindigt op `graphicdesigninstitute.com`
- Contoso gebruikt nog geen Azure AD. Contoso-gebruikers hebben een user principal name die eindigt op `contoso.com`.

In dit geval kunt u twee verbonden organisaties configureren. U maakt één verbonden organisatie voor grafisch ontwerp Institute en één voor contoso. Als u deze twee verbonden organisaties vervolgens aan een beleid toevoegt, kunnen gebruikers van elke organisatie met een user principal name die overeenkomt met het beleid, toegangs pakketten aanvragen. Gebruikers met een user principal name met een domein graphicdesigninstitute.com, moeten overeenkomen met het grafisch ontwerp Institute dat is verbonden met een organisatie en die aanvragen mag verzenden, terwijl gebruikers met een user principal name met een domein van contoso.com overeenkomen de verbonden contoso-organisatie en is ook toegestaan om pakketten aan te vragen. Omdat het grafische ontwerp Institute gebruikmaakt van Azure AD, worden gebruikers met een principal-naam die overeenkomt met een [geverifieerd domein](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) toegevoegd aan hun Tenant, zoals graphicdesigninstitute. voor beeld kan ook toegangs pakketten aanvragen met hetzelfde beleid.

![Voor beeld van verbonden organisatie](./media/entitlement-management-organization/connected-organization-example.png)

Hoe gebruikers van de Azure AD-Directory of het domein worden geverifieerd, is afhankelijk van het verificatie type. De verificatie typen voor verbonden organisaties zijn als volgt:

- Azure AD
- [Directe Federatie](../b2b/direct-federation.md)
- [Eenmalige wachtwoord code](../b2b/one-time-passcode.md) (domein)

Bekijk de volgende video voor een demonstratie van het toevoegen van een verbonden organisatie:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4dskS]

## <a name="add-a-connected-organization"></a>Een verbonden organisatie toevoegen

Volg deze stappen om een extern Azure AD-Directory of-domein toe te voegen als verbonden organisatie.

**Vereiste rol:** Globale beheerder, gebruikers beheerder of gast uitnodiging

1. Klik in de Azure Portal op **Azure Active Directory** en klik vervolgens op **Identity governance**.

1. Klik in het linkermenu op **verbonden organisaties** en klik vervolgens op **verbonden organisatie toevoegen**.

    ![Identiteits bestuur-verbonden organisaties-verbonden organisatie toevoegen](./media/entitlement-management-organization/connected-organization.png)

1. Voer op het tabblad **basis beginselen** een weergave naam en een beschrijving in voor de organisatie.

    ![Tabblad verbonden organisatie toevoegen-basis beginselen](./media/entitlement-management-organization/organization-basics.png)

1. Klik op het tabblad **Directory + domein** op **map toevoegen en domein** om het deel venster mappen en domeinen selecteren te openen.

1. Typ een domein naam om te zoeken naar de Azure AD-Directory of het domein. U moet de volledige domein naam opgeven.

1. Controleer of het de juiste organisatie is op basis van de naam en het verificatie type. Hoe gebruikers zich aanmelden, is afhankelijk van het verificatie type.

    ![Verbonden organisatie toevoegen-mappen en domeinen selecteren](./media/entitlement-management-organization/organization-select-directories-domains.png)

1. Klik op **toevoegen** om de Azure AD-Directory of het domein toe te voegen. Op dit moment kunt u slechts één Azure AD-adres lijst of-domein toevoegen per verbonden organisatie.

    > [!NOTE]
    > Alle gebruikers uit de Azure AD-adres lijst of het-domein kunnen dit toegangs pakket aanvragen. Dit omvat gebruikers in azure AD van alle subdomeinen die zijn gekoppeld aan de Directory, tenzij deze domeinen worden geblokkeerd door de Azure B2B-lijst voor toestaan of weigeren. Zie voor meer informatie, [uitnodigingen toestaan of blokkeren voor B2B-gebruikers van bepaalde organisaties](../b2b/allow-deny-list.md).

1. Wanneer u de Azure AD-Directory of het domein hebt toegevoegd, klikt u op **selecteren**.

    De organisatie wordt weer gegeven in de lijst.

    ![Tabblad verbonden organisatie toevoegen-mappen](./media/entitlement-management-organization/organization-directory-domain.png)

1. Voeg op het tabblad **sponsors** optionele sponsors toe voor deze verbonden organisatie.

    Sponsors zijn interne of externe gebruikers in uw directory die het contact punt vormen voor de relatie met deze verbonden organisatie. Interne sponsors zijn lid van gebruikers in uw Directory. Externe sponsors zijn gast gebruikers van de verbonden organisatie die eerder zijn uitgenodigd en zich al in uw directory bevinden. Sponsors kunnen worden gebruikt als goed keurders wanneer gebruikers in deze verbonden organisatie toegang aanvragen tot dit toegangs pakket. Zie [Azure Active Directory B2B-samenwerkings gebruikers toevoegen in de Azure Portal](../b2b/add-users-administrator.md)voor informatie over het uitnodigen van een gast gebruiker aan uw Directory.

    Wanneer u op **toevoegen/verwijderen**klikt, wordt er een deel venster weer gegeven waarin u de interne of externe sponsors kunt selecteren. In het deel venster wordt een niet-gefilterde lijst met gebruikers en groepen in uw directory weer gegeven.

    ![Toegangs pakket-beleid-verbonden organisatie toevoegen-tabblad sponsors](./media/entitlement-management-organization/organization-sponsors.png)

1. Controleer op het tabblad **controleren en maken** de instellingen van uw organisatie en klik vervolgens op **maken**.

    ![Toegangs pakket-beleid-verbonden organisatie toevoegen-tabblad controleren + maken](./media/entitlement-management-organization/organization-review-create.png)

## <a name="delete-a-connected-organization"></a>Een verbonden organisatie verwijderen

Als u geen relatie meer hebt met een extern Azure AD-adres lijst of-domein, kunt u de verbonden organisatie verwijderen.

**Vereiste rol:** Globale beheerder, gebruikers beheerder of gast uitnodiging

1. Klik in de Azure Portal op **Azure Active Directory** en klik vervolgens op **Identity governance**.

1. Klik in het menu links op **verbonden organisaties** en klik vervolgens om de verbonden organisatie te openen.

1. Klik op de pagina overzicht op **verwijderen** om de verbonden organisatie te verwijderen.

    Op dit moment kunt u een verbonden organisatie alleen verwijderen als er geen verbonden gebruikers zijn.

    ![Identiteits bestuur-verbonden organisaties-verbonden organisatie verwijderen](./media/entitlement-management-organization/organization-delete.png)

## <a name="next-steps"></a>Volgende stappen

- [Toegang tot externe gebruikers bepalen](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users)
- [Voor gebruikers die niet in uw Directory voor komt](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
