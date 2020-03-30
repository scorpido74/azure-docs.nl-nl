---
title: Een verbonden organisatie toevoegen aan Azure AD-rechtenbeheer - Azure Active Directory
description: Meer informatie over hoe u mensen buiten uw organisatie toegang somtotewerk doen, zodat u samenwerken aan projecten.
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
ms.openlocfilehash: ee370bc9c381eb11ae7cae53b31d0c987c52733c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128613"
---
# <a name="add-a-connected-organization-in-azure-ad-entitlement-management"></a>Een verbonden organisatie toevoegen in Azure AD-rechtenbeheer

Met Azure Active Directory (Azure AD)-rechtenbeheer u samenwerken met mensen buiten uw organisatie. Als u vaak samenwerkt met gebruikers in een externe Azure AD-map of -domein, u deze toevoegen als een verbonden organisatie. In dit artikel wordt beschreven hoe u een verbonden organisatie toevoegt, zodat u gebruikers buiten uw organisatie toestaan resources in uw directory op te vragen.

## <a name="what-is-a-connected-organization"></a>Wat is een verbonden organisatie?

Een verbonden organisatie is een externe Azure AD-map of -domein waarmee u een relatie hebt.

Stel dat u bij Woodgrove Bank werkt en dat u wilt samenwerken met twee externe organisaties. Deze twee organisaties hebben verschillende configuraties:

- Graphic Design Institute maakt gebruik van Azure AD en hun gebruikers hebben een gebruikersnaam die eindigt met *graphicdesigninstitute.com*.
- Contoso maakt nog geen gebruik van Azure AD. Contoso-gebruikers hebben een gebruikersnaam die eindigt met *contoso.com.*

In dit geval u twee verbonden organisaties configureren. Je creëert één verbonden organisatie voor Graphic Design Institute en één voor Contoso. Als u vervolgens de twee verbonden organisaties aan een beleid toevoegt, kunnen gebruikers van elke organisatie met een gebruikersnaam die overeenkomt met het beleid toegangspakketten aanvragen. Gebruikers met een gebruikersnaam met een domein van *graphicdesigninstitute.com* komen overeen met de organisatie die verbonden is met het Graphic Design Institute en mogen aanvragen indienen. Gebruikers met een gebruikersnaam met een domein van *contoso.com* zouden overeenkomen met de met Contoso verbonden organisatie en zouden ook pakketten kunnen aanvragen. En omdat Graphic Design Institute Azure AD gebruikt, kunnen gebruikers met een hoofdnaam die overeenkomt met een [geverifieerd domein](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) dat aan hun tenant is toegevoegd, zoals *graphicdesigninstitute.example,* ook toegangspakketten aanvragen met hetzelfde beleid.

![Voorbeeld van verbonden organisatie](./media/entitlement-management-organization/connected-organization-example.png)

Hoe gebruikers uit de Azure AD-map of domeinverificatie afhankelijk zijn van het verificatietype. De verificatietypen voor verbonden organisaties zijn:

- Azure AD
- [Directe federatie](../b2b/direct-federation.md)
- [Eenmalige toegangscode](../b2b/one-time-passcode.md) (domein)

Bekijk de volgende video voor een demonstratie van het toevoegen van een verbonden organisatie:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4dskS]

## <a name="add-a-connected-organization"></a>Een verbonden organisatie toevoegen

Als u een externe Azure AD-map of -domein als verbonden organisatie wilt toevoegen, volgt u de instructies in deze sectie.

**Vereisterol**: *Globale beheerder,* *gebruikersbeheerder*of *gastgenodigdie*

1. Selecteer Azure Active Directory in de **Azure-portal**en selecteer **Vervolgens Identiteitsbeheer**.

1. Selecteer verbonden **organisaties**in het linkerdeelvenster en selecteer **Verbonden organisatie toevoegen**.

    ![De knop 'Verbonden organisatie toevoegen'](./media/entitlement-management-organization/connected-organization.png)

1. Selecteer het tabblad **Basisbeginselen** en voer een weergavenaam en beschrijving in voor de organisatie.

    ![Het deelvenster Basisbeginselen toevoegen](./media/entitlement-management-organization/organization-basics.png)

1. Selecteer het tabblad **Directory + domein** en selecteer Map + domein **toevoegen**.

    Het deelvenster **Mappen selecteren + domeinen** wordt geopend.

1. Voer in het zoekvak een domeinnaam in om te zoeken naar de Azure AD-map of -domein. Zorg ervoor dat u de hele domeinnaam invoert.

1. Controleer of de organisatienaam en het verificatietype correct zijn. Hoe gebruikers zich aanmelden, is afhankelijk van het verificatietype.

    ![Het deelvenster 'Mappen + domeinen selecteren'](./media/entitlement-management-organization/organization-select-directories-domains.png)

1. Selecteer **Toevoegen** om de Azure AD-map of -domein toe te voegen. Momenteel u slechts één Azure AD-map of -domein per verbonden organisatie toevoegen.

    > [!NOTE]
    > Alle gebruikers uit de Azure AD-map of -domein kunnen dit toegangspakket aanvragen. Dit geldt ook voor gebruikers in Azure AD uit alle subdomeinen die aan de map zijn gekoppeld, tenzij deze domeinen worden geblokkeerd door de Lijst Met Azure AD Business to Business (B2B). Zie [Uitnodigingen voor B2B-gebruikers van specifieke organisaties toestaan of blokkeren](../b2b/allow-deny-list.md)voor meer informatie.

1. Nadat u de Azure AD-map of -domein hebt toegevoegd, selecteert u **Selecteren**.

    De organisatie wordt weergegeven in de lijst.

    ![Het deelvenster 'Map + domein'](./media/entitlement-management-organization/organization-directory-domain.png)

1. Selecteer het tabblad **Sponsors** en voeg vervolgens optionele sponsors toe voor deze verbonden organisatie.

    Sponsors zijn interne of externe gebruikers die al in uw directory staan en die het aanspreekpunt zijn voor de relatie met deze verbonden organisatie. Interne sponsors zijn lid gebruikers in uw directory. Externe sponsors zijn gastgebruikers van de verbonden organisatie die eerder zijn uitgenodigd en al in uw directory staan. Sponsors kunnen worden gebruikt als fiatteurs wanneer gebruikers in deze verbonden organisatie toegang vragen tot dit toegangspakket. Zie [Azure Active Directory B2B-samenwerkingsgebruikers toevoegen in de Azure-portal](../b2b/add-users-administrator.md)voor informatie over het uitnodigen van een gastgebruiker voor uw directory.

    Wanneer u **Toevoegen/verwijderen selecteert,** wordt een deelvenster geopend waarin u interne of externe sponsors kiezen. In het deelvenster wordt een ongefilterde lijst met gebruikers en groepen weergegeven in uw map.

    ![Het deelvenster Sponsors](./media/entitlement-management-organization/organization-sponsors.png)

1. Selecteer het tabblad **Controleren + maken,** controleer de instellingen van uw organisatie en selecteer **Vervolgens Maken**.

    ![Het deelvenster 'Controleren + maken'](./media/entitlement-management-organization/organization-review-create.png)

## <a name="update-a-connected-organization"></a>Een verbonden organisatie bijwerken 

Als de verbonden organisatie verandert in een ander domein, de naam van de organisatie verandert of als u de sponsors wilt wijzigen, u de verbonden organisatie bijwerken door de instructies in deze sectie te volgen.

**Vereisterol**: *Globale beheerder,* *gebruikersbeheerder*of *gastgenodigdie*

1. Selecteer Azure Active Directory in de **Azure-portal**en selecteer **Vervolgens Identiteitsbeheer**.

1. Selecteer in het linkerdeelvenster **Verbonden organisaties**en selecteer vervolgens de verbonden organisatie om deze te openen.

1. Selecteer **bewerken** om de naam of beschrijving van de organisatie te wijzigen in het overzichtsvenster van de verbonden organisatie.  

1. Selecteer **directory +** domein **bijwerken** in het deelvenster Map + in het map + domein bijwerken om te wijzigen in een andere map of domein.

1. Selecteer **in** het deelvenster Sponsors de optie **Interne sponsors toevoegen** of Externe sponsors **toevoegen** om een gebruiker als sponsor toe te voegen. Als u een sponsor wilt verwijderen, selecteert u de sponsor en selecteert u **verwijderen**in het rechterdeelvenster .


## <a name="delete-a-connected-organization"></a>Een verbonden organisatie verwijderen

Als u geen relatie meer hebt met een externe Azure AD-map of -domein, u de verbonden organisatie verwijderen.

**Vereisterol**: *Globale beheerder,* *gebruikersbeheerder*of *gastgenodigdie*

1. Selecteer Azure Active Directory in de **Azure-portal**en selecteer **Vervolgens Identiteitsbeheer**.

1. Selecteer in het linkerdeelvenster **Verbonden organisaties**en selecteer vervolgens de verbonden organisatie om deze te openen.

1. Selecteer **Verwijderen** om het te verwijderen in het overzichtsvenster van de verbonden organisatie.

    Momenteel u een verbonden organisatie alleen verwijderen als er geen verbonden gebruikers zijn.

    ![De knop Verwijderen van verbonden organisatie](./media/entitlement-management-organization/organization-delete.png)

## <a name="next-steps"></a>Volgende stappen

- [Toegang voor externe gebruikers beheren](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users)
- [Toegang regelen voor gebruikers die niet in uw directory staan](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
