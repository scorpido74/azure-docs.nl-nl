---
title: Hybride Azure Active Directory-apparaten oplossen
description: Het oplossen van problemen met hybride Azure Active Directory is toegetreden tot Windows 10- en Windows Server 2016-apparaten.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26e52930211611673b6fe2309e2dca067a91ebc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331774"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-devices"></a>Hybride Azure Active Directory-apparaten oplossen 

De inhoud van dit artikel is van toepassing op apparaten met Windows 10 of Windows Server 2016.

Zie voor andere Windows-clients het artikel [Probleemoplossing hybride Azure Active Directory met downlevel apparaten](troubleshoot-hybrid-join-windows-legacy.md).

In dit artikel wordt ervan uitgegaan dat u [hybride Azure Active Directory-samengevoegde apparaten](hybrid-azuread-join-plan.md) hebt geconfigureerd om de volgende scenario's te ondersteunen:

- Voorwaardelijke toegang op basis van het apparaat
- [Bedrijfsroaming van instellingen](../active-directory-windows-enterprise-state-roaming-overview.md)
- [Windows Hello voor Bedrijven](../active-directory-azureadjoin-passport-deployment.md)

Dit document biedt richtlijnen voor het oplossen van problemen om mogelijke problemen op te lossen. 

Voor Windows 10 en Windows Server 2016 ondersteunt hybride Azure Active Directory join de Windows 10 November 2015 Update en hoger.

## <a name="troubleshoot-join-failures"></a>Problemen met joinfouten oplossen

### <a name="step-1-retrieve-the-join-status"></a>Stap 1: De joinstatus ophalen 

**Ga als lid op om de joinstatus op te halen:**

1. Een opdrachtprompt openen als beheerder
2. Type `dsregcmd /status`

```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+

    AzureAdJoined: YES
 EnterpriseJoined: NO
         DeviceId: 5820fbe9-60c8-43b0-bb11-44aee233e4e7
       Thumbprint: B753A6679CE720451921302CA873794D94C6204A
   KeyContainerId: bae6a60b-1d2f-4d2a-a298-33385f6d05e9
      KeyProvider: Microsoft Platform Crypto Provider
     TpmProtected: YES
     KeySignTest: : MUST Run elevated to test.
              Idp: login.windows.net
         TenantId: 72b988bf-xxxx-xxxx-xxxx-2d7cd011xxxx
       TenantName: Contoso
      AuthCodeUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/authorize
   AccessTokenUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/token
           MdmUrl: https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
        MdmTouUrl: https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
  dmComplianceUrl: https://portal.manage-beta.microsoft.com/?portalAction=Compliance
      SettingsUrl: eyJVcmlzIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyJdfQ==
   JoinSrvVersion: 1.0
       JoinSrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/device/
        JoinSrvId: urn:ms-drs:enterpriseregistration.windows.net
    KeySrvVersion: 1.0
        KeySrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/key/
         KeySrvId: urn:ms-drs:enterpriseregistration.windows.net
     DomainJoined: YES
       DomainName: CONTOSO

+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

             NgcSet: YES
           NgcKeyId: {C7A9AEDC-780E-4FDA-B200-1AE15561A46B}
    WorkplaceJoined: NO
      WamDefaultSet: YES
WamDefaultAuthority: organizations
       WamDefaultId: https://login.microsoft.com
     WamDefaultGUID: {B16898C6-A148-4967-9171-64D755DA8520} (AzureAd)
         AzureAdPrt: YES
```

### <a name="step-2-evaluate-the-join-status"></a>Stap 2: De joinstatus evalueren 

Bekijk de volgende velden en controleer of de verwachte waarden zijn:

#### <a name="domainjoined--yes"></a>DomainJoined : JA  

In dit veld wordt aangegeven of het apparaat is verbonden met een on-premises Active Directory of niet. Als de waarde **NEE**is, kan het apparaat geen hybride Azure AD-join uitvoeren.  

#### <a name="workplacejoined--no"></a>WorkplaceJoined : NEE  

In dit veld wordt aangegeven of het apparaat is geregistreerd bij Azure AD als een persoonlijk apparaat (gemarkeerd als *Workplace Joined).* Deze waarde moet **NEE** zijn voor een met een domein verbonden computer die ook hybride Azure AD heeft samengevoegd. Als de waarde **JA**is, is een werk- of schoolaccount toegevoegd voordat de hybride Azure AD wordt voltooid. In dit geval wordt het account genegeerd wanneer u de verjaardagsupdateversie van Windows 10 (1607) gebruikt.

#### <a name="azureadjoined--yes"></a>AzureAdJoined : JA  

In dit veld wordt aangegeven of het apparaat is verbonden. De waarde is **JA** als het apparaat een Azure AD-apparaat is of een hybride Azure AD-apparaat.
Als de waarde **NEE**is, is de join to Azure AD nog niet voltooid. 

Ga naar de volgende stappen voor verdere probleemoplossing.

### <a name="step-3-find-the-phase-in-which-join-failed-and-the-errorcode"></a>Stap 3: De fase zoeken waarin deelnemen is mislukt en de foutcode

#### <a name="windows-10-1803-and-above"></a>Windows 10 1803 en hoger

Zoek naar 'Vorige registratie' onderafdeling in de sectie 'Diagnostische gegevens' van de join status output. Deze sectie wordt alleen weergegeven als het apparaat is samengevoegd en azure AD niet kan hybrideworden.
Het veld 'Foutfase' geeft de fase van de joinfout aan, terwijl 'Client ErrorCode' de foutcode van de bewerking Join aangeeft.

```
+----------------------------------------------------------------------+
     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-xxxx-xxxx-xxxx-94ae875d5245) is not found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e
+----------------------------------------------------------------------+
```

#### <a name="older-windows-10-versions"></a>Oudere Windows 10-versies

Gebruik Logboeklogboeken om de fase- en foutcode voor de joinfouten te vinden.

1. Open de **gebeurtenislogboeken voor gebruikersapparaatregistratie** in gebeurtenisviewer. Bevindt zich onder **Toepassingen en Services-logboek** > **Microsoft** > **Windows-gebruikersapparaatregistratie** **Windows** > 
2. Zoek naar evenementen met de volgende eventID's 304, 305, 307.

![Gebeurtenis logboek mislukte](./media/troubleshoot-hybrid-join-windows-current/1.png)

![Gebeurtenis logboek mislukte](./media/troubleshoot-hybrid-join-windows-current/2.png)

### <a name="step-4-check-for-possible-causes-and-resolutions-from-the-lists-below"></a>Stap 4: Controleer op mogelijke oorzaken en resoluties uit de onderstaande lijsten

#### <a name="pre-check-phase"></a>Pre-check fase

Mogelijke redenen voor het falen:

- Apparaat heeft geen zichtlijn naar de domeincontroller.
   - Het apparaat moet zich op het interne netwerk van de organisatie of op VPN bevinden met netwerkzichtlijn naar een on-premises Active Directory -domeincontroller (AD).

#### <a name="discover-phase"></a>Ontdek fase

Mogelijke redenen voor het falen:

- SCP-object (Service Connection Point) is verkeerd geconfigureerd/kan SCP-object uit DC niet lezen.
   - Een geldig SCP-object is vereist in het AD-forest, waartoe het apparaat behoort, dat verwijst naar een geverifieerde domeinnaam in Azure AD.
   - Details zijn te vinden in de sectie [Een serviceverbindingspunt configureren](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join).
- Het niet verbinden en ophalen van de detectiemetagegevens van het detectieeindpunt.
   - Het apparaat moet in `https://enterpriseregistration.windows.net`staat zijn om toegang te krijgen tot , in de SYSTEEM-context, om de registratie- en autorisatieeindpunten te ontdekken. 
   - Als de on-premises omgeving een uitgaande proxy vereist, moet de IT-beheerder ervoor zorgen dat het computeraccount van het apparaat in staat is om de uitgaande proxy te ontdekken en in stilte te verifiëren.
- Het niet verbinden van het eindpunt van gebruikersrealm en het uitvoeren van realm-detectie. (Windows 10 versie 1809 en later alleen)
   - Het apparaat moet in `https://login.microsoftonline.com`staat zijn om toegang te krijgen tot , in de systeemcontext, om realm detectie uit te voeren voor het geverifieerde domein en het domeintype te bepalen (beheerd /gefedereerd).
   - Als de on-premises omgeving een uitgaande proxy vereist, moet de IT-beheerder ervoor zorgen dat de systeemcontext op het apparaat de uitgaande proxy kan ontdekken en in stilte kan verifiëren.

**Veelvoorkomende foutcodes:**

- **DSREG_AUTOJOIN_ADCONFIG_READ_FAILED** (0x801c001d/-2145648611)
   - Reden: kan het SCP-object niet lezen en de azure AD-tenantgegevens ontvangen.
   - Oplossing: raadpleeg de sectie [Een serviceverbindingspunt configureren](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join).
- **DSREG_AUTOJOIN_DISC_FAILED** (0x801c0021/-2145648607)
   - Reden: Generieke discovery-fout. Kan de detectiemetagegevens van DRS niet krijgen.
   - Oplossing: Zoek de onderfout hieronder om verder te onderzoeken.
- **DSREG_AUTOJOIN_DISC_WAIT_TIMEOUT** (0x801c001f/-2145648609)
   - Reden: Er is een time-out ophet gebied van de operatie tijdens het uitvoeren van Discovery.
   - Oplossing: Zorg `https://enterpriseregistration.windows.net` ervoor dat dit toegankelijk is in de systeemcontext. Zie voor meer informatie de sectie [Netwerkconnectiviteitsvereisten](hybrid-azuread-join-managed-domains.md#prerequisites).
- **DSREG_AUTOJOIN_USERREALM_DISCOVERY_FAILED** (0x801c0021/-2145648611)
   - Reden: Generieke Realm Discovery mislukking. Kan het domeintype (beheerd/gefedereerd) van STS niet bepalen. 
   - Oplossing: Zoek de onderfout hieronder om verder te onderzoeken.

**Veelvoorkomende subfoutcodes:**

Als u de subfoutcode voor de detectiefoutcode wilt vinden, gebruikt u een van de volgende methoden.

##### <a name="windows-10-1803-and-above"></a>Windows 10 1803 en hoger

Zoek naar 'DRS Discovery Test' in het gedeelte 'Diagnostische gegevens' van de join status output. Deze sectie wordt alleen weergegeven als het apparaat is samengevoegd en azure AD niet kan hybrideworden.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : UN-ELEVATED User
               Client Time : 2019-06-05 08:25:29.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : FAIL [0x801c0021/0x80072ee2]
     DRS Connectivity Test : SKIPPED
    Token acquisition Test : SKIPPED
     Fallback to Sync-Join : ENABLED

+----------------------------------------------------------------------+
```

##### <a name="older-windows-10-versions"></a>Oudere Windows 10-versies

Gebruik Logboeklogboeken om de fase en foutcode voor de joinfouten te vinden.

1. Open de **gebeurtenislogboeken voor gebruikersapparaatregistratie** in gebeurtenisviewer. Bevindt zich onder **Toepassingen en Services-logboek** > **Microsoft** > **Windows-gebruikersapparaatregistratie** **Windows** > 
2. Zoek naar gebeurtenissen met de volgende eventIDs 201

![Gebeurtenis logboek mislukte](./media/troubleshoot-hybrid-join-windows-current/5.png)

###### <a name="network-errors"></a>Netwerkfouten

- **WININET_E_CANNOT_CONNECT** (0x80072efd/-2147012867)
   - Reden: Verbinding met de server kon niet worden vastgesteld
   - Oplossing: zorg voor netwerkconnectiviteit met de vereiste Microsoft-bronnen. Zie [Netwerkconnectiviteitsvereisten](hybrid-azuread-join-managed-domains.md#prerequisites)voor meer informatie .
- **WININET_E_TIMEOUT** (0x80072ee2/-2147012894)
   - Reden: Algemene netwerktime-out.
   - Oplossing: zorg voor netwerkconnectiviteit met de vereiste Microsoft-bronnen. Zie [Netwerkconnectiviteitsvereisten](hybrid-azuread-join-managed-domains.md#prerequisites)voor meer informatie .
- **WININET_E_DECODING_FAILED** (0x80072f8f/-2147012721)
   - Reden: Netwerkstack kan het antwoord van de server niet decoderen.
   - Oplossing: zorg ervoor dat de netwerkproxy de serverrespons niet verstoort en wijzigt.

###### <a name="http-errors"></a>HTTP-fouten

- **DSREG_DISCOVERY_TENANT_NOT_FOUND** (0x801c003a/-2145648582)
   - Reden: SCP-object geconfigureerd met verkeerde tenant-id. Of er zijn geen actieve abonnementen gevonden in de tenant.
   - Oplossing: ervoor zorgen dat scp-object is geconfigureerd met de juiste Azure AD-tenant-id en actieve abonnementen of aanwezig is in de tenant.
- **DSREG_SERVER_BUSY** (0x801c0025/-2145648603)
   - Reden: HTTP 503 van DRS-server.
   - Oplossing: server is momenteel niet beschikbaar. toekomstige join pogingen zal waarschijnlijk slagen zodra de server is weer online.

###### <a name="other-errors"></a>Andere fouten

- **E_INVALIDDATA** (0x8007000d/-2147024883)
   - Reden: Server reactie JSON kon niet worden ontleed. Waarschijnlijk te wijten aan proxy terug te keren HTTP 200 met een HTML auth pagina.
   - Oplossing: Als de on-premises omgeving een uitgaande proxy vereist, moet de IT-beheerder ervoor zorgen dat de systeemcontext op het apparaat de uitgaande proxy kan ontdekken en in stilte kan verifiëren.

#### <a name="authentication-phase"></a>Verificatiefase

Alleen van toepassing op federatieve domeinaccounts.

Redenen voor het mislukken:

- Kan een Access-token niet in stilte krijgen voor DRS-bron.
   - Windows 10-apparaten krijgen een auth-token van de federatieservice met geïntegreerde Windows-verificatie tot een actief WS-Trust-eindpunt. Details: [Federation Service Configuration](hybrid-azuread-join-manual.md#set-up-issuance-of-claims)

**Veelvoorkomende foutcodes:**

Gebruik Logboeklogboeken om de foutcode, subfoutcode, serverfoutcode en serverfoutbericht te vinden.

1. Open de **gebeurtenislogboeken voor gebruikersapparaatregistratie** in gebeurtenisviewer. Bevindt zich onder **Toepassingen en Services-logboek** > **Microsoft** > **Windows-gebruikersapparaatregistratie** **Windows** > 
2. Zoek naar gebeurtenissen met de volgende gebeurtenisID 305

![Gebeurtenis logboek mislukte](./media/troubleshoot-hybrid-join-windows-current/3.png)

##### <a name="configuration-errors"></a>Configuratiefouten

- **ERROR_ADAL_PROTOCOL_NOT_SUPPORTED** (0xcaa90017/-894894057)
   - Reden: Verificatieprotocol is geen WS-Trust.
   - Oplossing: De on-premises identiteitsprovider moet WS-Trust ondersteunen 
- **ERROR_ADAL_FAILED_TO_PARSE_XML** (0xcaa9002c/-894894036)
   - Reden: On-premises federation service heeft geen XML-antwoord retourneren.
   - Oplossing: zorg ervoor dat MEX-eindpunt een geldige XML retouraft. Zorg ervoor dat proxy niet interfereert en niet-xml-reacties retourt.
- **ERROR_ADAL_COULDNOT_DISCOVER_USERNAME_PASSWORD_ENDPOINT** (0xcaa90023/-894894045)
   - Reden: Kan geen eindpunt ontdekken voor verificatie van gebruikersnaam/wachtwoord.
   - Oplossing: Controleer de instellingen van de on-premises identiteitsprovider. Zorg ervoor dat de WS-Trust-eindpunten zijn ingeschakeld en zorg ervoor dat het MEX-antwoord deze juiste eindpunten bevat.

##### <a name="network-errors"></a>Netwerkfouten

- **ERROR_ADAL_INTERNET_TIMEOUT** (0xcaa82ee2/-894947614)
   - Reden: Algemene netwerktime-out.
   - Oplossing: Zorg `https://login.microsoftonline.com` ervoor dat dit toegankelijk is in de systeemcontext. Zorg ervoor dat de on-premises identiteitsprovider toegankelijk is in de SYSTEEMcontext. Zie [Netwerkconnectiviteitsvereisten](hybrid-azuread-join-managed-domains.md#prerequisites)voor meer informatie .
- **ERROR_ADAL_INTERNET_CONNECTION_ABORTED** (0xcaa82efe/-894947586)
   - Reden: Verbinding met het auth-eindpunt is afgebroken.
   - Oplossing: Probeer het opnieuw na enige tijd of probeer lid te worden vanaf een alternatieve stabiele netwerklocatie.
- **ERROR_ADAL_INTERNET_SECURE_FAILURE** (0xcaa82f8f/-894947441)
   - Reden: het TLS (Transport Layer Security), voorheen bekend als Secure Sockets Layer (SSL), kan niet worden gevalideerd.
   - Oplossing: Controleer de clienttijd scheeftrekken. Probeer het opnieuw na enige tijd of probeer lid te worden van een alternatieve stabiele netwerklocatie. 
- **ERROR_ADAL_INTERNET_CANNOT_CONNECT** (0xcaa82efd/-894947587)
   - Reden: De poging `https://login.microsoftonline.com` om verbinding te maken is mislukt.
   - Oplossing: Netwerkverbinding `https://login.microsoftonline.com`controleren met .

##### <a name="other-errors"></a>Andere fouten

- **ERROR_ADAL_SERVER_ERROR_INVALID_GRANT** (0xcaa20003/-895352829)
   - Reden: SAML-token van de on-premises identiteitsprovider is niet geaccepteerd door Azure AD.
   - Oplossing: Controleer de instellingen van de federatieserver. Zoek naar de serverfoutcode in de verificatielogboeken.
- **ERROR_ADAL_WSTRUST_REQUEST_SECURITYTOKEN_FAILED** (0xcaa90014/-894894060)
   - Reden: Server WS-Trust response gemeld fout uitzondering en het niet om bewering te krijgen
   - Oplossing: Controleer de instellingen van de federatieserver. Zoek naar de serverfoutcode in de verificatielogboeken.
- **ERROR_ADAL_WSTRUST_TOKEN_REQUEST_FAIL** (0xca90006/-894894074)
   - Reden: er is een fout ontvangen wanneer u toegangstoken probeert te krijgen van het tokeneindpunt.
   - Oplossing: Zoek naar de onderliggende fout in het ADAL-logboek. 
- **ERROR_ADAL_OPERATION_PENDING** (0xcaa1002d/-895418323)
   - Reden: Algemene ADAL mislukking
   - Oplossing: zoek naar de subfoutcode of serverfoutcode uit de verificatielogboeken.
    
#### <a name="join-phase"></a>Deelnemen aan fase

Redenen voor het mislukken:

Zoek het registratietype en zoek naar de foutcode in de onderstaande lijst.

#### <a name="windows-10-1803-and-above"></a>Windows 10 1803 en hoger

Zoek naar 'Vorige registratie' onderafdeling in de sectie 'Diagnostische gegevens' van de join status output. Deze sectie wordt alleen weergegeven als het apparaat is samengevoegd en azure AD niet kan hybrideworden.
Het veld 'Registratietype' geeft het type uitgevoerd join aan.

```
+----------------------------------------------------------------------+
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

#### <a name="older-windows-10-versions"></a>Oudere Windows 10-versies

Gebruik Logboeklogboeken om de fase en foutcode voor de joinfouten te vinden.

1. Open de **gebeurtenislogboeken voor gebruikersapparaatregistratie** in gebeurtenisviewer. Bevindt zich onder **Toepassingen en Services-logboek** > **Microsoft** > **Windows-gebruikersapparaatregistratie** **Windows** > 
2. Zoek naar gebeurtenissen met de volgende eventIDs 204

![Gebeurtenis logboek mislukte](./media/troubleshoot-hybrid-join-windows-current/4.png)

##### <a name="http-errors-returned-from-drs-server"></a>HTTP-fouten geretourneerd van DRS-server

- **DSREG_E_DIRECTORY_FAILURE** (0x801c03f2/-2145647630)
   - Reden: Een foutreactie van DRS ontvangen met ErrorCode: "DirectoryError"
   - Oplossing: Raadpleeg de serverfoutcode om mogelijke redenen en oplossingen.
- **DSREG_E_DEVICE_AUTHENTICATION_ERROR** (0x801c0002/-2145648638)
   - Reden: Ontvangen een foutreactie van DRS met ErrorCode: "AuthenticationError" en ErrorSubCode is NIET "DeviceNotFound". 
   - Oplossing: Raadpleeg de serverfoutcode om mogelijke redenen en oplossingen.
- **DSREG_E_DEVICE_INTERNALSERVICE_ERROR** (0x801c0006/-2145648634)
   - Reden: Een foutreactie van DRS ontvangen met ErrorCode: "DirectoryError"
   - Oplossing: Raadpleeg de serverfoutcode om mogelijke redenen en oplossingen.

##### <a name="tpm-errors"></a>TPM-fouten

- **NTE_BAD_KEYSET** (0x80090016/-2146893802)
   - Reden: TPM-bewerking is mislukt of ongeldig is
   - Oplossing: Waarschijnlijk te wijten aan een slechte sysprep afbeelding. Controleer of de machine waarvan de sysprep-afbeelding is gemaakt, niet is toegetreden tot Azure AD, dat hybride Azure AD is samengevoegd of azure AD is geregistreerd.
- **TPM_E_PCP_INTERNAL_ERROR** (0x80290407/-2144795641)
   - Reden: Generieke TPM-fout. 
   - Resolutie: Schakel TPM uit op apparaten met deze fout. Windows 10 versie 1809 en hoger detecteert automatisch TPM-fouten en voltooit hybride Azure AD-join zonder de TPM te gebruiken.
- **TPM_E_NOTFIPS** (0x80280036/-2144862154)
   - Reden: TPM in FIPS-modus wordt momenteel niet ondersteund.
   - Resolutie: Schakel TPM uit op apparaten met deze fout. Windows 1809 detecteert automatisch TPM-fouten en voltooit hybride Azure AD-join zonder de TPM te gebruiken.
- **NTE_AUTHENTICATION_IGNORED** (0x80090031/-2146893775)
   - Reden: TPM buitengesloten.
   - Oplossing: Tijdelijke fout. Wacht op de cooldown periode. Deelnemen poging na enige tijd moet slagen. Meer Informatie is te vinden in het artikel [TPM fundamentals](/windows/security/information-protection/tpm/tpm-fundamentals#anti-hammering)

##### <a name="network-errors"></a>Netwerkfouten

- **WININET_E_TIMEOUT** (0x80072ee2/-2147012894)
   - Reden: Algemene netwerktime-out proberen om het apparaat te registreren bij DRS
   - Resolutie: Controleer de `https://enterpriseregistration.windows.net`netwerkverbinding naar .
- **WININET_E_NAME_NOT_RESOLVED** (0x80072ee7/-2147012889)
   - Reden: de servernaam of het adres kan niet worden opgelost.
   - Resolutie: Controleer de `https://enterpriseregistration.windows.net`netwerkverbinding naar . Zorg ervoor dat de DNS-resolutie voor de hostnaam nauwkeurig is in de n/w en op het apparaat.
- **WININET_E_CONNECTION_ABORTED** (0x80072efe/-2147012866)
   - Reden: De verbinding met de server is abnormaal beëindigd.
   - Oplossing: Probeer het opnieuw na enige tijd of probeer lid te worden vanaf een alternatieve stabiele netwerklocatie.

##### <a name="federated-join-server-errors"></a>Federatieve joinserverfouten

| Serverfoutcode | Serverfoutbericht | Mogelijke redenen | Oplossing |
| --- | --- | --- | --- |
| DirectoryFout | Uw aanvraag wordt tijdelijk in beslag genomen. Probeer het na 300 seconden. | Verwachte fout. Mogelijk te wijten aan het maken van meerdere registratieaanvragen in snelle opeenvolging. | Opnieuw proberen lid te worden na de cooldown-periode |

##### <a name="sync-join-server-errors"></a>Fouten in joinserver synchroniseren

| Serverfoutcode | Serverfoutbericht | Mogelijke redenen | Oplossing |
| --- | --- | --- | --- |
| DirectoryFout | AADSTS90002: <UUID> Huurder niet gevonden. Deze fout kan optreden als er geen actieve abonnementen voor de tenant zijn. Neem contact op met uw abonnementsbeheerder. | Tenant-id in SCP-object is onjuist | Controleer of het SCP-object is geconfigureerd met de juiste Azure AD-tenant-id en actieve abonnementen en aanwezig is in de tenant. |
| DirectoryFout | Het apparaatobject van de opgegeven ID wordt niet gevonden. | Verwachte fout voor synchronisatie join. Het apparaatobject is niet gesynchroniseerd van AD naar Azure AD | Wacht tot de synchronisatie van Azure AD Connect is voltooid en de volgende joinpoging na synchronisatie wordt opgelost |
| VerificatieFout | De verificatie van de SID van de doelcomputer | Het certificaat op het Azure AD-apparaat komt niet overeen met het certificaat dat wordt gebruikt om de blob te ondertekenen tijdens de synchronisatiejoin. Deze fout betekent meestal dat de synchronisatie nog niet is voltooid. |  Wacht tot de synchronisatie van Azure AD Connect is voltooid en de volgende joinpoging na synchronisatie wordt opgelost |

### <a name="step-5-collect-logs-and-contact-microsoft-support"></a>Stap 5: Logboeken verzamelen en contact opnemen met Microsoft Support

Hier krijg je openbare scripts: [ https://1drv.ms/u/s! AkyTjQ17vtfagYkZ6VJzPg78e3o7PQ]( https://1drv.ms/u/s!AkyTjQ17vtfagYkZ6VJzPg78e3o7PQ)

1. Open een opdrachtprompt `start_ngc_tracing_public.cmd`voor beheerders en voer uit .
2. Voer de stappen uit om het probleem te reproduceren.
3. Stop met het uitvoeren `stop_ngc_tracing_public.cmd`van het logboekscript door het uitvoeren van .
4. Zip en stuur `%SYSTEMDRIVE%\TraceDJPP\*` de logs onder voor analyse.

## <a name="troubleshoot-post-join-issues"></a>Problemen met post-join oplossen

### <a name="retrieve-the-join-status"></a>De joinstatus ophalen 

#### <a name="wamdefaultset-yes-and-azureadprt-yes"></a>WamDefaultSet: JA en AzureADPrt: JA
  
Deze velden geven aan of de gebruiker zich bij het aanmelden bij het apparaat heeft geverifieerd bij Azure AD. Als de waarden **NEE**zijn, kan dit het gevolg zijn:

- Slechte opslagsleutel in de TPM die bij registratie aan het apparaat is gekoppeld (controleer de KeySignTest terwijl u verhoogd loopt).
- Alternatieve aanmeldings-id
- HTTP-proxy niet gevonden

## <a name="known-issues"></a>Bekende problemen
- Onder Instellingen -> Accounts -> Toegang tot werk of school kunnen hybride Azure AD-apparaten twee verschillende accounts weergeven, een voor Azure AD en een voor on-premises AD, wanneer deze zijn verbonden met mobiele hotspots of externe WiFi-netwerken. Dit is slechts een ui-probleem en heeft geen invloed op de functionaliteit. 
 
## <a name="next-steps"></a>Volgende stappen

Doorgaan [met het oplossen van problemen met apparaten met de opdracht dsregcmd](troubleshoot-device-dsregcmd.md)

Zie de [veelgestelde vragen over apparaatbeheer](faq.md) voor vragen
