---
title: Voorwaardelijke toegang vereist beheerd apparaat - Azure Active Directory
description: Meer informatie over het configureren van Azure Active Directory(Azure AD)-apparaatgebaseerde voorwaardelijke toegangsbeleidsregels waarvoor beheerde apparaten moeten worden beheerd voor toegang tot de cloud-app.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/22/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a3c71534febc3cdb6429d3092225ebc73f6cbe7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481480"
---
# <a name="how-to-require-managed-devices-for-cloud-app-access-with-conditional-access"></a>How To: Beheerde apparaten vereisen voor toegang tot cloud-apps met voorwaardelijke toegang

In een mobile-first, cloud-first wereld, Azure Active Directory (Azure AD) maakt single sign-on aan apps en services van overal. Geautoriseerde gebruikers hebben toegang tot uw cloud-apps vanaf een breed scala aan apparaten, waaronder mobiele apparaten en ook persoonlijke apparaten. Veel omgevingen hebben echter ten minste een paar apps die alleen toegankelijk mogen zijn voor apparaten die voldoen aan uw normen voor beveiliging en naleving. Deze apparaten worden ook wel beheerde apparaten genoemd. 

In dit artikel wordt uitgelegd hoe u beleid voor voorwaardelijke toegang configureren waarvoor beheerde apparaten toegang moeten krijgen tot bepaalde cloud-apps in uw omgeving. 

## <a name="prerequisites"></a>Vereisten

Beheerde apparaten voor toegang tot de cloud-app hebben azure **AD Conditional Access** en Azure **AD-apparaatbeheer** met elkaar verbonden. Als u nog niet bekend bent met een van deze gebieden, moet u eerst de volgende onderwerpen lezen:

- **[Voorwaardelijke toegang in Azure Active Directory](../active-directory-conditional-access-azure-portal.md)** - In dit artikel vindt u een conceptueel overzicht van voorwaardelijke toegang en de bijbehorende terminologie.
- **[Inleiding tot apparaatbeheer in Azure Active Directory](../devices/overview.md)** - In dit artikel vindt u een overzicht van de verschillende opties die u hebt om apparaten onder organisatiecontrole te krijgen. 
- Installeer de [windows 10-accountextensie voor Chrome-ondersteuning](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)in **Windows 10 Creators Update (versie 1703)** of hoger. Deze extensie is vereist wanneer een beleid voor voorwaardelijke toegang apparaatspecifieke gegevens vereist.

>[!NOTE] 
> We raden u aan het op Azure AD-apparaat gebaseerde voorwaardelijke toegangsbeleid te gebruiken om de beste handhaving te krijgen na de eerste apparaatverificatie. Dit geldt ook voor het sluiten van sessies als het apparaat uit de compliance en de stroom van apparaatcodes valt.


## <a name="scenario-description"></a>Scenariobeschrijving

Het beheersen van de balans tussen veiligheid en productiviteit is een uitdaging. De wildgroei aan ondersteunde apparaten om toegang te krijgen tot uw cloudbronnen helpt de productiviteit van uw gebruikers te verbeteren. Aan de andere kant wilt u waarschijnlijk niet dat bepaalde bronnen in uw omgeving worden geopend door apparaten met een onbekend beveiligingsniveau. Voor de betreffende bronnen moet u vereisen dat gebruikers er alleen toegang toe hebben via een beheerd apparaat. 

Met Voorwaardelijke toegang tot Azure AD u deze vereiste beantwoorden met één enkel beleid dat toegang verleent:

- Naar geselecteerde cloud-apps
- Voor geselecteerde gebruikers en groepen
- Een beheerd apparaat vereisen

## <a name="managed-devices"></a>Beheerde apparaten  

In eenvoudige termen, beheerde apparaten zijn apparaten die onder *een soort* van organisatorische controle. In Azure AD is de voorwaarde voor een beheerd apparaat dat het is geregistreerd bij Azure AD. Als u een apparaat registreert, wordt een identiteit voor het apparaat gemaakt in de vorm van een apparaatobject. Dit object wordt door Azure gebruikt om statusgegevens over een apparaat bij te houden. Als Azure AD-beheerder u dit object al gebruiken om de status van een apparaat in te schakelen (in- of uitschakelen).
  
![Op apparaten gebaseerde omstandigheden](./media/require-managed-devices/32.png)

Als u een apparaat wilt laten registreren bij Azure AD, hebt u drie opties: 

- **Azure AD-geregistreerde apparaten** - om een persoonlijk apparaat te laten registreren bij Azure AD
- **Azure AD heeft apparaten samengevoegd** : om een organisatie-Windows 10-apparaat te krijgen dat niet is gekoppeld aan een on-premises AD die is geregistreerd bij Azure AD. 
- **Hybride Azure AD-apparaten zijn samengevoegd** : om een Windows 10- of ondersteund apparaat op downniveau te krijgen dat is gekoppeld aan een on-premises AD dat is geregistreerd bij Azure AD.

Deze drie opties worden besproken in het artikel [Wat is een apparaatidentiteit?](../devices/overview.md)

Om een beheerd apparaat te worden, moet een geregistreerd apparaat een **hybride Azure AD-apparaat** zijn of een **apparaat dat is gemarkeerd als compatibel.**  

![Op apparaten gebaseerde omstandigheden](./media/require-managed-devices/47.png)
 
## <a name="require-hybrid-azure-ad-joined-devices"></a>Hybride Azure AD-samengevoegde apparaten vereisen

In uw beleid voor voorwaardelijke toegang u **hybride Azure AD-apparaat vereisen** selecteren om aan te geven dat de geselecteerde cloud-apps alleen toegankelijk zijn via een beheerd apparaat. 

![Op apparaten gebaseerde omstandigheden](./media/require-managed-devices/10.png)

Deze instelling is alleen van toepassing op apparaten op Windows 10 of Down-level, zoals Windows 7 of Windows 8, die zijn verbonden met een on-premises AD. U deze apparaten alleen registreren bij Azure AD via een hybride Azure AD-join, een [geautomatiseerd proces](../devices/hybrid-azuread-join-plan.md) om een Windows 10-apparaat te laten registreren. 

![Op apparaten gebaseerde omstandigheden](./media/require-managed-devices/45.png)

Wat maakt een hybride Azure AD-apparaat tot een beheerd apparaat?  Voor apparaten die zijn verbonden met een on-premises AD, wordt ervan uitgegaan dat de controle over deze apparaten wordt afgedwongen met behulp van beheeroplossingen zoals **Configuration Manager** of **groepsbeleid (GP)** om ze te beheren. Omdat azure AD geen methode heeft om te bepalen of een van deze methoden is toegepast op een apparaat, is het vereisen van een hybride Azure AD-apparaat een relatief zwak mechanisme om een beheerd apparaat nodig te hebben. Het is aan u als beheerder om te beoordelen of de methoden die worden toegepast op uw on-premises domein-verbonden apparaten sterk genoeg zijn om een beheerd apparaat te vormen als een dergelijk apparaat ook een hybride Azure AD-apparaat is.

## <a name="require-device-to-be-marked-as-compliant"></a>Apparaat vereisen dat het als compatibel is gemarkeerd

De optie om te *eisen dat een apparaat als compatibel moet worden gemarkeerd,* is het sterkste formulier om een beheerd apparaat aan te vragen.

![Op apparaten gebaseerde omstandigheden](./media/require-managed-devices/11.png)

Voor deze optie moet een apparaat zijn geregistreerd bij Azure AD en moet het ook worden gemarkeerd als compatibel met:
         
- Intune
- Een MDM-systeem (mobile device management) van derden dat Windows 10-apparaten beheert via Azure AD-integratie. MDM-systemen van derden voor andere typen apparaatbesturingssystemen dan Windows 10 worden niet ondersteund.
 
![Op apparaten gebaseerde omstandigheden](./media/require-managed-devices/46.png)

Voor een apparaat dat is gemarkeerd als compatibel, u ervan uitgaan dat: 

- De mobiele apparaten die uw personeel gebruikt om toegang te krijgen tot bedrijfsgegevens worden beheerd
- Mobiele apps die uw personeel gebruikt, worden beheerd
- Uw bedrijfsgegevens worden beschermd door te helpen de manier waarop uw personeel toegang heeft tot en deelt, te controleren
- Het apparaat en de bijbehorende apps voldoen aan de beveiligingseisen van het bedrijf

### <a name="known-behavior"></a>Bekend gedrag

Op Windows 7, iOS, Android, macOS en sommige webbrowsers van derden identificeert Azure AD het apparaat met behulp van een clientcertificaat dat is ingericht wanneer het apparaat is geregistreerd bij Azure AD. Wanneer een gebruiker zich voor het eerst aanmeldt via de browser wordt de gebruiker gevraagd het certificaat te selecteren. De eindgebruiker moet dit certificaat selecteren voordat hij de browser kan blijven gebruiken.

## <a name="next-steps"></a>Volgende stappen

Voordat u een op apparaten gebaseerd beleid voor voorwaardelijke toegang in uw omgeving configureert, moet u de [aanbevolen procedures voor voorwaardelijke toegang in Azure Active Directory bekijken.](best-practices.md)
