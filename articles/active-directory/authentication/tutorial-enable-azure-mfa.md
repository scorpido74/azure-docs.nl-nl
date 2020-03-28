---
title: Azure Multi-Factor Authentication inschakelen
description: In deze zelfstudie leert u hoe u Azure Multi-Factor Authentication inschakelt voor een groep gebruikers en de secundaire factorprompt test tijdens een aanmeldingsgebeurtenis.
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77154821"
---
# <a name="tutorial-secure-user-sign-in-events-with-azure-multi-factor-authentication"></a>Zelfstudie: Aanmeldingsgebeurtenissen voor gebruikers beveiligen met Azure Multi-Factor Authentication

Multi-factor authenticatie (MFA) is een proces waarbij een gebruiker wordt gevraagd tijdens een aanmeldingsgebeurtenis voor aanvullende vormen van identificatie. Deze prompt kan zijn om een code in te voeren op hun mobiele telefoon of om een vingerafdruk scan. Wanneer u een tweede vorm van verificatie nodig hebt, wordt de beveiliging verhoogd omdat deze extra factor niet gemakkelijk is voor een aanvaller om te verkrijgen of te dupliceren.

Azure Multi-Factor Authentication and Conditional Access policies geven de flexibiliteit om MFA in te schakelen voor gebruikers tijdens specifieke aanmeldingsgebeurtenissen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een beleid voor voorwaardelijke toegang maken om Azure Multi-Factor Authentication in te schakelen voor een groep gebruikers
> * De beleidsvoorwaarden configureren die vragen om MFA
> * Het MFA-proces als gebruiker testen

## <a name="prerequisites"></a>Vereisten

Als u deze zelfstudie wilt voltooien, hebt u de volgende bronnen en bevoegdheden nodig:

* Een werkende Azure AD-tenant met Azure AD Premium of proeflicentie ingeschakeld.
    * Maak er indien nodig [gratis een.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Een account met *algemene beheerdersbevoegdheden.*
* Een niet-beheerdersgebruiker met een wachtwoord dat u kent, zoals *testuser.* U test de ervaring van Azure Multi-Factor Authentication voor eindgebruikers met behulp van dit account in deze zelfstudie.
    * Zie [Snelstart: Nieuwe gebruikers toevoegen aan Azure Active Directory](../add-users-azure-active-directory.md)als u een gebruiker wilt maken.
* Een groep waarvan de niet-beheerdersgebruiker lid is, zoals *MFA-Test-Group.* U schakelt Azure Multi-Factor Authentication voor deze groep in in deze zelfstudie.
    * Als u een groep wilt maken, [raadpleegt](../active-directory-groups-create-azure-portal.md)u hoe u een groep maakt en leden toevoegt in Azure Active Directory .

## <a name="create-a-conditional-access-policy"></a>Beleid voor voorwaardelijke toegang maken

De aanbevolen manier om Azure Multi-Factor Authentication in te schakelen en te gebruiken, is met beleid voor voorwaardelijke toegang. Met Voorwaardelijke toegang u beleid maken en definiëren dat reageert op aanmeldingsgebeurtenissen en aanvullende acties aanvragen voordat een gebruiker toegang krijgt tot een toepassing of service.

![Overzichtsdiagram over hoe voorwaardelijke toegang werkt om het aanmeldingsproces te beveiligen](media/tutorial-enable-azure-mfa/conditional-access-overview.png)

Het beleid voor voorwaardelijke toegang kan gedetailleerd en specifiek zijn, met als doel gebruikers in staat te stellen productief te zijn, waar en wanneer dan ook, maar ook uw organisatie te beschermen. Laten we in deze zelfstudie een basisbeleid voor voorwaardelijke toegang maken om mfa te vragen wanneer een gebruiker zich aanmeldt bij de Azure-portal. In een latere zelfstudie in deze reeks configureert u Azure Multi-Factor Authentication met behulp van een op risico's gebaseerd beleid voor voorwaardelijke toegang.

Maak eerst een beleid voor voorwaardelijke toegang en wijs uw testgroep gebruikers als volgt toe:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een account met algemene *beheerdersmachtigingen.*
1. Zoek naar en selecteer **Azure Active Directory**en kies vervolgens **Beveiliging** in het menu aan de linkerkant.
1. Selecteer **Voorwaardelijke toegang**en kies + Nieuw **beleid**.
1. Voer een naam in voor het beleid, zoals *MFA Pilot*.
1. Kies **onder Toewijzingen** **gebruikers en groepen**en vervolgens de knop Gebruikers selecteren en **groepeert** de keuzerondje.
1. Schakel het selectievakje voor **gebruikers en groepen**in en **selecteer** vervolgens om door de beschikbare Azure AD-gebruikers en -groepen te bladeren.
1. Blader naar en selecteer uw Azure AD-groep, zoals *MFA-Test-Group,* en kies **Selecteer Selecteren**.

    [![](media/tutorial-enable-azure-mfa/select-group-for-conditional-access-cropped.png "Select your Azure AD group to use with the Conditional Access policy")](media/tutorial-enable-azure-mfa/select-group-for-conditional-access.png#lightbox)

1. Als u het beleid voor voorwaardelijke toegang voor de groep wilt toepassen, selecteert u **Gereed**.

## <a name="configure-the-conditions-for-multi-factor-authentication"></a>De voorwaarden voor meervoudige verificatie configureren

Als het beleid voor voorwaardelijke toegang is gemaakt en een testgroep gebruikers is toegewezen, definieert u nu de cloud-apps of -acties die het beleid activeren. Deze cloud-apps of -acties zijn de scenario's die u besluit extra verwerking te vereisen, zoals het vragen om MFA. U bijvoorbeeld besluiten dat toegang tot een financiële toepassing of het gebruik van beheertools vereist als een extra verificatieprompt.

Configureer voor deze zelfstudie het beleid voor voorwaardelijke toegang om MFA te vereisen wanneer een gebruiker zich aanmeldt bij de Azure-portal.

1. Selecteer **Cloud-apps of -acties**. U ervoor kiezen het beleid voor voorwaardelijke toegang toe te passen op *Alle cloud-apps* of *Apps selecteren.* Om flexibiliteit te bieden, u ook bepaalde apps uitsluiten van het beleid.

    Kies voor deze zelfstudie op de pagina *Opnemen* de **keuzerondje apps selecteren.**

1. Kies **Selecteren**en blader vervolgens door de lijst met beschikbare aanmeldingsgebeurtenissen die kunnen worden gebruikt.

    Kies voor deze zelfstudie **Microsoft Azure Management,** zodat het beleid van toepassing is op aanmeldingsgebeurtenissen voor de Azure-portal.

1. Als u de geselecteerde apps wilt toepassen, kiest **u Selecteren,** vervolgens **Gereed**.

    ![Selecteer de Microsoft Azure Management-app die u wilt opnemen in het beleid voor voorwaardelijke toegang](media/tutorial-enable-azure-mfa/select-azure-management-app.png)

Met toegangsbesturingselementen u bepalen welke vereisten een gebruiker moet krijgen om toegang te krijgen, zoals het nodig hebben van een goedgekeurde client-app of het gebruik van een apparaat waarvan Hybrid Azure AD is verbonden. Configureer in deze zelfstudie de toegangsbesturingselementen om MFA te vereisen tijdens een aanmeldingsgebeurtenis voor de Azure-portal.

1. Kies *onder Toegangsbesturingselementen*De optie **Verlenen**en controleer of de **keuzerondje voor toegangstoegang verlenen** is geselecteerd.
1. Schakel het selectievakje **voor Meervoudige verificatie vereisen**in en kies **Selecteren**.

Beleid voor voorwaardelijke toegang kan alleen op *Rapport* worden ingesteld als u wilt zien welke invloed de configuratie heeft op gebruikers of *uit* als u het gebruiksbeleid op dit moment niet wilt gebruiken. Als een testgroep van gebruikers werd gericht voor deze zelfstudie, laat het beleid inschakelen en vervolgens testen Azure Multi-Factor Authentication.

1. Het *inschakelen van* beleidsomschakeling instellen op **Aan**.
1. Als u het beleid voor voorwaardelijke toegang wilt toepassen, selecteert u **Maken**.

## <a name="test-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication testen

Laten we uw beleid voor voorwaardelijke toegang en Azure Multi-Factor Authentication in actie bekijken. Log eerst in bij een resource waarvoor MFA niet nodig is:

1. Open een nieuw browservenster in de InPrivate- of incognitomodus en blader naar[https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)
1. Meld u aan bij de niet-beheerderstestgebruiker, zoals *testgebruiker.* Er is geen prompt voor u om MFA te voltooien.
1. Sluit het browservenster.

Meld u nu aan bij de Azure-portal. Aangezien de Azure-portal is geconfigureerd in het beleid voor voorwaardelijke toegang om extra verificatie te vereisen, krijgt u een prompt voor azure multi-factorverificatie.

1. Open een nieuw browservenster in de InPrivate- of incognitomodus en blader naar [https://portal.azure.com](https://portal.azure.com).
1. Meld u aan bij de niet-beheerderstestgebruiker, zoals *testgebruiker.* U moet zich registreren voor Azure Multi-Factor Authentication en deze gebruiken. Volg de aanwijzingen om het proces te voltooien en controleer of u zich met succes aanmeldt bij de Azure-portal.

    ![Volg de browserprompts en vervolgens op uw geregistreerde multi-factor authenticatie prompt om in te loggen](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

1. Sluit het browservenster.

## <a name="clean-up-resources"></a>Resources opschonen

Als u het beleid voor voorwaardelijke toegang niet langer wilt gebruiken om Azure Multi-Factor Authentication in te schakelen die is geconfigureerd als onderdeel van deze zelfstudie, verwijdert u het beleid met de volgende stappen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Zoek naar en selecteer **Azure Active Directory**en kies vervolgens **Beveiliging** in het menu aan de linkerkant.
1. Selecteer **Voorwaardelijke toegang**en kies vervolgens het beleid dat u hebt gemaakt, zoals MFA *Pilot*
1. Kies **Verwijderen**en bevestig vervolgens dat u het beleid wilt verwijderen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u Azure Multi-Factor Authentication ingeschakeld met het beleid voor voorwaardelijke toegang voor een geselecteerde groep gebruikers. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een beleid voor voorwaardelijke toegang maken om Azure Multi-Factor Authentication in te schakelen voor een groep Azure AD-gebruikers
> * De beleidsvoorwaarden configureren die vragen om MFA
> * Het MFA-proces als gebruiker testen

> [!div class="nextstepaction"]
> [Wachtwoordterugschrijven inschakelen voor selfservice wachtwoordreset (SSPR)](tutorial-enable-writeback.md)
