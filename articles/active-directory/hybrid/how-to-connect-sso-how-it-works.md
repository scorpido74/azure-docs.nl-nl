---
title: 'Azure AD Connect: naadloze aanmelding - Zo werkt het | Microsoft Documenten'
description: In dit artikel wordt beschreven hoe de functie Seamless Single Sign-On van Azure Active Directory werkt.
services: active-directory
keywords: wat is Azure AD Connect, Active Directory installeren, vereiste onderdelen voor Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/16/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd4743bc38c3b2b4b9495b33535b4b73f48d1372
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71176670"
---
# <a name="azure-active-directory-seamless-single-sign-on-technical-deep-dive"></a>Azure Active Directory Seamless Single Sign-On: technische diepe duik

In dit artikel vindt u technische details over hoe de functie Seamless Single Sign-On (Seamless SSO) van Azure Active Directory werkt.

## <a name="how-does-seamless-sso-work"></a>Hoe werkt Seamless SSO?

Deze sectie bestaat uit drie delen:

1. De installatie van de Seamless SSO-functie.
2. Hoe een aanmeldingstransactie van één gebruiker in een webbrowser werkt met Seamless SSO.
3. Hoe een aanmeldingstransactie van één gebruiker op een native client werkt met Seamless SSO.

### <a name="how-does-set-up-work"></a>Hoe werkt het opzetten?

Naadloze SSO is ingeschakeld met Azure AD Connect zoals [hier](how-to-connect-sso-quick-start.md)wordt weergegeven. Terwijl u de functie inschakelt, vinden de volgende stappen plaats:

- In elk`AZUREADSSOACC`AD-forest dat u synchroniseert met Azure AD () wordt een computeraccount ( ) gemaakt in uw on-premises Active Directory (AD) dat u synchroniseert met Azure AD (met Azure AD Connect).
- Daarnaast worden een aantal Kerberos-serviceprincipalnamen (SPN's) gemaakt om te worden gebruikt tijdens het Azure AD-aanmeldingsproces.
- De Kerberos-decryptiesleutel van het computeraccount wordt veilig gedeeld met Azure AD. Als er meerdere AD-forests zijn, heeft elk computeraccount zijn eigen unieke Kerberos-decryptiesleutel.

>[!IMPORTANT]
> Het `AZUREADSSOACC` computeraccount moet om veiligheidsredenen sterk worden beschermd. Alleen domeinbeheerders moeten het computeraccount kunnen beheren. Controleer of Kerberos-delegatie op het computeraccount is uitgeschakeld en dat geen enkel `AZUREADSSOACC` ander account in Active Directory delegatiemachtigingen heeft op het computeraccount.. Sla het computeraccount op in een organisatie-eenheid (OU) waar ze niet per ongeluk kunnen worden verwijderd en waar alleen domeinbeheerders toegang hebben. De Kerberos decryptie sleutel op de computer account moet ook worden behandeld als gevoelig. We raden u ten zeerste aan om `AZUREADSSOACC` de [Kerberos-decryptiesleutel](how-to-connect-sso-faq.md) van het computeraccount ten minste om de 30 dagen te omrollen.

Zodra de set-up is voltooid, werkt Seamless SSO op dezelfde manier als elke andere aanmelding die gebruikmaakt van Integrated Windows Authentication (IWA).

### <a name="how-does-sign-in-on-a-web-browser-with-seamless-sso-work"></a>Hoe werkt aanmelden in een webbrowser met Seamless SSO?

De aanmeldingsstroom in een webbrowser is als volgt:

1. De gebruiker probeert toegang te krijgen tot een webtoepassing (bijvoorbeeld de Outlook Web App - https://outlook.office365.com/owa/) vanaf een bedrijfsapparaat dat is verbonden met een domein binnen uw bedrijfsnetwerk.
2. Als de gebruiker nog niet is aangemeld, wordt de gebruiker doorgestuurd naar de aanmeldingspagina van Azure AD.
3. De gebruiker typt zijn gebruikersnaam in op de aanmeldingspagina van Azure AD.

   >[!NOTE]
   >Voor [bepaalde toepassingen](./how-to-connect-sso-faq.md)worden stappen 2 & 3 overgeslagen.

4. Met JavaScript op de achtergrond daagt Azure AD de browser uit om via een ongeautoriseerde reactie van 401 een Kerberos-ticket te bieden.
5. De browser vraagt op zijn beurt een `AZUREADSSOACC` ticket van Active Directory voor het computeraccount (dat Azure AD vertegenwoordigt).
6. Active Directory zoekt het computeraccount en stuurt een Kerberos-ticket terug naar de browser die is versleuteld met het geheim van het computeraccount.
7. De browser stuurt het Kerberos-ticket dat het heeft verkregen van Active Directory door naar Azure AD.
8. Azure AD decodeert het Kerberos-ticket, dat de identiteit bevat van de gebruiker die is aangemeld bij het bedrijfsapparaat, met behulp van de eerder gedeelde sleutel.
9. Na evaluatie retourneert Azure AD een token terug naar de toepassing of vraagt de gebruiker om aanvullende bewijzen uit te voeren, zoals Multi-Factor Authentication.
10. Als de aanmelding van de gebruiker succesvol is, heeft de gebruiker toegang tot de toepassing.

Het volgende diagram illustreert alle componenten en de stappen die daarbij betrokken zijn.

![Naadloze single sign-on - Web-app flow](./media/how-to-connect-sso-how-it-works/sso2.png)

Naadloze SSO is opportunistisch, wat betekent dat als het mislukt, de aanmeldingservaring terugvalt naar zijn normale gedrag - dat wil zeggen, de gebruiker moet zijn wachtwoord invoeren om in te loggen.

### <a name="how-does-sign-in-on-a-native-client-with-seamless-sso-work"></a>Hoe werkt aanmelden bij een native client met Seamless SSO?

De aanmeldingsstroom op een native client is als volgt:

1. De gebruiker probeert toegang te krijgen tot een native toepassing (bijvoorbeeld de Outlook-client) vanaf een bedrijfsapparaat dat is verbonden met een domein binnen uw bedrijfsnetwerk.
2. Als de gebruiker nog niet is aangemeld, haalt de native toepassing de gebruikersnaam van de gebruiker op uit de Windows-sessie van het apparaat.
3. De app stuurt de gebruikersnaam naar Azure AD en haalt het WS-Trust MEX-eindpunt van uw tenant op. Dit WS-Trust-eindpunt wordt uitsluitend gebruikt door de Seamless SSO-functie en is geen algemene implementatie van het WS-Trust-protocol op Azure AD.
4. De app vraagt vervolgens het MEX-eindpunt van WS-Trust om te zien of er een geïntegreerd verificatieeindpunt beschikbaar is. Het geïntegreerde authenticatieeindpunt wordt uitsluitend gebruikt door de Seamless SSO-functie.
5. Als stap 4 slaagt, wordt een Kerberos-uitdaging uitgegeven.
6. Als de app het Kerberos-ticket kan ophalen, wordt het doorsturen naar het geïntegreerde verificatieeindpunt van Azure AD.
7. Azure AD decodeert het Kerberos-ticket en valideert het.
8. Azure AD meldt de gebruiker in en geeft een SAML-token uit aan de app.
9. De app verzendt vervolgens het SAML-token naar het OAuth2-tokeneindpunt van Azure AD.
10. Azure AD valideert het SAML-token en geeft de app een toegangstoken en een vernieuwingstoken voor de opgegeven bron en een id-token.
11. De gebruiker krijgt toegang tot de bron van de app.

Het volgende diagram illustreert alle componenten en de stappen die daarbij betrokken zijn.

![Naadloze single sign-on - Native app flow](./media/how-to-connect-sso-how-it-works/sso14.png)

## <a name="next-steps"></a>Volgende stappen

- [**Snel aan de slag**](how-to-connect-sso-quick-start.md) : ga aan de slag met Azure AD Seamless SSO.
- [**Veelgestelde vragen**](how-to-connect-sso-faq.md) - Antwoorden op veelgestelde vragen.
- [**Problemen oplossen**](tshoot-connect-sso.md) : meer informatie over het oplossen van veelvoorkomende problemen met de functie.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - Voor het indienen van nieuwe functieaanvragen.
