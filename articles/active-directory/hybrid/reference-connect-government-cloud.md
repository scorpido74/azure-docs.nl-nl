---
title: 'Azure AD Connect: hybride identiteits overwegingen voor Azure Government'
description: Speciale overwegingen voor het implementeren van Azure AD Connect met de overheids Cloud.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81378923"
---
# <a name="hybrid-identity-considerations-for-azure-government"></a>Overwegingen voor hybride identiteit voor Azure Government 
In het volgende document worden de overwegingen beschreven voor het implementeren van een hybride omgeving met de Azure Government Cloud.  Deze informatie wordt verstrekt als referentie voor beheerders en architecten die werken met de Azure Government Cloud.  
> [!NOTE] 
> Als u een on-premises AD-omgeving wilt integreren met de Azure Governemnt-Cloud, moet u een upgrade uitvoeren naar de nieuwste versie van [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594). 

> [!NOTE] 
> Raadpleeg de [documentatie](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints) voor een volledige lijst van Amerikaanse Government DoD-eind punten. 

## <a name="pass-through-authentication"></a>Pass-Through-verificatie 
De volgende informatie is bedoeld voor de implementatie van Pass-Through-verificatie (PTA) en de Azure Government Cloud.

### <a name="allow-access-to-urls"></a>Toegang tot URL's toestaan  
Voordat u de Pass-Through-verificatie agent implementeert, controleert u of er een firewall is tussen uw servers en Azure AD. Als uw firewall of Proxy DNS-White List toestaat, voegt u de volgende verbindingen toe: 
> [!NOTE]
> De volgende richt lijnen zijn ook van toepassing op het installeren van de connector voor de [toepassings proxy](https://aka.ms/whyappproxy) voor Azure Government omgevingen.

|URL |Hoe dat wordt gebruikt|
|-----|-----| 
|*. msappproxy.us *. servicebus.usgovcloudapi.net|Communicatie tussen de agent en de Azure AD-Cloud service |
|mscrl.microsoft.us:80 crl.microsoft.us:80 </br>ocsp.msocsp.us:80 www.microsoft.us:80| De agent gebruikt deze Url's om certificaten te verifiëren.| 
|login.Windows.us Secure.aadcdn.microsoftonline-p.com *. microsoftonline.us </br> *. microsoftonline-p.us </br>*. msauth.net </br> *. msauthimages.net </br>*. msecnd.net</br>*. msftauth.net </br>*. msftauthimages.net</br>*. phonefactor.net </br>enterpriseregistration.windows.net</br>management.azure.com </br>policykeyservice.dc.ad.msft.net</br>ctdl.windowsupdate.us:80| De agent gebruikt deze Url's tijdens het registratie proces.| 

### <a name="install-the-agent-for-the-azure-government-cloud"></a>De agent voor de Azure Government Cloud installeren 
Als u de agent voor de Azure Government Cloud wilt installeren, moet u de volgende specifieke stappen uitvoeren: Ga in de opdracht regel terminal naar map waarin het uitvoer bare bestand voor de installatie van de agent zich bevindt. Voer de volgende opdracht uit, waarmee wordt aangegeven dat de installatie is voor Azure Government. 

Voor Passthrough-verificatie: 
```
AADConnectAuthAgentSetup.exe ENVIRONMENTNAME="AzureUSGovernment"
```

Voor toepassings proxy:
```
AADApplicationProxyConnectorInstaller.exe ENVIRONMENTNAME="AzureUSGovernment" 
```

## <a name="single-sign-on"></a>Eenmalige aanmelding 
Uw Azure AD Connect-server instellen: als u Pass-Through-verificatie gebruikt als uw aanmeldings methode, is er geen aanvullende controle op vereisten vereist. Als u wachtwoord hash-synchronisatie gebruikt als uw aanmeldings methode en als er een firewall is tussen Azure AD Connect en Azure AD, moet u ervoor zorgen dat:
- U gebruikt versie 1.1.644.0 of hoger van Azure AD Connect. 
- Als uw firewall of Proxy DNS-White List toestaat, voegt u de verbindingen toe aan de *. msapproxy.us-Url's via poort 443. Als dat niet het geval is, verleent u toegang tot de IP-bereiken van het Azure-Data Center, die wekelijks worden bijgewerkt. Deze vereiste is alleen van toepassing wanneer u de functie inschakelt. Het is niet vereist voor de werkelijke gebruikers aanmeldingen. 

### <a name="rolling-out-seamless-sso"></a>Naadloze SSO implementeren 
U kunt met behulp van de onderstaande instructies de naadloze SSO voor uw gebruikers geleidelijk uitrollen. U begint met het toevoegen van de volgende Azure AD-URL aan de intranet zone-instellingen van alle of geselecteerde gebruikers met behulp van groepsbeleid in Active Directory:https://autologon.microsoft.us 

Daarnaast moet u de beleids instelling voor intranet zones met de naam updates toestaan voor de status balk via script inschakelen via groepsbeleid. Browser overwegingen Mozilla Firefox (alle platformen) Mozilla Firefox maakt niet automatisch gebruik van Kerberos-verificatie. Elke gebruiker moet de URL van Azure AD hand matig toevoegen aan hun Firefox-instellingen door de volgende stappen te volgen: 
1. Voer Firefox uit en voer about: config in de adres balk in. Sluit alle meldingen die u ziet. 
2. Zoek naar de voor keuren netwerk. Negotiate-auth. Trusted-uri's. In deze voor keur worden vertrouwde sites van Firefox voor Kerberos-verificatie weer gegeven. 
3. Klik met de rechter muisknop en selecteer wijzigen. 
4. Voer https://autologon.microsoft.us in het veld in.
5. Selecteer OK en open vervolgens de browser opnieuw. 

### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Micro soft Edge op basis van chroom (alle platformen) 
Als `AuthNegotiateDelegateAllowlist` u de of de `AuthServerAllowlist` beleids instellingen in uw omgeving hebt overschreven, moet u ervoor zorgen dat u de URL vanhttps://autologon.microsoft.us) Azure AD (aan deze ook toevoegt). 

### <a name="google-chrome-all-platforms"></a>Google Chrome (alle platformen) 
Als `AuthNegotiateDelegateWhitelist` u de of de `AuthServerWhitelist` beleids instellingen in uw omgeving hebt overschreven, moet u ervoor zorgen dat u de URL vanhttps://autologon.microsoft.us) Azure AD (aan deze ook toevoegt). 

## <a name="next-steps"></a>Volgende stappen
[Pass-through Authentication](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)
[Eenmalige aanmelding voor](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites) Pass-Through-verificatie 
