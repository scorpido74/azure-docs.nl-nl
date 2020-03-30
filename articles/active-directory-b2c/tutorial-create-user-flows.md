---
title: Zelfstudie - Gebruikersstromen maken - Azure Active Directory B2C
description: Meer informatie over het maken van gebruikersstromen in de Azure-portal om aanmelden, aanmelden en bewerken van gebruikersprofielen voor uw toepassingen in Azure Active Directory B2C in te schakelen.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 06/07/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 51adbb74635f66ca86347b536dc2607566dcb725
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264243"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>Zelfstudie: Gebruikersstromen maken in Azure Active Directory B2C

In uw toepassingen u [gebruikersstromen](user-flow-overview.md) hebben waarmee gebruikers zich kunnen aanmelden, zich kunnen aanmelden of hun profiel kunnen beheren. U meerdere gebruikersstromen van verschillende typen maken in uw Azure Active Directory B2C -tenant (Azure AD B2C) en deze zo nodig in uw toepassingen gebruiken. Gebruikersstromen kunnen worden hergebruikt voor toepassingen.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Een aanmeldings- en aanmeldingsstroom maken
> * Een gebruikersstroom voor profielbewerking maken
> * Een gebruikersstroom voor het opnieuw instellen van het wachtwoord maken

In deze zelfstudie ziet u hoe u een aantal aanbevolen gebruikersstromen maakt met behulp van de Azure-portal. Zie De stroom wachtwoordreferenties voor [broneigenaren configureren in Azure AD B2C](configure-ropc.md)als u informatie zoekt over het instellen van een ropc-stroom (Resource Owner Password Credentials) in uw toepassing.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

[Registreer uw toepassingen](tutorial-register-applications.md) die deel uitmaken van de gebruikersstromen die u wilt maken.

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Een aanmeldings- en aanmeldingsstroom maken

De aanmeldings- en aanmeldingsstroom verwerkt zowel aanmeldings- als aanmeldingservaringen met één configuratie. Gebruikers van uw toepassing worden naar het juiste pad geleid, afhankelijk van de context.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer het pictogram **Directory + Abonnement** op de werkbalk van de portal en selecteer vervolgens de map met uw Azure AD B2C-tenant.

    ![Deelvenster B2C-tenant, map- en abonnementsvenster, Azure-portal](./media/tutorial-create-user-flows/directory-subscription-pane.png)

1. Zoek en selecteer **Azure AD B2C**in de Azure-portal .
1. Selecteer **onder Beleid**de optie **Gebruikersstromen (beleidsregels)** en selecteer **Vervolgens Nieuwe gebruikersstroom**.

    ![Gebruikersstromen pagina in portal met knop Nieuwe gebruikersstroom gemarkeerd](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

1. Selecteer op **het tabblad Aanbevolen** de **gebruikersstroom aanmelden en aanmelden.**

    ![Selecteer een gebruikersstroompagina met Aanmelden en aanmeldingsstroom gemarkeerd](./media/tutorial-create-user-flows/signup-signin-type.png)

1. Voer een **Naam** in voor de gebruikersstroom. U zich bijvoorbeeld *aanmelden1*.
1. Selecteer **Aanmelding via e-mail**voor **identiteitsproviders.**

    ![Gebruikersstroompagina maken in Azure-portal met gemarkeerde eigenschappen](./media/tutorial-create-user-flows/signup-signin-properties.png)

1. Kies **voor gebruikerskenmerken en -claims**de claims en kenmerken die u tijdens het aanmelden van de gebruiker wilt verzamelen en verzenden. Selecteer bijvoorbeeld **Meer weergeven**en kies vervolgens kenmerken en claims voor **Land/Regio,** **Weergavenaam**en **Postcode**. Klik op **OK**.

    ![Pagina Kenmerken en claimsselectie met drie geselecteerde claims](./media/tutorial-create-user-flows/signup-signin-attributes.png)

1. Klik **op Maken** om de gebruikersstroom toe te voegen. Een voorvoegsel van *B2C_1* wordt automatisch toegevoegd aan de naam.

### <a name="test-the-user-flow"></a>De gebruikersstroom testen

1. Selecteer de gebruikersstroom die u hebt gemaakt om de overzichtspagina te openen en selecteer **Vervolgens Gebruikersstroom uitvoeren**.
1. Selecteer **voor Toepassing**de webtoepassing met de naam *webapp1* die u eerder hebt geregistreerd. De URL van `https://jwt.ms`het **antwoord** moet worden weergegeven .
1. Klik **op Gebruikersstroom uitvoeren**en selecteer Nu **aanmelden**.

    ![Gebruikersstroompagina in portal uitvoeren met knop Gebruikersstroom uitvoeren gemarkeerd](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. Voer een geldig e-mailadres in, klik op **Verificatiecode verzenden,** voer de verificatiecode in die u ontvangt en selecteer **Code verifiëren**.
1. Voer een nieuw wachtwoord in en bevestig het wachtwoord.
1. Selecteer uw land en regio, voer de gewenste naam in, voer een postcode in en klik op **Maken**. Het token wordt `https://jwt.ms` teruggestuurd naar en moet aan u worden weergegeven.
1. U de gebruikersstroom nu opnieuw uitvoeren en u moet zich kunnen aanmelden met het account dat u hebt gemaakt. Het geretourneerde token bevat de claims die u hebt geselecteerd van land/regio, naam en postcode.

## <a name="create-a-profile-editing-user-flow"></a>Een gebruikersstroom voor profielbewerking maken

Als u gebruikers in staat wilt stellen hun profiel in uw toepassing te bewerken, gebruikt u een gebruikersstroom voor profielbewerking.

1. Selecteer in het menu van de overzichtspagina van de Azure AD B2C-tenant de optie **Gebruikersstromen (beleidsregels)** en selecteer **Vervolgens Nieuwe gebruikersstroom**.
1. Selecteer de gebruikersstroom **van profielbewerking** op het **tabblad Aanbevolen.**
1. Voer een **Naam** in voor de gebruikersstroom. Bijvoorbeeld, *profielbewerking1*.
1. Selecteer **Aanmelden voor identiteiten**voor **identiteitsproviders**.
1. Kies **voor gebruikerskenmerken**de kenmerken die de klant moet kunnen bewerken in zijn profiel. Selecteer bijvoorbeeld **Meer weergeven**en kies vervolgens zowel kenmerken als claims voor **Weergavenaam** en **Functie-titel**. Klik op **OK**.
1. Klik **op Maken** om de gebruikersstroom toe te voegen. Een voorvoegsel van *B2C_1* wordt automatisch toegevoegd aan de naam.

### <a name="test-the-user-flow"></a>De gebruikersstroom testen

1. Selecteer de gebruikersstroom die u hebt gemaakt om de overzichtspagina te openen en selecteer **Vervolgens Gebruikersstroom uitvoeren**.
1. Selecteer **voor Toepassing**de webtoepassing met de naam *webapp1* die u eerder hebt geregistreerd. De URL van `https://jwt.ms`het **antwoord** moet worden weergegeven .
1. Klik **op Gebruikersstroom uitvoeren**en meld u aan met het account dat u eerder hebt gemaakt.
1. U nu de weergavenaam en de functietitel voor de gebruiker wijzigen. Klik **op Doorgaan**. Het token wordt `https://jwt.ms` teruggestuurd naar en moet aan u worden weergegeven.

## <a name="create-a-password-reset-user-flow"></a>Een gebruikersstroom voor het opnieuw instellen van het wachtwoord maken

Als u gebruikers van uw toepassing in staat wilt stellen hun wachtwoord opnieuw in te stellen, gebruikt u een gebruikersstroom voor het opnieuw instellen van wachtwoorden.

1. Selecteer in het overzichtsmenu van azure AD B2C-tenant de optie **Gebruikersstromen (beleidsregels)** en selecteer **Vervolgens Nieuwe gebruikersstroom**.
1. Selecteer de gebruikersstroom **voor het opnieuw instellen van** wachtwoorden op het tabblad **Aanbevolen.**
1. Voer een **Naam** in voor de gebruikersstroom. Bijvoorbeeld, *passwordreset1*.
1. Schakel **wachtwoord opnieuw instellen in met e-mailadres**voor **identiteitsproviders.**
1. Klik onder Toepassingsclaims op **Meer weergeven** en kies de claims die u wilt terugsturen in de autorisatietokens die naar uw toepassing worden teruggestuurd. Selecteer bijvoorbeeld **Object-ID van gebruiker**.
1. Klik op **OK**.
1. Klik **op Maken** om de gebruikersstroom toe te voegen. Een voorvoegsel van *B2C_1* wordt automatisch toegevoegd aan de naam.

### <a name="test-the-user-flow"></a>De gebruikersstroom testen

1. Selecteer de gebruikersstroom die u hebt gemaakt om de overzichtspagina te openen en selecteer **Vervolgens Gebruikersstroom uitvoeren**.
1. Selecteer **voor Toepassing**de webtoepassing met de naam *webapp1* die u eerder hebt geregistreerd. De URL van `https://jwt.ms`het **antwoord** moet worden weergegeven .
1. Klik **op Gebruikersstroom uitvoeren,** controleer het e-mailadres van het account dat u eerder hebt gemaakt en selecteer **Doorgaan**.
1. Je hebt nu de mogelijkheid om het wachtwoord voor de gebruiker te wijzigen. Wijzig het wachtwoord en selecteer **Doorgaan**. Het token wordt `https://jwt.ms` teruggestuurd naar en moet aan u worden weergegeven.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een aanmeldings- en aanmeldingsstroom maken
> * Een gebruikersstroom voor profielbewerking maken
> * Een gebruikersstroom voor het opnieuw instellen van het wachtwoord maken

Lees vervolgens meer over het toevoegen van identiteitsproviders aan uw toepassingen om gebruikersaanmelding in te schakelen bij providers zoals Azure AD, Amazon, Facebook, GitHub, LinkedIn, Microsoft of Twitter.

> [!div class="nextstepaction"]
> [Identiteitsproviders toevoegen aan uw toepassingen >](tutorial-add-identity-providers.md)