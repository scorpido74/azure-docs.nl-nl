---
title: Azure MFA NPS-extensie oplossen - Azure Active Directory
description: Hulp bij het oplossen van problemen met de NPS-extensie voor Azure Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3462ada0864d2d8321b1936e94f947c55c754879
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294506"
---
# <a name="resolve-error-messages-from-the-nps-extension-for-azure-multi-factor-authentication"></a>Resolve error messages from the NPS extension for Azure Multi-Factor Authentication (Foutberichten van de NPS-extensie voor Azure Multi-Factor Authentication oplossen)

Als u fouten tegenkomt met de NPS-extensie voor Azure Multi-Factor Authentication, gebruikt u dit artikel om sneller een oplossing te bereiken. NPS-extensielogboeken worden gevonden in Logboeken onder **Netwerkbeleid voor aangepaste weergaven** > **serverrollen** > **en toegangsservices** op de server waarop de NPS-extensie is geïnstalleerd.

## <a name="troubleshooting-steps-for-common-errors"></a>Problemen oplossen voor veelvoorkomende fouten

| Foutcode | Stappen voor probleemoplossing |
| ---------- | --------------------- |
| **CONTACT_SUPPORT** | [Neem contact op met de ondersteuning](#contact-microsoft-support)en vermeld de lijst met stappen voor het verzamelen van logboeken. Geef zoveel mogelijk informatie over wat er vóór de fout is gebeurd, inclusief tenant-id en gebruikersnaam (UPN). |
| **CLIENT_CERT_INSTALL_ERROR** | Er kan een probleem zijn met de manier waarop het clientcertificaat is geïnstalleerd of gekoppeld aan uw tenant. Volg de instructies in [Troubleshooting the MFA NPS extension](howto-mfa-nps-extension.md#troubleshooting) to investigate client cert problems. |
| **ESTS_TOKEN_ERROR** | Volg de instructies in [Troubleshooting the MFA NPS extension](howto-mfa-nps-extension.md#troubleshooting) to investigate client cert and ADAL token problems. |
| **HTTPS_COMMUNICATION_ERROR** | De NPS-server kan geen reacties ontvangen van Azure MFA. Controleer of uw firewalls bidirectioneel zijn geopend voor verkeer van en naarhttps://adnotifications.windowsazure.com |
| **HTTP_CONNECT_ERROR** | Controleer op de server waarop de NPS-extensie wordt uitgevoerd of u bereiken `https://adnotifications.windowsazure.com` en `https://login.microsoftonline.com/`. Als deze sites niet worden geladen, u problemen oplossen met de verbinding op die server. |
| **NPS-extensie voor Azure MFA:** <br> NPS-extensie voor Azure MFA voert alleen Secundaire Auth uit voor Radius-aanvragen in de status AccessAccept. Aanvraag ontvangen voor gebruikersnaam gebruiker met antwoordstatus AccessReject, het negeren van het verzoek. | Deze fout weerspiegelt meestal een verificatiefout in AD of dat de NPS-server geen reacties van Azure AD kan ontvangen. Controleer of uw firewalls bidirectioneel open `https://adnotifications.windowsazure.com` zijn `https://login.microsoftonline.com` voor verkeer van en naar en met poorten 80 en 443. Het is ook belangrijk om te controleren of op het tabblad IN-bellen van netwerktoegangsmachtigingen de instelling is ingesteld op "toegang beheren via NPS-netwerkbeleid". Deze fout kan ook worden geactiveerd als de gebruiker geen licentie toegewezen krijgt. |
| **REGISTRY_CONFIG_ERROR** | Er ontbreekt een sleutel in het register voor de toepassing, wat kan zijn omdat het [PowerShell-script](howto-mfa-nps-extension.md#install-the-nps-extension) niet is uitgevoerd na de installatie. De foutmelding moet de ontbrekende sleutel bevatten. Zorg ervoor dat u de sleutel onder HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa hebt. |
| **REQUEST_FORMAT_ERROR** <br> Radiusaanvraag ontbreekt verplicht kenmerk Radius userName\Identifier. Controleren of NPS RADIUS-aanvragen ontvangt | Deze fout weerspiegelt meestal een installatieprobleem. De NPS-extensie moet worden geïnstalleerd in NPS-servers die RADIUS-aanvragen kunnen ontvangen. NPS-servers die zijn geïnstalleerd als afhankelijkheden voor services zoals RDG en RRAS, ontvangen geen straalaanvragen. NPS Extension werkt niet wanneer geïnstalleerd over dergelijke installaties en fouten uit, omdat het niet kan lezen van de details van de verificatie aanvraag. |
| **REQUEST_MISSING_CODE** | Zorg ervoor dat het wachtwoordversleutelingsprotocol tussen de NPS- en NAS-servers de secundaire verificatiemethode ondersteunt die u gebruikt. **PAP** ondersteunt alle verificatiemethoden van Azure MFA in de cloud: telefoongesprek, eenrichtingssms, melding van mobiele apps en verificatiecode voor mobiele apps. **CHAPV2** en **EAP** ondersteunen telefoongesprekken en meldingen van mobiele apps. |
| **USERNAME_CANONICALIZATION_ERROR** | Controleer of de gebruiker aanwezig is in uw on-premises Active Directory-exemplaar en of de NPS-service machtigingen heeft om toegang te krijgen tot de directory. Als u cross-forest vertrouwensrelaties gebruikt, neemt [u contact op met ondersteuning](#contact-microsoft-support) voor verdere hulp. |

### <a name="alternate-login-id-errors"></a>Alternatieve inlog-id-fouten

| Foutcode | Foutbericht | Stappen voor probleemoplossing |
| ---------- | ------------- | --------------------- |
| **ALTERNATE_LOGIN_ID_ERROR** | Fout: userObjectSid opzoekervaring is mislukt | Controleer of de gebruiker bestaat in uw on-premises Active Directory-exemplaar. Als u cross-forest vertrouwensrelaties gebruikt, neemt [u contact op met ondersteuning](#contact-microsoft-support) voor verdere hulp. |
| **ALTERNATE_LOGIN_ID_ERROR** | Fout: Alternatieve loginid-opzoeking is mislukt | Controleer of LDAP_ALTERNATE_LOGINID_ATTRIBUTE is ingesteld op een [geldig kenmerk van de active directory](https://msdn.microsoft.com/library/ms675090(v=vs.85).aspx). <br><br> Als LDAP_FORCE_GLOBAL_CATALOG is ingesteld op True of LDAP_LOOKUP_FORESTS is geconfigureerd met een niet-lege waarde, controleert u of u een globale catalogus hebt geconfigureerd en of het kenmerk AlternateLoginId eraan is toegevoegd. <br><br> Als LDAP_LOOKUP_FORESTS is geconfigureerd met een niet-lege waarde, controleert u of de waarde juist is. Als er meer dan één bosnaam is, moeten de namen worden gescheiden met puntkomma's, niet met spaties. <br><br> Als deze stappen het probleem niet oplossen, neemt [u contact op met de ondersteuning](#contact-microsoft-support) voor meer hulp. |
| **ALTERNATE_LOGIN_ID_ERROR** | Fout: Alternatieve loginid-waarde is leeg | Controleer of het kenmerk AlternateLoginId is geconfigureerd voor de gebruiker. |

## <a name="errors-your-users-may-encounter"></a>Fouten die uw gebruikers kunnen tegenkomen

| Foutcode | Foutbericht | Stappen voor probleemoplossing |
| ---------- | ------------- | --------------------- |
| **Toegang geweigerd** | Bellertenant heeft geen toegangsmachtigingen om verificatie voor de gebruiker uit te voeren | Controleer of het tenantdomein en het domein van de gebruikersnaam (UPN) hetzelfde zijn. Zorg er bijvoorbeeld user@contoso.com voor dat u probeert te verifiëren bij de Contoso-tenant. De UPN vertegenwoordigt een geldige gebruiker voor de tenant in Azure. |
| **AuthenticationMethodNotConfigured** | De opgegeven verificatiemethode is niet geconfigureerd voor de gebruiker | Laat de gebruiker zijn verificatiemethoden toevoegen of verifiëren volgens de instructies in [Uw instellingen beheren voor verificatie in twee stappen.](../user-help/multi-factor-authentication-end-user-manage-settings.md) |
| **AuthenticationMethodNotSupported AuthenticationMethodNotSupported AuthenticationMethodNotSupported AuthenticationMethod** | Opgegeven verificatiemethode wordt niet ondersteund. | Verzamel al uw logboeken die deze fout bevatten en [neem contact op met ondersteuning.](#contact-microsoft-support) Wanneer u contact opneemt met de ondersteuning, geeft u de gebruikersnaam en de secundaire verificatiemethode op die de fout heeft geactiveerd. |
| **BecAccessGeweigerd** | MSODS Bec call terug toegang geweigerd, waarschijnlijk is de gebruikersnaam niet gedefinieerd in de tenant | De gebruiker is on-premises aanwezig in Active Directory, maar wordt niet gesynchroniseerd met Azure AD door AD Connect. Of de gebruiker ontbreekt voor de tenant. Voeg de gebruiker toe aan Azure AD en laat deze verificatiemethoden toevoegen volgens de instructies in [Uw instellingen beheren voor verificatie in twee stappen.](../user-help/multi-factor-authentication-end-user-manage-settings.md) |
| **Ongeldig formaat** of **StrongAuthenticationServiceInvalidparameter** | Het telefoonnummer is in een onherkenbare indeling | Laat de gebruiker zijn verificatietelefoonnummers corrigeren. |
| **Ongeldige sessie** | De opgegeven sessie is ongeldig of is mogelijk verlopen | De sessie heeft meer dan drie minuten in beslag genomen. Controleer of de gebruiker de verificatiecode of reactie op de app-melding binnen drie minuten na het starten van de verificatieaanvraag invoert. Als dit het probleem niet oplost, controleert u of er geen netwerklatencies zijn tussen client, NAS Server, NPS Server en het Azure MFA-eindpunt.  |
| **NoDefaultAuthenticationMethodIsConfigured NoDefaultAuthenticationMethodIsConfigured NoDefaultAuthenticationMethodIsConfigured NoDefault** | Er is geen standaardverificatiemethode geconfigureerd voor de gebruiker | Laat de gebruiker zijn verificatiemethoden toevoegen of verifiëren volgens de instructies in [Uw instellingen beheren voor verificatie in twee stappen.](../user-help/multi-factor-authentication-end-user-manage-settings.md) Controleer of de gebruiker een standaardverificatiemethode heeft gekozen en configureer die methode voor zijn/haar account. |
| **OathCodePinIncorrect** | Verkeerde code en pin ingevoerd. | Deze fout wordt niet verwacht in de NPS-extensie. Als uw gebruiker dit tegenkomt, [neemt u contact op met de ondersteuning](#contact-microsoft-support) voor help voor het oplossen van problemen. |
| **ProofDataNotFound** | Bewijsgegevens zijn niet geconfigureerd voor de opgegeven verificatiemethode. | Laat de gebruiker een andere verificatiemethode proberen of een nieuwe verificatiemethode toevoegen volgens de instructies in [Uw instellingen beheren voor verificatie in twee stappen.](../user-help/multi-factor-authentication-end-user-manage-settings.md) Als de gebruiker deze fout blijft zien nadat u hebt bevestigd dat de verificatiemethode correct is ingesteld, [neemt u contact op met de ondersteuning](#contact-microsoft-support). |
| **SMSAuthFailedWrongCodePinEntered** | Verkeerde code en pin ingevoerd. (OneWaySMS) | Deze fout wordt niet verwacht in de NPS-extensie. Als uw gebruiker dit tegenkomt, [neemt u contact op met de ondersteuning](#contact-microsoft-support) voor help voor het oplossen van problemen. |
| **TenantIsGeblokkeerd** | Huurder is geblokkeerd | [Neem contact op met ondersteuning](#contact-microsoft-support) met Directory-ID vanaf de pagina Azure AD-eigenschappen in de Azure-portal. |
| **UserNotFound** | De opgegeven gebruiker is niet gevonden | De tenant is niet langer zichtbaar als actief in Azure AD. Controleer of uw abonnement actief is en of u over de vereiste first party-apps beschikt. Zorg er ook voor dat de huurder in het certificaat onderwerp is zoals verwacht en het cert is nog steeds geldig en geregistreerd onder de service principal. |

## <a name="messages-your-users-may-encounter-that-arent-errors"></a>Berichten die uw gebruikers kunnen tegenkomen die geen fouten zijn

Soms kunnen uw gebruikers berichten ontvangen van Multi-Factor Authentication omdat hun verificatieverzoek is mislukt. Dit zijn geen fouten in het product van de configuratie, maar zijn opzettelijke waarschuwingen die verklaren waarom een verificatieverzoek is geweigerd.

| Foutcode | Foutbericht | Aanbevolen stappen | 
| ---------- | ------------- | ----------------- |
| **OathCodeIncorrect** | Verkeerde code ingevoerd\OATH Code Incorrect | De gebruiker heeft de verkeerde code ingevoerd. Laat ze het opnieuw proberen door een nieuwe code aan te vragen of opnieuw aan te melden. | 
| **SMSAuthFailedMaxAllowedCodeRetryReached** | Maximaal toegestane code opnieuw geprobeerd bereikt | De gebruiker heeft de verificatieuitdaging te vaak mislukt. Afhankelijk van je instellingen moeten ze mogelijk nu worden gedeblokkeerd door een beheerder.  |
| **SMSAuthFailedWrongCodeEntered** | Verkeerde code ingevoerd/SMS OTP Onjuist | De gebruiker heeft de verkeerde code ingevoerd. Laat ze het opnieuw proberen door een nieuwe code aan te vragen of opnieuw aan te melden. |

## <a name="errors-that-require-support"></a>Fouten die ondersteuning vereisen

Als u een van deze fouten tegenkomt, raden we u aan [contact op te nemen met de ondersteuning](#contact-microsoft-support) voor diagnostische hulp. Er is geen standaardset stappen die deze fouten kunnen oplossen. Wanneer u contact opneemt met ondersteuning, moet u zoveel mogelijk informatie opnemen over de stappen die tot een fout hebben geleid en uw tenantgegevens.

| Foutcode | Foutbericht |
| ---------- | ------------- |
| **OngeldigParameter** | Aanvraag mag niet ongeldig zijn |
| **OngeldigParameter** | ObjectId mag niet null of leeg zijn voor ReplicationScope:{0} |
| **OngeldigParameter** | De lengte van \{Bedrijfsnaam 0}\ is langer dan de maximaal toegestane lengte{1} |
| **OngeldigParameter** | UserPrincipalName mag niet null of leeg zijn |
| **OngeldigParameter** | De meegeleverde TenantId is niet in de juiste vorm |
| **OngeldigParameter** | SessionId mag niet ongeldig of leeg zijn |
| **OngeldigParameter** | Kon geen ProofData van verzoek of Msods oplossen. De ProofData is niet bekend |
| **InternalError** |  |
| **OathCodePinIncorrect** |  |
| **VersieNotOndersteund** |  |
| **MFAPinnotsetup** |  |

## <a name="next-steps"></a>Volgende stappen

### <a name="troubleshoot-user-accounts"></a>Gebruikersaccounts oplossen

Als uw gebruikers [problemen hebben met verificatie in twee stappen,](../user-help/multi-factor-authentication-end-user-troubleshoot.md)u hen helpen bij het zelf diagnosticeren van problemen.

### <a name="health-check-script"></a>Script voor statuscontrole

Het [statuscontrolescript azure MFA NPS Extension](https://docs.microsoft.com/samples/azure-samples/azure-mfa-nps-extension-health-check/azure-mfa-nps-extension-health-check/) voert een basisstatuscontrole uit bij het oplossen van problemen met de NPS-extensie. Voer het script uit en kies optie 3.

### <a name="contact-microsoft-support"></a>Contact opnemen met Microsoft Ondersteuning

Als u extra hulp nodig hebt, neemt u contact op met een ondersteuningsmedewerker via [azure multi-factor authentication server-ondersteuning.](https://support.microsoft.com/oas/default.aspx?prid=14947) Wanneer u contact met ons opneemt, is het handig als u zoveel mogelijk informatie over uw probleem opnemen. Informatie die u verstrekken omvat de pagina waar u de fout, de specifieke foutcode, de specifieke sessie-ID, de ID van de gebruiker die de fout heeft gezien, en foutopsporingslogboeken hebt gezien.

Als u foutopsporingslogboeken wilt verzamelen voor diagnostische ondersteuning, gebruikt u de volgende stappen op de NPS-server:

1. Open registereditor en blader naar HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa set **VERBOSE_LOG** op **TRUE**
2. Open een opdrachtprompt van administrator en voer deze opdrachten uit:

   ```
   Mkdir c:\NPS
   Cd NPS
   netsh trace start Scenario=NetConnection capture=yes tracefile=c:\NPS\nettrace.etl
   logman create trace "NPSExtension" -ow -o c:\NPS\NPSExtension.etl -p {7237ED00-E119-430B-AB0F-C63360C8EE81} 0xffffffffffffffff 0xff -nb 16 16 -bs 1024 -mode Circular -f bincirc -max 4096 -ets
   logman update trace "NPSExtension" -p {EC2E6D3A-C958-4C76-8EA4-0262520886FF} 0xffffffffffffffff 0xff -ets
   ```

3. Reproduceer het probleem

4. Stop de tracering met deze opdrachten:

   ```
   logman stop "NPSExtension" -ets
   netsh trace stop
   wevtutil epl AuthNOptCh C:\NPS\%computername%_AuthNOptCh.evtx
   wevtutil epl AuthZOptCh C:\NPS\%computername%_AuthZOptCh.evtx
   wevtutil epl AuthZAdminCh C:\NPS\%computername%_AuthZAdminCh.evtx
   Start .
   ```

5. Open registereditor en blader naar HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa-set **VERBOSE_LOG** op **FALSE**
6. Rits de inhoud van de Map C:\NPS vast en voeg het ritsbestand toe aan de ondersteuningsaanvraag.
