---
title: Wat is de voor waarde van de locatie in Azure Active Directory voorwaardelijke toegang? | Microsoft Docs
description: Meer informatie over hoe u de locatie voorwaarde kunt gebruiken om de toegang tot uw Cloud-apps te beheren op basis van de netwerk locatie van een gebruiker.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.workload: identity
ms.date: 04/12/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 799a994e1351f62fac9f5a07060658cea60c9274
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74065736"
---
# <a name="what-is-the-location-condition-in-azure-active-directory-conditional-access"></a>Wat is de voor waarde van de locatie in Azure Active Directory voorwaardelijke toegang? 

Met [Azure Active Directory (Azure AD) voorwaardelijke toegang](../active-directory-conditional-access-azure-portal.md)kunt u bepalen hoe geautoriseerde gebruikers toegang hebben tot uw Cloud-apps. De locatie voorwaarde van een beleid voor voorwaardelijke toegang stelt u in staat om instellingen voor toegangs beheer te koppelen aan de netwerk locaties van uw gebruikers.

In dit artikel vindt u de informatie die u nodig hebt om de locatie voorwaarde te configureren.

## <a name="locations"></a>Locaties

Met Azure AD kunt u eenmalige aanmelding op apparaten, apps en services vanaf een wille keurige locatie op het open bare Internet. Met de locatie voorwaarde kunt u de toegang tot uw Cloud-apps beheren op basis van de netwerk locatie van een gebruiker. Veelvoorkomende use cases voor de locatie voorwaarde zijn:

- Vereisen multi-factor Authentication voor gebruikers die toegang hebben tot een service wanneer ze zijn uitgeschakeld in het bedrijfs netwerk.
- Toegang blok keren voor gebruikers die toegang hebben tot een service van specifieke landen of regio's.

Een locatie is een label voor een netwerk locatie die een benoemde locatie of multi-factor Authentication-betrouw bare Ip's vertegenwoordigt.

## <a name="named-locations"></a>Benoemde locaties

Met benoemde locaties kunt u logische groeperingen van IP-adresbereiken of landen en regio's maken.

U kunt toegang krijgen tot uw benoemde locaties in het gedeelte **beheren** van de pagina voorwaardelijke toegang.

![Benoemde locaties in voorwaardelijke toegang](./media/location-condition/02.png)

Een benoemde locatie heeft de volgende onderdelen:

![Een nieuwe benoemde locatie maken](./media/location-condition/42.png)

- **Naam** : de weergave naam van een benoemde locatie.
- **IP-adresbereiken** : een of meer IPv4-adresbereiken in CIDR-indeling. Het opgeven van een IPv6-adres bereik wordt niet ondersteund.

   > [!NOTE]
   > IPv6-adresbereiken kunnen momenteel niet worden opgenomen in een benoemde locatie. Dit betekent dat IPv6-bereiken niet kunnen worden uitgesloten van een beleid voor voorwaardelijke toegang.

- **Markeren als vertrouwde locatie** : een vlag die u kunt instellen voor een benoemde locatie om een vertrouwde locatie aan te duiden. Normaal gesp roken zijn vertrouwde locaties netwerk gebieden die worden beheerd door uw IT-afdeling. Naast voorwaardelijke toegang worden vertrouwde benoemde locaties ook gebruikt door Azure Identity Protection en Azure AD-beveiligings rapporten om fout- [positieven](../reports-monitoring/concept-risk-events.md#impossible-travel-to-atypical-locations-1)te reduceren.
- **Landen/regio's** : met deze optie kunt u een of meer land of regio selecteren om een benoemde locatie te definiëren.
- **Onbekende gebieden bevatten** -sommige IP-adressen zijn niet toegewezen aan een bepaald land of bepaalde regio. Met deze optie kunt u kiezen of deze IP-adressen op de genoemde locatie moeten worden opgenomen. Gebruik deze instelling wanneer het beleid dat gebruikmaakt van de benoemde locatie, moet worden toegepast op onbekende locaties.

Het aantal benoemde locaties dat u kunt configureren, is beperkt door de grootte van het gerelateerde object in azure AD. U kunt locaties configureren op basis van de volgende beperkingen:

- Eén benoemde locatie met Maxi maal 1200 IP-bereiken.
- Maxi maal 90 benoemde locaties waaraan één IP-bereik is toegewezen.

Beleid voor voorwaardelijke toegang is van toepassing op IPv4-en IPv6-verkeer. Op momenteel benoemde locaties mogen geen IPv6-bereiken worden geconfigureerd. Deze beperking veroorzaakt de volgende situaties:

- Beleid voor voorwaardelijke toegang kan niet worden gericht op specifieke IPv6-bereiken
- Beleid voor voorwaardelijke toegang kan specifieke IPV6-bereiken niet uitsluiten

Als een beleid is geconfigureerd om te worden toegepast op een wille keurige locatie, is dit van toepassing op IPv4-en IPv6-verkeer. Benoemde locaties die zijn geconfigureerd voor opgegeven landen en regio's ondersteunen alleen IPv4-adressen. IPv6-verkeer wordt alleen opgenomen als de optie voor het opnemen van onbekende gebieden is geselecteerd.

## <a name="trusted-ips"></a>Goedgekeurde IP-adressen

U kunt ook IP-adresbereiken configureren die het lokale intranet van uw organisatie vertegenwoordigen in de instellingen van de [multi-factor Authentication-Service](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx). Met deze functie kunt u Maxi maal 50 IP-adresbereiken configureren. De IP-adresbereiken bevinden zich in CIDR-indeling. Zie [Trusted ip's](../authentication/howto-mfa-mfasettings.md#trusted-ips)(Engelstalig) voor meer informatie.  

Als u vertrouwde IP-adressen hebt geconfigureerd, worden ze als door **MFA vertrouwde ip's** weer gegeven in de lijst met locaties voor de voor waarde van de locatie.

### <a name="skipping-multi-factor-authentication"></a>Multi-factor Authentication overs Laan

Op de pagina instellingen voor multi-factor Authentication-Service kunt u zakelijke intranet gebruikers identificeren door **multi-factor Authentication overs laan voor aanvragen van federatieve gebruikers op mijn intranet**te selecteren. Deze instelling geeft aan dat de binnen bedrijfs netwerk claim, die wordt uitgegeven door AD FS, moet worden vertrouwd en gebruikt om de gebruiker te identificeren als in het bedrijfs netwerk. Zie [de functie voor vertrouwde IP-adressen inschakelen met behulp van voorwaardelijke toegang](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access)voor meer informatie.

Nadat u deze optie hebt ingeschakeld, worden door de benoemde locatie die door **MFA wordt vertrouwd ip's** toegepast op alle beleids regels waarvoor deze optie is geselecteerd.

Voor mobiele en desktop toepassingen, met een lange levens duur van sessies, wordt voorwaardelijke toegang regel matig opnieuw geëvalueerd. De standaard waarde is eenmaal per uur. Wanneer de binnen bedrijfs netwerk claim alleen wordt uitgegeven op het moment van de eerste authenticatie, heeft Azure AD mogelijk geen lijst met vertrouwde IP-adresbereiken. In dit geval is het moeilijker om te bepalen of de gebruiker zich nog in het bedrijfs netwerk bevindt:

1. Controleer of het IP-adres van de gebruiker zich in een van de vertrouwde IP-adresbereiken bevindt.
2. Controleer of de eerste drie octetten van het IP-adres van de gebruiker overeenkomen met de eerste drie octetten van het IP-adres van de eerste verificatie. Het IP-adres wordt vergeleken met de eerste verificatie wanneer de interne claim van het bedrijfs netwerk oorspronkelijk werd uitgegeven en de locatie van de gebruiker is gevalideerd.

Als beide stappen mislukken, wordt een gebruiker beschouwd als niet meer op een vertrouwd IP-adres.

## <a name="location-condition-configuration"></a>Configuratie van locatie voorwaarde

Wanneer u de locatie voorwaarde configureert, hebt u de mogelijkheid om onderscheid te maken tussen:

- Elke locatie
- Alle vertrouwde locaties
- Geselecteerde locaties

![Configuratie van locatie voorwaarde](./media/location-condition/01.png)

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

> [!WARNING]
> Als uw apparaat alleen een IPv6-adres heeft, wordt het configureren van de locatie voorwaarde niet ondersteund.

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Bulksgewijs uploaden en downloaden van benoemde locaties

Wanneer u benoemde locaties maakt of bijwerkt voor bulk updates, kunt u een CSV-bestand uploaden of downloaden met de IP-bereiken. Bij een upload worden de IP-bereiken in de lijst vervangen door die van het bestand. Elke rij van het bestand bevat één IP-adres bereik in CIDR-notatie.

### <a name="cloud-proxies-and-vpns"></a>Cloud-proxy's en Vpn's

Wanneer u een door de Cloud gehoste proxy of VPN-oplossing gebruikt, is het IP-adres dat door Azure AD wordt gebruikt bij het evalueren van een beleid, het IP-adres van de proxy. De header X-Forwarding for (XFF) die het open bare IP-adres van de gebruiker bevat, wordt niet gebruikt omdat er geen validatie is van een vertrouwde bron. Daarom zou een methode voor het Faking van een IP-adres worden weer gegeven.

Als er een Cloud proxy is ingesteld, kan een beleid dat wordt gebruikt om een aan een domein gekoppeld apparaat te vereisen, worden gebruikt, of de in-Corpnet claim van AD FS.

### <a name="api-support-and-powershell"></a>API-ondersteuning en Power shell

API en Power shell worden nog niet ondersteund voor benoemde locaties of voor beleids regels voor voorwaardelijke toegang.

## <a name="next-steps"></a>Volgende stappen

- Als u wilt weten hoe u een beleid voor voorwaardelijke toegang kunt configureren, raadpleegt u [MFA vereisen voor specifieke apps met voorwaardelijke toegang Azure Active Directory](app-based-mfa.md).
- Als u klaar bent voor het configureren van beleid voor voorwaardelijke toegang voor uw omgeving, raadpleegt u de [Aanbevolen procedures voor voorwaardelijke toegang in azure Active Directory](best-practices.md).
