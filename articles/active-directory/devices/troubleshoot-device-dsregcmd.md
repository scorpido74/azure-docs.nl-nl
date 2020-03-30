---
title: Problemen oplossen met de opdracht dsregcmd - Azure Active Directory
description: De uitvoer van dsregcmd gebruiken om de status van apparaten in Azure AD te begrijpen
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2cd782cdab625934fe60617142e5ac0baf756398
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128761"
---
# <a name="troubleshooting-devices-using-the-dsregcmd-command"></a>Probleemoplossing voor apparaten met de opdracht dsregcmd

Het hulpprogramma voor dsregcmd /status moet worden uitgevoerd als een domeingebruikersaccount.

## <a name="device-state"></a>Apparaatstatus

In deze sectie worden de parameters voor de status van het apparaat weergegeven. In de onderstaande tabel worden de criteria vermeld voor het apparaat dat zich in verschillende join-staten moet bevinden.

| AzureAdJoined | EnterpriseJoined | DomainJoined DomainJoined | Apparaatstatus |
| ---   | ---   | ---   | ---   |
| JA | NO | NO | Azure AD is lid geworden |
| NO | NO | JA | Domein samengevoegd |
| JA | NO | JA | Hybride AD lid geworden |
| NO | JA | JA | On-premises DRS Toegetreden |

> [!NOTE]
> Status Workplace Join (Azure AD-status wordt weergegeven in de sectie Gebruikersstatus

- **AzureAdJoined:** - Stel in op 'JA' als het apparaat is verbonden met Azure AD. "NEE" anders.
- **EnterpriseJoined:** - Stel in op 'JA' als het apparaat is verbonden met een on-premises DRS. Een apparaat kan niet zowel EnterpriseJoined als AzureAdJoined zijn.
- **DomainJoined:** - Stel in op 'JA' als het apparaat is verbonden met een domein (AD).
- **DomainName:** - Stel in op de naam van het domein als het apparaat is verbonden met een domein.

### <a name="sample-device-state-output"></a>Uitvoer van de status van een voorbeeldapparaat

```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+
             AzureAdJoined : YES
          EnterpriseJoined : NO
              DomainJoined : YES
                DomainName : HYBRIDADFS
+----------------------------------------------------------------------+
```

## <a name="device-details"></a>Apparaatgegevens

Alleen weergegeven wanneer het apparaat Azure AD is toegetreden of hybride Azure AD is toegetreden (niet Azure AD-geregistreerd). In deze sectie worden apparaatidentificerende gegevens weergegeven die in de cloud zijn opgeslagen.

- **DeviceId:** - Unieke id van het apparaat in de Azure AD-tenant
- **Duimafdruk:** - Duimafdruk van het apparaatcertificaat 
- **DeviceCertificateValidity:** - Geldigheid van het apparaatcertificaat
- **KeyContainerId:** - ContainerId van de persoonlijke sleutel van het apparaat die is gekoppeld aan het apparaatcertificaat
- **KeyProvider:** - KeyProvider (Hardware/Software) die wordt gebruikt om de persoonlijke sleutel van het apparaat op te slaan.
- **TpmProtected:** - "JA" als de persoonlijke sleutel van het apparaat is opgeslagen in een Hardware TPM.

### <a name="sample-device-details-output"></a>Voorbeeld apparaat details output

```
+----------------------------------------------------------------------+
| Device Details                                                       |
+----------------------------------------------------------------------+

                  DeviceId : e92325d0-xxxx-xxxx-xxxx-94ae875dxxxx
                Thumbprint : D293213EF327483560EED8410CAE36BB67208179
 DeviceCertificateValidity : [ 2019-01-11 21:02:50.000 UTC -- 2029-01-11 21:32:50.000 UTC ]
            KeyContainerId : 13e68a58-xxxx-xxxx-xxxx-a20a2411xxxx
               KeyProvider : Microsoft Software Key Storage Provider
              TpmProtected : NO
+----------------------------------------------------------------------+
```

## <a name="tenant-details"></a>Tenantgegevens

Alleen weergegeven wanneer het apparaat Azure AD is toegetreden of hybride Azure AD is toegetreden (niet Azure AD-geregistreerd). In deze sectie worden de algemene tenantgegevens weergegeven wanneer een apparaat wordt samengevoegd met Azure AD.

> [!NOTE]
> Als de MDM-URL's in deze sectie leeg zijn, geeft dit aan dat de MDM niet is geconfigureerd of dat de huidige gebruiker niet in het bereik van MDM-inschrijving valt. Controleer de mobiliteitsinstellingen in Azure AD om uw MDM-configuratie te controleren.

> [!NOTE]
> Zelfs als u MDM URL's ziet, betekent dit niet dat het apparaat wordt beheerd door een MDM. De informatie wordt weergegeven als de tenant een MDM-configuratie heeft voor automatische inschrijving, zelfs als het apparaat zelf niet wordt beheerd. 

### <a name="sample-tenant-details-output"></a>Uitvoer van voorbeeldtenantdetails

```
+----------------------------------------------------------------------+
| Tenant Details                                                       |
+----------------------------------------------------------------------+

                TenantName : HybridADFS
                  TenantId : 96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
                       Idp : login.windows.net
               AuthCodeUrl : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/authorize
            AccessTokenUrl : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/token
                    MdmUrl : https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
                 MdmTouUrl : https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
          MdmComplianceUrl : https://portal.manage-beta.microsoft.com/?portalAction=Compliance
               SettingsUrl : eyJVxxxxIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyxxxx==
            JoinSrvVersion : 1.0
                JoinSrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/device/
                 JoinSrvId : urn:ms-drs:enterpriseregistration.windows.net
             KeySrvVersion : 1.0
                 KeySrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/key/
                  KeySrvId : urn:ms-drs:enterpriseregistration.windows.net
        WebAuthNSrvVersion : 1.0
            WebAuthNSrvUrl : https://enterpriseregistration.windows.net/webauthn/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/
             WebAuthNSrvId : urn:ms-drs:enterpriseregistration.windows.net
    DeviceManagementSrvVer : 1.0
    DeviceManagementSrvUrl : https://enterpriseregistration.windows.net/manage/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/
     DeviceManagementSrvId : urn:ms-drs:enterpriseregistration.windows.net
+----------------------------------------------------------------------+
```

## <a name="user-state"></a>Gebruikersstatus

In deze sectie wordt de status weergegeven van verschillende kenmerken voor de gebruiker die momenteel op het apparaat is ingelogd.

> [!NOTE]
> De opdracht moet worden uitgevoerd in een gebruikerscontext om de geldige status op te halen.

- **NgcSet:** - Stel in op 'JA' als er een Windows Hello-toets is ingesteld voor de huidige aangemelde gebruiker.
- **NgcKeyId:** - ID van de Windows Hello-toets als deze is ingesteld voor de huidige aangemelde gebruiker.
- **CanReset:** - Geeft aan of de Windows Hello-toets door de gebruiker kan worden gereset. 
- **Mogelijke waarden:** - DestructiveOnly, NonDestructiveOnly, DestructiveAndNonDestructive, of Unknown if error. 
- **WorkplaceJoined:** - Stel in op 'JA' als azure AD-geregistreerde accounts zijn toegevoegd aan het apparaat in de huidige NTUSER-context.
- **WamDefaultSet:** - Stel in op 'JA' als er een WAM-standaardWebAccount wordt gemaakt voor de ingelogde gebruiker. In dit veld kan een fout worden weergegeven als dsreg /status wordt uitgevoerd vanaf een opdrachtprompt met verhoogde bevoegdheid. 
- **WamDefaultAuthority:** - Ingesteld op "organisaties" voor Azure AD.
- **WamDefaultId:** -https://login.microsoft.comAltijd " " voor Azure AD.
- **WamDefaultGUID:** - GuiD van de WAM-provider (Azure AD/Microsoft-account) voor de standaard WAM WebAccount. 

### <a name="sample-user-state-output"></a>Uitvoer van gebruikersstatus van een voorbeeld

```
+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

                    NgcSet : YES
                  NgcKeyId : {FA0DB076-A5D7-4844-82D8-50A2FB42EC7B}
                  CanReset : DestructiveAndNonDestructive
           WorkplaceJoined : NO
             WamDefaultSet : YES
       WamDefaultAuthority : organizations
              WamDefaultId : https://login.microsoft.com
            WamDefaultGUID : { B16898C6-A148-4967-9171-64D755DA8520 } (AzureAd)

+----------------------------------------------------------------------+
```

## <a name="sso-state"></a>SSO-status

Deze sectie kan worden genegeerd voor door Azure AD geregistreerde apparaten.

> [!NOTE]
> De opdracht moet worden uitgevoerd in een gebruikerscontext om de geldige status voor die gebruiker op te halen.

- **AzureAdPrt:** - Stel in op 'JA' als er een PRT op het apparaat aanwezig is voor de aangemelde gebruiker.
- **AzureAdPrtUpdateTime:** - Stel in op de tijd in UTC toen de PRT voor het laatst werd bijgewerkt.
- **AzureAdPrtExpiryTime:** - Stel in op de tijd in UTC wanneer de PRT verloopt als deze niet wordt verlengd.
- **AzureAdPrtAuthority:** - URL van azure AD-autoriteit
- **EnterprisePrt:** - Stel in op 'JA' als het apparaat PRT heeft van on-premises ADFS. Voor hybride Azure AD-apparaten kan het apparaat prt van zowel Azure AD als on-premises AD tegelijkertijd hebben. On-premises aangesloten apparaten hebben alleen een Enterprise PRT.
- **EnterprisePrtUpdateTime:** - Stel in op de tijd in UTC toen de Enterprise PRT voor het laatst werd bijgewerkt.
- **EnterprisePrtExpiryTime:** - Stel in op de tijd in UTC wanneer de PRT verloopt als deze niet wordt verlengd.
- **EnterprisePrtAuthority:** - URL van adfs-autoriteit

### <a name="sample-sso-state-output"></a>Voorbeeld van SSO-statusuitvoer

```
+----------------------------------------------------------------------+
| SSO State                                                            |
+----------------------------------------------------------------------+

                AzureAdPrt : YES
      AzureAdPrtUpdateTime : 2019-01-24 19:15:26.000 UTC
      AzureAdPrtExpiryTime : 2019-02-07 19:15:26.000 UTC
       AzureAdPrtAuthority : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
             EnterprisePrt : YES
   EnterprisePrtUpdateTime : 2019-01-24 19:15:33.000 UTC
   EnterprisePrtExpiryTime : 2019-02-07 19:15:33.000 UTC
    EnterprisePrtAuthority : https://fs.hybridadfs.nttest.microsoft.com:443/adfs

+----------------------------------------------------------------------+
```

## <a name="diagnostic-data"></a>Diagnostische gegevens

### <a name="pre-join-diagnostics"></a>Pre-join diagnostiek

Deze sectie wordt alleen weergegeven als het apparaat is samengevoegd en azure AD niet kan hybrideworden.

In deze sectie worden verschillende tests uitgevoerd om bij het uitvoeren van joinfouten een diagnose te stellen. Deze sectie bevat ook de details van de vorige (?). Deze informatie omvat de foutfase, de foutcode, de serveraanvraag-id, serverresponshttp-status, het foutbericht voor serverreacties.

- **Gebruikerscontext:** - De context waarin de diagnostiek wordt uitgevoerd. Mogelijke waarden: SYSTEEM, UN-ELEVATED User, ELEVATED User. 

   > [!NOTE]
   > Aangezien de werkelijke join wordt uitgevoerd in de systeemcontext, is het uitvoeren van de diagnostische gegevens in de systeemcontext het dichtst bij het werkelijke joinscenario. Als u diagnostische gegevens wilt uitvoeren in de systeemcontext, moet de opdracht dsregcmd /status worden uitgevoerd vanaf een opdrachtprompt met verhoogde bevoegdheid.

- **Clienttijd:** - De systeemtijd in UTC.
- **AD-connectiviteitstest:** - Test voert een connectiviteitstest uit op de domeincontroller. Fout in deze test zal waarschijnlijk resulteren in Join fouten in pre-check fase.
- **AD-configuratietest:** - Test leest en controleert of het SCP-object correct is geconfigureerd in het on-premises AD-forest. Fouten in deze test zou waarschijnlijk resulteren in Join fouten in de discover fase met de foutcode 0x801c001d.
- **DRS Discovery Test:** - Test haalt de DRS-eindpunten uit detectiemetadata eindpunt en voert een user realm request. Fouten in deze test zouden waarschijnlijk resulteren in Join fouten in de discover fase.
- **DRS Connectivity Test:** - Test voert basisconnectiviteitstest uit naar het DRS-eindpunt.
- **Tokenacquisitietest:** - Test probeert een Azure AD-verificatietoken te krijgen als de gebruikerstenant wordt gefedereerd. Fouten in deze test zou waarschijnlijk resulteren in Join fouten in de auth fase. Als auth mislukt sync join zal worden geprobeerd als fallback, tenzij fallback is expliciet uitgeschakeld met de onderstaande register sleutel instellingen.
```
    Keyname: Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ
    Value: FallbackToSyncJoin
    Type:  REG_DWORD
    Value: 0x0 -> Disabled
    Value: 0x1 -> Enabled
    Default (No Key): Enabled
 ```
- **Terugval naar Sync-Join:** - Instellen op 'Ingeschakeld' als de bovenstaande registersleutel, om te voorkomen dat de terugval om te synchroniseren met auth-fouten, niet aanwezig is. Deze optie is beschikbaar in Windows 10 1803 en hoger.
- **Vorige registratie:** - Tijd dat de vorige join-poging heeft plaatsgevonden. Alleen mislukte Join-pogingen worden geregistreerd.
- **Foutfase:** - Het stadium van de join waarin het werd afgebroken. Mogelijke waarden zijn pre-check, discover, auth, join.
- **Client foutcode:** - Client foutcode geretourneerd (HRESULT).
- **Serverfoutcode:** - Serverfoutcode als er een verzoek naar de server en de server is verzonden, is teruggestuurd met een foutcode. 
- **Serverbericht:** - Serverbericht geretourneerd samen met de foutcode.
- **Https-status:** - Http-status geretourneerd door de server.
- **Aanvraag-id:** - De clientrequestId verzonden naar de server. Handig om te correleren met server-side logs.

### <a name="sample-pre-join-diagnostics-output"></a>Voorbeeld van pre-join-diagnostische uitvoer

In het volgende voorbeeld ziet u dat de diagnostische test mislukt met een detectiefout.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : SYSTEM
               Client Time : 2019-01-31 09:25:31.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : FAIL [0x801c0021/0x801c000c]
     DRS Connectivity Test : SKIPPED
    Token acquisition Test : SKIPPED
     Fallback to Sync-Join : ENABLED

     Previous Registration : 2019-01-31 09:23:30.000 UTC
               Error Phase : discover
          Client ErrorCode : 0x801c0021

+----------------------------------------------------------------------+
```

In het volgende voorbeeld ziet diagnostische tests worden uitgevoerd, maar de registratiepoging is mislukt met een mapfout, die wordt verwacht voor synchronisatie join. Zodra de azure AD Connect-synchronisatietaak is voltooid, kan het apparaat lid worden.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : SYSTEM
               Client Time : 2019-01-31 09:16:50.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : PASS
     DRS Connectivity Test : PASS
    Token acquisition Test : PASS
     Fallback to Sync-Join : ENABLED

     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-7ac4-4714-88a1-94ae875d5245) is not found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e

+----------------------------------------------------------------------+
```

### <a name="post-join-diagnostics"></a>Diagnose na het aansluiten

In deze sectie wordt de uitvoer weergegeven van geestelijke gezondheidcontroles die zijn uitgevoerd op een apparaat dat is aangesloten op de cloud.

- **AadRecoveryEnabled:** - Als "JA", de sleutels opgeslagen in het apparaat zijn niet bruikbaar en het apparaat is gemarkeerd voor herstel. De volgende aanmelding zal de herstelstroom activeren en het apparaat opnieuw registreren.
- **KeySignTest:** - Als "PASSED" de apparaatsleutels in goede gezondheid verkeren. Als KeySignTest uitvalt, wordt het apparaat meestal gemarkeerd voor herstel. De volgende aanmelding zal de herstelstroom activeren en het apparaat opnieuw registreren. Voor hybride Azure AD-apparaten is het herstel stil. Terwijl Azure AD is toegetreden of Azure AD is geregistreerd, wordt gebruikersverificatie gevraagd om het apparaat te herstellen en opnieuw te registreren als dat nodig is. **De KeySignTest vereist verhoogde bevoegdheden.**

#### <a name="sample-post-join-diagnostics-output"></a>Voorbeeld van diagnostische uitvoer na het deelnemen

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

         AadRecoveryEnabled: NO
               KeySignTest : PASSED
+----------------------------------------------------------------------+
```

## <a name="ngc-prerequisite-check"></a>NGC-vereistecontrole

In deze sectie worden de perquisite-controles uitgevoerd voor de inrichting van Windows Hello for Business (WHFB). 

> [!NOTE]
> Het is mogelijk dat u geen NGC-vereiste controlegegevens in dsregcmd /status ziet als de gebruiker de WHFB al heeft geconfigureerd.

- **IsDeviceJoined:** - Stel in op 'JA' als het apparaat is verbonden met Azure AD.
- **IsUserAzureAD:** - Stel in op 'JA' als de aangemelde gebruiker aanwezig is in Azure AD .
- **PolicyEnabled:** - Stel in op 'JA' als het WHFB-beleid is ingeschakeld op het apparaat.
- **PostLogonEnabled:** - Stel in op 'JA' als WHFB-inschrijving native wordt geactiveerd door het platform. Als deze is ingesteld op 'NEE', geeft dit aan dat de inschrijving van Windows Hello voor Bedrijven wordt geactiveerd door een aangepast mechanisme
- **DeviceEligible:** - Stel in op 'JA' als het apparaat voldoet aan de hardwarevereiste voor inschrijving bij WHFB.
- **SessionIsNotRemote:** - Stel in op "JA" als de huidige gebruiker rechtstreeks op het apparaat is ingelogd en niet op afstand.
- **CertEnrollment:** - Specifiek voor de implementatie van WHFB Certificate Trust, met vermelding van de certificaatinschrijvingsinstantie voor WHFB. Ingesteld op "inschrijvingsautoriteit" als de bron van WHFB-beleid groepsbeleid is, "beheer van mobiele apparaten" als de bron MDM is. "geen" anders
- **AdfsRefreshToken:** - Specifiek voor de implementatie van WHFB-certificaatvertrouwensrelaties. Alleen aanwezig als CertEnrollment "inschrijvingsautoriteit" is. Geeft aan of het apparaat een zakelijke PRT voor de gebruiker heeft.
- **AdfsRaIsReady:** - Specifiek voor de implementatie van WHFB-certificaatvertrouwensrelaties.  Alleen aanwezig als CertEnrollment "inschrijvingsautoriteit" is. Stel in op 'JA' als ADFS in detectiemetagegevens aangeeft dat het WHFB ondersteunt *en* of de sjabloon voor aanmeldingscertificaat beschikbaar is.
- **LogonCertTemplateReady:** - Specifiek voor de implementatie van WHFB-certificaatvertrouwensrelaties. Alleen aanwezig als CertEnrollment "inschrijvingsautoriteit" is. Stel in op 'JA' als de sjabloon van het aanmeldingscertificaat geldig is en helpt bij het oplossen van problemen met ADFS RA.
- **PreReqResult:** - Biedt resultaat van alle WHFB vereiste evaluatie. Ingesteld op "Zal Provision" als WHFB inschrijving zou worden gestart als een post-logon taak wanneer de gebruiker zich aanmeldt de volgende keer.

### <a name="sample-ngc-prerequisite-check-output"></a>Voorbeeld NGC-vereistecontrole uitvoer

```
+----------------------------------------------------------------------+
| Ngc Prerequisite Check                                               |
+----------------------------------------------------------------------+

            IsDeviceJoined : YES
             IsUserAzureAD : YES
             PolicyEnabled : YES
          PostLogonEnabled : YES
            DeviceEligible : YES
        SessionIsNotRemote : YES
            CertEnrollment : enrollment authority
          AdfsRefreshToken : YES
             AdfsRaIsReady : YES
    LogonCertTemplateReady : YES ( StateReady )
              PreReqResult : WillProvision
+----------------------------------------------------------------------+
```

## <a name="next-steps"></a>Volgende stappen

Zie de [veelgestelde vragen over apparaatbeheer](faq.md) voor vragen
