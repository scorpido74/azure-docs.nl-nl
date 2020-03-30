---
title: Een beveiligingssleutel instellen als verificatiemethode - Azure AD
description: Uw pagina Beveiligingsgegevens (voorbeeld) instellen om uw identiteit te verifiëren om een FIDO2-beveiligingssleutel (Fast Identity Online) als verificatiemethode te gebruiken.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: librown
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: curtand
ms.openlocfilehash: ee868ded0dc3a46f2ec20d0141aa2576cbf71f45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062333"
---
# <a name="set-up-a-security-key-as-your-verification-method"></a>Een beveiligingssleutel instellen als verificatiemethode

U beveiligingssleutels gebruiken als een wachtwoordloze aanmeldingsmethode binnen uw organisatie. Een beveiligingssleutel is een fysiek apparaat dat wordt gebruikt met een unieke pincode om u aan te melden bij uw werk- of schoolaccount. Omdat beveiligingssleutels vereisen dat u het fysieke apparaat en iets dat alleen u kent, wordt het beschouwd als een sterkere verificatiemethode dan een gebruikersnaam en wachtwoord.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-key.md)]

>[!Note]
> Als u de optie beveiligingssleutel niet ziet, is het mogelijk dat uw organisatie u niet toestaat deze optie te gebruiken voor verificatie. In dit geval moet u een andere methode kiezen of contact opnemen met de helpdesk van uw organisatie voor meer hulp.

## <a name="security-verification-versus-password-reset-authentication"></a>Verificatie van beveiliging versus verificatie voor het opnieuw instellen van wachtwoorden

Beveiligingsinformatiemethoden worden gebruikt voor zowel tweestapsbeveiligingsverificatie als voor het opnieuw instellen van wachtwoorden. Niet elke methode kan echter voor beide worden gebruikt.

| Methode | Gebruikt voor |
| ------ | -------- |
| Authenticator-app | Tweeledige verificatie en verificatie voor het opnieuw instellen van het wachtwoord. |
| Sms-berichten | Tweeledige verificatie en verificatie voor het opnieuw instellen van het wachtwoord. |
| Telefoonoproep | Tweeledige verificatie en verificatie voor het opnieuw instellen van het wachtwoord. |
| Beveiligingssleutel | Tweeledige verificatie en verificatie voor het opnieuw instellen van het wachtwoord. |
| E-mailaccount | Alleen voor verificatie voor het opnieuw instellen van het wachtwoord. Voor tweeledige verificatie moet u een andere methode kiezen. |
| Beveiligingsvragen | Alleen voor verificatie voor het opnieuw instellen van het wachtwoord. Voor tweeledige verificatie moet u een andere methode kiezen. |

## <a name="what-is-a-security-key"></a>Wat is een beveiligingssleutel?

We ondersteunen momenteel verschillende ontwerpen en providers van beveiligingssleutels met behulp van de [FiDO(FIDO2)](https://fidoalliance.org/fido2/) wachtwoordloze verificatieprotocollen (Fast Identity Online). Met deze sleutels u zich aanmelden bij uw werk- of schoolaccount om toegang te krijgen tot de cloudbronnen van uw organisatie op een ondersteund apparaat en webbrowser.

Uw beheerder of uw organisatie zal u een beveiligingssleutel bieden als deze nodig is voor uw werk- of schoolaccount. Er zijn verschillende soorten beveiligingssleutels die u gebruiken, bijvoorbeeld een USB-sleutel die u aansluit op uw apparaat of een NFC-toets die u op een NFC-lezer tikt. U meer informatie vinden over uw beveiligingssleutel, inclusief het type dat het is, uit de documentatie van de fabrikant.

> [!Note]
> Als u geen FIDO2-beveiligingssleutel gebruiken, zijn er andere verificatiemethoden zonder wachtwoord die u gebruiken, zoals de Microsoft Authenticator-app of Windows Hello. Zie [Wat is de Microsoft Authenticator-app voor](user-help-auth-app-overview.md)meer informatie over de Microsoft Authenticator-app? Zie Overzicht van Windows Hello voor meer informatie over Windows [Hello.](https://www.microsoft.com/windows/windows-hello)

## <a name="before-you-begin"></a>Voordat u begint

Voordat u uw beveiligingssleutel registreren, moet het volgende het volgende zijn:

- Uw beheerder heeft deze functie ingeschakeld voor gebruik binnen uw organisatie.

- U bevindt zich op een apparaat waarop de Windows 10 May 2019 Update wordt uitgevoerd en gebruikt een ondersteunde browser.

- U hebt een fysieke beveiligingssleutel goedgekeurd door uw beheerder of uw organisatie. Uw beveiligingssleutel moet zowel FIDO2- als Microsoft-compatibel zijn. Als u vragen hebt over uw beveiligingssleutel en of deze compatibel is, neemt u contact op met de helpdesk van uw organisatie.

## <a name="register-your-security-key"></a>Uw beveiligingssleutel registreren

U moet uw beveiligingssleutel maken en deze een unieke pincode geven voordat u zich met de sleutel aanmelden bij uw werk- of schoolaccount. Je hebt mogelijk maximaal 10 sleutels geregistreerd bij je account. 

1. Ga naar de pagina https://myprofile.microsoft.com Mijn **profiel** op en meld je aan als je dat nog niet hebt gedaan.

2. Selecteer **Beveiligingsgegevens**, selecteer **Methode toevoegen**en selecteer vervolgens de **beveiligingssleutel** in de vervolgkeuzelijst **Een methode** toevoegen.

    ![Het vakmethode toevoegen, met de beveiligingssleutel geselecteerd](media/security-info/security-info-security-key-add-method.png)

3. Selecteer **Toevoegen**en selecteer vervolgens het type beveiligingssleutel dat u hebt, een **USB-apparaat** of **NFC-apparaat.**

    ![Kies of u een USB- of NFC-type beveiligingssleutel hebt](media/security-info/security-info-security-key-choose-type.png)

    > [!Note]
    > Als u niet zeker weet welk type beveiligingssleutel u hebt, raadpleegt u de documentatie van de fabrikant. Als u niet zeker bent van de fabrikant, neemt u contact op met de helpdesk van uw organisatie voor hulp.

4. Zorg ervoor dat uw beveiligingssleutel fysiek beschikbaar is en selecteer vervolgens in het vak **Beveiligingssleutel** **volgende**.

    ![Registratievak beveiligingssleutel](media/security-info/security-info-security-key-start-setup.png)

    Er verschijnt een nieuw vak om u te helpen bij het instellen van uw nieuwe aanmeldingsmethode.

5. Selecteer **Volgende**in het vak Uw **nieuwe aanmeldingsmethode** instellen en selecteer vervolgens:

    - Als uw beveiligingssleutel een USB-apparaat is, plaatst u uw beveiligingssleutel in de USB-poort van uw apparaat.

    - Als uw beveiligingssleutel een NFC-apparaat is, tikt u op uw beveiligingssleutel naar uw lezer.

6. Typ uw unieke pincode voor beveiligingssleutels in het **windows-beveiligingsvak** en selecteer **OK**.

    Je gaat terug naar het vak **Je nieuwe aanmeldingsmethode instellen.**

7. Selecteer **Volgende**.

8. Ga terug naar de pagina **Beveiligingsgegevens,** typ een naam die u later herkent voor uw nieuwe beveiligingssleutel en selecteer **Volgende**.

    ![Pagina beveiligingsinformatie, een naam van uw beveiligingssleutel](media/security-info/security-info-security-key-name.png)

    Uw beveiligingssleutel is geregistreerd en klaar voor u om te gebruiken voor het aanmelden bij uw werk- of schoolaccount.

9. Selecteer **Gereed** om het vak **beveiligingssleutel te** sluiten.

    De pagina **Beveiligingsgegevens** wordt bijgewerkt met uw beveiligingssleutelgegevens.

    ![Pagina beveiligingsinformatie, met alle geregistreerde methoden weergegeven](media/security-info/security-info-security-key-configured.png)

## <a name="delete-a-security-key-from-your-security-info"></a>Een beveiligingssleutel uit uw beveiligingsgegevens verwijderen

Als u een beveiligingssleutel niet meer wilt gebruiken of niet meer wilt gebruiken, u de sleutel uit uw beveiligingsgegevens verwijderen. Hoewel hiermee wordt voorkomen dat de beveiligingssleutel wordt gebruikt met uw werk- of schoolaccount, blijft de beveiligingssleutel uw gegevens en referentiegegevens opslaan. Als u uw gegevens en referentiegegevens wilt verwijderen uit de beveiligingssleutel zelf, moet u de instructies volgen in het gedeelte [Beveiligingssleutel](#reset-your-security-key) van Microsoft opnieuw instellen in dit artikel.

1. Selecteer de koppeling **Verwijderen** in de beveiligingssleutel die u wilt verwijderen.

2. Selecteer **Ok** in het vak **Beveiligingssleutel verwijderen.**

    Uw beveiligingssleutel wordt verwijderd en u deze niet meer gebruiken om u aan te melden bij uw werk- of schoolaccount.

>[!Important]
>Als u per ongeluk een beveiligingssleutel verwijdert, u deze opnieuw registreren met behulp van de instructies in het gedeelte [Hoe u uw beveiligingssleutelgedeelte](#register-your-security-key) van dit artikel registreren.

## <a name="manage-your-security-key-settings-from-windows-settings"></a>Uw instellingen voor beveiligingssleutels beheren vanuit Windows-instellingen

U de instellingen van uw beveiligingssleutel beheren vanuit de **Windows-app Instellingen,** inclusief het opnieuw instellen van uw beveiligingssleutel en het maken van een nieuwe pincode voor beveiligingssleutels.

### <a name="reset-your-security-key"></a>Uw beveiligingssleutel opnieuw instellen

Als u alle accountgegevens wilt verwijderen die zijn opgeslagen op uw fysieke beveiligingssleutel, moet u de sleutel terugsturen naar de standaardinstellingen van de fabriek. Als u uw beveiligingssleutel opnieuw instelt, wordt alles van de sleutel verwijderd, zodat u opnieuw beginnen.

>[!IMPORTANT]
>Als u uw beveiligingssleutel opnieuw instelt, wordt alles verwijderd, van de sleutel, waardoor deze wordt ingesteld op fabrieksstandaardinstellingen.
>
> **Alle gegevens en referenties worden gewist.**

#### <a name="to-reset-your-security-key"></a>Uw beveiligingssleutel opnieuw instellen

1. Open de app Windows-instellingen, selecteer **Accounts,** selecteer **Aanmeldingsopties,** selecteer **Beveiligingssleutel**en selecteer **Vervolgens Beheren**.

2. Plaats uw beveiligingssleutel in de USB-poort of tik op uw NFC-lezer om uw identiteit te verifiëren.

3. Volg de instructies op het scherm op basis van uw specifieke fabrikant van beveiligingssleutels. Als uw sleutelfabrikant niet in de instructies op het scherm staat, raadpleegt u de site van de fabrikant voor meer informatie.

4. Selecteer **Sluiten** om het scherm **Beheren te** sluiten.

### <a name="create-a-new-security-key-pin"></a>Een nieuwe pincode voor beveiligingssleutels maken

U een nieuwe pincode voor de beveiliging van uw beveiligingssleutel maken.

#### <a name="to-create-a-new-security-key-pin"></a>Een nieuwe pincode voor beveiligingssleutels maken

1. Open de app Windows-instellingen, selecteer **Accounts,** selecteer **Aanmeldingsopties,** selecteer **Beveiligingssleutel**en selecteer **Vervolgens Beheren**.

2. Plaats uw beveiligingssleutel in de USB-poort of tik op uw NFC-lezer om uw identiteit te verifiëren.
3. Selecteer **Toevoegen** in het **pingebied beveiligingssleutel,** typ en bevestig uw nieuwe pincode voor beveiligingssleutels en selecteer **OK**.

     De beveiligingssleutel wordt bijgewerkt met de nieuwe pincode voor beveiligingssleutels voor gebruik met uw werk- of schoolaccount. Als u besluit uw pincode opnieuw te wijzigen, u de knop **Wijzigen** selecteren.
4. Selecteer **Sluiten** om het scherm **Beheren te** sluiten.

## <a name="additional-security-info-methods"></a>Aanvullende beveiligingsinformatiemethoden

Om een beveiligingssleutel te registreren, moet u ten minste één extra beveiligingsverificatiemethode hebben geregistreerd. Zie de [sectie Overzicht](security-info-add-update-methods-overview.md) voor meer informatie. 

## <a name="next-steps"></a>Volgende stappen

- Lees voor meer informatie over verificatiemethoden zonder wachtwoord de [Openbare Preview van FIDO2-beveiligingssleutels van Microsoft, waarmee u een blog zonder wachtwoord inloggen](https://www.onmsft.com/news/microsofts-azure-ad-begins-public-preview-of-fido2-security-keys-enabling-passwordless-logins) of de Microsoft [Authenticator-app lezen?](user-help-auth-app-overview.md) en [windows hello-overzichtsartikelen.](https://www.microsoft.com/windows/windows-hello)

- Voor meer gedetailleerde informatie over [microsoft-compatibele beveiligingssleutels](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/microsoft-compatible-security-key).

- Stel uw wachtwoord opnieuw in als u dit hebt verloren of bent vergeten, uit het [Portal voor wachtwoordherstel](https://passwordreset.microsoftonline.com/) of volg de stappen in het artikel [Uw werk- of schoolaccount wachtwoord opnieuw instellen](active-directory-passwords-update-your-own-password.md).

- Krijg tips voor het oplossen van problemen en hulp met problemen met aanmelden in het artikel [U kunt niet aanmelden bij uw Microsoft-account](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
