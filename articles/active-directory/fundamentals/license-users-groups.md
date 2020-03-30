---
title: Licenties toewijzen of verwijderen - Azure Active Directory | Microsoft Documenten
description: Instructies voor het toewijzen of verwijderen van Azure Active Directory-licenties van uw gebruikers of groepen.
services: active-directory
author: msaburnley
manager: daveba
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 20ec1d1909a53a9de29e12be33957acfd1643698
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128826"
---
# <a name="assign-or-remove-licenses-in-the-azure-active-directory-portal"></a>Licenties toewijzen of verwijderen in de Azure Active Directory-portal

Voor veel Azure Active Directory-services (Azure AD) moet u elk van uw gebruikers of groepen (en geassocieerde leden) een licentie geven voor die service. Alleen gebruikers met actieve licenties hebben toegang tot de gelicentieerde Azure AD-services waarvoor dit waar is. Licenties worden per tenant toegepast en worden niet overgedragen aan andere tenants. 

## <a name="available-license-plans"></a>Beschikbare licentieplannen

Er zijn verschillende licentieplannen beschikbaar voor de Azure AD-service, waaronder:

- Azure AD Free

- Azure AD Premium P1

- Azure AD Premium P2

Zie [Welke licentie heb ik nodig](https://azure.microsoft.com/pricing/details/active-directory/)voor specifieke informatie over elk licentieplan en de bijbehorende licentiegegevens?

Niet alle Microsoft-services zijn beschikbaar op alle locaties. Voordat een licentie aan een groep kan worden toegewezen, moet u de **gebruikslocatie** voor alle leden opgeven. U deze waarde instellen in het gebied **Azure Active Directory &gt; Users &gt; Profile &gt; Settings** in Azure AD. Elke gebruiker waarvan de gebruikslocatie niet is opgegeven, neemt de locatie van de Azure AD-organisatie over.

## <a name="view-license-plans-and-plan-details"></a>Licentieplannen en plandetails weergeven

U uw beschikbare serviceplannen bekijken, inclusief de afzonderlijke licenties, de vervaldatums controleren en het aantal beschikbare toewijzingen bekijken.

### <a name="to-find-your-service-plan-and-plan-details"></a>Uw serviceplan en plandetails vinden

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) met een licentiebeheerdersaccount in uw Azure AD-organisatie.

1. Selecteer **Azure Active Directory**en selecteer Vervolgens **Licenties**.

    ![Pagina Licenties, met aantal aangeschafte services en toegewezen licenties](media/license-users-groups/license-details-blade.png)

1. Selecteer de koppeling **Gekocht** om de pagina **Producten** te bekijken en de nummers **Toegewezen,** **Beschikbaar**en **Verlopen binnenkort** voor uw licentieplannen te bekijken.

    ![servicespagina, met servicelicentieplannen en bijbehorende licentiegegevens](media/license-users-groups/license-products-blade-with-products.png)

1. Selecteer een abonnementsnaam om de gelicentieerde gebruikers en groepen te zien.

## <a name="assign-licenses-to-users-or-groups"></a>Licenties toewijzen aan gebruikers of groepen

Zorg ervoor dat iedereen die een gelicentieerde Azure AD-service moet gebruiken, de juiste licentie heeft. U de licentierechten toevoegen aan gebruikers of aan een hele groep.

### <a name="to-assign-a-license-to-a-user"></a>Een licentie toewijzen aan een gebruiker

1. Selecteer **op** de pagina Producten de naam van het licentieplan dat u aan de gebruiker wilt toewijzen.

    ![servicespagina, met gemarkeerd servicelicentieplan](media/license-users-groups/license-products-blade-with-product-highlight.png)

1. Selecteer op de overzichtspagina licentieplan de optie **Toewijzen**.

    ![servicespagina, met gemarkeerde optie Toewijzen](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

1. Selecteer **op** de pagina Toewijzen de optie **Gebruikers en groepen**en zoek en selecteer vervolgens de gebruiker die u de licentie toewijst.

    ![Licentiepagina toewijzen, met gemarkeerde zoek- en selectieopties](media/license-users-groups/assign-license-blade-with-highlight.png)

1. Selecteer **Toewijzingsopties,** zorg ervoor dat u de juiste licentieopties hebt ingeschakeld en selecteer **OK**.

    ![Pagina licentieoptie, met alle opties beschikbaar in het licentieplan](media/license-users-groups/license-option-blade-assignments.png)

    De licentiepagina-updates **toewijzen** om aan te geven dat een gebruiker is geselecteerd en dat de toewijzingen zijn geconfigureerd.

    > [!NOTE]
    > Niet alle Microsoft-services zijn beschikbaar op alle locaties. Voordat een licentie aan een gebruiker kan worden toegewezen, moet u de **gebruikslocatie**opgeven. U deze waarde instellen in het gebied **Azure Active Directory &gt; Users &gt; Profile &gt; Settings** in Azure AD. Elke gebruiker waarvan de gebruikslocatie niet is opgegeven, neemt de locatie van de Azure AD-organisatie over.

1. Selecteer **Toewijzen**.

    De gebruiker wordt toegevoegd aan de lijst met gelicentieerde gebruikers en heeft toegang tot de meegeleverde Azure AD-services.
    > [!NOTE]
    > Licenties kunnen ook rechtstreeks aan een gebruiker worden toegewezen vanaf de **pagina Licenties van** de gebruiker. Als een gebruiker een licentie heeft toegewezen via een groepslidmaatschap en u dezelfde licentie rechtstreeks aan de gebruiker wilt toewijzen, kan dit alleen worden gedaan vanaf de pagina **Producten** die alleen in stap 1 wordt vermeld.

### <a name="to-assign-a-license-to-a-group"></a>Een licentie toewijzen aan een groep

1. Selecteer **op** de pagina Producten de naam van het licentieplan dat u aan de gebruiker wilt toewijzen.

    ![Producten blad, met gemarkeerde product licentie plan](media/license-users-groups/license-products-blade-with-product-highlight.png)

1. Selecteer op de pagina **Azure Active Directory Premium Plan 2** de optie **Toewijzen**.

    ![Pagina Producten, met gemarkeerde optie Toewijzen](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

1. Selecteer **op** de pagina Toewijzen de optie **Gebruikers en groepen**en zoek en selecteer vervolgens de groep die u de licentie toewijst.

    ![Licentiepagina toewijzen, met gemarkeerde zoek- en selectieopties](media/license-users-groups/assign-group-license-blade-with-highlight.png)

1. Selecteer **Toewijzingsopties,** zorg ervoor dat u de juiste licentieopties hebt ingeschakeld en selecteer **OK**.

    ![Pagina licentieoptie, met alle opties beschikbaar in het licentieplan](media/license-users-groups/license-option-blade-group-assignments.png)

    De licentiepagina-updates **toewijzen** om aan te geven dat een gebruiker is geselecteerd en dat de toewijzingen zijn geconfigureerd.

1. Selecteer **Toewijzen**.

    De groep wordt toegevoegd aan de lijst met gelicentieerde groepen en alle leden hebben toegang tot de meegeleverde Azure AD-services.

## <a name="remove-a-license"></a>Een licentie verwijderen

U een licentie verwijderen van de Azure AD-gebruikerspagina van een gebruiker, van de pagina groepsoverzicht voor een groepstoewijzing of vanaf de pagina Azure **AD-licenties** om de gebruikers en groepen voor een licentie te bekijken.

### <a name="to-remove-a-license-from-a-user"></a>Een licentie van een gebruiker verwijderen

1. Selecteer op de pagina **Gelicentieerde gebruikers** voor het serviceplan de gebruiker die de licentie niet meer moet hebben. Bijvoorbeeld, _Alain Charon_.

1. Selecteer **Licentie verwijderen**.

    ![Pagina gelicentieerde gebruikers met licentieoptie verwijderen gemarkeerd](media/license-users-groups/license-products-user-blade-with-remove-option-highlight.png)

> [!IMPORTANT]
> Licenties die een gebruiker overneemt van een groep, kunnen niet rechtstreeks worden verwijderd. In plaats daarvan moet u de gebruiker verwijderen uit de groep waaruit hij de licentie erft.

### <a name="to-remove-a-license-from-a-group"></a>Een licentie uit een groep verwijderen

1. Selecteer op de pagina **Licentiegroepen** voor het licentieplan de groep die de licentie niet meer zou moeten hebben.

1. Selecteer **Licentie verwijderen**.

    ![Pagina Gelicentieerde groepen met de optie Licentie verwijderen gemarkeerd](media/license-users-groups/license-products-group-blade-with-remove-option-highlight.png)
    
    > [!NOTE]
    > Wanneer een on-premises gebruikersaccount dat is gesynchroniseerd met Azure AD buiten het bereik voor de synchronisatie valt of wanneer de synchronisatie wordt verwijderd, wordt de gebruiker zacht verwijderd in Azure AD. Wanneer dit gebeurt, worden licenties die rechtstreeks of via groepslicenties aan de gebruiker zijn toegewezen, gemarkeerd als **opgeschort** in plaats van **verwijderd.**

## <a name="next-steps"></a>Volgende stappen

Nadat u uw licenties hebt toegewezen, u de volgende processen uitvoeren:

- [Problemen met licentietoewijzing identificeren en oplossen](../users-groups-roles/licensing-groups-resolve-problems.md)

- [Gelicentieerde gebruikers toevoegen aan een groep voor licenties](../users-groups-roles/licensing-groups-migrate-users.md)

- [Scenario's, beperkingen en bekende problemen met groepen om licenties te beheren in Azure Active Directory](../users-groups-roles/licensing-group-advanced.md)

- [Profielgegevens toevoegen of wijzigen](active-directory-users-profile-azure-portal.md)
