---
title: Zelf studie-gebruikers stromen maken-Azure Active Directory B2C
description: Meer informatie over het maken van gebruikers stromen in de Azure Portal voor het inschakelen van registreren, aanmelden en het bewerken van gebruikers profielen voor uw toepassingen in Azure Active Directory B2C.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79264243"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>Zelf studie: gebruikers stromen maken in Azure Active Directory B2C

In uw toepassingen kunt u [gebruikers stromen](user-flow-overview.md) gebruiken waarmee gebruikers zich kunnen registreren, aanmelden of hun profiel beheren. U kunt meerdere gebruikers stromen van verschillende typen in uw Azure Active Directory B2C-Tenant (Azure AD B2C) maken en deze naar behoefte gebruiken in uw toepassingen. Gebruikers stromen kunnen opnieuw worden gebruikt in verschillende toepassingen.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Een registratie en aanmeldings gebruikers stroom maken
> * Een gebruikersstroom voor profielbewerking maken
> * Een gebruikersstroom voor het opnieuw instellen van het wachtwoord maken

Deze zelf studie laat zien hoe u een aantal aanbevolen gebruikers stromen kunt maken met behulp van de Azure Portal. Als u informatie zoekt over het instellen van een stroom voor het wacht woord voor een resource-eigenaar (ROPC) in uw toepassing, raadpleegt u [de gegevens stroom voor het wacht woord voor de resource-eigenaar configureren in azure AD B2C](configure-ropc.md).

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

[Registreer uw toepassingen](tutorial-register-applications.md) die deel uitmaken van de gebruikers stromen die u wilt aanmaken.

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Een registratie en aanmeldings gebruikers stroom maken

Met de registratie-en aanmeldings gebruikers stroom worden zowel registratie-als aanmeldings ervaringen met één configuratie afgehandeld. Gebruikers van uw toepassing ondervinden het juiste pad, afhankelijk van de context.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het pictogram voor het adres van de map en het **abonnement** op de werk balk van de portal en selecteer vervolgens de map die uw Azure AD B2C Tenant bevat.

    ![B2C-Tenant, het deel venster mappen en abonnementen, Azure Portal](./media/tutorial-create-user-flows/directory-subscription-pane.png)

1. Zoek in het Azure Portal naar en selecteer **Azure AD B2C**.
1. Onder **beleids regels**selecteert u **gebruikers stromen (beleid)** en selecteert u vervolgens **nieuwe gebruikers stroom**.

    ![De pagina Gebruikers stromen in de portal met de knop nieuwe gebruikers stroom gemarkeerd](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

1. Op het tabblad **Aanbevolen** selecteert u de flow **registreren en aanmelden in** de gebruikers stroom.

    ![Selecteer een pagina gebruikers stroom met de markering aanmelden en aanmeldings stroom gemarkeerd](./media/tutorial-create-user-flows/signup-signin-type.png)

1. Voer een **Naam** in voor de gebruikersstroom. Bijvoorbeeld *signupsignin1*.
1. Voor **id-providers**selecteert u **e-mail aanmelding**.

    ![De pagina gebruikers stroom maken in Azure Portal met gemarkeerde eigenschappen](./media/tutorial-create-user-flows/signup-signin-properties.png)

1. Kies voor **gebruikers kenmerken en claims**de claims en kenmerken die u wilt verzamelen en verzenden van de gebruiker tijdens de registratie. Selecteer bijvoorbeeld **meer weer geven**en kies vervolgens kenmerken en claims voor **land/regio**, **weergave naam**en **Post code**. Klik op **OK**.

    ![Pagina kenmerken en claims selecteren met drie claims geselecteerd](./media/tutorial-create-user-flows/signup-signin-attributes.png)

1. Klik op **maken** om de gebruikers stroom toe te voegen. Een voor voegsel van *B2C_1* wordt automatisch toegevoegd aan de naam.

### <a name="test-the-user-flow"></a>De gebruikers stroom testen

1. Selecteer de gebruikers stroom die u hebt gemaakt om de pagina overzicht te openen en selecteer vervolgens **gebruikers stroom uitvoeren**.
1. Selecteer voor **toepassing**de webtoepassing met de naam *webapp1* die u eerder hebt geregistreerd. In de **antwoord-URL** moeten `https://jwt.ms`worden weer gegeven.
1. Klik op **gebruikers stroom uitvoeren**en selecteer **nu aanmelden**.

    ![De pagina gebruikers stroom uitvoeren in de portal met de knop gebruikers stroom uitvoeren gemarkeerd](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. Voer een geldig e-mail adres in, klik op **verificatie code verzenden**, voer de verificatie code in die u ontvangt en selecteer vervolgens **code verifiëren**.
1. Voer een nieuw wacht woord in en bevestig het wacht woord.
1. Selecteer uw land en regio, voer de naam in die u wilt weer geven, voer een post code in en klik op **maken**. Het token wordt geretourneerd naar `https://jwt.ms` en moet worden weer gegeven.
1. U kunt de gebruikers stroom nu opnieuw uitvoeren en u moet zich kunnen aanmelden met het account dat u hebt gemaakt. Het geretourneerde token bevat de claims die u hebt geselecteerd van land/regio, naam en post code.

## <a name="create-a-profile-editing-user-flow"></a>Een gebruikersstroom voor profielbewerking maken

Als u gebruikers in staat wilt stellen hun profiel te bewerken in uw toepassing, gebruikt u een gebruikers stroom voor het bewerken van profielen.

1. Selecteer in het menu van de pagina overzicht van Azure AD B2C tenants de optie **gebruikers stromen (beleid)** en selecteer vervolgens **nieuwe gebruikers stroom**.
1. Selecteer de gebruikers stroom voor het **bewerken van profielen** op het tabblad **Aanbevolen** .
1. Voer een **Naam** in voor de gebruikersstroom. Bijvoorbeeld *profileediting1*.
1. Voor **id-providers**selecteert u **lokaal account aanmelden**.
1. Kies voor **gebruikers kenmerken**de kenmerken die door de klant in hun profiel kunnen worden bewerkt. Selecteer bijvoorbeeld **meer weer geven**en kies vervolgens zowel kenmerken als claims voor **weergave naam** en **functie titel**. Klik op **OK**.
1. Klik op **maken** om de gebruikers stroom toe te voegen. Een voor voegsel van *B2C_1* wordt automatisch toegevoegd aan de naam.

### <a name="test-the-user-flow"></a>De gebruikers stroom testen

1. Selecteer de gebruikers stroom die u hebt gemaakt om de pagina overzicht te openen en selecteer vervolgens **gebruikers stroom uitvoeren**.
1. Selecteer voor **toepassing**de webtoepassing met de naam *webapp1* die u eerder hebt geregistreerd. In de **antwoord-URL** moeten `https://jwt.ms`worden weer gegeven.
1. Klik op **gebruikers stroom uitvoeren**en meld u aan met het account dat u eerder hebt gemaakt.
1. U hebt nu de mogelijkheid om de weergave naam en de taak titel voor de gebruiker te wijzigen. Klik op **Doorgaan**. Het token wordt geretourneerd naar `https://jwt.ms` en moet worden weer gegeven.

## <a name="create-a-password-reset-user-flow"></a>Een gebruikersstroom voor het opnieuw instellen van het wachtwoord maken

Als u gebruikers van uw toepassing de mogelijkheid wilt bieden hun wacht woord opnieuw in te stellen, gebruikt u een gebruikers stroom voor het opnieuw instellen van wacht woorden.

1. Selecteer in het menu Azure AD B2C overzicht tenants de optie **gebruikers stromen (beleid)** en selecteer vervolgens **nieuwe gebruikers stroom**.
1. Selecteer de gebruikers stroom voor **wacht woord opnieuw instellen** op het tabblad **Aanbevolen** .
1. Voer een **Naam** in voor de gebruikersstroom. Bijvoorbeeld *passwordreset1*.
1. Schakel **wacht woord opnieuw instellen met e-mail adres**in bij **id-providers**.
1. Onder toepassings claims klikt u op **meer weer geven** en kiest u de claims die u wilt laten retour neren in de autorisatie tokens die worden teruggestuurd naar uw toepassing. Selecteer bijvoorbeeld **Object-ID van gebruiker**.
1. Klik op **OK**.
1. Klik op **maken** om de gebruikers stroom toe te voegen. Een voor voegsel van *B2C_1* wordt automatisch toegevoegd aan de naam.

### <a name="test-the-user-flow"></a>De gebruikers stroom testen

1. Selecteer de gebruikers stroom die u hebt gemaakt om de pagina overzicht te openen en selecteer vervolgens **gebruikers stroom uitvoeren**.
1. Selecteer voor **toepassing**de webtoepassing met de naam *webapp1* die u eerder hebt geregistreerd. In de **antwoord-URL** moeten `https://jwt.ms`worden weer gegeven.
1. Klik op **gebruikers stroom uitvoeren**, Controleer het e-mail adres van het account dat u eerder hebt gemaakt en selecteer **door gaan**.
1. U hebt nu de mogelijkheid om het wacht woord voor de gebruiker te wijzigen. Wijzig het wacht woord en selecteer **door gaan**. Het token wordt geretourneerd naar `https://jwt.ms` en moet worden weer gegeven.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een registratie en aanmeldings gebruikers stroom maken
> * Een gebruikersstroom voor profielbewerking maken
> * Een gebruikersstroom voor het opnieuw instellen van het wachtwoord maken

Meer informatie over het toevoegen van id-providers aan uw toepassingen zodat gebruikers zich kunnen aanmelden met providers zoals Azure AD, Amazon, Facebook, GitHub, LinkedIn, micro soft of Twitter.

> [!div class="nextstepaction"]
> [Voeg id-providers toe aan uw toepassingen >](tutorial-add-identity-providers.md)