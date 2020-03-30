---
title: 'Azure AD Connect: problemen met Azure AD-connectiviteit oplossen | Microsoft Documenten'
description: Hier wordt uitgelegd hoe u verbindingsproblemen met Azure AD Connect oplossen.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 3aa41bb5-6fcb-49da-9747-e7a3bd780e64
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72dbb404d1b4d3618909e0233f332d2f98b51516
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049723"
---
# <a name="troubleshoot-azure-ad-connectivity"></a>Azure AD-connectiviteit oplossen
In dit artikel wordt uitgelegd hoe de connectiviteit tussen Azure AD Connect en Azure AD werkt en hoe u verbindingsproblemen oplossen. Deze problemen worden waarschijnlijk gezien in een omgeving met een proxyserver.

## <a name="troubleshoot-connectivity-issues-in-the-installation-wizard"></a>Verbindingsproblemen oplossen in de wizard installatie
Azure AD Connect gebruikt moderne verificatie (met behulp van de ADAL-bibliotheek) voor verificatie. De installatiewizard en de correcte synchronisatieengine vereisen dat machine.config correct is geconfigureerd, aangezien deze twee .NET-toepassingen zijn.

In dit artikel laten we zien hoe Fabrikam verbinding maakt met Azure AD via zijn proxy. De proxyserver heet fabrikamproxy en gebruikt poort 8080.

Eerst moeten we ervoor zorgen dat [**machine.config**](how-to-connect-install-prerequisites.md#connectivity) correct is geconfigureerd.  
![machineconfig](./media/tshoot-connect-connectivity/machineconfig.png)

> [!NOTE]
> In sommige niet-Microsoft-blogs wordt gedocumenteerd dat er wijzigingen moeten worden aangebracht in miiserver.exe.config. Echter, dit bestand is overschreven op elke upgrade, dus zelfs als het werkt tijdens de eerste installatie, het systeem stopt met werken aan de eerste upgrade. Om die reden is de aanbeveling om machine.config in plaats daarvan bij te werken.
>
>

De proxyserver moet ook de vereiste URL's hebben geopend. De officiële lijst is gedocumenteerd in [Office 365-URL's en IP-adresbereiken.](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)

Of these URLs, the following table is the absolute bare minimum to be able to connect to Azure AD at all. Deze lijst bevat geen optionele functies, zoals terugschrijven met wachtwoorden of Azure AD Connect Health. Het is hier gedocumenteerd om te helpen bij het oplossen van problemen voor de eerste configuratie.

| URL | Poort | Beschrijving |
| --- | --- | --- |
| mscrl.microsoft.com |HTTP/80 |Wordt gebruikt om CRL-lijsten te downloaden. |
| \*.verisign.com |HTTP/80 |Wordt gebruikt om CRL-lijsten te downloaden. |
| \*.entrust.net |HTTP/80 |Wordt gebruikt om CRL-lijsten voor MFA te downloaden. |
| \*.windows.net |HTTPS/443 |Wordt gebruikt om u aan te melden bij Azure AD. |
| secure.aadcdn.microsoftonline-p.com |HTTPS/443 |Gebruikt voor MFA. |
| \*.microsoftonline.com |HTTPS/443 |Wordt gebruikt om uw Azure AD-map en import/exportgegevens te configureren. |

## <a name="errors-in-the-wizard"></a>Fouten in de wizard
De wizard installatie gebruikt twee verschillende beveiligingscontexten. Op de pagina **Verbinding maken met Azure AD,** gebruikt deze de momenteel aangemelde gebruiker. Op de pagina **Configureren**wordt het account [dat de service voor de synchronisatieengine uitvoert,](reference-connect-accounts-permissions.md#adsync-service-account)gewijzigd. Als er een probleem is, wordt deze waarschijnlijk al weergegeven op de **PAGINA Verbinding maken met Azure AD** in de wizard, omdat de proxyconfiguratie globaal is.

De volgende problemen zijn de meest voorkomende fouten die u tegenkomt in de installatiewizard.

### <a name="the-installation-wizard-has-not-been-correctly-configured"></a>De wizard Installatie is niet correct geconfigureerd
Deze fout wordt weergegeven wanneer de wizard zelf de proxy niet kan bereiken.  
![nomachineconfig nomachineconfig](./media/tshoot-connect-connectivity/nomachineconfig.png)

* Als u deze fout ziet, controleert u of de [machine.config](how-to-connect-install-prerequisites.md#connectivity) correct is geconfigureerd.
* Als dit er goed uitziet, volgt u de stappen in [Proxy-connectiviteit verifiëren](#verify-proxy-connectivity) om te zien of het probleem ook buiten de wizard aanwezig is.

### <a name="a-microsoft-account-is-used"></a>Er wordt een Microsoft-account gebruikt
Als u een **Microsoft-account** gebruikt in plaats van een **school- of organisatieaccount,** ziet u een algemene fout.  
![Er wordt een Microsoft-account gebruikt](./media/tshoot-connect-connectivity/unknownerror.png)

### <a name="the-mfa-endpoint-cannot-be-reached"></a>Het MFA-eindpunt kan niet worden bereikt
Deze fout wordt weergegeven **https://secure.aadcdn.microsoftonline-p.com** als het eindpunt niet kan worden bereikt en de globale beheerder MFA heeft ingeschakeld.  
![nomachineconfig nomachineconfig](./media/tshoot-connect-connectivity/nomicrosoftonlinep.png)

* Als u deze fout ziet, controleert u of het eindpunt **secure.aadcdn.microsoftonline-p.com** aan de proxy is toegevoegd.

### <a name="the-password-cannot-be-verified"></a>Het wachtwoord kan niet worden geverifieerd
Als de wizard installatie is geslaagd verbinding te maken met Azure AD, maar het wachtwoord zelf niet kan worden geverifieerd, ziet u deze fout:  
![Slecht wachtwoord.](./media/tshoot-connect-connectivity/badpassword.png)

* Is het wachtwoord een tijdelijk wachtwoord en moet het worden gewijzigd? Is het eigenlijk het juiste wachtwoord? Probeer je aan `https://login.microsoftonline.com` te melden bij (op een andere computer dan de Azure AD Connect-server) en controleer of het account bruikbaar is.

### <a name="verify-proxy-connectivity"></a>Proxy-connectiviteit verifiëren
Als u wilt controleren of de Azure AD Connect-server werkelijke verbinding heeft met proxy en internet, gebruikt u een aantal PowerShell om te zien of de proxy webaanvragen toestaat of niet. Voer in een PowerShell-prompt uit `Invoke-WebRequest -Uri https://adminwebservice.microsoftonline.com/ProvisioningService.svc`. (Technisch gezien is de `https://login.microsoftonline.com` eerste oproep is en deze URI werkt ook, maar de andere URI is sneller te reageren.)

PowerShell gebruikt de configuratie in machine.config om contact op te nemen met de proxy. De instellingen in winhttp/netsh mogen geen invloed hebben op deze cmdlets.

Als de proxy correct is geconfigureerd, moet u ![een successtatus krijgen: proxy200](./media/tshoot-connect-connectivity/invokewebrequest200.png)

Als u **geen verbinding maken met de externe server,** probeert PowerShell een rechtstreeks gesprek te voeren zonder de proxy te gebruiken of is DNS niet correct geconfigureerd. Controleer of het **bestand machine.config** correct is geconfigureerd.
![niet in staat om verbinding te maken](./media/tshoot-connect-connectivity/invokewebrequestunable.png)

Als de proxy niet correct is geconfigureerd, ![krijgt u](./media/tshoot-connect-connectivity/invokewebrequest403.png)
![een foutmelding: proxy200 proxy407](./media/tshoot-connect-connectivity/invokewebrequest407.png)

| Fout | Fouttekst | Opmerking |
| --- | --- | --- |
| 403 |Verboden |De proxy is niet geopend voor de gevraagde URL. Bekijk de proxyconfiguratie opnieuw en controleer of de [URL's](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) zijn geopend. |
| 407 |Proxyverificatie vereist |De proxyserver vereiste een aanmelding en er werd geen aanmelding verstrekt. Als uw proxyserver verificatie vereist, moet u deze instelling hebben geconfigureerd in de machine.config. Zorg er ook voor dat u domeinaccounts gebruikt voor de gebruiker die de wizard en het serviceaccount uitvoert. |

### <a name="proxy-idle-timeout-setting"></a>Instelling voor inactieve proxy
Wanneer Azure AD Connect een exportaanvraag naar Azure AD verzendt, kan het tot 5 minuten duren voordat Azure AD de aanvraag verwerkt voordat een antwoord wordt gestart. Dit kan vooral gebeuren als er een aantal groepsobjecten zijn met grote groepslidmaatschappen die in dezelfde exportaanvraag zijn opgenomen. Zorg ervoor dat de inactieve time-out proxy groter is dan 5 minuten. Anders kan een onderbroken verbindingsprobleem met Azure AD worden waargenomen op de Azure AD Connect-server.

## <a name="the-communication-pattern-between-azure-ad-connect-and-azure-ad"></a>Het communicatiepatroon tussen Azure AD Connect en Azure AD
Als u al deze voorgaande stappen hebt gevolgd en nog steeds geen verbinding maken, u op dit moment beginnen met het bekijken van netwerklogboeken. Deze sectie documenteert een normaal en succesvol verbindingspatroon. Het is ook een opsomming van gemeenschappelijke rode haringen die kunnen worden genegeerd wanneer u het lezen van het netwerk logs.

* Er zijn `https://dc.services.visualstudio.com`telefoontjes naar. Het is niet vereist om deze URL te openen in de proxy voor de installatie om te slagen en deze oproepen kunnen worden genegeerd.
* U ziet dat dns-resolutie de werkelijke hosts vermeldt die zich in de DNS-naamruimte nsatc.net en andere naamruimten niet onder microsoftonline.com. Er zijn echter geen webserviceaanvragen op de werkelijke servernamen en u hoeft deze URL's niet aan de proxy toe te voegen.
* De eindpunten adminwebservice en provisioningapi zijn detectie eindpunten en gebruikt om het werkelijke eindpunt te gebruiken te vinden. Deze eindpunten zijn verschillend afhankelijk van uw regio.

### <a name="reference-proxy-logs"></a>Referentieproxylogboeken
Hier is een dump van een werkelijke proxy log en de installatie wizard pagina van waar het is genomen (dubbele vermeldingen naar hetzelfde eindpunt zijn verwijderd). Deze sectie kan worden gebruikt als referentie voor uw eigen proxy en netwerklogboeken. De werkelijke eindpunten kunnen verschillen in uw omgeving (met name die URL's in *cursief).*

**Verbinding maken met Azure AD**

| Time | URL |
| --- | --- |
| 1/11/2016 8:31 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:31 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:32 |connect://*bba800-anker*.microsoftonline.com:443 |
| 1/11/2016 8:32 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:33 |connect://provisioningapi.microsoftonline.com:443 |
| 1/11/2016 8:33 |connect://*bwsc02-relay*.microsoftonline.com:443 |

**Configureren**

| Time | URL |
| --- | --- |
| 1/11/2016 8:43 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:43 |connect://*bba800-anker*.microsoftonline.com:443 |
| 1/11/2016 8:43 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://*bba900-anker*.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://*bba800-anker*.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:46 |connect://provisioningapi.microsoftonline.com:443 |
| 1/11/2016 8:46 |connect://*bwsc02-relay*.microsoftonline.com:443 |

**Eerste synchronisatie**

| Time | URL |
| --- | --- |
| 1/11/2016 8:48 |connect://login.windows.net:443 |
| 1/11/2016 8:49 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:49 |connect://*bba900-anker*.microsoftonline.com:443 |
| 1/11/2016 8:49 |connect://*bba800-anker*.microsoftonline.com:443 |

## <a name="authentication-errors"></a>Verificatiefouten
In deze sectie worden fouten beschreven die kunnen worden geretourneerd uit ADAL (de verificatiebibliotheek die wordt gebruikt door Azure AD Connect) en PowerShell. De uitgelegde fout moet u helpen bij het begrijpen van uw volgende stappen.

### <a name="invalid-grant"></a>Ongeldige subsidie
Ongeldige gebruikersnaam of wachtwoord. Zie [Het wachtwoord kan niet worden geverifieerd](#the-password-cannot-be-verified)voor meer informatie.

### <a name="unknown-user-type"></a>Onbekend gebruikerstype
Uw Azure AD-map kan niet worden gevonden of opgelost. Misschien probeer je in te loggen met een gebruikersnaam in een niet-geverifieerd domein?

### <a name="user-realm-discovery-failed"></a>Detectie van gebruikersrealm is mislukt
Problemen met netwerk- of proxyconfiguratie. Het netwerk is niet bereikbaar. Zie [Verbindingsproblemen oplossen in de wizard Installatie](#troubleshoot-connectivity-issues-in-the-installation-wizard).

### <a name="user-password-expired"></a>Gebruikerswachtwoord verlopen
Uw referenties zijn verlopen. Wijzig uw wachtwoord.

### <a name="authorization-failure"></a>Autorisatiefout
Kan de gebruiker niet autoriseren om actie uit te voeren in Azure AD.

### <a name="authentication-canceled"></a>Verificatie geannuleerd
De multi-factor authenticatie (MFA) uitdaging werd geannuleerd.

<div id="connect-msolservice-failed">
<!--
  Empty div just to act as an alias for the "Connect To MS Online Failed" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="connect-to-ms-online-failed"></a>Verbinding maken met MS Online is mislukt
Verificatie is geslaagd, maar Azure AD PowerShell heeft een verificatieprobleem.

<div id="get-msoluserrole-failed">
<!--
  Empty div just to act as an alias for the "Azure AD Global Admin Role Needed" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="azure-ad-global-admin-role-needed"></a>Rol azure AD Global Admin nodig
De gebruiker is geverifieerd. De gebruiker krijgt echter geen globale beheerdersrol toegewezen. Zo [kunt u de globale beheerrol aan](../users-groups-roles/directory-assign-admin-roles.md) de gebruiker toewijzen. 

<div id="privileged-identity-management">
<!--
  Empty div just to act as an alias for the "Privileged Identity Management Enabled" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="privileged-identity-management-enabled"></a>Privileged Identity Management ingeschakeld
Verificatie is geslaagd. Privileged identity management is ingeschakeld en u bent momenteel geen globale beheerder. Zie [Privileged Identity Management](../privileged-identity-management/pim-getting-started.md)voor meer informatie.

<div id="get-msolcompanyinformation-failed">
<!--
  Empty div just to act as an alias for the "Company Information Unavailable" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="company-information-unavailable"></a>Bedrijfsinformatie niet beschikbaar
Verificatie is geslaagd. Kan geen bedrijfsgegevens ophalen uit Azure AD.

<div id="get-msoldomain-failed">
<!--
  Empty div just to act as an alias for the "Domain Information Unavailable" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="domain-information-unavailable"></a>Domeingegevens niet beschikbaar
Verificatie is geslaagd. Kan domeingegevens niet ophalen uit Azure AD.

### <a name="unspecified-authentication-failure"></a>Niet-gespecificeerde verificatiefout
Wordt weergegeven als onverwachte fout in de wizard installatie. Kan gebeuren als u een **Microsoft-account** probeert te gebruiken in plaats van een **school- of organisatieaccount.**

## <a name="troubleshooting-steps-for-previous-releases"></a>Problemen oplossen voor eerdere versies.
Met releases die beginnen met buildnummer 1.1.105.0 (uitgebracht in februari 2016), werd de inlogassistent met pensioen. Deze sectie en de configuratie moeten niet langer nodig zijn, maar wordt als referentie bewaard.

Als de assistent voor één aanmelding werkt, moet winhttp worden geconfigureerd. Deze configuratie kan worden gedaan met [**netsh**](how-to-connect-install-prerequisites.md#connectivity).  
![Netsh](./media/tshoot-connect-connectivity/netsh.png)

### <a name="the-sign-in-assistant-has-not-been-correctly-configured"></a>De aanmeldingsassistent is niet correct geconfigureerd
Deze fout wordt weergegeven wanneer de aanmeldingsassistent de proxy niet kan bereiken of wanneer de proxy de aanvraag niet toestaat.
![nonetsh nonetsh](./media/tshoot-connect-connectivity/nonetsh.png)

* Als u deze fout ziet, kijkt u naar de proxyconfiguratie in [netsh](how-to-connect-install-prerequisites.md#connectivity) en controleert u of deze juist is.
  ![netshshow](./media/tshoot-connect-connectivity/netshshow.png)
* Als dit er goed uitziet, volgt u de stappen in [Proxy-connectiviteit verifiëren](#verify-proxy-connectivity) om te zien of het probleem ook buiten de wizard aanwezig is.

## <a name="next-steps"></a>Volgende stappen
Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).
