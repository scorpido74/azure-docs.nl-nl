---
title: Quick Start-Azure AD self-service wacht woord opnieuw instellen
description: In deze Quick Start leert u hoe u de self-service voor wachtwoord herstel van Azure AD kunt configureren zodat gebruikers hun eigen wacht woorden opnieuw kunnen instellen en de overhead van de IT-afdeling verlagen.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: quickstart
ms.date: 12/10/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: a168f9bf58c4942fc0b76b9ffefc2b32b5bfbe5a
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75549360"
---
# <a name="quickstart-configure-azure-active-directory-self-service-password-reset"></a>Snelstartgids: Azure Active Directory self-service voor wachtwoord herstel configureren

In deze Quick Start configureert u Azure Active Directory (AD) self-service voor wachtwoord herstel (SSPR) zodat gebruikers hun wacht woord opnieuw kunnen instellen of hun accounts mogen ontgrendelen. Met SSPR kunnen gebruikers hun eigen referenties opnieuw instellen zonder helpdesk beheerder of assistentie. Deze mogelijkheid stelt gebruikers in staat om toegang tot hun account te krijgen zonder te hoeven wachten op aanvullende ondersteuning.

> [!IMPORTANT]
> In deze Quick start ziet u een beheerder hoe selfservice voor wachtwoord herstel moet worden ingeschakeld. Als u een eind gebruiker bent al geregistreerd voor de selfservice voor het opnieuw instellen van het wacht woord en deze wilt weer geven in uw account, gaat u naar https://aka.ms/sspr.
>
> Als uw IT-team de mogelijkheid om uw eigen wacht woord opnieuw in te stellen niet heeft ingeschakeld, kunt u contact met de Help Desk vinden voor meer informatie.

## <a name="prerequisites"></a>Vereisten

* Een werkende Azure AD-tenant waarop minimaal een proeflicentie is ingeschakeld.
    * Maak indien nodig [een gratis versie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Een account met de bevoegdheden van een globale beheerder.
* Een niet-beheerder test gebruiker met een wacht woord dat u kent, zoals *test User*.
    * Als u een gebruiker wilt maken, raadpleegt u [Quick Start: nieuwe gebruikers toevoegen aan Azure Active Directory](../add-users-azure-active-directory.md).
* Een test groep om te testen of de gebruiker van de niet-beheerder test een lid is, zoals *SSPR-test groep*.
    * Als u een groep wilt maken, raadpleegt u [een groep maken en leden toevoegen in azure Active Directory](../active-directory-groups-create-azure-portal.md).

## <a name="enable-self-service-password-reset"></a>Selfservice voor wachtwoordherstel inschakelen

[Dit proces als een video op YouTube weer geven](https://youtu.be/Pa0eyqjEjvQ)

1. Selecteer in het menu Azure Portal of op de **Start** pagina **Azure Active Directory** en kies vervolgens **wacht woord opnieuw instellen**.

1. Kies **geselecteerd**op de pagina **Eigenschappen** onder de optie voor **selfservice voor wachtwoord herstel is ingeschakeld**.
1. Kies **groep selecteren**en selecteer vervolgens uw test groep die is gemaakt als onderdeel van de sectie vereisten van dit artikel, zoals *SSPR-test groep*. Wanneer u klaar bent, selecteert u **Opslaan**.
1. Maak op de pagina **verificatie methoden** de volgende opties en kies vervolgens **Opslaan**:
    * Het aantal methoden dat is vereist om het wachtwoord opnieuw in te stellen: **1**
    * Methoden voor gebruikers:
        * **Mobiele app-code**
        * **E-mail**

    > [!div class="mx-imgBorder"]
    > ![Verificatie methoden kiezen voor SSPR][Authentication]

4. Maak op de pagina **registratie** de volgende opties en kies vervolgens **Opslaan**:
   * Vereisen dat gebruikers zich bij aanmelding registreren: **Ja**
   * Het aantal dagen instellen waarna gebruikers wordt gevraagd om de verificatiegegevens opnieuw te bevestigen: **365**

## <a name="test-self-service-password-reset"></a>Selfservice voor wachtwoord opnieuw instellen testen

Nu kunt u uw SSPR-configuratie testen met een test gebruiker die deel uitmaakt van de groep die u in de vorige sectie hebt geselecteerd, zoals *test User*. Aangezien Microsoft sterke verificatievereisten afdwingt voor Azure-beheerdersaccounts, kan het testen met een beheerdersaccount het resultaat beïnvloeden. Zie ons [artikel over wachtwoordbeleid](concept-sspr-policy.md) voor meer informatie over het wachtwoordbeleid voor beheerders.

1. Open een nieuw browservenster in de InPrivate- of incognitomodus en browse naar [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup).
2. Meld u aan met een test gebruiker die geen beheerder is, *zoals test User, en*Registreer uw telefoon voor authenticatie.
3. Zodra u klaar bent, selecteert u de gemarkeerde knop **goed** en sluit u het browser venster.
4. Open een nieuw browservenster in de InPrivate- of incognitomodus en browse naar [https://aka.ms/sspr](https://aka.ms/sspr).
5. Voer de gebruikers-ID van de niet-beheerder test gebruikers in, zoals *test User*, de tekens uit de captcha en selecteer **volgende**.
6. Volg de verificaties tappen om uw wacht woord opnieuw in te stellen.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de selfservice voor het opnieuw instellen van wacht woorden wilt uitschakelen, zoekt en selecteert u **Azure Active Directory** in de Azure Portal. Selecteer **eigenschappen** > **wacht woord opnieuw instellen**en kies vervolgens **geen** onder **self-service voor wacht woord opnieuw instellen is ingeschakeld**. Wanneer u klaar bent, selecteert u **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u self-service voor wachtwoord herstel voor uw Cloud gebruikers kunt configureren. Als u wilt weten hoe u een gedetailleerdere implementatie uitvoert, kunt u verder gaan met onze implementatiehandleiding.

> [!div class="nextstepaction"]
> [Selfservice voor wachtwoordherstel implementeren](howto-sspr-deployment.md)

[Authentication]: ./media/quickstart-sspr/sspr-authentication-methods.png "Azure Active Directory-verificatiemethoden die beschikbaar zijn en hoeveel er vereist zijn"

<!-- INTERNAL LINKS -->
[register-sspr]: ../user-help/active-directory-passwords-reset-register.md
[reset-password]: ../user-help/active-directory-passwords-update-your-own-password.md
