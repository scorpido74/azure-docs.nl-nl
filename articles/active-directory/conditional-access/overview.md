---
title: Wat is voorwaardelijke toegang in Azure Active Directory?
description: Ontdek hoe voorwaardelijke toegang de kern vormt van het nieuwe identiteitsgestuurde controlevlak.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 09/17/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b044a4fd4e29bfe35abff7a4b36e5bae783328b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240910"
---
# <a name="what-is-conditional-access"></a>Wat is voorwaardelijke toegang?

De moderne beveiligingsperimeter reikt nu verder dan het netwerk van een organisatie en omvat de identiteit van gebruikers en apparaten. Organisaties kunnen deze identiteitssignalen gebruiken als onderdeel van hun toegangscontrolebeslissingen. 

Voorwaardelijke toegang is het hulpmiddel dat wordt gebruikt door Azure Active Directory om signalen samen te brengen, beslissingen te nemen en organisatiebeleid af te dwingen. Conditional Access vormt de kern van het nieuwe identiteitsgestuurde controlevliegtuig.

![Conceptueel voorwaardelijk signaal plus beslissing om handhaving te krijgen](./media/overview/conditional-access-signal-decision-enforcement.png)

Voorwaardelijke toegang beleid op hun eenvoudigste zijn als-dan-instructies, als een gebruiker wil toegang tot een bron, dan moeten ze een actie te voltooien. Voorbeeld: Een salarismanager wil toegang krijgen tot de salaristoepassing en moet multi-factor authenticatie uitvoeren om toegang te krijgen.

Beheerders worden geconfronteerd met twee primaire doelen:

- Geef gebruikers de mogelijkheid overal en altijd productief te zijn
- De activa van de organisatie beschermen

Door het beleid voor voorwaardelijke toegang te gebruiken, u de juiste toegangsbesturingselementen toepassen wanneer dat nodig is om uw organisatie veilig te houden en uit de buurt van uw gebruiker te blijven wanneer dit niet nodig is.

![Conceptuele voorwaardelijke toegangsprocesstroom](./media/overview/conditional-access-overview-how-it-works.png)

Beleid voor voorwaardelijke toegang wordt afgedwongen nadat de eerste-factorverificatie is voltooid. Voorwaardelijke toegang is niet bedoeld als de eerste verdedigingslinie van een organisatie voor scenario's zoals dos-aanvallen (denial-of-service), maar kan signalen van deze gebeurtenissen gebruiken om de toegang te bepalen.

## <a name="common-signals"></a>Gemeenschappelijke signalen

Veelvoorkomende signalen waarmee voorwaardelijke toegang rekening kan houden bij het nemen van een beleidsbeslissing, zijn de volgende signalen:

- Gebruikers- of groepslidmaatschap
   - Beleid kan worden gericht op specifieke gebruikers en groepen die beheerders een fijnmazige controle geven over de toegang.
- IP-locatie-informatie
   - Organisaties kunnen vertrouwde IP-adresbereiken maken die kunnen worden gebruikt bij het nemen van beleidsbeslissingen. 
   - Beheerders kunnen ip-bereiken in hele landen opgeven om verkeer te blokkeren of toe te staan.
- Apparaat
   - Gebruikers met apparaten van specifieke platforms of gemarkeerd met een specifieke status kunnen worden gebruikt bij het afdwingen van beleid voor voorwaardelijke toegang.
- Toepassing
   - Gebruikers die toegang proberen te krijgen tot specifieke toepassingen, kunnen verschillende beleid voor voorwaardelijke toegang activeren. 
- Real-time en berekende risicodetectie
   - Dankzij de integratie van signalen met Azure AD Identity Protection kan het beleid voor voorwaardelijke toegang worden geïdentificeerd om risicovol aanmeldingsgedrag te identificeren. Beleidsregels kunnen gebruikers vervolgens dwingen wachtwoordwijzigingen of meervoudige verificatie uit te voeren om hun risiconiveau te verlagen of worden geblokkeerd van toegang totdat een beheerder handmatige actie onderneemt.
- Microsoft Cloud App Security (MCAS)
   - Hiermee kunnen gebruikerstoegang en sessies in realtime worden gecontroleerd en gecontroleerd, waardoor de zichtbaarheid en controle over de toegang tot en activiteiten die binnen uw cloudomgeving worden uitgevoerd, worden vergroot.

## <a name="common-decisions"></a>Gemeenschappelijke besluiten

- Toegang blokkeren
   - Meest beperkende beslissing
- Toegang verlenen
   - Het minst beperkende besluit kan nog steeds een of meer van de volgende opties vereisen:
      - Meervoudige verificatie vereisen
      - Apparaat vereisen dat het als compatibel is gemarkeerd
      - Hybride Azure AD-apparaat vereisen
      - Goedgekeurde client-app vereisen
      - Beleid voor app-beveiliging vereisen (voorbeeld)

## <a name="commonly-applied-policies"></a>Algemeen toegepast beleid

Veel organisaties hebben gemeenschappelijke toegangsproblemen waarmee beleid voor voorwaardelijke toegang kan helpen, zoals:

- Multifactorauthenticatie vereisen voor gebruikers met een administratieve rol
- Multifactorauthenticatie vereisen voor Azure-beheertaken
- Aanmeldingen blokkeren voor gebruikers die verouderde verificatieprotocollen proberen te gebruiken
- Vertrouwde locaties vereisen voor Azure Multi-Factor Authentication-registratie
- Blokkeren of toegang verlenen vanaf specifieke locaties
- Riskant aanmeldingsgedrag blokkeren
- Apparaten die door de organisatie worden beheerd voor specifieke toepassingen vereisen

## <a name="customer-case-studies"></a>Casestudy's van klanten

Ontdek hoe andere organisaties Azure AD Conditional Access gebruiken om geautomatiseerde toegangscontrolebeslissingen te definiëren en te implementeren. De volgende uitgelichte verhalen laten zien hoe aan deze behoeften van de klant wordt voldaan.

* [Wipro stimuleert de productiviteit van mobiele apparaten met Microsoft-cloudbeveiligingstools om de betrokkenheid van klanten te verbeteren.](https://customers.microsoft.com/story/wipro-professional-services-enterprise-mobility-security) Met het beleid voor voorwaardelijke toegang in Azure AD kan het bedrijf documenten, resources en toepassingen delen met vertrouwde externe entiteiten---die hun eigen referenties kunnen gebruiken--- terwijl het bedrijf de controle over zijn eigen bedrijfsgegevens behoudt.
* [Aramex delivery limited - Global logistics and transportation company creëert cloud-connected office met identity and access management oplossing.](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en) Het waarborgen van veilige toegang was vooral moeilijk met de externe medewerkers van Aramex. Het bedrijf past nu Voorwaardelijke toegang toe om deze externe werknemers toegang te geven tot hun SaaS-toepassingen van buiten het netwerk. De regel Voorwaardelijke toegang bepaalt of multifactorauthenticatie wordt afgedwongen, waardoor alleen de juiste mensen de juiste toegang krijgen.

## <a name="license-requirements"></a>Licentievereisten

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

Klanten met [Microsoft 365 Business-licenties](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) hebben ook toegang tot voorwaardelijke toegangsfuncties. 

## <a name="next-steps"></a>Volgende stappen

[Een beleid voor voorwaardelijke toegang stukje voor stuk bouwen](concept-conditional-access-policies.md)

Zie [Uw voorwaardelijke toegangsimplementatie](plan-conditional-access.md)plannen in Azure Active Directory voor meer informatie over het implementeren van voorwaardelijke toegang in uw omgeving.

[Meer informatie over identiteitsbescherming](../identity-protection/overview-v2.md)

[Meer informatie over Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security)

[Meer informatie over Microsoft Intune](/intune/index)