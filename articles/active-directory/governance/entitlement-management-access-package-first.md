---
title: 'Zelf studie: uw eerste toegangs pakket maken in het beheer van rechten van Azure AD-Azure Active Directory'
description: Stapsgewijze zelf studie voor het maken van uw eerste toegangs pakket in Azure Active Directory rechten beheer.
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
ms.openlocfilehash: de4d4a1825149a512d7abdb192d8fb9d49e85a20
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73174871"
---
# <a name="tutorial-create-your-first-access-package-in-azure-ad-entitlement-management"></a>Zelf studie: uw eerste toegangs pakket maken in het beheer van rechten van Azure AD

Het beheren van de toegang tot alle resources die werk nemers nodig hebben, zoals groepen, toepassingen en sites, is een belang rijke functie voor organisaties. U wilt werk nemers het juiste toegangs niveau geven om productief te zijn en hun toegang te verwijderen wanneer deze niet meer nodig is.

In deze zelf studie werkt u voor de Woodgrove Bank als IT-beheerder. U wordt gevraagd om een pakket met resources te maken voor een marketing campagne waarvoor interne gebruikers een self-service aanvraag kunnen indienen. Aanvragen vereisen geen goed keuring en de gebruikers toegang verloopt na 30 dagen. Voor deze zelf studie zijn de resources van de marketing campagne slechts lid van één groep, maar dit kan een verzameling van groepen, toepassingen of share point online-sites zijn.

![Overzicht van scenario's](./media/entitlement-management-access-package-first/elm-scenario-overview.png)

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een toegangs pakket maken met een groep als een resource
> * Een gebruiker in uw Directory toestaan om toegang aan te vragen
> * Laat zien hoe een interne gebruiker het toegangs pakket kan aanvragen

Bekijk de volgende video voor een stapsgewijze demonstratie van het proces voor het implementeren van Azure Active Directory rechten beheer, inclusief het maken van uw eerste toegangs pakket:

>[!VIDEO https://www.youtube.com/embed/zaaKvaaYwI4]

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-beheer wilt gebruiken, moet u over een van de volgende licenties beschikken:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5-licentie

Zie [licentie vereisten](entitlement-management-overview.md#license-requirements)voor meer informatie.

## <a name="step-1-set-up-users-and-group"></a>Stap 1: gebruikers en groepen instellen

Een resource directory bevat een of meer resources om te delen. In deze stap maakt u een groep met de naam **Marketing Resources** in de map Woodgrove Bank die de doel resource is voor het beheer van rechten. U hebt ook een interne aanvrager ingesteld.

**Vereiste rol:** Globale beheerder of gebruikers beheerder

![Gebruikers en groepen maken](./media/entitlement-management-access-package-first/elm-users-groups.png)

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als globale beheerder of gebruikers beheerder.  

1. Klik in de linkernavigatiebalk op **Azure Active Directory**.

1. De volgende twee gebruikers maken of configureren. U kunt deze namen of andere namen gebruiken. **Admin1** kan de gebruiker zijn met wie u momenteel bent aangemeld.

    | Naam | Directory-functie |
    | --- | --- |
    | **Admin1** | Globale beheerder<br/>-of-<br/>Gebruikers beheerder |
    | **Requestor1** | Gebruiker |

1. Maak een Azure AD-beveiligings groep met de naam **Marketing Resources** met het lidmaatschaps type **toegewezen**.

    Deze groep wordt de doel resource voor het beheer van rechten. De groep moet leeg zijn om te starten.

## <a name="step-2-create-an-access-package"></a>Stap 2: een toegangs pakket maken

Een *toegangs pakket* is een bundel van resources die een team of project nodig heeft en die wordt beheerd door beleid. Toegangs pakketten worden gedefinieerd in containers die *catalogi*worden genoemd. In deze stap maakt u een toegangs pakket voor een **marketing campagne** in de **algemene** catalogus.

**Vereiste rol:** Globale beheerder, gebruikers beheerder, catalogus eigenaar of toegangs pakket beheer

![Een toegangs pakket maken](./media/entitlement-management-access-package-first/elm-access-package.png)

1. Klik in de Azure Portal in het navigatie venster links op **Azure Active Directory**.

1. Klik in het linkermenu op **Identity governance**

1. Klik in het linkermenu op **toegangs pakketten**.  Als u de **toegang geweigerd**ziet, controleert u of er een Azure AD Premium P2-licentie aanwezig is in uw Directory.

1. Klik op **nieuw toegangs pakket**.

    ![Het beheer van rechten in het Azure Portal](./media/entitlement-management-shared/access-packages-list.png)

1. Typ op het tabblad **basis beginselen** het toegangs pakket naam **marketing campagne** en beschrijving **toegang tot resources voor de campagne**.

1. Laat de vervolg keuzelijst **catalogus** ingesteld op **Algemeen**.

    ![Nieuw toegangs pakket-tabblad basis](./media/entitlement-management-access-package-first/basics.png)

1. Klik op **volgende** om het tabblad **resource rollen** te openen.

    Op dit tabblad selecteert u de resources en de resource functie die u wilt toevoegen in het toegangs pakket.

1. Klik op **groepen en teams**.

1. Zoek en selecteer in het deel venster groepen selecteren de groep **Marketing Resources** die u eerder hebt gemaakt.

    Standaard ziet u groepen binnen en buiten de **algemene** catalogus. Wanneer u een groep buiten de **algemene** catalogus selecteert, wordt deze toegevoegd aan de **algemene** catalogus.

    ![Nieuw toegangs pakket-tabblad Resource rollen](./media/entitlement-management-access-package-first/resource-roles-select-groups.png)

1. Klik op **selecteren** om de groep toe te voegen aan de lijst.

1. Selecteer **lid**in de vervolg keuzelijst **rol** .

    ![Nieuw toegangs pakket-tabblad Resource rollen](./media/entitlement-management-access-package-first/resource-roles.png)

1. Klik op **volgende** om het tabblad **aanvragen** te openen.

    Op dit tabblad maakt u een aanvraag beleid. Een *beleid* definieert de regels of Guardrails voor toegang tot een toegangs pakket. U maakt een beleid waarmee een specifieke gebruiker in de resource directory dit toegangs pakket kan aanvragen.

1. Klik in de sectie **gebruikers die toegang kunnen aanvragen** , op **voor gebruikers in uw directory** en klik vervolgens op **specifieke gebruikers en groepen**.

    ![Nieuw toegangs pakket-tabblad aanvragen](./media/entitlement-management-access-package-first/requests.png)

1. Klik op **gebruikers en groepen toevoegen**.

1. Selecteer in het deel venster gebruikers en groepen selecteren de **Requestor1** -gebruiker die u eerder hebt gemaakt.

    ![Nieuw toegangs pakket-tabblad aanvragen-gebruikers en groepen selecteren](./media/entitlement-management-access-package-first/requests-select-users-groups.png)

1. Klik op **Selecteren**.

1. Schuif omlaag naar de secties **goed keuring** en **aanvragen inschakelen** .

1. Verlof **moet goed keuring** zijn ingesteld op **Nee**.

1. Voor **Schakel aanvragen**klikt u op **Ja** om dit toegangs pakket in te scha kelen zodra het is gemaakt.

    ![Nieuw toegangs pakket-tabblad aanvragen-goed keuring en aanvragen inschakelen](./media/entitlement-management-access-package-first/requests-approval-enable.png)

1. Klik op **volgende** om het tabblad **levenscyclus** te openen.

1. Stel in de sectie **verval datum** **toegangs pakket toewijzingen** in op het **aantal dagen**.

1. Stel **toewijzingen verloopt na** **30** dagen.

    ![Tabblad nieuw toegangs pakket-levenscyclus](./media/entitlement-management-access-package-first/lifecycle.png)

1. Klik op **volgende** om het tabblad **controleren + maken** te openen.

    ![Nieuw toegangs pakket-tabblad controleren + maken](./media/entitlement-management-access-package-first/review-create.png)

    Na enkele ogen blikken ziet u een melding dat het toegangs pakket is gemaakt.

1. Klik in het linkermenu van het toegangs pakket voor de marketing campagne op **overzicht**.

1. Kopieer de **koppeling naar de portal van mijn toegang**.

    U gebruikt deze koppeling voor de volgende stap.

    ![Overzicht van toegangs pakketten-koppeling naar mijn Access-Portal](./media/entitlement-management-shared/my-access-portal-link.png)

## <a name="step-3-request-access"></a>Stap 3: toegang aanvragen

In deze stap voert u de stappen uit als **interne aanvrager** en vraagt u toegang tot het toegangs pakket. Aanvragers verzenden hun aanvragen met behulp van een site met de naam mijn toegangs Portal. De portal van mijn toegang kan aanvragers aanvragen voor toegangs pakketten verzenden, de toegangs pakketten waartoe ze al toegang hebben, en hun aanvraag geschiedenis weer geven.

**Vereiste rol:** Interne aanvrager

1. Meld u af bij de Azure Portal.

1. Navigeer in een nieuw browser venster naar de Portal koppeling van mijn toegang die u in de vorige stap hebt gekopieerd.

1. Meld u aan bij de portal My Access als **Requestor1**.

    Het toegangs pakket voor de **marketing campagne** moet worden weer geven.

1. Als dat nodig is, klikt u in de kolom **Beschrijving** op de pijl om de details van het toegangs pakket weer te geven.

    ![Mijn Access-Portal-toegangs pakketten](./media/entitlement-management-shared/my-access-access-packages.png)

1. Klik op het vinkje om het pakket te selecteren.

1. Klik op **toegang aanvragen** om het deel venster toegang aanvragen te openen.

    ![Mijn toegangs Portal-knop toegang aanvragen](./media/entitlement-management-access-package-first/my-access-request-access-button.png)

1. Typ in het vak **zakelijke rechtvaardiging** de uitvulling die **Ik werk aan de nieuwe marketing campagne**.

    ![Mijn Access-portal-toegang aanvragen](./media/entitlement-management-shared/my-access-request-access.png)

1. Klik op **Submit**

1. Klik in het menu links op **aanvraag geschiedenis** om te controleren of uw aanvraag is verzonden.

## <a name="step-4-validate-that-access-has-been-assigned"></a>Stap 4: controleren of de toegang is toegewezen

In deze stap bevestigt u dat de **interne aanvrager** het toegangs pakket heeft toegewezen en dat ze nu lid zijn van de groep **Marketing Resources** .

**Vereiste rol:** Globale beheerder, gebruikers beheerder, catalogus eigenaar of toegangs pakket beheer

1. Meld u af bij de portal van mijn toegang.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als **admin1**.

1. Klik op **Azure Active Directory** en klik vervolgens op **Identity governance**.

1. Klik in het linkermenu op **toegangs pakketten**.

1. Zoek en klik op **Marketing Campaign** Access package.

1. Klik in het menu links op **aanvragen**.

    U ziet Requestor1 en het eerste beleid met de status **bezorgd**.

1. Klik op de aanvraag om de details van de aanvraag weer te geven.

    ![Toegangs pakket-aanvraag Details](./media/entitlement-management-access-package-first/request-details.png)

1. Klik in de linkernavigatiebalk op **Azure Active Directory**.

1. Klik op **groepen** en open de groep **Marketing Resources** .

1. Klik op **leden**.

    U ziet **Requestor1** vermeld als een lid.

    ![Leden van marketing resources](./media/entitlement-management-access-package-first/group-members.png)

## <a name="step-5-clean-up-resources"></a>Stap 5: resources opschonen

In deze stap verwijdert u de wijzigingen die u hebt aangebracht en verwijdert u het toegangs pakket voor de **marketing campagne** .

**Vereiste rol:**  Globale beheerder of gebruikers beheerder

1. Klik in de Azure Portal op **Azure Active Directory** en klik vervolgens op **Identity governance**.

1. Open het toegangs pakket voor de **marketing campagne** .

1. Klik op **toewijzingen**.

1. Klik voor **Requestor1**op het weglatings teken ( **...** ) en klik vervolgens op **toegang verwijderen**. In het bericht dat wordt weer gegeven, klikt u op **Ja**.

    Na enkele ogen blikken wordt de status gewijzigd van bezorgd in verlopen.

1. Klik op **resource rollen**.

1. Klik voor **Marketing Resources**op het weglatings teken ( **...** ) en klik vervolgens op **resource functie verwijderen**. In het bericht dat wordt weer gegeven, klikt u op **Ja**.

1. Open de lijst met toegangs pakketten.

1. Klik voor **marketing campagne**op het weglatings teken ( **...** ) en klik vervolgens op **verwijderen**. In het bericht dat wordt weer gegeven, klikt u op **Ja**.

1. In Azure Active Directory verwijdert u de gebruikers die u hebt gemaakt, zoals **Requestor1** en **admin1**.

1. De groep **Marketing Resources** verwijderen.

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel voor meer informatie over veelvoorkomende scenario stappen in het beheer van rechten.
> [!div class="nextstepaction"]
> [Algemene scenario's](entitlement-management-scenarios.md)
