---
title: Wijs toe of verwijder licenties - Azure Active Directory | Microsoft Docs
description: Instructies over het toewijzen of verwijderen van licenties voor Azure Active Directory van uw gebruikers of groepen.
services: active-directory
author: msaburnley
manager: daveba
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 869713967a6a89611ab2ea9ca7b7516f090a5f92
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72034251"
---
# <a name="assign-or-remove-licenses-in-the-azure-active-directory-portal"></a>Licenties toewijzen of verwijderen in de Azure Active Directory Portal

Voor veel Azure Active Directory-Services (Azure AD) moet u een licentie voor elk van uw gebruikers of groepen (en de bijbehorende leden) voor die service. Alleen gebruikers met actieve licenties kunnen de gelicentieerde Azure AD-Services openen en gebruiken waarvoor dat wel het geval is.

## <a name="available-license-plans"></a>Beschik bare licentie plannen

Er zijn verschillende licentie abonnementen beschikbaar voor de Azure AD-service, waaronder:

- Azure AD Free

- Azure AD Premium P1

- Azure AD Premium P2

Zie [welke licentie heb ik nodig](https://azure.microsoft.com/pricing/details/active-directory/)voor specifieke informatie over elk licentie plan en de bijbehorende licentie gegevens?.

Niet alle Microsoft-services zijn beschikbaar in alle locaties. Voordat een licentie kan worden toegewezen aan een groep, moet u de **gebruikslocatie** voor alle leden. U kunt deze waarde instellen in de **Azure Active Directory &gt; gebruikers &gt; profiel &gt; instellingen** gebied in Azure AD. Alle gebruikers waarvan de gebruiks locatie niet is opgegeven, nemen de locatie van de Azure AD-organisatie over.

## <a name="view-license-plans-and-plan-details"></a>Licentie plannen en plan gegevens weer geven

U kunt uw beschik bare service abonnementen, inclusief de afzonderlijke licenties, bekijken, verval datums in behandeling controleren en het aantal beschik bare toewijzingen weer geven.

### <a name="to-find-your-service-plan-and-plan-details"></a>Uw service plan en plan details vinden

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) met een licentie beheerders account in uw Azure AD-organisatie.

1. Selecteer **Azure Active Directory**, en selecteer vervolgens **licenties**.

    ![De pagina licenties, met het aantal aangeschafte Services en toegewezen licenties](media/license-users-groups/license-details-blade.png)

1. Selecteer de **aangeschafte** koppeling om de pagina **producten** weer te geven en om de **toegewezen**, **beschik bare**en **verlopende binnenkort** cijfers voor uw licentie plannen te bekijken.

    ![Services-pagina, met Service licentie plannen en bijbehorende licentie gegevens](media/license-users-groups/license-products-blade-with-products.png)

1. Selecteer een naam voor het abonnement om de gelicentieerde gebruikers en groepen weer te geven.

## <a name="assign-licenses-to-users-or-groups"></a>Licenties toewijzen aan gebruikers of groepen

Zorg ervoor dat iedereen die gebruik van een gelicentieerde Azure AD-service de juiste licentie heeft. U kunt de licentie rechten toevoegen aan gebruikers of aan een volledige groep.

### <a name="to-assign-a-license-to-a-user"></a>Een licentie toewijzen aan een gebruiker

1. Selecteer op de pagina **producten** de naam van het licentie plan dat u wilt toewijzen aan de gebruiker.

    ![pagina Services, met gemarkeerd service licentie plan](media/license-users-groups/license-products-blade-with-product-highlight.png)

1. Selecteer op de pagina overzicht licentie plan de optie **toewijzen**.

    ![pagina Services, met gemarkeerde toewijzings optie](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

1. Op de **toewijzen** weergeeft, schakelt **gebruikers en groepen**, en vervolgens zoekt en selecteert u de gebruiker die u bij het toewijzen van de licentie.

    ![Licentie-pagina met Selecteer opties voor gemarkeerde zoeken en toewijzen](media/license-users-groups/assign-license-blade-with-highlight.png)

1. Selecteer **toewijzingsopties**, zorg ervoor dat u hebt de juiste licentie-opties die zijn ingeschakeld en selecteer vervolgens **OK**.

    ![Pagina licentie opties, met alle beschik bare opties in het licentie plan](media/license-users-groups/license-option-blade-assignments.png)

    De **licentie toewijzen** pagina updates om weer te geven dat een gebruiker is geselecteerd en of de toewijzingen zijn geconfigureerd.

    > [!NOTE]
    > Niet alle Microsoft-services zijn beschikbaar in alle locaties. Voordat een licentie kan worden toegewezen aan een gebruiker, moet u de **gebruikslocatie**. U kunt deze waarde instellen in de **Azure Active Directory &gt; gebruikers &gt; profiel &gt; instellingen** gebied in Azure AD. Alle gebruikers waarvan de gebruiks locatie niet is opgegeven, nemen de locatie van de Azure AD-organisatie over.

1. Selecteer **Toewijzen**.

    De gebruiker wordt toegevoegd aan de lijst met gebruikers met een licentie en toegang heeft tot de Azure AD-services.

### <a name="to-assign-a-license-to-a-group"></a>Een licentie toewijzen aan een groep

1. Selecteer op de pagina **producten** de naam van het licentie plan dat u wilt toewijzen aan de gebruiker.

    ![Blade producten, met gemarkeerd product licentie plan](media/license-users-groups/license-products-blade-with-product-highlight.png)

1. Op de **Azure Active Directory Premium Plan 2** weergeeft, schakelt **toewijzen**.

    ![Pagina van de producten, met de optie voor gemarkeerde toewijzen](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

1. Op de **toewijzen** weergeeft, schakelt **gebruikers en groepen**, en vervolgens zoekt en selecteert u de groep die u bij het toewijzen van de licentie.

    ![Licentie-pagina met Selecteer opties voor gemarkeerde zoeken en toewijzen](media/license-users-groups/assign-group-license-blade-with-highlight.png)

1. Selecteer **toewijzingsopties**, zorg ervoor dat u hebt de juiste licentie-opties die zijn ingeschakeld en selecteer vervolgens **OK**.

    ![Pagina licentie opties, met alle beschik bare opties in het licentie plan](media/license-users-groups/license-option-blade-group-assignments.png)

    De **licentie toewijzen** pagina updates om weer te geven dat een gebruiker is geselecteerd en of de toewijzingen zijn geconfigureerd.

1. Selecteer **Toewijzen**.

    De groep wordt toegevoegd aan de lijst met groepen met licenties en alle leden van de hebben toegang tot de Azure AD-services.

## <a name="remove-a-license"></a>Een licentie verwijderen

U kunt een licentie verwijderen van de Azure AD-gebruikers pagina van een gebruiker, via de groeps overzichts pagina voor een groeps toewijzing, of vanaf de Azure AD- **licenties** pagina om de gebruikers en groepen voor een licentie weer te geven.

### <a name="to-remove-a-license-from-a-user"></a>Een licentie van een gebruiker verwijderen

1. Selecteer op de pagina **gelicentieerde gebruikers** voor het service abonnement de gebruiker die de licentie niet meer mag hebben. Bijvoorbeeld, _Alain Charon_.

1. Selecteer **verwijderen licentie**.

    ![Gebruikers met een licentie-pagina met de optie licentie verwijderen gemarkeerd](media/license-users-groups/license-products-user-blade-with-remove-option-highlight.png)

> [!IMPORTANT]
> Licenties die een gebruiker heeft overgenomen van een groep, kunnen niet rechtstreeks worden verwijderd. In plaats daarvan moet u de gebruiker te verwijderen uit de groep waarvan ze de licentie bent overnemen.

### <a name="to-remove-a-license-from-a-group"></a>Een licentie van een groep verwijderen

1. Selecteer op de pagina **gelicentieerde groepen** voor het licentie plan de groep die niet langer de licentie mag hebben.

1. Selecteer **verwijderen licentie**.

    ![Groepen met licenties pagina met de optie licentie verwijderen gemarkeerd](media/license-users-groups/license-products-group-blade-with-remove-option-highlight.png)

## <a name="next-steps"></a>Volgende stappen

Nadat u uw licenties hebt toegewezen, kunt u de volgende processen uitvoeren:

- [Identificeren en oplossen van problemen toewijzing](../users-groups-roles/licensing-groups-resolve-problems.md)

- [Gelicentieerde gebruikers toevoegen aan een groep voor licentieverlening](../users-groups-roles/licensing-groups-migrate-users.md)

- [Scenario's, beperkingen en bekende problemen met behulp van groepen beheren in Azure Active Directory-licentieverlening](../users-groups-roles/licensing-group-advanced.md)

- [Toevoegen of wijzigen van de profielgegevens](active-directory-users-profile-azure-portal.md)
