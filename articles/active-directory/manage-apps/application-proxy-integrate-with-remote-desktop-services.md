---
title: Extern bureaublad publiceren met Azure AD-appproxy | Microsoft Documenten
description: Dekt de basisprincipes van Azure AD Application Proxy-connectors.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mimart
ms.custom: it-pro
ms.reviewer: harshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6ca64e2de5734c567173fc735776074f4c87fbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67108468"
---
# <a name="publish-remote-desktop-with-azure-ad-application-proxy"></a>Extern bureaublad publiceren met Azure AD-toepassingsproxy

Remote Desktop Service en Azure AD Application Proxy werken samen om de productiviteit te verbeteren van werknemers die zich niet van het bedrijfsnetwerk bevinden. 

Het beoogde publiek voor dit artikel is:
- Huidige application proxy-klanten die meer toepassingen aan hun eindgebruikers willen aanbieden door on-premises toepassingen te publiceren via Extern bureaublad-services.
- Huidige klanten van Extern bureaublad-services die het aanvalsoppervlak van hun implementatie willen verminderen met Azure AD-toepassingsproxy. Dit scenario biedt een beperkte set controle- en voorwaardelijke toegangsbesturingselementen in twee stappen voor RDS.

## <a name="how-application-proxy-fits-in-the-standard-rds-deployment"></a>Hoe Application Proxy past in de standaard RDS-implementatie

Een standaard RDS-implementatie omvat verschillende Extern bureaublad-functieservices die op Windows Server worden uitgevoerd. Als we kijken naar de [architectuur van Extern bureaublad-services,](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture)zijn er meerdere implementatieopties. In tegenstelling tot andere RDS-implementatieopties heeft de [RDS-implementatie met Azure AD Application Proxy](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture) (weergegeven in het volgende diagram) een permanente uitgaande verbinding van de server waarop de connectorservice wordt uitgevoerd. Andere implementaties laten binnenkomende verbindingen openen via een load balancer.

![Application Proxy zit tussen de RDS VM en het openbare internet](./media/application-proxy-integrate-with-remote-desktop-services/rds-with-app-proxy.png)

In een RDS-implementatie worden de rd-webrol en de rd gateway-rol uitgevoerd op op internet gerichte machines. Deze eindpunten worden blootgesteld om de volgende redenen:
- RD Web biedt de gebruiker een openbaar eindpunt om in te loggen en de verschillende on-premises toepassingen en desktops te bekijken waartoe hij toegang heeft. Bij het selecteren van een resource wordt een RDP-verbinding gemaakt met behulp van de native app op het besturingssysteem.
- Rd Gateway komt in beeld zodra een gebruiker de RDP-verbinding start. De RD Gateway verwerkt versleuteld RDP-verkeer dat via internet komt en vertaalt dit naar de on-premises server waarmee de gebruiker verbinding maakt. In dit scenario is het verkeer dat de Extern bureaublad-gateway ontvangt afkomstig van de Azure AD Application Proxy.

>[!TIP]
>Als u RDS nog niet eerder hebt geïmplementeerd of meer informatie wilt voordat u begint, leest u hoe [u RDS naadloos implementeert met Azure Resource Manager en Azure Marketplace.](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure)

## <a name="requirements"></a>Vereisten

- Gebruik een andere client dan de Extern bureaublad-webclient, omdat de webclient geen application proxy ondersteunt.

- Zowel de RD Web- als de RD Gateway-eindpunten moeten zich op dezelfde machine bevinden en met een gemeenschappelijke hoofdwortel. RD Web en RD Gateway worden gepubliceerd als één toepassing met Application Proxy, zodat u één aanmeldingservaring hebben tussen de twee toepassingen.

- U had [RDS](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure)al moeten hebben geïmplementeerd en [Application Proxy moeten inschakelen.](application-proxy-add-on-premises-application.md)

- In dit scenario wordt ervan uitgegaan dat uw eindgebruikers via Internet Explorer gaan op Windows 7- of Windows 10-bureaubladen die verbinding maken via de RD-webpagina. Zie [Ondersteuning voor andere clientconfiguraties](#support-for-other-client-configurations)als u andere besturingssystemen moet ondersteunen.

- Bij het publiceren van RD Web wordt aanbevolen om dezelfde interne en externe FQDN te gebruiken. Als de interne en externe FQDN's anders zijn, moet u De vertaling van de koptekst aanvragen uitschakelen om te voorkomen dat de client ongeldige koppelingen ontvangt. 

- Schakel in Internet Explorer de RDS ActiveX-invoegtoepassing in.

- Voor de azure AD-pre-authenticatiestroom kunnen gebruikers alleen verbinding maken met resources die met hen zijn gepubliceerd in het deelvenster **RemoteApp en Desktops.** Gebruikers kunnen geen verbinding maken met een bureaublad via het **deelvenster Verbinding maken met een extern pc-deelvenster.**

## <a name="deploy-the-joint-rds-and-application-proxy-scenario"></a>Het gezamenlijke RDS- en Toepassingsproxyscenario implementeren

Volg na het instellen van RDS- en Azure AD-toepassingsproxy voor uw omgeving de stappen om de twee oplossingen te combineren. Deze stappen doorlopen het publiceren van de twee webgerichte RDS-eindpunten (RD Web en RD Gateway) als toepassingen en leiden vervolgens verkeer op uw RDS om door Application Proxy te gaan.

### <a name="publish-the-rd-host-endpoint"></a>Het eindpunt van de Rd-host publiceren

1. [Publiceer een nieuwe toepassingstoepassing Application Proxy](application-proxy-add-on-premises-application.md) met de volgende waarden:
   - Interne URL: `https://\<rdhost\>.com/` `\<rdhost\>` , waar is de algemene hoofdmap die Rd Web en Rd Gateway delen.
   - Externe URL: Dit veld wordt automatisch ingevuld op basis van de naam van de toepassing, maar u deze wijzigen. Uw gebruikers gaan naar deze URL wanneer ze toegang krijgen tot RDS.
   - Pre-verificatiemethode: Azure Active Directory
   - URL-headers vertalen: Nee
2. Gebruikers toewijzen aan de gepubliceerde RD-toepassing. Zorg ervoor dat ze allemaal toegang hebben tot RDS, ook.
3. Laat de enkele aanmeldingsmethode voor de toepassing als **Azure AD single sign-on disabled**. Uw gebruikers wordt gevraagd om één keer te verifiëren bij Azure AD en één keer naar RD Web, maar hebben eenmalige aanmelding bij Rd Gateway.
4. Selecteer **Azure Active Directory**en vervolgens **App-registraties**. Kies uw app in de lijst.
5. Selecteer **Onder Beheren**de optie **Branding**.
6. Werk het **veld URL van** de startpagina bij `https://\<rdhost\>.com/RDWeb`om naar het RD-webeindpunt te wijzen (zoals ).

### <a name="direct-rds-traffic-to-application-proxy"></a>Extern bureaublad-verkeer naar application proxy leiden

Maak verbinding met de RDS-implementatie als beheerder en wijzig de naam van de Extern bureaublad-gatewayserver voor de implementatie. Deze configuratie zorgt ervoor dat verbindingen via de Azure AD Application Proxy-service gaan.

1. Maak verbinding met de RDS-server met de rol Extern bureaublad Connection Broker.
2. **Serverbeheer starten**.
3. Selecteer **Extern bureaublad-services** in het deelvenster aan de linkerkant.
4. Selecteer **Overzicht**.
5. Selecteer in de sectie Implementatieoverzicht het vervolgkeuzemenu en kies **Implementatie-eigenschappen bewerken**.
6. Wijzig op het tabblad Extern bureaublad-gateway het veld **Servernaam** in de externe URL die u instelt voor het eindpunt van de Extern bureaublad-host in Application Proxy.
7. Wijzig het veld **Aanmeldingsmethode** in **Wachtwoordverificatie**.

   ![Scherm Implementatie-eigenschappen op RDS](./media/application-proxy-integrate-with-remote-desktop-services/rds-deployment-properties.png)

8. Voer deze opdracht uit voor elke verzameling. Vervang * \<uw\> collectienaam* en * \<\> proxyfrontendurl* door uw eigen informatie. Met deze opdracht u één aanmelding tussen Rd Web en Rd Gateway inschakelen en de prestaties optimaliseren:

   ```
   Set-RDSessionCollectionConfiguration -CollectionName "<yourcollectionname>" -CustomRdpProperty "pre-authentication server address:s:<proxyfrontendurl>`nrequire pre-authentication:i:1"
   ```

   **Bijvoorbeeld:**
   ```
   Set-RDSessionCollectionConfiguration -CollectionName "QuickSessionCollection" -CustomRdpProperty "pre-authentication server address:s:https://remotedesktoptest-aadapdemo.msappproxy.net/`nrequire pre-authentication:i:1"
   ```
   >[!NOTE]
   >De bovenstaande opdracht maakt gebruik van een backtick in "'nrequire".

9. Voer de volgende opdracht uit om de wijziging van de aangepaste RDP-eigenschappen te verifiëren en de inhoud van het RDP-bestand weer te geven die voor deze verzameling van RDWeb wordt gedownload:
    ```
    (get-wmiobject -Namespace root\cimv2\terminalservices -Class Win32_RDCentralPublishedRemoteDesktop).RDPFileContents
    ```

Nu u Extern bureaublad hebt geconfigureerd, heeft Azure AD Application Proxy het overgenomen als het internetgerichte onderdeel van RDS. U de andere openbare internet-gerichte eindpunten op uw Extern bureaublad-web- en Extern bureaublad-gatewaymachines verwijderen.

## <a name="test-the-scenario"></a>Het scenario testen

Test het scenario met Internet Explorer op een Windows 7- of 10-computer.

1. Ga naar de externe URL die u hebt ingesteld of zoek uw toepassing in het [deelvenster MyApps](https://myapps.microsoft.com).
2. U wordt gevraagd te verifiëren bij Azure Active Directory. Gebruik een account dat u aan de toepassing hebt toegewezen.
3. U wordt gevraagd zich te authenticeren naar Rd Web.
4. Zodra uw RDS-verificatie is geslaagd, u het gewenste bureaublad of de gewenste toepassing selecteren en aan de slag gaan.

## <a name="support-for-other-client-configurations"></a>Ondersteuning voor andere clientconfiguraties

De configuratie in dit artikel is voor gebruikers op Windows 7 of 10, met Internet Explorer plus de RDS ActiveX-invoegtoepassing. Als dat nodig is, u echter andere besturingssystemen of browsers ondersteunen. Het verschil zit hem in de verificatiemethode die u gebruikt.

| Verificatiemethode | Ondersteunde clientconfiguratie |
| --------------------- | ------------------------------ |
| Verificatie vooraf    | Windows 7/10 met Internet Explorer + RDS ActiveX-invoegtoepassing |
| Passthrough | Elk ander besturingssysteem dat de Microsoft Remote Desktop-toepassing ondersteunt |

De pre-authenticatiestroom biedt meer beveiligingsvoordelen dan de passthrough-stroom. Met pre-authenticatie u Azure AD-verificatiefuncties gebruiken, zoals single sign-on, Conditional Access en tweestapsverificatie voor uw on-premises resources. U zorgt er ook voor dat alleen geverifieerd verkeer uw netwerk bereikt.

Om passthrough-verificatie te gebruiken, zijn er slechts twee wijzigingen in de stappen in dit artikel:
1. Stel in Stap 1 [van de RD-hosteindestap](#publish-the-rd-host-endpoint) de methode Preauthentication in op **Passthrough**.
2. Sla stap 8 volledig over in [Direct RDS-verkeer naar Application Proxy.](#direct-rds-traffic-to-application-proxy)

## <a name="next-steps"></a>Volgende stappen

[Externe toegang tot SharePoint inschakelen met Azure AD-toepassingsproxy](application-proxy-integrate-with-sharepoint-server.md)  
[Beveiligingsoverwegingen voor toegang tot apps op afstand met Azure AD-toepassingsproxy](application-proxy-security.md)
