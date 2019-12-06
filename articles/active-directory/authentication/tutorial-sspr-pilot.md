---
title: Een Azure AD SSPR-pilot inschakelen
description: In deze zelfstudie schakelt u de selfservice voor wachtwoordherstel van Azure AD in voor een groep pilotgebruikers
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 08/16/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecdde4ef12c6991fad53f2286ee462fec31606ae
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74846280"
---
# <a name="tutorial-complete-an-azure-ad-self-service-password-reset-pilot-roll-out"></a>Zelfstudie: een pilot van een Azure AD-selfservice voor wachtwoordherstel uitrollen

In deze zelfstudie rolt u in uw organisatie een pilot uit van de Azure AD-selfservice voor wachtwoordherstel (SSPR) en test u deze met behulp van een niet-beheerdersaccount.

Het is belang rijk dat testen van de selfservice voor wachtwoord herstel worden uitgevoerd met niet-beheerders accounts. Microsoft beheert het wachtwoordherstelbeleid voor beheerdersaccounts en vereist het gebruik van sterkere verificatiemethoden. Dit beleid staat het gebruik van beveiligingsvragen en -antwoorden niet toe en vereist het gebruik van twee methoden voor het opnieuw instellen.

> [!div class="checklist"]
> * Selfservice voor wachtwoordherstel inschakelen
> * SSPR testen als een gebruiker

## <a name="prerequisites"></a>Vereisten

* Een globale-beheerdersaccount

## <a name="enable-self-service-password-reset"></a>Selfservice voor wachtwoordherstel inschakelen

1. Meld u aan bij [Azure Portal](https://portal.azure.com) met een globale-beheerdersaccount.
1. Blader naar **Azure Active Directory** en selecteer **Wachtwoord opnieuw instellen**.
1. Start een pilotgroep door de selfservice voor wachtwoordherstel in te schakelen voor een subset van gebruikers in uw organisatie.
   * Kies op de pagina **Eigenschappen** onder de optie **self-service voor wacht woord opnieuw instellen is ingeschakeld**, selecteer **geselecteerd**en kies een test groep.
      * Alleen leden van de specifieke Azure Active Directory-groep die u kiest, kunnen de SSPR-functionaliteit gebruiken. Het is raadzaam om een groep gebruikers te definiëren en deze instelling te gebruiken wanneer u deze functionaliteit implementeert voor het testen van een concept. Het nesten van beveiligingsgroepen wordt hier ondersteund.
      * Zorg ervoor dat de gebruikers in de groep die u hebt gekozen de juiste licentie hebben.
   * Klik op **Opslaan**.
1. Op de pagina **Verificatiemethoden**
   * Stel het **aantal methoden in dat moet worden ingesteld** op **1**
   * Kies welke **Methoden voor gebruikers** uw organisatie wil toestaan. Voor deze zelf studie schakelt u de selectie vakjes in om **e-mail**, **mobiele telefoon**, **zakelijke telefoon**, **mobiele app-melding**en **code voor mobiele apps**in te scha kelen.
   * Klik op **Opslaan**.
1. Op de pagina **Registratie**
   * Selecteer **Ja** bij **Vereisen dat gebruiker zich bij aanmelding registreren**.
   * Stel **Aantal dagen waarna gebruikers wordt gevraagd om de verificatiegegevens opnieuw te bevestigen** in op **180**.
   * Klik op **Opslaan**.
1. Op de pagina **Meldingen**
   * Stel **Gebruikers een melding tonen over het opnieuw instellen van hun wachtwoord** in op **Ja**.
   * Stel **Alle beheerders waarschuwen wanneer andere beheerders hun wachtwoord opnieuw instellen** in op **Ja**.
1. Op de pagina **Aanpassing**
   * Microsoft raadt aan **Helpdeskkoppeling aanpassen** in te stellen op **Ja** en in het veld **Aangepast e-mailadres of aangepaste URL van de helpdesk** een e-mailadres of webpagina-URL op te geven waar uw gebruikers meer hulp van uw organisatie kunnen krijgen.
   * Voor deze zelf studie gaan we de **koppeling naar de Help Desk aanpassen** ingesteld op **Nee**.

Selfservice voor wachtwoordherstel is nu geconfigureerd voor cloudgebruikers in uw pilotgroep.

## <a name="test-sspr-as-a-user"></a>SSPR testen als een gebruiker

Test de selfservice voor wachtwoordherstel met behulp van een testgebruiker die geen beheerder is en die lid is van uw pilotgroep. **Als u een account gebruikt waaraan beheerders rollen zijn toegewezen, kunnen de verificatie methoden en het nummer afwijken van wat u hebt geselecteerd als micro soft het beheerders beleid beheert.**

1. Open een nieuw browservenster om InPrivate- of incognitomodus.
1. Gebruik een testgebruiker om u met behulp van de registratieportal op [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) te registreren voor het zelf opnieuw instellen van een wachtwoord.
1. Ga als dezelfde testgebruiker naar de selfserviceportal voor wachtwoordherstel [​​ https://aka.ms/sspr](https://aka.ms/sspr) en probeer uw wachtwoord opnieuw in te stellen met behulp van de informatie die u in de vorige stap hebt verstrekt.
1. U zou uw wachtwoord met succes opnieuw moeten kunnen instellen.

## <a name="clean-up-resources"></a>Resources opschonen

Als u besluit dat u niet langer gebruik wilt maken van de functionaliteit die u als onderdeel van deze zelfstudie hebt geconfigureerd, moet u de volgende wijziging aanbrengen.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Blader naar **Azure Active Directory** en selecteer **Wachtwoord opnieuw instellen**.
1. Op de pagina **Eigenschappen** kiest u **Geen** onder **Selfservice voor wachtwoord opnieuw instellen is ingeschakeld**.
1. Klik op **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u de selfservice voor wachtwoordherstel ingeschakeld. Ga door naar de volgende zelfstudie om te zien hoe een on-premises Active Directory Domain Services-infrastructuur kan worden geïntegreerd met de selfservice voor wachtwoordherstel.

> [!div class="nextstepaction"]
> [On-premises write-backintegratie van selfservice voor wachtwoordherstel inschakelen](tutorial-enable-writeback.md)
