---
title: Hiervoor vereiste poorten en protocollen voor hybride identiteit - Azure | Microsoft Documenten
description: Deze pagina is een technische referentiepagina voor poorten die moeten worden geopend voor Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: de97b225-ae06-4afc-b2ef-a72a3643255b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 03/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: da318840426d1c0b94eab06b89ff3152df9d26fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331096"
---
# <a name="hybrid-identity-required-ports-and-protocols"></a>Voor hybride identiteit benodigde poorten en protocollen
Het volgende document is een technische referentie over de vereiste poorten en protocollen voor de implementatie van een hybride identiteitsoplossing. Gebruik de volgende afbeelding en raadpleeg de bijbehorende tabel.

![Wat is Azure AD Connect?](./media/reference-connect-ports/required3.png)

## <a name="table-1---azure-ad-connect-and-on-premises-ad"></a>Tabel 1 - Azure AD Connect en on-premises AD
In deze tabel worden de poorten en protocollen beschreven die nodig zijn voor communicatie tussen de Azure AD Connect-server en on-premises AD.

| Protocol | Poorten | Beschrijving |
| --- | --- | --- |
| DNS |53 (TCP/UDP) |DNS-lookups op het bestemmingsbos. |
| Kerberos |88 (TCP/UDP) |Kerberos-verificatie naar het AD-forest. |
| MS-RPC |135 (TCP) |Wordt gebruikt tijdens de eerste configuratie van de wizard Azure AD Connect wanneer deze wordt gekoppeld aan het AD-forest en ook tijdens wachtwoordsynchronisatie. |
| LDAP |389 (TCP/UDP) |Wordt gebruikt voor het importeren van gegevens uit AD. Gegevens worden versleuteld met Kerberos Sign & Seal. |
| SMB | 445 (TCP) |Wordt gebruikt door Seamless SSO om een computeraccount in het AD-forest te maken. |
| LDAP/SSL |636 (TCP/UDP) |Wordt gebruikt voor het importeren van gegevens uit AD. De gegevensoverdracht wordt ondertekend en versleuteld. Alleen gebruikt als u TLS gebruikt. |
| RPC |49152- 65535 (Random high RPC Port)(TCP) |Wordt gebruikt tijdens de eerste configuratie van Azure AD Connect wanneer deze wordt gekoppeld aan de AD-forests en tijdens wachtwoordsynchronisatie. Zie [KB929851](https://support.microsoft.com/kb/929851), [KB832017](https://support.microsoft.com/kb/832017)en [KB224196](https://support.microsoft.com/kb/224196) voor meer informatie. |
|WinRM  | 5985 (TCP) |Alleen gebruikt als u AD FS met gMSA installeert door de wizard Azure AD Connect|
|AD DS-webservices | 9389 (TCP) |Alleen gebruikt als u AD FS met gMSA installeert door de wizard Azure AD Connect |

## <a name="table-2---azure-ad-connect-and-azure-ad"></a>Tabel 2 - Azure AD Connect en Azure AD
In deze tabel worden de poorten en protocollen beschreven die nodig zijn voor communicatie tussen de Azure AD Connect-server en Azure AD.

| Protocol | Poorten | Beschrijving |
| --- | --- | --- |
| HTTP |80 (TCP) |Wordt gebruikt om CRL's (Certificate Revocation Lists) te downloaden om TLS/SSL-certificaten te verifiëren. |
| HTTPS |443(TCP) |Wordt gebruikt om te synchroniseren met Azure AD. |

Zie [Office 365-URL's en IP-adresbereiken en](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) [Azure AD Connect-connectiviteit](tshoot-connect-connectivity.md#troubleshoot-connectivity-issues-in-the-installation-wizard)oplossen voor een lijst met URL's en IP-adressen die u in uw firewall moet openen.

## <a name="table-3---azure-ad-connect-and-ad-fs-federation-serverswap"></a>Tabel 3 - Azure AD Connect en AD FS Federation Servers/WAP
In deze tabel worden de poorten en protocollen beschreven die nodig zijn voor de communicatie tussen de Azure AD Connect-server en de AD FS Federation/WAP-servers.  

| Protocol | Poorten | Beschrijving |
| --- | --- | --- |
| HTTP |80 (TCP) |Wordt gebruikt om CRL's (Certificate Revocation Lists) te downloaden om TLS/SSL-certificaten te verifiëren. |
| HTTPS |443(TCP) |Wordt gebruikt om te synchroniseren met Azure AD. |
| WinRM |5985 |WinRM-listener |

## <a name="table-4---wap-and-federation-servers"></a>Tabel 4 - WAP- en Federatieservers
In deze tabel worden de poorten en protocollen beschreven die nodig zijn voor de communicatie tussen de Federation-servers en WAP-servers.

| Protocol | Poorten | Beschrijving |
| --- | --- | --- |
| HTTPS |443(TCP) |Gebruikt voor verificatie. |

## <a name="table-5---wap-and-users"></a>Tabel 5 - WAP en gebruikers
In deze tabel worden de poorten en protocollen beschreven die nodig zijn voor de communicatie tussen gebruikers en de WAP-servers.

| Protocol | Poorten | Beschrijving |
| --- | --- | --- |
| HTTPS |443(TCP) |Wordt gebruikt voor apparaatverificatie. |
| TCP |49443 (TCP) |Wordt gebruikt voor certificaatverificatie. |

## <a name="table-6a--6b---pass-through-authentication-with-single-sign-on-sso-and-password-hash-sync-with-single-sign-on-sso"></a>Tabel 6a & 6b - Pass-through-verificatie met eenmalige aanmelding (SSO) en wachtwoordhashsynchronisatie met enkele aanmelding (SSO)
In de volgende tabellen worden de poorten en protocollen beschreven die nodig zijn voor communicatie tussen azure AD Connect en Azure AD.

### <a name="table-6a---pass-through-authentication-with-sso"></a>Tabel 6a - Pass-through-verificatie met SSO
|Protocol|Poortnummer|Beschrijving
| --- | --- | ---
|HTTP|80|Schakel uitgaand HTTP-verkeer in voor beveiligingsvalidatie zoals SSL. Ook nodig voor de connector auto-update mogelijkheid om goed te functioneren.
|HTTPS|443| Schakel uitgaand HTTPS-verkeer in voor bewerkingen zoals het in- en uitschakelen van de functie, het registreren van connectors, het downloaden van connector-updates en het afhandelen van alle aanmeldingsverzoeken van gebruikers.

Daarnaast moet Azure AD Connect directe IP-verbindingen kunnen maken met de [IP-bereiken van azure-datacenters.](https://www.microsoft.com/download/details.aspx?id=41653)

### <a name="table-6b---password-hash-sync-with-sso"></a>Tabel 6b - Wachtwoord hash sync met SSO

|Protocol|Poortnummer|Beschrijving
| --- | --- | ---
|HTTPS|443| SSO-registratie inschakelen (alleen vereist voor het SSO-registratieproces).

Daarnaast moet Azure AD Connect directe IP-verbindingen kunnen maken met de [IP-bereiken van azure-datacenters.](https://www.microsoft.com/download/details.aspx?id=41653) Nogmaals, dit is alleen vereist voor het SSO-registratieproces.

## <a name="table-7a--7b---azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabel 7a & 7b - Azure AD Connect-statusagent voor (AD FS/Sync) en Azure AD
In de volgende tabellen worden de eindpunten, poorten en protocollen beschreven die nodig zijn voor communicatie tussen Azure AD Connect Health-agents en Azure AD

### <a name="table-7a---ports-and-protocols-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabel 7a - Poorten en protocollen voor Azure AD Connect-statusagent voor (AD FS/Sync) en Azure AD
In deze tabel worden de volgende uitgaande poorten en protocollen beschreven die nodig zijn voor communicatie tussen de Azure AD Connect Health-agents en Azure AD.  

| Protocol | Poorten | Beschrijving |
| --- | --- | --- |
| HTTPS |443(TCP) |Uitgaand |
| Azure Service Bus |5671 (TCP) |Uitgaand |

Azure Service Bus-poort 5671 is niet langer vereist voor de nieuwste versie van de agent. De nieuwste Azure AD Connect Health-agentversie vereiste alleen poort 443.

### <a name="7b---endpoints-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>7b - Eindpunten voor Azure AD Connect-statusagent voor (AD FS/Sync) en Azure AD
Zie de sectie Vereisten [voor de azure AD Connect-statusagent voor](how-to-connect-health-agent-install.md#requirements)een lijst met eindpunten.

