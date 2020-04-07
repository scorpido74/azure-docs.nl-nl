---
title: Selfservice wachtwoord opnieuw instellen voor Windows - Azure Active Directory
description: Self-service wachtwoord opnieuw instellen inschakelen met vergeten wachtwoord op het Windows-inlogscherm
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
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743341"
---
# <a name="how-to-enable-password-reset-from-the-windows-login-screen"></a>How to: Wachtwoordopnieuw instellen inschakelen vanaf het Windows-aanmeldingsscherm

Voor machines met Windows 7, 8, 8.1 en 10 u gebruikers in staat stellen hun wachtwoord opnieuw in te stellen op het Windows-inlogscherm. Gebruikers hoeven niet langer een apparaat met een webbrowser te vinden om toegang te krijgen tot de [SSPR-portal.](https://aka.ms/sspr)

![Voorbeeld Windows 7 en 10 inlogschermen met SSPR-koppeling weergegeven](./media/howto-sspr-windows/windows-reset-password.png)

## <a name="general-limitations"></a>Algemene beperkingen

- Het opnieuw instellen van wachtwoorden wordt momenteel niet ondersteund vanaf een extern bureaublad of van uitgebreide hyper-v-sessies.
- Van sommige leveranciers van referenties van derden is bekend dat ze problemen veroorzaken met deze functie.
- Het uitschakelen van UAC via wijziging van [de Registersleutel van EnableLUA](https://docs.microsoft.com/openspecs/windows_protocols/ms-gpsb/958053ae-5397-4f96-977f-b7700ee461ec) is bekend dat het problemen veroorzaakt.
- Deze functie werkt niet voor netwerken met 802.1x netwerkverificatie geïmplementeerd en de optie "Uitvoeren vlak voor de gebruiker aanmelding". Voor netwerken waarvoor 802.1x-netwerkverificatie is geïmplementeerd, wordt het aanbevolen computerverificatie te gebruiken om deze functie in te schakelen.
- Hybride Azure AD-aangesloten machines moeten een netwerkverbindingslijn hebben voor een domeincontroller om het nieuwe wachtwoord te gebruiken en referenties in de cache bij te werken.
- Als u een afbeelding gebruikt, moet u ervoor zorgen dat de webcache wordt gewist voor de ingebouwde administrator voordat u de stap CopyProfile uitvoert. Meer informatie over deze stap vindt u in het ondersteuningsartikel [Prestaties slecht bij het gebruik van aangepast standaardgebruikersprofiel.](https://support.microsoft.com/help/4056823/performance-issue-with-custom-default-user-profile)
- Van de volgende instellingen is bekend dat ze de mogelijkheid om wachtwoorden op Windows 10-apparaten te gebruiken en opnieuw in te stellen, verstoren
    - Als Ctrl+Alt+Del vereist is door het beleid in versies van Windows 10 v1809, werkt **het wachtwoord opnieuw instellen** niet.
    - Als meldingen voor het vergrendelen van het scherm zijn uitgeschakeld, werkt **Wachtwoord opnieuw instellen** niet.
    - HideFastUserSwitching, indien ingesteld op ingeschakeld of 1
    - DontDisplayLastUserName, indien ingesteld op ingeschakeld of 1
    - NoLockScreen, indien ingesteld op ingeschakeld of 1
    - EnableLostMode, indien ingesteld op het apparaat
    - Explorer.exe is vervangen door een aangepaste shell
- De combinatie van de volgende specifieke drie instellingen kan ervoor zorgen dat deze functie niet werkt.
    - Interactieve aanmelding: Ctrl+ALT+DEL = Uitgeschakeld
    - LockScreenAppMeldingen uitschakelen = 1 of ingeschakeld
    - Windows SKU is geen thuis- of professionele editie

## <a name="windows-10-password-reset"></a>Windows 10 wachtwoord opnieuw instellen

### <a name="windows-10-prerequisites"></a>Vereisten voor Windows 10

- Een beheerder moet azure AD self-service password reset inschakelen vanuit de Azure-portal.
- **Gebruikers moeten zich registreren voor SSPR voordat ze deze functie gebruiken**
- Vereisten voor netwerkproxy
   - Windows 10-apparaten 
       - Poort 443 `passwordreset.microsoftonline.com` aan en`ajax.aspnetcdn.com`
       - Windows 10-apparaten ondersteunen alleen proxyconfiguratie op machineniveau
- Voer ten minste Windows 10, versie april 2018 Update (v1803) uit en de apparaten moeten:
    - Neemt deel aan Azure AD
    - lid is van hybride Azure Active Directory

### <a name="enable-for-windows-10-using-intune"></a>Inschakelen voor Windows 10 met Intune

Intune gebruiken om de configuratie te wijzigen zodat gebruikers het wachtwoord opnieuw kunnen instellen vanuit het aanmeldingsscherm, is de meest flexibele methode. Met Intune kunt u een wijziging van de configuratie implementeren voor een specifieke groep machines die u hebt gedefinieerd. Voor deze methode is een Intune-registratie van het apparaat vereist.

#### <a name="create-a-device-configuration-policy-in-intune"></a>Een beleid voor apparaatconfiguratie maken in Intune

1. Meld u aan bij [Azure Portal](https://portal.azure.com) en klik op **Intune**.
1. Een nieuw apparaatconfiguratieprofiel maken door naar **Apparaatconfiguratieprofielen** > **Profiles** > **Profiel maken**
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
      - Klik **op OK**
   - Klik **op OK**
1. Klik **op Maken**
1. Dit beleid kan worden toegewezen aan specifieke gebruikers, apparaten of groepen. Meer informatie is te vinden in het artikel [Gebruikers- en apparaatprofielen toewijzen in Microsoft Intune.](https://docs.microsoft.com/intune/device-profile-assign)

### <a name="enable-for-windows-10-using-the-registry"></a>Inschakelen voor Windows 10 met behulp van het register

1. Meld u met administratorreferenties aan op de Windows-pc
1. Voer **regedit** uit als administrator
1. Stel de volgende registersleutel in
   - `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      - `"AllowPasswordReset"=dword:00000001`

#### <a name="troubleshooting-windows-10-password-reset"></a>Problemen met Windows 10-wachtwoordopnieuw instellen

Het auditlogboek van Azure AD bevat informatie over het IP-adres en het ClientType waarvoor het wachtwoord opnieuw is ingesteld.

![Voorbeeld van windows 7-wachtwoordreset in het Azure AD-controlelogboek](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Wanneer gebruikers hun wachtwoord opnieuw instellen vanaf het inlogscherm van een Windows `defaultuser1` 10-apparaat, wordt een tijdelijk account met lage bevoegdheden aangemaakt. Dit account wordt gebruikt om het proces voor het opnieuw instellen van het wachtwoord te beveiligen. Het account zelf heeft een willekeurig gegenereerd wachtwoord, wordt niet weergegeven voor het aanmelden van het apparaat en wordt automatisch verwijderd nadat de gebruiker zijn wachtwoord opnieuw heeft ingesteld. Er `defaultuser` kunnen meerdere profielen bestaan, maar kunnen veilig worden genegeerd.

## <a name="windows-7-8-and-81-password-reset"></a>Windows 7, 8 en 8.1 wachtwoord opnieuw instellen

### <a name="windows-7-8-and-81-prerequisites"></a>Windows 7, 8 en 8.1 vereisten

- Een beheerder moet azure AD self-service password reset inschakelen vanuit de Azure-portal.
- **Gebruikers moeten zich registreren voor SSPR voordat ze deze functie gebruiken**
- Vereisten voor netwerkproxy
   - Windows 7,8 en 8.1-apparaten
       - Poort 443 aan`passwordreset.microsoftonline.com`
- Gepatchte Windows 7 of Windows 8.1 Besturingssysteem.
- TLS 1.2 is ingeschakeld met behulp van de richtlijnen in [tls-registerinstellingen (Transport Layer Security).](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12)
- Als er meer dan één externe referentieprovider is ingeschakeld op uw machine, zien gebruikers meer dan één gebruikersprofiel op het inlogscherm.

> [!WARNING]
> TLS 1.2 moet worden ingeschakeld, niet alleen ingesteld op automatisch onderhandelen

### <a name="install"></a>Installeren

1. Download het juiste installatieprogramma voor de versie van Windows die u wilt inschakelen.
   - Software is beschikbaar in het Microsoft-downloadcentrum op[https://aka.ms/sspraddin](https://aka.ms/sspraddin)
1. Meld u aan bij de machine waar u de installatieinstallatie wilt installeren en voer de installatieprogramma uit.
1. Na de installatie wordt een reboot ten zeerste aanbevolen.
1. Kies na het opnieuw opstarten een gebruiker en klik op 'Wachtwoord vergeten?' om de workflow voor het opnieuw instellen van wachtwoorden te starten.
1. Voltooi de werkstroom na de stappen op het scherm om uw wachtwoord opnieuw in te stellen.

![Voorbeeld Windows 7 klikte op 'Wachtwoord vergeten?' SSPR-stroom](media/howto-sspr-windows/windows-7-sspr.png)

#### <a name="silent-installation"></a>Installatie op de achtergrond

- Voor stille installatie gebruikt u de opdracht "msiexec /i SsprWindowsLogon.PROD.msi /qn"
- Voor stille verwijderen, gebruik de opdracht "msiexec /x SsprWindowsLogon.PROD.msi /qn"

#### <a name="troubleshooting-windows-7-8-and-81-password-reset"></a>Problemen met Windows 7, 8 en 8.1 wachtwoordopnieuw instellen

Gebeurtenissen worden zowel op de machine als in Azure AD geregistreerd. Azure AD-gebeurtenissen bevatten informatie over het IP-adres en ClientType waar het wachtwoord opnieuw is ingesteld.

![Voorbeeld van windows 7-wachtwoordreset in het Azure AD-controlelogboek](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Als extra logging vereist is, kan een registersleutel op de machine worden gewijzigd om verbose logging in te schakelen. Schakel alleen verboselogging in voor probleemoplossingsdoeleinden.

`HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}`

- Als u verboselogging `REG_DWORD: "EnableLogging"`wilt inschakelen, maakt u een , en stelt u deze in op 1.
- Als u de verbose-logboekregistratie wilt uitschakelen, wijzigt u de `REG_DWORD: "EnableLogging"` 0.

## <a name="what-do-users-see"></a>Wat gebruikers zien

Wat verandert er voor de gebruiker nu u een wachtwoordreset voor uw Windows-apparaten hebt geconfigureerd? Hoe weten ze dat ze hun wachtwoord opnieuw kunnen instellen via het aanmeldingsscherm?

![Voorbeeld Windows 7 en 10 inlogschermen met SSPR-koppeling weergegeven](./media/howto-sspr-windows/windows-reset-password.png)

Wanneer gebruikers zich proberen aan te melden, zien ze nu een **wachtwoord opnieuw instellen** of **Wachtwoordkoppeling vergeten** waarmee de selfservice-wachtwoordreset-ervaring op het inlogscherm wordt geopend. Deze functionaliteit maakt het mogelijk dat gebruikers hun wachtwoord opnieuw instellen zonder dat ze een ander apparaat moeten gebruiken om toegang te krijgen tot een webbrowser.

Uw gebruikers vinden hulp voor het gebruik van deze functie in [Uw wachtwoord voor werk of school opnieuw instellen](../user-help/active-directory-passwords-update-your-own-password.md)

## <a name="next-steps"></a>Volgende stappen

[Verificatiemethoden plannen om toe te staan](concept-authentication-methods.md)

[Windows 10 configureren](https://docs.microsoft.com/windows/configuration/)
