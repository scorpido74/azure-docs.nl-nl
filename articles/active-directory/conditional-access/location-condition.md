---
title: Voor waarde voor de locatie in Azure Active Directory voorwaardelijke toegang
description: Meer informatie over hoe u de locatie voorwaarde kunt gebruiken om de toegang tot uw Cloud-apps te beheren op basis van de netwerk locatie van een gebruiker.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.workload: identity
ms.date: 05/28/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: 781d8b89dd1b7fa6b2ed9707f6d4c485b4abdf20
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/30/2020
ms.locfileid: "84220616"
---
# <a name="using-the-location-condition-in-a-conditional-access-policy"></a>De locatie voorwaarde gebruiken in een beleid voor voorwaardelijke toegang 

Zoals uitgelegd in het [overzichts artikel](overview.md) beleid voor voorwaardelijke toegang zijn de meest eenvoudige een instructie if-then waarmee signalen worden gecombineerd, om beslissingen te nemen en organisatie beleid af te dwingen. Een van de signalen die kunnen worden opgenomen in het besluitvormings proces is de netwerk locatie.

![Conceptuele voorwaardelijke signalen plus beslissing voor het afdwingen van afdwinging](./media/location-condition/conditional-access-signal-decision-enforcement.png)

Organisaties kunnen deze netwerk locatie gebruiken voor algemene taken, zoals: 

- Vereisen multi-factor Authentication voor gebruikers die toegang hebben tot een service wanneer ze zijn uitgeschakeld in het bedrijfs netwerk.
- Toegang blok keren voor gebruikers die toegang hebben tot een service van specifieke landen of regio's.

De netwerk locatie wordt bepaald door het open bare IP-adres dat een client biedt om Azure Active Directory. Beleid voor voorwaardelijke toegang is standaard van toepassing op alle IPv4-en IPv6-adressen. 

> [!TIP]
> IPV6-bereiken worden alleen ondersteund in de interface van de **[benoemde locatie (preview)](#preview-features)** . 

## <a name="named-locations"></a>Benoemde locaties

Locaties worden aangeduid in het Azure portal onder **Azure Active Directory**  >  **beveiligings**  >  **voorwaardelijke toegang**met de  >  **naam locaties**. Deze benoemde netwerk locaties kunnen locaties bevatten zoals de hoofd netwerkbereiken van een organisatie, bereiken van VPN-netwerken of bereiken die u wilt blok keren. 

![Benoemde locaties in de Azure Portal](./media/location-condition/new-named-location.png)

Als u een locatie wilt configureren, moet u ten minste een **naam** en het IP-bereik opgeven. 

Het aantal benoemde locaties dat u kunt configureren, is beperkt door de grootte van het gerelateerde object in azure AD. U kunt locaties configureren op basis van de volgende beperkingen:

- Eén benoemde locatie met Maxi maal 1200 IPv4-bereiken.
- Maxi maal 90 benoemde locaties waaraan één IP-bereik is toegewezen.

> [!TIP]
> IPV6-bereiken worden alleen ondersteund in de interface van de **[benoemde locatie (preview)](#preview-features)** . 

### <a name="trusted-locations"></a>Vertrouwde locaties

Bij het maken van een netwerk locatie heeft een beheerder de mogelijkheid om een locatie te markeren als een vertrouwde locatie. 

![Vertrouwde locaties in de Azure Portal](./media/location-condition/new-trusted-location.png)

Met deze optie kunt u bepalen in het beleid voor voorwaardelijke toegang, waarbij u bijvoorbeeld registratie voor multi-factor Authentication van een vertrouwde netwerk locatie wilt vereisen. Dit is ook van toepassing op het Azure AD Identity Protection risico berekening, waardoor het risico van een gebruiker wordt verkort wanneer dit afkomstig is van een locatie die als vertrouwd is gemarkeerd.

### <a name="countries-and-regions"></a>Landen en regio's

Sommige organisaties kunnen ervoor kiezen om volledige IP-grenzen van landen of regio's te definiëren als benoemde locaties voor beleids regels voor voorwaardelijke toegang. Ze kunnen deze locaties gebruiken bij het blok keren van onnodig verkeer wanneer ze weten dat gebruikers nooit van een locatie, zoals Noord-Korea, zijn. Deze toewijzingen van het IP-adres aan het land worden regel matig bijgewerkt. 

> [!NOTE]
> Landen bevatten geen IPv6-adresbereiken, alleen bekende IPv4-adresbereiken.

![Maak een nieuwe locatie op basis van een land of regio in de Azure Portal](./media/location-condition/new-named-location-country-region.png)

#### <a name="include-unknown-areas"></a>Onbekende gebieden bevatten

Sommige IP-adressen zijn niet toegewezen aan een bepaald land of bepaalde regio. Als u deze IP-locaties wilt vastleggen, schakelt u het selectie vakje **onbekende gebieden opnemen** bij het definiëren van een locatie in. Met deze optie kunt u kiezen of deze IP-adressen op de genoemde locatie moeten worden opgenomen. Gebruik deze instelling wanneer het beleid dat gebruikmaakt van de benoemde locatie, moet worden toegepast op onbekende locaties.

### <a name="configure-mfa-trusted-ips"></a>Vertrouwde IP-adressen van MFA configureren

U kunt ook IP-adresbereiken configureren die het lokale intranet van uw organisatie vertegenwoordigen in de instellingen van de [multi-factor Authentication-Service](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx). Met deze functie kunt u Maxi maal 50 IP-adresbereiken configureren. De IP-adresbereiken bevinden zich in CIDR-indeling. Zie [Trusted ip's](../authentication/howto-mfa-mfasettings.md#trusted-ips)(Engelstalig) voor meer informatie.  

Als u vertrouwde IP-adressen hebt geconfigureerd, worden ze als door **MFA vertrouwde ip's** weer gegeven in de lijst met locaties voor de voor waarde van de locatie.

### <a name="skipping-multi-factor-authentication"></a>Multi-factor Authentication overs Laan

Op de pagina instellingen voor multi-factor Authentication-Service kunt u zakelijke intranet gebruikers identificeren door **multi-factor Authentication overs laan voor aanvragen van federatieve gebruikers op mijn intranet**te selecteren. Deze instelling geeft aan dat de binnen bedrijfs netwerk claim, die wordt uitgegeven door AD FS, moet worden vertrouwd en gebruikt om de gebruiker te identificeren als in het bedrijfs netwerk. Zie [de functie voor vertrouwde IP-adressen inschakelen met behulp van voorwaardelijke toegang](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access)voor meer informatie.

Nadat u deze optie hebt ingeschakeld, worden door de benoemde locatie die door **MFA wordt vertrouwd ip's** toegepast op alle beleids regels waarvoor deze optie is geselecteerd.

Voor mobiele en desktop toepassingen, met een lange levens duur van sessies, wordt voorwaardelijke toegang regel matig opnieuw geëvalueerd. De standaard waarde is eenmaal per uur. Wanneer de binnen bedrijfs netwerk claim alleen wordt uitgegeven op het moment van de eerste authenticatie, heeft Azure AD mogelijk geen lijst met vertrouwde IP-adresbereiken. In dit geval is het moeilijker om te bepalen of de gebruiker zich nog in het bedrijfs netwerk bevindt:

1. Controleer of het IP-adres van de gebruiker zich in een van de vertrouwde IP-adresbereiken bevindt.
1. Controleer of de eerste drie octetten van het IP-adres van de gebruiker overeenkomen met de eerste drie octetten van het IP-adres van de eerste verificatie. Het IP-adres wordt vergeleken met de eerste verificatie wanneer de interne claim van het bedrijfs netwerk oorspronkelijk werd uitgegeven en de locatie van de gebruiker is gevalideerd.

Als beide stappen mislukken, wordt een gebruiker beschouwd als niet meer op een vertrouwd IP-adres.

## <a name="preview-features"></a>Preview-functies

Naast de algemeen beschik bare benoemde locatie functie is er ook een benoemde locatie (preview-versie). U kunt de voor beeld van een benoemde locatie openen met behulp van de banner aan de bovenkant van de Blade huidige benoemde locatie.

![De preview-versie voor benoemde locaties uitproberen](./media/location-condition/preview-features.png)

Met de voor beeld van benoemde locatie kunt u

- Maxi maal 195 benoemde locaties configureren
- Maxi maal 2000 IP-bereiken per benoemde locatie configureren
- Maxi maal IPv6-adressen configureren

Er zijn ook extra controles toegevoegd om de wijziging van de onjuiste configuratie te verminderen.

- Privé-IP-adresbereiken kunnen niet meer worden geconfigureerd
- Het aantal IP-adressen dat kan worden opgenomen in een bereik, is beperkt. Alleen CIDR-maskers die groter zijn dan/8, zijn toegestaan bij het configureren van een IP-bereik.

Er zijn nu twee opties voor het maken van de preview-versie: 

- **Locatie van landen**
- **Locatie van IP-bereiken**

> [!NOTE]
> Landen bevatten geen IPv6-adresbereiken, alleen bekende IPv4-adresbereiken.

![Preview-interface voor benoemde locaties](./media/location-condition/named-location-preview.png)

## <a name="location-condition-in-policy"></a>Voor waarde voor locatie in beleid

Wanneer u de locatie voorwaarde configureert, hebt u de mogelijkheid om onderscheid te maken tussen:

- Elke locatie
- Alle vertrouwde locaties
- Geselecteerde locaties

### <a name="any-location"></a>Elke locatie

Als u een **wille keurige locatie** selecteert, wordt er standaard een beleid toegepast op alle IP-adressen. Dit betekent elk adres op internet. Deze instelling is niet beperkt tot IP-adressen die u hebt geconfigureerd als benoemde locatie. Wanneer u **een wille keurige locatie**selecteert, kunt u nog steeds specifieke locaties uitsluiten van een beleid. U kunt bijvoorbeeld een beleid Toep assen op alle locaties behalve vertrouwde locaties om het bereik in te stellen op alle locaties, met uitzonde ring van het bedrijfs netwerk.

### <a name="all-trusted-locations"></a>Alle vertrouwde locaties

Deze optie is van toepassing op:

- Alle locaties die zijn gemarkeerd als vertrouwde locatie
- **Vertrouwde IP-adressen voor MFA** (indien geconfigureerd)

### <a name="selected-locations"></a>Geselecteerde locaties

Met deze optie kunt u een of meer benoemde locaties selecteren. Een gebruiker moet verbinding maken vanaf een van de geselecteerde locaties om een beleid met deze instelling toe te passen. Wanneer u op het besturings element benoemde netwerk selectie **selecteren** klikt, wordt de lijst met benoemde netwerken weer gegeven. In de lijst wordt ook weer gegeven als de netwerk locatie is gemarkeerd als vertrouwd. De benoemde locatie met de naam **MFA Trusted ip's** wordt gebruikt voor het toevoegen van de IP-instellingen die kunnen worden geconfigureerd op de instellings pagina van de multi-factor Authentication-service.

## <a name="what-you-should-know"></a>Wat u moet weten

### <a name="when-is-a-location-evaluated"></a>Wanneer is een locatie geëvalueerd?

Beleid voor voorwaardelijke toegang wordt geëvalueerd wanneer:

- Een gebruiker meldt zich eerst aan bij een web-app, mobiele of bureaublad toepassing.
- Een mobiele of bureaublad toepassing die gebruikmaakt van moderne verificatie, gebruikt een vernieuwings token om een nieuw toegangs token te verkrijgen. Deze controle is standaard eenmaal per uur.

Dit betekent dat voor mobiele en bureaublad toepassingen met moderne verificatie een wijziging in de locatie wordt gedetecteerd binnen een uur nadat de netwerk locatie is gewijzigd. Voor mobiele en desktop toepassingen die geen moderne verificatie gebruiken, wordt het beleid toegepast op elke token aanvraag. De frequentie van de aanvraag kan variëren op basis van de toepassing. Op dezelfde manier wordt voor webtoepassingen het beleid toegepast bij de eerste aanmelding en is het goed voor de levens duur van de sessie in de webtoepassing. Als gevolg van verschillen in de levens duur van sessies tussen toepassingen, kan de tijd tussen de beleids evaluatie ook verschillen. Telkens wanneer de toepassing een nieuw aanmeldings token aanvraagt, wordt het beleid toegepast.

Standaard geeft Azure AD per uur een token uit. Nadat het bedrijfs netwerk is verplaatst, wordt het beleid binnen een uur afgedwongen voor toepassingen die gebruikmaken van moderne verificatie.

### <a name="user-ip-address"></a>IP-adres van gebruiker

Het IP-adres dat wordt gebruikt in de beleids evaluatie is het open bare IP-adres van de gebruiker. Voor apparaten in een particulier netwerk is dit IP-adres niet het client-IP-adres van het apparaat van de gebruiker op het intranet. het is het door het netwerk gebruikte adressen om verbinding te maken met het open bare Internet.

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Bulksgewijs uploaden en downloaden van benoemde locaties

Wanneer u benoemde locaties maakt of bijwerkt voor bulk updates, kunt u een CSV-bestand uploaden of downloaden met de IP-bereiken. Bij een upload worden de IP-bereiken in de lijst vervangen door die bereiken uit het bestand. Elke rij van het bestand bevat één IP-adres bereik in CIDR-notatie.

### <a name="cloud-proxies-and-vpns"></a>Cloud-proxy's en Vpn's

Wanneer u een door de Cloud gehoste proxy of VPN-oplossing gebruikt, is het IP-adres dat door Azure AD wordt gebruikt bij het evalueren van een beleid, het IP-adres van de proxy. De header X-Forwarding for (XFF) die het open bare IP-adres van de gebruiker bevat, wordt niet gebruikt omdat er geen validatie is van een vertrouwde bron. Daarom zou een methode voor het Faking van een IP-adres worden weer gegeven.

Als er een Cloud proxy is ingesteld, kan een beleid dat wordt gebruikt om een aan een domein gekoppeld apparaat te vereisen, worden gebruikt, of de in-Corpnet claim van AD FS.

### <a name="api-support-and-powershell"></a>API-ondersteuning en Power shell

API en Power shell worden nog niet ondersteund voor benoemde locaties.

## <a name="next-steps"></a>Volgende stappen

- Als u wilt weten hoe u een beleid voor voorwaardelijke toegang kunt configureren, raadpleegt u het artikel [buil ding a Conditional Access Policy](concept-conditional-access-policies.md).
- Zoekt u een voor beeld beleid met behulp van de locatie voorwaarde? Zie het artikel [voorwaardelijke toegang: toegang blok keren op locatie](howto-conditional-access-policy-location.md)
