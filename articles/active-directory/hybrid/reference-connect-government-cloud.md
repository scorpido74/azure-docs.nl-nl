---
title: 'Azure AD Connect: hybride identiteitsoverwegingen voor Azure-overheid'
description: Speciale overwegingen voor het implementeren van Azure AD Connect met de overheidscloud.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ad28beb68afb5207e7b36c5d76c4dac808c5114
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81378923"
---
# <a name="hybrid-identity-considerations-for-azure-government"></a>Hybride identiteitsoverwegingen voor Azure Government 
In het volgende document worden de overwegingen beschreven voor het implementeren van een hybride omgeving met de Azure Government-cloud.  Deze informatie wordt verstrekt als referentie voor beheerders en architecten die werken met de Azure Government-cloud.  
> [!NOTE] 
> Als u een on-premises AD-omgeving wilt integreren met de Azure Governemnt-cloud, moet u upgraden naar de nieuwste versie van [Azure AD Connect.](https://www.microsoft.com/download/details.aspx?id=47594) 

> [!NOTE] 
> Voor een volledige lijst van DoD-eindpunten van de Amerikaanse overheid raadpleegt u de [documentatie](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints) 

## <a name="pass-through-authentication"></a>Pass-Through-verificatie 
De volgende informatie wordt verstrekt voor de implementatie van pass-through authentication (PTA) en de Azure Government cloud.

### <a name="allow-access-to-urls"></a>Toegang tot URL's toestaan  
Controleer voordat u de verificatieagent voor doorgeefgegevens implementeert of er een firewall is tussen uw servers en Azure AD. Als uw firewall of proxy DNS-whitelisting toestaat, voegt u de volgende verbindingen toe: 
> [!NOTE]
> De volgende richtlijnen zijn ook van toepassing op het installeren van de [Application Proxy-connector](https://aka.ms/whyappproxy) voor Azure Government-omgevingen.

|URL |Hoe dat wordt gebruikt|
|-----|-----| 
|*.msappproxy.us *.servicebus.usgovcloudapi.net|Communicatie tussen de agent en de Azure AD-cloudservice |
|mscrl.microsoft.us:80 crl.microsoft.us:80 </br>ocsp.msocsp.us:80 www.microsoft.us:80| De agent gebruikt deze URL's om certificaten te verifiëren.| 
|login.windows.us secure.aadcdn.microsoftonline-p.com *secure.aadcdn.microsoftonline-p.com microsoftonline.us </br> *.microsoftonline-p.us </br>*.msauth.net </br> *.msauthimages.net </br>*.msecnd.net</br>*.msftauth.net </br>*.msftauthimages.net</br>*.phonefactor.net </br>enterpriseregistration.windows.net</br>management.azure.com </br>policykeyservice.dc.ad.msft.net</br>ctdl.windowsupdate.us:80| De agent gebruikt deze URL's tijdens het registratieproces.| 

### <a name="install-the-agent-for-the-azure-government-cloud"></a>De agent voor de Azure Government-cloud installeren 
Als u de agent voor de Azure Government-cloud wilt installeren, moet u de volgende specifieke stappen volgen: Navigeer in de opdrachtregelterminal naar de map waar de uitvoerbare voor het installeren van de agent zich bevindt. Voer de volgende opdracht uit die aangeeft dat de installatie voor Azure Government is. 

Voor Passthrough-verificatie: 
```
AADConnectAuthAgentSetup.exe ENVIRONMENTNAME="AzureUSGovernment"
```

Voor application proxy:
```
AADApplicationProxyConnectorInstaller.exe ENVIRONMENTNAME="AzureUSGovernment" 
```

## <a name="single-sign-on"></a>Eén enkel teken op 
Uw Azure AD Connect-server instellen: als u Pass-through Authentication als aanmeldingsmethode gebruikt, is er geen extra vereistecontrole vereist. Als u wachtwoordhashsynchronisatie als aanmeldingsmethode gebruikt en als er een firewall is tussen Azure AD Connect en Azure AD, moet u ervoor zorgen dat:
- U gebruikt versie 1.1.644.0 of hoger van Azure AD Connect. 
- Als uw firewall of proxy DNS-whitelisting toestaat, voegt u de verbindingen toe aan de URL's *.msapproxy.us via poort 443. Zo niet, geef dan toegang tot de IP-bereiken van Azure-datacenters, die wekelijks worden bijgewerkt. Deze voorwaarde is alleen van toepassing wanneer u de functie inschakelt. Dit is niet vereist voor daadwerkelijke gebruikersaanmeldingen. 

### <a name="rolling-out-seamless-sso"></a>Naadloze SSO uitrollen 
U Seamless SSO geleidelijk uitrollen naar uw gebruikers met behulp van de onderstaande instructies. U begint met het toevoegen van de volgende Azure AD-URL aan de instellingen van alle of geselecteerde instellingen voor intranetvan gebruikers met groepsbeleid in Active Directory:https://autologon.microsoft.us 

Daarnaast moet u via groepsbeleid een beleidsinstelling intranetzone inschakelen met de naam Updates toestaan voor de statusbalk via het script. Browser overwegingen Mozilla Firefox (alle platforms) Mozilla Firefox maakt niet automatisch gebruik van Kerberos authenticatie. Elke gebruiker moet de AZURE AD-URL handmatig toevoegen aan zijn Firefox-instellingen met behulp van de volgende stappen: 
1. Voer Firefox uit en voer about:config in de adresbalk in. Alle meldingen die u ziet, verwijderen. 
2. Zoek naar de voorkeur van network.negotiate-auth.trusted-uris. Deze voorkeur geeft een overzicht van de vertrouwde sites van Firefox voor Kerberos-verificatie. 
3. Klik met de rechtermuisknop en selecteer Wijzigen. 
4. Voer https://autologon.microsoft.us in het veld.
5. Selecteer OK en open de browser opnieuw. 

### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Microsoft Edge op basis van Chromium (alle platforms) 
Als u de `AuthNegotiateDelegateAllowlist` of de `AuthServerAllowlist` beleidsinstellingen in uw omgeving hebt overschreven,https://autologon.microsoft.us) moet u ervoor zorgen dat u de URL van Azure AD toevoegt ( ook aan deze instellingen. 

### <a name="google-chrome-all-platforms"></a>Google Chrome (alle platforms) 
Als u de `AuthNegotiateDelegateWhitelist` of de `AuthServerWhitelist` beleidsinstellingen in uw omgeving hebt overschreven,https://autologon.microsoft.us) moet u ervoor zorgen dat u de URL van Azure AD toevoegt ( ook aan deze instellingen. 

## <a name="next-steps"></a>Volgende stappen
[Pass-through Authentication](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)
[Single Sign-on](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites) 
