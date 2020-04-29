---
title: Azure Multi-Factor Authentication inschakelen
description: In deze zelf studie leert u hoe u Azure Multi-Factor Authentication inschakelt voor een groep gebruikers en hoe u de secundaire factor prompt kunt testen tijdens een aanmeldings gebeurtenis.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 02/11/2020
ms.author: iainfou
author: iainfoulds
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 253eb23be03c1cc0f2abf4ad1fed734426dc287d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77154821"
---
# <a name="tutorial-secure-user-sign-in-events-with-azure-multi-factor-authentication"></a>Zelf studie: aanmeldings gebeurtenissen voor gebruikers beveiligen met Azure Multi-Factor Authentication

Multi-factor Authentication (MFA) is een proces waarbij een gebruiker wordt gevraagd tijdens een aanmeldings gebeurtenis voor aanvullende vormen van identificatie. U kunt dit ook doen door een code in te voeren op de cellphone of om een vingerafdruk scan te bieden. Wanneer u een tweede vorm van verificatie nodig hebt, wordt de beveiliging verhoogd omdat deze extra factor niet duidelijk is voor een aanvaller om te voor komen dat deze wordt opgehaald of gedupliceerd.

Azure Multi-Factor Authentication en beleid voor voorwaardelijke toegang bieden de flexibiliteit om MFA in te scha kelen voor gebruikers tijdens specifieke aanmeldings gebeurtenissen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een beleid voor voorwaardelijke toegang maken om Azure Multi-Factor Authentication in te scha kelen voor een groep gebruikers
> * De beleids voorwaarden configureren die vragen om MFA
> * Het MFA-proces testen als een gebruiker

## <a name="prerequisites"></a>Vereisten

U hebt de volgende resources en bevoegdheden nodig om deze zelf studie te volt ooien:

* Een werkende Azure AD-Tenant met Azure AD Premium of een proef licentie ingeschakeld.
    * Maak indien nodig [een gratis versie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Een account met *globale beheerders* bevoegdheden.
* Een niet-beheerders gebruiker met een wacht woord dat u kent, zoals *test User*. In deze zelf studie test u de Azure Multi-Factor Authentication-ervaring voor eind gebruikers met dit account.
    * Als u een gebruiker wilt maken, raadpleegt u [Quick Start: nieuwe gebruikers toevoegen aan Azure Active Directory](../add-users-azure-active-directory.md).
* Een groep waarvan de niet-beheerders gebruiker lid is, zoals *MFA-test groep*. In deze zelf studie schakelt u Azure Multi-Factor Authentication in voor deze groep.
    * Als u een groep wilt maken, raadpleegt u [een groep maken en leden toevoegen in azure Active Directory](../active-directory-groups-create-azure-portal.md).

## <a name="create-a-conditional-access-policy"></a>Beleid voor voorwaardelijke toegang maken

De aanbevolen manier om Azure Multi-Factor Authentication in te scha kelen met behulp van beleid voor voorwaardelijke toegang. Met voorwaardelijke toegang kunt u beleids regels maken en definiëren die reageren op aanmeldings gebeurtenissen en aanvullende acties aanvragen voordat een gebruiker toegang krijgt tot een toepassing of service.

![Overzichts diagram van hoe voorwaardelijke toegang het aanmeldings proces kan beveiligen](media/tutorial-enable-azure-mfa/conditional-access-overview.png)

Beleids regels voor voorwaardelijke toegang kunnen nauw keurig en specifiek zijn, met als doel gebruikers in staat te stellen overal en altijd productief te zijn, maar ook uw organisatie te beschermen. In deze zelf studie maken we een basis beleid voor voorwaardelijke toegang om te vragen om MFA wanneer een gebruiker zich aanmeldt bij de Azure Portal. In een latere zelf studie in deze serie configureert u Azure Multi-Factor Authentication met behulp van een op risico gebaseerd beleid voor voorwaardelijke toegang.

Maak eerst een beleid voor voorwaardelijke toegang en wijs de test groep gebruikers als volgt toe:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een account met *globale beheerders* machtigingen.
1. Zoek en selecteer **Azure Active Directory**en kies vervolgens **beveiliging** in het menu aan de linkerkant.
1. Selecteer **voorwaardelijke toegang**en kies vervolgens **+ Nieuw beleid**.
1. Voer een naam in voor het beleid, zoals *MFA pilot*.
1. Klik onder **toewijzingen**op **gebruikers en groepen**en vervolgens op het keuze rondje **gebruikers en groepen selecteren** .
1. Schakel het selectie vakje in voor **gebruikers en groepen**en **Selecteer** vervolgens om te bladeren door de BEschik bare Azure AD-gebruikers en-groepen.
1. Blader naar en selecteer uw Azure AD-groep, zoals *MFA-test groep*en kies vervolgens **selecteren**.

    [![](media/tutorial-enable-azure-mfa/select-group-for-conditional-access-cropped.png "Select your Azure AD group to use with the Conditional Access policy")](media/tutorial-enable-azure-mfa/select-group-for-conditional-access.png#lightbox)

1. Selecteer **gereed**om het beleid voor voorwaardelijke toegang toe te passen voor de groep.

## <a name="configure-the-conditions-for-multi-factor-authentication"></a>De voor waarden voor multi-factor Authentication configureren

Wanneer het beleid voor voorwaardelijke toegang is gemaakt en een test groep van gebruikers is toegewezen, definieert u nu de Cloud-apps of acties die het beleid activeren. Deze Cloud-apps of-acties zijn de scenario's waarvoor u aanvullende verwerking nodig hebt, zoals om te vragen om MFA. U kunt bijvoorbeeld besluiten dat toegang tot een financiële toepassing of het gebruik van beheer hulpprogramma's vereist is als een extra verificatie prompt.

Voor deze zelf studie configureert u het beleid voor voorwaardelijke toegang om MFA te vereisen wanneer een gebruiker zich aanmeldt bij de Azure Portal.

1. Selecteer **Cloud-apps of -acties**. U kunt ervoor kiezen het beleid voor voorwaardelijke toegang toe te passen op *alle Cloud-apps* of *apps te selecteren*. Om flexibiliteit te bieden, kunt u ook bepaalde apps uitsluiten van het beleid.

    Voor deze zelf studie kiest u op de pagina *insluiting* de keuze rondje **apps selecteren** .

1. Kies **selecteren**en blader vervolgens door de lijst met beschik bare aanmeldings gebeurtenissen die kunnen worden gebruikt.

    Voor deze zelf studie kiest u **Microsoft Azure beheer** , zodat het beleid van toepassing is op aanmeldings gebeurtenissen in de Azure Portal.

1. Klik op **selecteren**en vervolgens op **gereed**om de Select-apps toe te passen.

    ![Selecteer de Microsoft Azure beheer-app die u wilt toevoegen in het beleid voor voorwaardelijke toegang](media/tutorial-enable-azure-mfa/select-azure-management-app.png)

Met toegangs beheer kunt u de vereisten definiëren voor een gebruiker om toegang te krijgen, zoals het vereisen van een goedgekeurde client-app of het gebruik van een apparaat dat lid is van de hybride Azure AD. In deze zelf studie configureert u de toegangs elementen om MFA te vereisen tijdens een aanmeldings gebeurtenis voor de Azure Portal.

1. Kies onder *toegangs beheer*de optie **verlenen**en zorg ervoor dat het keuze rondje **toegang weigeren** is geselecteerd.
1. Schakel het selectie vakje voor **multi-factor Authentication vereisen**in en kies vervolgens **selecteren**.

Beleid voor voorwaardelijke toegang kan worden ingesteld op *alleen-rapport* als u wilt zien hoe de configuratie van invloed is op gebruikers of *uit* als u het beleid op dit moment niet wilt gebruiken. Als een test groep van gebruikers is gericht op deze zelf studie, kunt u het beleid inschakelen en vervolgens Azure-Multi-Factor Authentication testen.

1. Stel de *Schakel* optie voor het inschakelen van beleid in **op aan**.
1. Selecteer **maken**om het beleid voor voorwaardelijke toegang toe te passen.

## <a name="test-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication testen

Laten we uw beleid voor voorwaardelijke toegang en Azure-Multi-Factor Authentication in actie zien. Meld u eerst aan bij een resource die geen MFA vereist:

1. Open een nieuw browser venster in de InPrivate-of Incognito-modus en blader naar[https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)
1. Meld u aan met uw test gebruiker die geen beheerder is, *zoals test User.* U wordt niet gevraagd om MFA te volt ooien.
1. Sluit het browservenster.

Meld u nu aan bij de Azure Portal. Als de Azure Portal is geconfigureerd in het beleid voor voorwaardelijke toegang om extra verificatie te vereisen, ontvangt u een Azure Multi-Factor Authentication-prompt.

1. Open een nieuw browser venster in de InPrivate-of Incognito-modus [https://portal.azure.com](https://portal.azure.com)en blader naar.
1. Meld u aan met uw test gebruiker die geen beheerder is, *zoals test User.* U moet zich registreren voor en Azure Multi-Factor Authentication gebruiken. Volg de aanwijzingen om het proces te volt ooien en te controleren of u zich hebt aangemeld bij de Azure Portal.

    ![Volg de aanwijzingen in de browser en klik vervolgens op uw geregistreerde multi-factor Authentication-prompt om u aan te melden](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

1. Sluit het browservenster.

## <a name="clean-up-resources"></a>Resources opschonen

Als u het beleid voor voorwaardelijke toegang niet meer wilt gebruiken om Azure Multi-Factor Authentication in te scha kelen als onderdeel van deze zelf studie, verwijdert u het beleid met behulp van de volgende stappen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Zoek en selecteer **Azure Active Directory**en kies vervolgens **beveiliging** in het menu aan de linkerkant.
1. Selecteer **voorwaardelijke toegang**en kies vervolgens het beleid dat u hebt gemaakt, zoals *MFA pilot*
1. Kies **verwijderen**en bevestig dat u het beleid wilt verwijderen.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u Azure Multi-Factor Authentication ingeschakeld met behulp van beleids regels voor voorwaardelijke toegang voor een geselecteerde groep gebruikers. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een beleid voor voorwaardelijke toegang maken om Azure Multi-Factor Authentication in te scha kelen voor een groep van Azure AD-gebruikers
> * De beleids voorwaarden configureren die vragen om MFA
> * Het MFA-proces testen als een gebruiker

> [!div class="nextstepaction"]
> [Wacht woord terugschrijven inschakelen voor Self-service voor wachtwoord herstel (SSPR)](tutorial-enable-writeback.md)
