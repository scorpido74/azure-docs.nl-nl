---
title: Cookie-instellingen voor toepassingsproxy - Azure Active Directory | Microsoft Documenten
description: Azure Active Directory (Azure AD) heeft toegangs- en sessiecookies voor toegang tot on-premises toepassingen via Application Proxy. In dit artikel lees je hoe je de cookie-instellingen gebruiken en configureren.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcedb24a0efdbabaaef150fc3d5aff07d210ce23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481361"
---
# <a name="cookie-settings-for-accessing-on-premises-applications-in-azure-active-directory"></a>Cookie-instellingen voor toegang tot on-premises toepassingen in Azure Active Directory

Azure Active Directory (Azure AD) heeft toegangs- en sessiecookies voor toegang tot on-premises toepassingen via Application Proxy. Ontdek hoe u de cookie-instellingen van Application Proxy gebruiken. 

## <a name="what-are-the-cookie-settings"></a>Wat zijn de cookie-instellingen?

[Application Proxy](application-proxy.md) gebruikt de volgende instellingen voor toegangs- en sessiecookies.

| Cookie-instelling | Standaard | Beschrijving | Aanbevelingen |
| -------------- | ------- | ----------- | --------------- |
| Alleen-HTTP-cookies gebruiken | **Nee** | **Ja,** hiermee kan Application Proxy de HTTPOnly-vlag opnemen in HTTP-antwoordkoppen. Deze vlag biedt extra beveiligingsvoordelen, bijvoorbeeld, het voorkomt dat client-side scripting (CSS) van het kopiëren of wijzigen van de cookies.<br></br><br></br>Voordat we de HTTP-Only-instelling ondersteunden, versleutelde en verzondapplicationproxy cookies via een beveiligd TLS-kanaal om te beschermen tegen wijzigingen. | Gebruik **Ja** vanwege de extra beveiligingsvoordelen.<br></br><br></br>Gebruik **Nee** voor clients of gebruikersagents die wel toegang tot de sessiecookie vereisen. Gebruik bijvoorbeeld **Nee** voor een RDP- of MTSC-client die verbinding maakt met een Extern bureaublad-gatewayserver via Application Proxy.|
| Beveiligde cookies gebruiken | **Nee** | **Ja,** hiermee kan Application Proxy de beveiligde vlag opnemen in HTTP-antwoordkoppen. Secure Cookies verbetert de beveiliging door cookies via een door TLS beveiligd kanaal zoals HTTPS te verzenden. Dit voorkomt dat cookies worden waargenomen door onbevoegde partijen als gevolg van de overdracht van de cookie in duidelijke tekst. | Gebruik **Ja** vanwege de extra beveiligingsvoordelen.|
| Permanente cookies gebruiken | **Nee** | **Ja,** hiermee kan Application Proxy instellen dat de toegangscookies niet verlopen wanneer de webbrowser wordt gesloten. De persistentie duurt totdat het toegangstoken verloopt of totdat de gebruiker de permanente cookies handmatig verwijdert. | Gebruik **Nee** vanwege het beveiligingsrisico dat gepaard gaat met het verifiëren van gebruikers.<br></br><br></br>We raden u aan alleen **Ja** te gebruiken voor oudere toepassingen die geen cookies tussen processen kunnen delen. Het is beter om uw toepassing bij te werken om het delen van cookies tussen processen te verwerken in plaats van permanente cookies te gebruiken. U hebt bijvoorbeeld permanente cookies nodig om een gebruiker in staat te stellen Office-documenten in de Explorer-weergave te openen vanaf een SharePoint-site. Zonder permanente cookies kan deze bewerking mislukken als de toegangscookies niet worden gedeeld tussen de browser, het explorer-proces en het Office-proces. |

## <a name="samesite-cookies"></a>SameSite-cookies
Vanaf versie Chrome 80 en uiteindelijk in browsers die Chromium gebruiken, worden cookies die het [samesite-kenmerk](https://web.dev/samesite-cookies-explained) niet specificeren, behandeld alsof ze zijn ingesteld op **SameSite=Lax.** Het kenmerk SameSite geeft aan hoe cookies moeten worden beperkt tot een context op dezelfde site. Wanneer deze is ingesteld op Lax, wordt de cookie alleen verzonden naar aanvragen op dezelfde site of navigatie op het hoogste niveau. Application Proxy vereist echter dat deze cookies in de context van derden worden bewaard om gebruikers goed te laten inloggen tijdens hun sessie. Als gevolg hiervan maken we updates voor de toegang tot de Application Proxy en sessiecookies om nadelige gevolgen van deze wijziging te voorkomen. De updates omvatten:

* Het kenmerk **SameSite** instellen op **Geen**. Hierdoor kunnen Application Proxy-toegang en sessiescookies correct worden verzonden in de context van derden.
* De instelling **Veilige cookie gebruiken** ingesteld om **Ja** als standaard te gebruiken. Chrome vereist ook dat de cookies de Secure-vlag opgeven, anders wordt deze geweigerd. Deze wijziging is van toepassing op alle bestaande toepassingen die worden gepubliceerd via Application Proxy. Houd er rekening mee dat toepassingsproxytoegangscookies altijd zijn ingesteld op Beveiligen en alleen via HTTPS zijn verzonden. Deze wijziging is alleen van toepassing op de sessiecookies.

Deze wijzigingen in Application Proxy cookies zullen worden uitgerold in de loop van de komende weken voor de Chrome 80 release datum.

Als uw back-endtoepassing cookies heeft die beschikbaar moeten zijn in een context van derden, moet u zich bovendien expliciet aanmelden door uw toepassing te wijzigen om SameSite=None voor deze cookies te gebruiken. Application Proxy vertaalt de set-cookie header naar de URLS en respecteert de instellingen voor deze cookies die door de back-end applicatie zijn ingesteld.



## <a name="set-the-cookie-settings---azure-portal"></a>De cookie-instellingen instellen - Azure-portal
Ga als een te werk om de cookie-instellingen in te stellen met de Azure-portal:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). 
2. Navigeer naar **Azure Active Directory** > **Enterprise-toepassingen** > **Alle toepassingen**.
3. Selecteer de toepassing waarvoor u een cookie-instelling wilt inschakelen.
4. Klik **op Toepassingsproxy**.
5. Stel **onder Aanvullende instellingen**de cookie-instelling in op **Ja** of **Nee**.
6. Klik op **Opslaan** om uw wijzigingen toe te passen. 

## <a name="view-current-cookie-settings---powershell"></a>Huidige cookie-instellingen weergeven - PowerShell

Als u de huidige cookie-instellingen voor de toepassing wilt bekijken, gebruikt u de opdracht PowerShell:  

```powershell
Get-AzureADApplicationProxyApplication -ObjectId <ObjectId> | fl * 
```

## <a name="set-cookie-settings---powershell"></a>Cookie-instellingen instellen - PowerShell

In de volgende PowerShell-opdrachten ```<ObjectId>``` is de ObjectId van de toepassing. 

**Http-Only Cookie** 

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $false 
```

**Veilige cookie**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $false 
```

**Permanente cookies**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $false 
```
