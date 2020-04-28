---
title: Self-service voor wachtwoord herstel voor Windows-Azure Active Directory
description: Self-service voor wachtwoord herstel inschakelen met het wacht woord verg eten in het Windows-aanmeldings scherm
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4f08161daf1d9c1a4431d9e3fba3ca741d88b16
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80743341"
---
# <a name="how-to-enable-password-reset-from-the-windows-login-screen"></a>Procedure: wacht woord opnieuw instellen inschakelen vanuit het Windows-aanmeldings scherm

Voor computers met Windows 7, 8, 8,1 en 10 kunt u gebruikers in staat stellen hun wacht woord opnieuw in te stellen in het Windows-aanmeldings scherm. Gebruikers hoeven niet langer een apparaat met een webbrowser te vinden om toegang te krijgen tot de [SSPR-Portal](https://aka.ms/sspr).

![Voor beeld van een Windows 7-en 10-Aanmeldings scherm met een SSPR-koppeling](./media/howto-sspr-windows/windows-reset-password.png)

## <a name="general-limitations"></a>Algemene beperkingen

- Het opnieuw instellen van het wacht woord wordt momenteel niet ondersteund vanuit een Extern bureaublad of via Hyper-V-uitgebreide sessies.
- Sommige externe referentie providers zijn bekend om problemen met deze functie te veroorzaken.
- Het uitschakelen van UAC via wijziging van de [register sleutel EnableLUA](https://docs.microsoft.com/openspecs/windows_protocols/ms-gpsb/958053ae-5397-4f96-977f-b7700ee461ec) is bekend bij het veroorzaken van problemen.
- Deze functie werkt niet voor netwerken met 802.1 x-netwerk verificatie geïmplementeerd en de optie ' direct voor gebruikers aanmelding uitvoeren '. Voor netwerken waarvoor 802.1x-netwerkverificatie is geïmplementeerd, wordt het aanbevolen computerverificatie te gebruiken om deze functie in te schakelen.
- Hybride Azure AD-computers moeten over een netwerk verbinding beschikken om het nieuwe wacht woord te kunnen gebruiken en referenties in de cache op te slaan.
- Als u een installatie kopie gebruikt voordat u Sysprep uitvoert, moet u ervoor zorgen dat de webcache voor de ingebouwde beheerder is gewist voordat u de stap CopyProfile uitvoert. Meer informatie over deze stap vindt u in de ondersteunings artikelen [verslechte prestaties bij gebruik van aangepast standaard gebruikers profiel](https://support.microsoft.com/help/4056823/performance-issue-with-custom-default-user-profile).
- De volgende instellingen zijn bekend om te voor komen dat wacht woorden op Windows 10-apparaten kunnen worden gebruikt en opnieuw worden ingesteld
    - Als Ctrl + Alt + del vereist is voor het beleid in versies van Windows 10 vóór v1809, werkt **wacht woord opnieuw instellen** niet.
    - Als meldingen voor het vergrendelen van het scherm zijn uitgeschakeld, werkt **Wachtwoord opnieuw instellen** niet.
    - HideFastUserSwitching, indien ingesteld op ingeschakeld of 1
    - DontDisplayLastUserName, indien ingesteld op ingeschakeld of 1
    - NoLockScreen, indien ingesteld op ingeschakeld of 1
    - EnableLostMode, indien ingesteld op het apparaat
    - Explorer.exe is vervangen door een aangepaste shell
- De combi natie van de volgende drie instellingen kan ervoor zorgen dat deze functie niet werkt.
    - Interactieve aanmelding: CTRL + ALT + DEL = disabled is uitgeschakeld
    - DisableLockScreenAppNotifications = 1 of ingeschakeld
    - Windows SKU is niet Home of Professional Edition

## <a name="windows-10-password-reset"></a>Windows 10-wacht woord opnieuw instellen

### <a name="windows-10-prerequisites"></a>Windows 10-vereisten

- Een beheerder moet Azure AD selfservice voor wachtwoord herstel inschakelen vanuit het Azure Portal.
- **Gebruikers moeten zich registreren voor SSPR voordat ze deze functie gebruiken**
- Vereisten voor netwerk proxy
   - Windows 10-apparaten 
       - Poort 443 tot `passwordreset.microsoftonline.com` en`ajax.aspnetcdn.com`
       - Windows 10-apparaten ondersteunen alleen proxy configuratie op computer niveau
- Voer ten minste Windows 10, versie april 2018 update (v1803) uit en de apparaten moeten zijn:
    - Neemt deel aan Azure AD
    - lid is van hybride Azure Active Directory

### <a name="enable-for-windows-10-using-intune"></a>Inschakelen voor Windows 10 met intune

Intune gebruiken om de configuratie te wijzigen zodat gebruikers het wachtwoord opnieuw kunnen instellen vanuit het aanmeldingsscherm, is de meest flexibele methode. Met Intune kunt u een wijziging van de configuratie implementeren voor een specifieke groep machines die u hebt gedefinieerd. Voor deze methode is een Intune-registratie van het apparaat vereist.

#### <a name="create-a-device-configuration-policy-in-intune"></a>Een beleid voor apparaatconfiguratie maken in Intune

1. Meld u aan bij [Azure Portal](https://portal.azure.com) en klik op **Intune**.
1. Een nieuw configuratie profiel voor **het apparaat maken** > door te gaan naar apparaatconfiguratie**profielen** > **profiel maken**
   - Geef een beschrijvende naam op voor het profiel
   - Geef eventueel een duidelijke beschrijving van het profiel op
   - Platform **Windows 10 en hoger**
   - Profieltype **Aangepast**
1. **Instellingen** configureren
   - **Voeg de volgende OMA-URI-instelling toe** om de koppeling Wachtwoord opnieuw instellen in te schakelen
      - Geef een beschrijvende naam op om uit te leggen wat de instelling doet
      - Geef eventueel een duidelijke beschrijving van de instelling
      - **OMA-URI** ingesteld op `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset`
      - **Gegevenstype** ingesteld op **Geheel getal**
      - **Waarde** ingesteld op **1**
      - Klik op **OK**
   - Klik op **OK**
1. Klik op **maken**
1. Dit beleid kan worden toegewezen aan specifieke gebruikers, apparaten of groepen. Meer informatie vindt u in het artikel [gebruikers-en apparaatprofielen toewijzen in Microsoft intune](https://docs.microsoft.com/intune/device-profile-assign).

### <a name="enable-for-windows-10-using-the-registry"></a>Inschakelen voor Windows 10 met behulp van het REGI ster

1. Meld u met administratorreferenties aan op de Windows-pc
1. Voer **regedit** uit als administrator
1. Stel de volgende registersleutel in
   - `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      - `"AllowPasswordReset"=dword:00000001`

#### <a name="troubleshooting-windows-10-password-reset"></a>Problemen oplossen met Windows 10 wacht woord opnieuw instellen

Het auditlogboek van Azure AD bevat informatie over het IP-adres en het ClientType waarvoor het wachtwoord opnieuw is ingesteld.

![Voor beeld van een Windows 7-wacht woord opnieuw instellen in het Azure AD-controle logboek](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Wanneer gebruikers hun wacht woord opnieuw instellen vanuit het aanmeldings scherm van een Windows 10-apparaat, `defaultuser1` wordt een tijdelijke account met beperkte bevoegdheden gemaakt. Dit account wordt gebruikt om het proces voor het opnieuw instellen van het wachtwoord te beveiligen. Het account zelf heeft een wille keurig gegenereerd wacht woord, wordt niet weer gegeven voor het aanmelden van apparaten en wordt automatisch verwijderd nadat de gebruiker het wacht woord opnieuw heeft ingesteld. Er `defaultuser` kunnen meerdere profielen bestaan, maar deze kunnen veilig worden genegeerd.

## <a name="windows-7-8-and-81-password-reset"></a>Windows 7, 8 en 8,1 wacht woord opnieuw instellen

### <a name="windows-7-8-and-81-prerequisites"></a>Vereisten voor Windows 7, 8 en 8,1

- Een beheerder moet Azure AD selfservice voor wachtwoord herstel inschakelen vanuit het Azure Portal.
- **Gebruikers moeten zich registreren voor SSPR voordat ze deze functie gebruiken**
- Vereisten voor netwerk proxy
   - Windows 7-, 8-en 8,1-apparaten
       - Poort 443 tot`passwordreset.microsoftonline.com`
- Een patch uitgevoerd voor Windows 7-of Windows 8,1-besturings systeem.
- TLS 1,2 ingeschakeld met behulp van de richt lijnen die zijn gevonden in de [register instellingen van Transport Layer Security (TLS)](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12).
- Als er meer dan één externe referentie provider op uw computer is ingeschakeld, zien gebruikers meer dan één gebruikers profiel in het aanmeldings scherm.

> [!WARNING]
> TLS 1,2 moet zijn ingeschakeld, niet alleen ingesteld op automatisch onderhandelen

### <a name="install"></a>Installeren

1. Down load het juiste installatie programma voor de versie van Windows die u wilt inschakelen.
   - Software is beschikbaar in het micro soft Download centrum op[https://aka.ms/sspraddin](https://aka.ms/sspraddin)
1. Meld u aan bij de computer waarop u wilt installeren en voer het installatie programma uit.
1. Na de installatie wordt u ten zeerste aangeraden opnieuw op te starten.
1. Nadat het systeem opnieuw is opgestart, kiest u in het aanmeldings scherm een gebruiker en klikt u op wacht woord verg eten? om de werk stroom voor het opnieuw instellen van wacht woorden te initiëren.
1. Voltooi de werk stroom volgens de stappen op het scherm om uw wacht woord opnieuw in te stellen.

![Voor beeld van Windows 7 op ' wacht woord verg eten? ' SSPR-stroom](media/howto-sspr-windows/windows-7-sspr.png)

#### <a name="silent-installation"></a>Installatie op de achtergrond

- Gebruik de opdracht ' msiexec/i SsprWindowsLogon. PROD. msi/qn ' voor een installatie op de achtergrond.
- Voor onachtergrond verwijderen gebruikt u de opdracht "msiexec/x SsprWindowsLogon. PROD. msi/qn"

#### <a name="troubleshooting-windows-7-8-and-81-password-reset"></a>Problemen met het opnieuw instellen van wacht woorden in Windows 7, 8 en 8,1 oplossen

Gebeurtenissen worden geregistreerd op de computer en in azure AD. Azure AD-gebeurtenissen bevatten informatie over het IP-adres en de ClientType waar het wacht woord opnieuw wordt ingesteld.

![Voor beeld van een Windows 7-wacht woord opnieuw instellen in het Azure AD-controle logboek](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Als aanvullende logboek registratie vereist is, kan een register sleutel op de computer worden gewijzigd om uitgebreide logboek registratie in te scha kelen. Schakel uitgebreide logboek registratie in voor het oplossen van problemen.

`HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}`

- Als u uitgebreide logboek registratie wilt inschakelen, `REG_DWORD: "EnableLogging"`maakt u een en stelt u deze in op 1.
- Als u uitgebreide logboek registratie wilt uitschakelen, `REG_DWORD: "EnableLogging"` wijzigt u in 0.

## <a name="what-do-users-see"></a>Wat gebruikers zien

Nu u het opnieuw instellen van het wacht woord voor uw Windows-apparaten hebt geconfigureerd, wat verandert de gebruiker? Hoe weten ze dat ze hun wachtwoord opnieuw kunnen instellen via het aanmeldingsscherm?

![Voor beeld van een Windows 7-en 10-Aanmeldings scherm met een SSPR-koppeling](./media/howto-sspr-windows/windows-reset-password.png)

Wanneer gebruikers zich proberen aan te melden, zien ze nu de koppeling **wacht woord opnieuw instellen** of **verg eten wacht woord** waarmee de self-service voor het opnieuw instellen van wacht woorden wordt geopend in het aanmeldings scherm. Deze functionaliteit maakt het mogelijk dat gebruikers hun wachtwoord opnieuw instellen zonder dat ze een ander apparaat moeten gebruiken om toegang te krijgen tot een webbrowser.

Uw gebruikers vinden hulp voor het gebruik van deze functie in [Uw wachtwoord voor werk of school opnieuw instellen](../user-help/active-directory-passwords-update-your-own-password.md)

## <a name="next-steps"></a>Volgende stappen

[Verificatie methoden plannen om toe te staan](concept-authentication-methods.md)

[Windows 10 configureren](https://docs.microsoft.com/windows/configuration/)
