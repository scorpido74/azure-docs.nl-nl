---
title: Zelfstudie - Toegangspakket maken - Azure AD-rechtenbeheer
description: Stapsgewijze zelfstudie voor het maken van uw eerste toegangspakket in Azure Active Directory-rechtenbeheer.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.subservice: compliance
ms.date: 10/22/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd4feeb83acc3842874e7a2e4bbd32dacabcc00d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75422656"
---
# <a name="tutorial-create-your-first-access-package-in-azure-ad-entitlement-management"></a>Zelfstudie: Uw eerste toegangspakket maken in Azure AD-rechtenbeheer

Het beheren van toegang tot alle resources die werknemers nodig hebben, zoals groepen, toepassingen en sites, is een belangrijke functie voor organisaties. U wilt werknemers het juiste toegangsniveau geven dat ze nodig hebben om productief te zijn en hun toegang te verwijderen wanneer dit niet langer nodig is.

In deze zelfstudie werk je voor Woodgrove Bank als IT-beheerder. U bent gevraagd om een pakket resources te maken voor een marketingcampagne die interne gebruikers kunnen aanvragen voor selfservice. Aanvragen vereisen geen goedkeuring en de toegang van de gebruiker verloopt na 30 dagen. Voor deze zelfstudie zijn de bronnen voor marketingcampagnes slechts lid van één groep, maar het kan een verzameling groepen, toepassingen of SharePoint Online-sites zijn.

![Overzicht van scenario's](./media/entitlement-management-access-package-first/elm-scenario-overview.png)

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een toegangspakket maken met een groep als resource
> * Een gebruiker in uw map toestaan om toegang aan te vragen
> * Laten zien hoe een interne gebruiker het toegangspakket kan aanvragen

Bekijk de volgende video voor een stapsgewijze demonstratie van het implementatieproces voor Azure Active Directory-rechtenbeheer, inclusief het maken van uw eerste toegangspakket:

>[!VIDEO https://www.youtube.com/embed/zaaKvaaYwI4]

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-rechtenbeheer wilt gebruiken, moet u een van de volgende licenties hebben:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5-licentie

Zie [Licentievereisten voor](entitlement-management-overview.md#license-requirements)meer informatie.

## <a name="step-1-set-up-users-and-group"></a>Stap 1: Gebruikers en groep instellen

Een resourcemap heeft een of meer bronnen om te delen. In deze stap maakt u een groep met de naam **Marketingresources** in de map Woodgrove Bank, de doelbron voor beheer van rechten. U stelt ook een interne aanvrager in.

**Vereiste rol:** Globale beheerder of gebruikersbeheerder

![Gebruikers en groepen maken](./media/entitlement-management-access-package-first/elm-users-groups.png)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) als globale beheerder of gebruikersbeheerder.  

1. Klik in de linkernavigatie op **Azure Active Directory**.

1. De volgende twee gebruikers maken of configureren. U deze namen of verschillende namen gebruiken. **Admin1** kan de gebruiker zijn waarop u zich momenteel hebt aangemeld.

    | Name | Maprol |
    | --- | --- |
    | **Beheerder1** | Globale beheerder<br/>-of-<br/>Gebruikersbeheerder |
    | **Verzoeker1** | Gebruiker |

1. Maak een Azure AD-beveiligingsgroep met de naam **Marketingresources** met een **lidmaatschapstype Toegewezen**.

    Deze groep is de doelgroep voor het beheer van rechten. De groep moet leeg zijn van leden om te beginnen.

## <a name="step-2-create-an-access-package"></a>Stap 2: Een toegangspakket maken

Een *toegangspakket* is een bundel van resources die een team of project nodig heeft en wordt beheerd met beleid. Toegangspakketten worden gedefinieerd in containers die *catalogi*worden genoemd. In deze stap maakt u een **marketingcampagnetoegangspakket** in de **algemene** catalogus.

**Vereiste rol:** Globale beheerder, gebruikersbeheerder, cataloguseigenaar of Access-pakketbeheer

![Een toegangspakket maken](./media/entitlement-management-access-package-first/elm-access-package.png)

1. Klik in de Azure-portal in de linkernavigatie op **Azure Active Directory**.

1. Klik in het linkermenu op **Identiteitsbeheer**

1. Klik in het linkermenu op **Toegangspakketten**.  Als u **Access geweigerd**ziet, moet u ervoor zorgen dat er een Azure AD Premium P2-licentie aanwezig is in uw directory.

1. Klik **op Nieuw toegangspakket**.

    ![Rechtenbeheer in de Azure-portal](./media/entitlement-management-shared/access-packages-list.png)

1. Typ op het tabblad **Basisbeginselen** het toegangspakket voor **marketingcampagne** en beschrijving **Toegang tot bronnen voor de campagne**.

1. Laat de vervolgkeuzelijst **Catalogus** instellen op **Algemeen**.

    ![Nieuw toegangspakket - tabblad Basisbeginselen](./media/entitlement-management-access-package-first/basics.png)

1. Klik **op Volgende** om het tabblad **Resourcerollen** te openen.

    Selecteer op dit tabblad de resources en de resourcerol die u in het toegangspakket wilt opnemen.

1. Klik **op Groepen en Teams**.

1. Zoek en selecteer in het deelvenster Groepen selecteren de groep **Marketingresources** die u eerder hebt gemaakt.

    Standaard ziet u groepen binnen en buiten de **algemene** catalogus. Wanneer u een groep buiten **de** algemene catalogus selecteert, wordt deze toegevoegd aan de **algemene** catalogus.

    ![Nieuw toegangspakket - tabblad Resourcerollen](./media/entitlement-management-access-package-first/resource-roles-select-groups.png)

1. Klik **op Selecteren** om de groep aan de lijst toe te voegen.

1. Selecteer **Lid**in de vervolgkeuzelijst **Rol** .

    ![Nieuw toegangspakket - tabblad Resourcerollen](./media/entitlement-management-access-package-first/resource-roles.png)

1. Klik **op Volgende** om het tabblad Aanvragen te **openen.**

    Op dit tabblad maakt u een aanvraagbeleid. Een *beleid* definieert de regels of vangrails om toegang te krijgen tot een toegangspakket. U maakt een beleid waarmee een specifieke gebruiker in de bronmap dit toegangspakket kan aanvragen.

1. Klik **in** de sectie Gebruikers die toegang kunnen aanvragen op Gebruikers in uw **map** en klik vervolgens op Specifieke gebruikers **en groepen**.

    ![Nieuw toegangspakket - tabblad Aanvragen](./media/entitlement-management-access-package-first/requests.png)

1. Klik **op Gebruikers en groepen toevoegen**.

1. Selecteer in het deelvenster Gebruikers en groepen selecteren de **gebruiker Requestor1** die u eerder hebt gemaakt.

    ![Nieuw toegangspakket - Tabblad Aanvragen - Gebruikers en groepen selecteren](./media/entitlement-management-access-package-first/requests-select-users-groups.png)

1. Klik **op Selecteren**.

1. Schuif omlaag naar de secties **Goedkeuring** en **Aanvragen inschakelen.**

1. Goedkeuring **vereisen** instellen op **Nee**.

1. Klik op **Ja** inschakelen **voor**Aanvragen inschakelen om dit toegangspakket te kunnen aanvragen zodra het is gemaakt.

    ![Nieuw toegangspakket - Tabblad Aanvragen - Aanvragen en Aanvragen inschakelen](./media/entitlement-management-access-package-first/requests-approval-enable.png)

1. Klik **op Volgende** om het tabblad **Levenscyclus** te openen.

1. Stel **in** de sectie Verloop de **toewijzingen van toegangspakketten in die verlopen** **in aantal dagen**.

1. Stel **toewijzingen verlopen na** **30** dagen.

    ![Nieuw toegangspakket - tabblad Levenscyclus](./media/entitlement-management-access-package-first/lifecycle.png)

1. Klik **op Volgende** om het tabblad Controleren + Maken **te** openen.

    ![Nieuw toegangspakket - Tabblad Controleren + Maken](./media/entitlement-management-access-package-first/review-create.png)

    Na een paar ogenblikken ziet u een melding dat het toegangspakket is gemaakt.

1. Klik in het linkermenu van het toegangspakket marketingcampagne op **Overzicht**.

1. Kopieer de **koppeling Mijn Access-portal**.

    Je gebruikt deze link voor de volgende stap.

    ![Overzicht van toegangspakketten - Koppeling mijn Access-portal](./media/entitlement-management-shared/my-access-portal-link.png)

## <a name="step-3-request-access"></a>Stap 3: Toegang aanvragen

In deze stap voert u de stappen uit als **interne aanvrager** en vraagt u toegang tot het toegangspakket. Aanvragers dienen hun verzoeken in via een site genaamd de My Access-portal. Met de My Access-portal kunnen aanvragers verzoeken om toegangspakketten indienen, de toegangspakketten bekijken waartoe ze al toegang hebben en hun aanvraaggeschiedenis bekijken.

**Vereiste rol:** Interne aanvrager

1. Meld u af bij de Azure-portal.

1. Navigeer in een nieuw browservenster naar de koppeling Mijn Access-portal die u in de vorige stap hebt gekopieerd.

1. Meld u aan bij de My Access-portal als **Requestor1**.

    U ziet het toegangspakket **voor marketingcampagnes.**

1. Klik indien nodig in de kolom **Beschrijving** op de pijl om details over het toegangspakket weer te geven.

    ![Mijn Access-portal - Toegangspakketten](./media/entitlement-management-shared/my-access-access-packages.png)

1. Klik op het vinkje om het pakket te selecteren.

1. Klik **op Toegang aanvragen** om het deelvenster Toegangs aanvragen te openen.

    ![Knop Mijn toegangsportal - Toegang aanvragen](./media/entitlement-management-access-package-first/my-access-request-access-button.png)

1. Typ in het vak **Zakelijke rechtvaardiging** de rechtvaardiging die ik aan de **nieuwe marketingcampagne werk.**

    ![Mijn Access-portal - Toegang aanvragen](./media/entitlement-management-shared/my-access-request-access.png)

1. Klik **op Verzenden**.

1. Klik in het linkermenu op **Geschiedenis aanvragen** om te controleren of uw aanvraag is ingediend.

## <a name="step-4-validate-that-access-has-been-assigned"></a>Stap 4: Valideren dat de toegang is toegewezen

In deze stap bevestigt u dat de **interne aanvrager** het toegangspakket is toegewezen en dat deze nu lid is van de groep **Marketingresources.**

**Vereiste rol:** Globale beheerder, gebruikersbeheerder, cataloguseigenaar of Access-pakketbeheer

1. Meld u af bij de My Access-portal.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) als **beheerder1**.

1. Klik op **Azure Active Directory** en klik vervolgens op **Identiteitsbeheer**.

1. Klik in het linkermenu op **Toegangspakketten**.

1. Zoek en klik op het toegangspakket **voor marketingcampagnes.**

1. Klik in het linkermenu op **Aanvragen**.

    U ziet Requestor1 en het initiële beleid met de status **Geleverd.**

1. Klik op het verzoek om de aanvraaggegevens te bekijken.

    ![Toegangspakket - Details aanvragen](./media/entitlement-management-access-package-first/request-details.png)

1. Klik in de linkernavigatie op **Azure Active Directory**.

1. Klik **op Groepen** en open de groep **Marketingresources.**

1. Klik **op Leden**.

    U ziet **Requestor1** als lid.

    ![Leden van marketingbronnen](./media/entitlement-management-access-package-first/group-members.png)

## <a name="step-5-clean-up-resources"></a>Stap 5: Resources opschonen

In deze stap verwijdert u de wijzigingen die u hebt aangebracht en verwijdert u het toegangspakket **voor marketingcampagne.**

**Vereiste rol:**  Globale beheerder of gebruikersbeheerder

1. Klik in de Azure-portal op **Azure Active Directory** en klik vervolgens op **Identiteitsbeheer**.

1. Open het toegangspakket **voor marketingcampagnes.**

1. Klik **op Toewijzingen**.

1. Klik **voor Requestor1**op de ellips (**...**) en klik vervolgens op **Toegang verwijderen**. Klik in het bericht dat wordt weergegeven op **Ja**.

    Na enkele ogenblikken verandert de status van Geleverd naar Verlopen.

1. Klik **op Resourcerollen**.

1. Klik voor **Marketingresources**op de ellips (**...**) en klik vervolgens op **Resourcerol verwijderen**. Klik in het bericht dat wordt weergegeven op **Ja**.

1. Open de lijst met toegangspakketten.

1. Klik voor **marketingcampagne**op de ellips (**...**) en klik vervolgens op **Verwijderen**. Klik in het bericht dat wordt weergegeven op **Ja**.

1. Verwijder in Azure Active Directory alle gebruikers die u hebt gemaakt, zoals **Requestor1** en **Admin1.**

1. Verwijder de groep **Marketingbronnen.**

## <a name="next-steps"></a>Volgende stappen

Ga verder naar het volgende artikel voor meer informatie over veelvoorkomende scenariostappen in het beheer van rechten.
> [!div class="nextstepaction"]
> [Algemene scenario's](entitlement-management-scenarios.md)
