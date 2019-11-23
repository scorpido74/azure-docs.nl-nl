---
title: Self-service password reset for Windows - Azure Active Directory
description: How to enable self-service password reset using forgot password at the Windows login screen
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44e25efcb068fe51f05dbbde50e8a96da492a735
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381236"
---
# <a name="how-to-enable-password-reset-from-the-windows-login-screen"></a>How to: Enable password reset from the Windows login screen

For machines running Windows 7, 8, 8.1, and 10 you can enable users to reset their password at the Windows login screen. Users no longer have to find a device with a web browser to access the [SSPR portal](https://aka.ms/sspr).

![Example Windows 7 and 10 login screens with SSPR link shown](./media/howto-sspr-windows/windows-reset-password.png)

## <a name="general-limitations"></a>Algemene beperkingen

- Password reset is not currently supported from a Remote Desktop or from Hyper-V enhanced sessions.
- Deze functie werkt niet voor netwerken waarvoor 802.1x-netwerkverificatie is geïmplementeerd en de optie Onmiddellijk uitvoeren voor gebruiker zich aanmeldt. Voor netwerken waarvoor 802.1x-netwerkverificatie is geïmplementeerd, wordt het aanbevolen computerverificatie te gebruiken om deze functie in te schakelen.
- Hybrid Azure AD joined machines must have network connectivity line of sight to a domain controller to use the new password and update cached credentials.
- If using an image, prior to running sysprep ensure that the web cache is cleared for the built-in Administrator prior to performing the CopyProfile step. More information about this step can be found in the support article [Performance poor when using custom default user profile](https://support.microsoft.com/help/4056823/performance-issue-with-custom-default-user-profile).
- The following settings are known to interfere with the ability to use and reset passwords on Windows 10 devices
    - If Ctrl+Alt+Del is required by policy in versions of Windows 10 before v1809, **Reset password** will not work.
    - Als meldingen voor het vergrendelen van het scherm zijn uitgeschakeld, werkt **Wachtwoord opnieuw instellen** niet.
    - HideFastUserSwitching, indien ingesteld op ingeschakeld of 1
    - DontDisplayLastUserName, indien ingesteld op ingeschakeld of 1
    - NoLockScreen, indien ingesteld op ingeschakeld of 1
    - EnableLostMode, indien ingesteld op het apparaat
    - Explorer.exe is vervangen door een aangepaste shell
- The combination of the following specific three settings can cause this feature to not work.
    - Interactive logon: Do not require CTRL+ALT+DEL = Disabled
    - DisableLockScreenAppNotifications = 1 or Enabled
    - IsContentDeliveryPolicyEnforced = 1 or True

## <a name="windows-10-password-reset"></a>Windows 10 password reset

### <a name="windows-10-prerequisites"></a>Windows 10 prerequisites

- An administrator must enable Azure AD self-service password reset from the Azure portal.
- **Users must register for SSPR before using this feature**
- Network proxy requirements
   - Windows 10 devices 
       - Port 443 to `passwordreset.microsoftonline.com` and `ajax.aspnetcdn.com`
       - Windows 10 devices only support machine-level proxy configuration
- Run at least Windows 10, version April 2018 Update (v1803), and the devices must be either:
    - Azure AD joined
    - Hybrid Azure AD joined

### <a name="enable-for-windows-10-using-intune"></a>Enable for Windows 10 using Intune

Intune gebruiken om de configuratie te wijzigen zodat gebruikers het wachtwoord opnieuw kunnen instellen vanuit het aanmeldingsscherm, is de meest flexibele methode. Met Intune kunt u een wijziging van de configuratie implementeren voor een specifieke groep machines die u hebt gedefinieerd. Voor deze methode is een Intune-registratie van het apparaat vereist.

#### <a name="create-a-device-configuration-policy-in-intune"></a>Een beleid voor apparaatconfiguratie maken in Intune

1. Meld u aan bij [Azure Portal](https://portal.azure.com) en klik op **Intune**.
1. Maak een nieuw apparaatconfiguratieprofiel door te gaan naar **Apparaatconfiguratie** > **Profielen** > **Profiel maken**
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
1. Klik op **Maken**.
1. This policy can be assigned to specific users, devices, or groups. More information can be found in the article [Assign user and device profiles in Microsoft Intune](https://docs.microsoft.com/intune/device-profile-assign).

### <a name="enable-for-windows-10-using-the-registry"></a>Enable for Windows 10 using the Registry

1. Meld u met administratorreferenties aan op de Windows-pc
1. Voer **regedit** uit als administrator
1. Stel de volgende registersleutel in
   - `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      - `"AllowPasswordReset"=dword:00000001`

#### <a name="troubleshooting-windows-10-password-reset"></a>Troubleshooting Windows 10 password reset

Het auditlogboek van Azure AD bevat informatie over het IP-adres en het ClientType waarvoor het wachtwoord opnieuw is ingesteld.

![Example Windows 7 password reset in the Azure AD Audit log](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

When users reset their password from the login screen of a Windows 10 device, a low-privilege temporary account called `defaultuser1` is created. Dit account wordt gebruikt om het proces voor het opnieuw instellen van het wachtwoord te beveiligen. Het account zelf kent een willekeurig gegenereerd wachtwoord, wordt niet getoond bij het aanmelden op een apparaat en wordt automatisch verwijderd nadat de gebruiker het wachtwoord opnieuw heeft ingesteld. Multiple `defaultuser` profiles may exist but can be safely ignored.

## <a name="windows-7-8-and-81-password-reset"></a>Windows 7, 8, and 8.1 password reset

### <a name="windows-7-8-and-81-prerequisites"></a>Windows 7, 8, and 8.1 prerequisites

- An administrator must enable Azure AD self-service password reset from the Azure portal.
- **Users must register for SSPR before using this feature**
- Network proxy requirements
   - Windows 7, 8, and 8.1 devices
       - Port 443 to `passwordreset.microsoftonline.com`
- Patched Windows 7 or Windows 8.1 Operating System.
- TLS 1.2 enabled using the guidance found in [Transport Layer Security (TLS) registry settings](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12).
- If more than one 3rd party credential provider is enabled on your machine, users will see more than one user profile on the login screen.

> [!WARNING]
> TLS 1.2 must be enabled, not just set to auto negotiate

### <a name="install"></a>Installatie

1. Download the appropriate installer for the version of Windows you would like to enable.
   - Software is available on the Microsoft download center at [https://aka.ms/sspraddin](https://aka.ms/sspraddin)
1. Sign in to the machine where you would like to install, and run the installer.
1. After installation, a reboot is highly recommended.
1. After the reboot, at the login screen choose a user and click "Forgot password?" to initiate the password reset workflow.
1. Complete the workflow following the onscreen steps to reset your password.

![Example Windows 7 clicked "Forgot password?" SSPR flow](media/howto-sspr-windows/windows-7-sspr.png)

#### <a name="silent-installation"></a>Silent installation

- For silent install, use the command “msiexec /i SsprWindowsLogon.PROD.msi /qn”
- For silent uninstall, use the command “msiexec /x SsprWindowsLogon.PROD.msi /qn”

#### <a name="troubleshooting-windows-7-8-and-81-password-reset"></a>Troubleshooting Windows 7, 8, and 8.1 password reset

Events will be logged both on the machine and in Azure AD. Azure AD Events will include information about the IP address and ClientType where the password reset occurred.

![Example Windows 7 password reset in the Azure AD Audit log](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

If additional logging is required, a registry key on the machine can be changed to enable verbose logging. Enable verbose logging for troubleshooting purposes only.

`HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}`

- To enable verbose logging, create a `REG_DWORD: “EnableLogging”`, and set it to 1.
- To disable verbose logging, change the `REG_DWORD: “EnableLogging”` to 0.

## <a name="what-do-users-see"></a>Wat gebruikers zien

Now that you have configured password reset for your Windows devices, what changes for the user? Hoe weten ze dat ze hun wachtwoord opnieuw kunnen instellen via het aanmeldingsscherm?

![Example Windows 7 and 10 login screens with SSPR link shown](./media/howto-sspr-windows/windows-reset-password.png)

When users attempt to sign in, they now see a **Reset password** or **Forgot password** link that opens the self-service password reset experience at the login screen. Deze functionaliteit maakt het mogelijk dat gebruikers hun wachtwoord opnieuw instellen zonder dat ze een ander apparaat moeten gebruiken om toegang te krijgen tot een webbrowser.

Uw gebruikers vinden hulp voor het gebruik van deze functie in [Uw wachtwoord voor werk of school opnieuw instellen](../user-help/active-directory-passwords-update-your-own-password.md)

## <a name="next-steps"></a>Volgende stappen

[Plan authentication methods to allow](concept-authentication-methods.md)

[Configure Windows 10](https://docs.microsoft.com/windows/configuration/)
