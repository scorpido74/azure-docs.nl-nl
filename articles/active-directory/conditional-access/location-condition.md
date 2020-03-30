---
title: Locatievoorwaarde in voorwaardelijke toegang van Azure Active Directory
description: Meer informatie over het gebruik van de locatievoorwaarde om de toegang tot uw cloud-apps te beheren op basis van de netwerklocatie van een gebruiker.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.workload: identity
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 915675af1e646f2cb77e36c0018ed372ff9496fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263229"
---
# <a name="what-is-the-location-condition-in-azure-active-directory-conditional-access"></a>Wat is de locatievoorwaarde in voorwaardelijke toegang van Azure Active Directory? 

Met [voorwaardelijke toegang voor Azure Active Directory (Azure AD)](../active-directory-conditional-access-azure-portal.md)u bepalen hoe geautoriseerde gebruikers toegang hebben tot uw cloud-apps. Met de locatievoorwaarde van een beleid voor voorwaardelijke toegang u toegangsbesturingselementen koppelen aan de netwerklocaties van uw gebruikers.

In dit artikel vindt u de informatie die u nodig hebt om de locatievoorwaarde te configureren.

## <a name="locations"></a>Locaties

Azure AD maakt single sign-on op apparaten, apps en services vanaf elke locatie op het openbare internet mogelijk. Met de locatievoorwaarde u de toegang tot uw cloud-apps beheren op basis van de netwerklocatie van een gebruiker. Veelvoorkomende gebruiksvoorbeelden voor de locatievoorwaarde zijn:

- Vereist multi-factor authenticatie voor gebruikers die toegang hebben tot een service wanneer ze zich buiten het bedrijfsnetwerk bevinden.
- Toegang blokkeren voor gebruikers die toegang hebben tot een service vanuit specifieke landen of regio's.

Een locatie is een label voor een netwerklocatie die een benoemde locatie of multi-factor authenticatie Trusted IP's vertegenwoordigt.

## <a name="named-locations"></a>Benoemde locaties

Met benoemde locaties u logische groeperingen van IP-adresbereiken of landen en regio's maken.

U hebt toegang tot uw benoemde locaties in het gedeelte **Beheren** van de pagina Voorwaardelijke toegang.

![Benoemde locaties in Voorwaardelijke toegang](./media/location-condition/02.png)

Een benoemde locatie heeft de volgende componenten:

![Een nieuwe locatie met nieuwe naam maken](./media/location-condition/42.png)

- **Naam** - De weergavenaam van een benoemde locatie.
- **IP-bereiken** - Een of meer IPv4-adresbereiken in CIDR-indeling. Het opgeven van een IPv6-adresbereik wordt niet ondersteund.

   > [!NOTE]
   > IPv6-adresbereiken kunnen momenteel niet worden opgenomen in een benoemde locatie. Dit betekent dat IPv6-bereiken niet kunnen worden uitgesloten van een beleid voor voorwaardelijke toegang.

- **Markeren als vertrouwde locatie** - een vlag die u instellen voor een benoemde locatie om een vertrouwde locatie aan te geven. Vertrouwde locaties zijn doorgaans netwerkgebieden die worden beheerd door uw IT-afdeling. Naast voorwaardelijke toegang worden vertrouwde benoemde locaties ook gebruikt door Azure Identity Protection en Azure AD-beveiligingsrapporten om [fout-positieven](../reports-monitoring/concept-risk-events.md#impossible-travel-to-atypical-locations-1)te verminderen.
- **Landen/regio's** - Met deze optie u een of meer landen of regio's selecteren om een benoemde locatie te definiëren.
- **Onbekende gebieden opnemen** - Sommige IP-adressen zijn niet toegewezen aan een specifiek land of regio. Met deze optie u kiezen of deze IP-adressen moeten worden opgenomen in de genoemde locatie. Gebruik deze instelling wanneer het beleid met de benoemde locatie moet worden toegepast op onbekende locaties.

Het aantal benoemde locaties dat u configureren, wordt beperkt door de grootte van het gerelateerde object in Azure AD. U locaties configureren op basis van de volgende beperkingen:

- Een benoemde locatie met maximaal 1200 IP-bereiken.
- Maximaal 90 benoemde locaties met één IP-bereik dat aan elk van hen is toegewezen.

Het beleid voor voorwaardelijke toegang is van toepassing op IPv4- en IPv6-verkeer. Met de huidige locaties kunnen iPv6-bereiken niet worden geconfigureerd. Deze beperking veroorzaakt de volgende situaties:

- Beleid voor voorwaardelijke toegang kan niet worden gericht op specifieke IPv6-bereiken
- Beleid voor voorwaardelijke toegang kan specifieke IPV6-bereiken niet uitsluiten

Als een beleid is geconfigureerd om toe te passen op 'Elke locatie', is dit van toepassing op IPv4- en IPv6-verkeer. Benoemde locaties die zijn geconfigureerd voor bepaalde landen en regio's ondersteunen alleen IPv4-adressen. IPv6-verkeer is alleen inbegrepen als de optie om onbekende gebieden op te nemen is geselecteerd.

## <a name="trusted-ips"></a>Goedgekeurde IP-adressen

U ook IP-adresbereiken configureren die het lokale intranet van uw organisatie weergeven in de [instellingen voor multi-factor authenticatieservice.](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx) Met deze functie u maximaal 50 IP-adresbereiken configureren. De IP-adresbereiken zijn in CIDR-indeling. Zie [Vertrouwde IP's voor](../authentication/howto-mfa-mfasettings.md#trusted-ips)meer informatie.  

Als u Vertrouwde IP's hebt geconfigureerd, worden deze weergegeven als **MFA Trusted IPS** in de lijst met locaties voor de locatievoorwaarde.

### <a name="skipping-multi-factor-authentication"></a>Meervoudige verificatie overslaan

Op de pagina multi-factor authenticatieserviceinstellingen u gebruikers van bedrijfsintranetgebruikers identificeren door **multifactorverificatie overslaan voor aanvragen van federatieve gebruikers op mijn intranet te**selecteren. Deze instelling geeft aan dat de claim van het binnenbedrijfsnetwerk, die wordt uitgegeven door AD FS, moet worden vertrouwd en gebruikt om de gebruiker te identificeren als zijnde op het bedrijfsnetwerk. Zie [De functie Vertrouwde IP's inschakelen met voorwaardelijke toegang](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access)voor meer informatie.

Nadat u deze optie hebt ingedrukt, is MFA **Trusted IPS** van toepassing op elk beleid dat met deze optie is geselecteerd.

Voor mobiele en desktoptoepassingen, die een sessielevensduur van lange duur hebben, wordt Voorwaardelijke toegang periodiek opnieuw geëvalueerd. De standaardinstelling is eenmaal per uur. Wanneer de claim voor binnenbedrijfsnetwerken alleen wordt uitgegeven op het moment van de eerste verificatie, heeft Azure AD mogelijk geen lijst met vertrouwde IP-bereiken. In dit geval is het moeilijker om te bepalen of de gebruiker zich nog steeds op het bedrijfsnetwerk bevindt:

1. Controleer of het IP-adres van de gebruiker zich in een van de vertrouwde IP-bereiken bevindt.
2. Controleer of de eerste drie octetten van het IP-adres van de gebruiker overeenkomen met de eerste drie octetten van het IP-adres van de eerste verificatie. Het IP-adres wordt vergeleken met de initiële verificatie toen de claim voor het binnenbedrijfsnetwerk oorspronkelijk werd uitgegeven en de gebruikerslocatie is gevalideerd.

Als beide stappen mislukken, wordt een gebruiker beschouwd als niet langer op een vertrouwd IP.If both steps fail, a user is considered to be no longer at a trusted IP.

## <a name="location-condition-configuration"></a>Configuratie van locatievoorwaarde

Wanneer u de locatievoorwaarde configureert, u onderscheid maken tussen:

- Elke locatie
- Alle vertrouwde locaties
- Geselecteerde locaties

![Configuratie van locatievoorwaarde](./media/location-condition/01.png)

### <a name="any-location"></a>Elke locatie

Als u **Elke locatie** selecteert, wordt een beleid toegepast op alle IP-adressen, wat betekent dat elk adres op internet wordt gewijzigd. Deze instelling is niet beperkt tot IP-adressen die u hebt geconfigureerd als locatie met naam. Wanneer u **Een locatie**selecteert, u nog steeds specifieke locaties uitsluiten van een beleid. U bijvoorbeeld een beleid toepassen op alle locaties, behalve vertrouwde locaties, om het bereik in te stellen op alle locaties, behalve het bedrijfsnetwerk.

### <a name="all-trusted-locations"></a>Alle vertrouwde locaties

Deze optie is van toepassing op:

- Alle locaties die zijn gemarkeerd als vertrouwde locatie
- **MFA Trusted IPS** (indien geconfigureerd)

### <a name="selected-locations"></a>Geselecteerde locaties

Met deze optie u een of meer benoemde locaties selecteren. Als u een beleid met deze instelling wilt toepassen, moet een gebruiker verbinding maken vanaf een van de geselecteerde locaties. Wanneer u op Het benoemde netwerkselectiebesturingselement **selecteren** klikt waarin de lijst met benoemde netwerken wordt weergegeven, wordt deze geopend. In de lijst wordt ook aangegeven of de netwerklocatie is gemarkeerd als vertrouwd. De benoemde locatie genaamd **MFA Trusted IP's** wordt gebruikt om de IP-instellingen op te nemen die kunnen worden geconfigureerd op de pagina met de instelling van de multi-factor-verificatieservice.

## <a name="what-you-should-know"></a>Wat u moet weten

### <a name="when-is-a-location-evaluated"></a>Wanneer wordt een locatie geëvalueerd?

Beleid voor voorwaardelijke toegang wordt geëvalueerd wanneer:

- Een gebruiker meldt zich in eerste instantie aan bij een web-, mobiele of desktoptoepassing.
- Een mobiele of desktoptoepassing die moderne verificatie gebruikt, gebruikt een vernieuwingstoken om een nieuw toegangstoken te verkrijgen. Standaard is deze controle eenmaal per uur.

Deze controle betekent voor mobiele en desktop toepassingen met behulp van moderne authenticatie, een verandering in de locatie zou worden gedetecteerd binnen een uur na het wijzigen van de locatie van het netwerk. Voor mobiele en desktoptoepassingen die geen moderne verificatie gebruiken, wordt het beleid toegepast op elke tokenaanvraag. De frequentie van de aanvraag kan variëren op basis van de aanvraag. Ook voor webapplicaties wordt het beleid toegepast bij de eerste aanmelding en is het goed voor de levensduur van de sessie bij de webtoepassing. Vanwege verschillen in sessielevensduur tussen toepassingen, zal de tijd tussen beleidsevaluatie ook variëren. Elke keer dat de toepassing een nieuw aanmeldingstoken aanvraagt, wordt het beleid toegepast.

Azure AD geeft standaard een token uit op uurbasis. Na het verplaatsen van het bedrijfsnetwerk wordt binnen een uur het beleid afgedwongen voor toepassingen die moderne verificatie gebruiken.

### <a name="user-ip-address"></a>IP-adres van gebruiker

Het IP-adres dat wordt gebruikt bij beleidsevaluatie is het openbare IP-adres van de gebruiker. Voor apparaten op een privénetwerk is dit IP-adres niet het client-IP-IP-adres van het apparaat van de gebruiker op het intranet, maar het adres dat door het netwerk wordt gebruikt om verbinding te maken met het openbare internet.

> [!WARNING]
> Als uw apparaat alleen een IPv6-adres heeft, wordt het configureren van de locatievoorwaarde niet ondersteund.

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Bulk uploaden en downloaden van benoemde locaties

Wanneer u benoemde locaties maakt of bijwerkt, u voor bulkupdates een CSV-bestand met de IP-bereiken uploaden of downloaden. Een upload vervangt de IP-bereiken in de lijst door die uit het bestand. Elke rij van het bestand bevat één IP-adresbereik in CIDR-indeling.

### <a name="cloud-proxies-and-vpns"></a>Cloudproxy's en VPN's

Wanneer u een door de cloud gehoste proxy of VPN-oplossing gebruikt, wordt het IP-adres dat Azure AD gebruikt tijdens de evaluatie van een beleid het IP-adres van de proxy. De X-Forwarded-For (XFF) header die het openbare IP-adres van de gebruiker bevat, wordt niet gebruikt omdat er geen validatie is dat het afkomstig is van een vertrouwde bron, dus zou een methode presenteren om een IP-adres te faken.

Wanneer een cloudproxy aanwezig is, kan een beleid worden gebruikt waarvoor een domeinlid apparaat vereist is, of de inside corpnet-claim van AD FS.

### <a name="api-support-and-powershell"></a>API-ondersteuning en PowerShell

API en PowerShell worden nog niet ondersteund voor benoemde locaties of voor beleid voor voorwaardelijke toegang.

## <a name="next-steps"></a>Volgende stappen

- Zie MFA vereisen voor specifieke apps met [Voorwaardelijke toegang met Azure Active Directory Conditional Access](app-based-mfa.md)als u wilt weten hoe u een beleid voor voorwaardelijke toegang configureren.
- Als u klaar bent om beleid voor voorwaardelijke toegang voor uw omgeving te configureren, raadpleegt u de [aanbevolen procedures voor voorwaardelijke toegang in Azure Active Directory](best-practices.md).
