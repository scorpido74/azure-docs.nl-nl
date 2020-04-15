---
title: Gecombineerde registratie voor SSPR en MFA - Azure Active Directory
description: Azure AD Multi-Factor Authentication en selfservice password reset registration (preview)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26656b6dafd91d47c05c2d1f923e53f4ba790cf8
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309921"
---
# <a name="combined-security-information-registration-preview"></a>Registratie van gecombineerde beveiligingsgegevens (voorbeeld)

Voor gecombineerde registratie registreerden gebruikers verificatiemethoden voor Azure Multi-Factor Authentication en selfservice password reset (SSPR) afzonderlijk. Mensen waren in de war dat soortgelijke methoden werden gebruikt voor Multi-Factor Authentication en SSPR, maar ze moesten registreren voor beide functies. Nu, met gecombineerde registratie, kunnen gebruikers zich eenmalig registreren en de voordelen van zowel Multi-Factor Authentication als SSPR krijgen.

![Mijn profiel met geregistreerde beveiligingsgegevens voor een gebruiker](media/concept-registration-mfa-sspr-combined/combined-security-info-defualts-registered.png)

Voordat u de nieuwe ervaring inschakelt, controleert u deze op de beheerder gerichte documentatie en de op de gebruiker gerichte documentatie om ervoor te zorgen dat u de functionaliteit en het effect van deze functie begrijpt. Baseer uw training op de [gebruikersdocumentatie](../user-help/user-help-security-info-overview.md) om uw gebruikers voor te bereiden op de nieuwe ervaring en te helpen zorgen voor een succesvolle implementatie.

Azure AD combined security information registration is currently not available to national clouds like Azure US Government, Azure Germany, or Azure China 21Vianet.

|     |
| --- |
| Gecombineerde beveiligingsgegevensregistratie voor Multi-Factor Authentication en Azure Active Directory (Azure AD) selfservice wachtwoord reset is een openbare preview-functie van Azure AD. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie over voorvertoningen.|
|     |

> [!IMPORTANT]
> Gebruikers die zijn ingeschakeld voor zowel de oorspronkelijke preview als de verbeterde gecombineerde registratie-ervaring, zien het nieuwe gedrag. Gebruikers die voor beide ervaringen zijn ingeschakeld, zien alleen de nieuwe Mijn profiel-ervaring. Het nieuwe My Profile sluit aan bij de look en feel van gecombineerde registratie en biedt een naadloze ervaring voor gebruikers. Gebruikers kunnen Mijn profiel [https://myprofile.microsoft.com](https://myprofile.microsoft.com)zien door naar .

> [!NOTE] 
> U een foutbericht tegenkomen terwijl u toegang probeert te krijgen tot de optie Beveiligingsgegevens. Bijvoorbeeld: 'Sorry, we kunnen je niet aanmelden'. Bevestig in dit geval dat u geen configuratie- of groepsbeleidsobject hebt dat cookies van derden blokkeert in de webbrowser. 

Mijn profielpagina's zijn gelokaliseerd op basis van de taalinstellingen van de computer die toegang heeft tot de pagina. Microsoft slaat de meest recente taal op die in de browsercache wordt gebruikt, zodat latere pogingen om toegang te krijgen tot de pagina's worden weergegeven in de laatste taal die wordt gebruikt. Als u de cache wist, worden de pagina's opnieuw weergegeven. Als u een bepaalde taal wilt `?lng=<language>` forceren, u `<language>` toevoegen aan het einde van de URL, waar is de code van de taal die u wilt renderen.

![SSPR of andere beveiligingsverificatiemethoden instellen](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-combined-registration"></a>Beschikbare methoden voor gecombineerde registratie

Gecombineerde registratie ondersteunt de volgende verificatiemethoden en acties:

|   | Registreren | Wijzigen | Verwijderen |
| --- | --- | --- | --- |
| Microsoft Authenticator | Ja (maximaal 5) | Nee | Ja |
| Andere authenticator-app | Ja (maximaal 5) | Nee | Ja |
| Hardwaretoken | Nee | Nee | Ja |
| Telefoon | Ja | Ja | Ja |
| Alternatieve telefoon | Ja | Ja | Ja |
| Zakelijke telefoon | Nee | Nee | Nee |
| Email | Ja | Ja | Ja |
| Beveiligingsvragen | Ja | Nee | Ja |
| App-wachtwoorden | Ja | Nee | Ja |
| FIDO2-beveiligingssleutels<br />*Beheerde modus alleen vanaf de pagina [Beveiligingsinfo](https://mysignins.microsoft.com/security-info)*| Ja | Ja | Ja |

> [!NOTE]
> App-wachtwoorden zijn alleen beschikbaar voor gebruikers die zijn afgedwongen voor multi-factorauthenticatie. App-wachtwoorden zijn niet beschikbaar voor gebruikers die zijn ingeschakeld voor multi-factorauthenticatie via een beleid voor voorwaardelijke toegang.

Gebruikers kunnen een van de volgende opties instellen als de standaardmethode voor multifactorverificatie:

- Microsoft Authenticator – melding.
- Authenticator app of hardware token – code.
- Telefoongesprek.
- Sms-bericht.

Als we doorgaan met het toevoegen van meer verificatiemethoden aan Azure AD, zijn deze methoden beschikbaar in gecombineerde registratie.

## <a name="combined-registration-modes"></a>Gecombineerde registratiemodi

Er zijn twee modi van gecombineerde registratie: onderbreken en beheren.

- **Interrupt-modus** is een wizard-achtige ervaring, gepresenteerd aan gebruikers wanneer ze registreren of vernieuwen hun beveiligingsgegevens bij aanmelding.

- **De beheermodus** maakt deel uit van het gebruikersprofiel en stelt gebruikers in staat om hun beveiligingsgegevens te beheren.

Voor beide modi moeten gebruikers die eerder een methode hebben geregistreerd die kan worden gebruikt voor multifactorauthenticatie multifactorverificatie uitvoeren voordat ze toegang hebben tot hun beveiligingsgegevens.

### <a name="interrupt-mode"></a>Interrupt-modus

Gecombineerde registratie respecteert zowel Multi-Factor Authentication als SSPR-beleid, als beide zijn ingeschakeld voor uw tenant. Met dit beleid wordt bepaald of een gebruiker wordt onderbroken voor registratie tijdens het aanmelden en welke methoden beschikbaar zijn voor registratie.

Hier volgen verschillende scenario's waarin gebruikers kunnen worden gevraagd hun beveiligingsgegevens te registreren of te vernieuwen:

- Registratie van meervoudige verificatie die wordt afgedwongen via identiteitsbescherming: gebruikers wordt gevraagd zich te registreren tijdens het aanmelden. Ze registreren Multi-Factor Authentication methoden en SSPR methoden (als de gebruiker is ingeschakeld voor SSPR).
- Multi-Factor Authentication registratie afgedwongen via multi-factor authenticatie per gebruiker: Gebruikers wordt gevraagd zich te registreren tijdens het aanmelden. Ze registreren Multi-Factor Authentication methoden en SSPR methoden (als de gebruiker is ingeschakeld voor SSPR).
- Registratie van meervoudige verificatie die wordt afgedwongen via voorwaardelijke toegang of ander beleid: gebruikers wordt gevraagd zich te registreren wanneer ze een bron gebruiken waarvoor multifactorverificatie vereist is. Ze registreren Multi-Factor Authentication methoden en SSPR methoden (als de gebruiker is ingeschakeld voor SSPR).
- SSPR-registratie afgedwongen: Gebruikers wordt gevraagd zich te registreren tijdens het aanmelden. Zij registreren alleen SSPR-methoden.
- SSPR refresh afgedwongen: Gebruikers moeten hun beveiligingsgegevens controleren met een interval die door de beheerder is ingesteld. Gebruikers krijgen hun gegevens te zien en kunnen de huidige informatie bevestigen of indien nodig wijzigingen aanbrengen.

Wanneer de registratie wordt afgedwongen, krijgen gebruikers het minimumaantal methoden te zien dat moet voldoen aan zowel multi-factor authenticatie als SSPR-beleid, van de meeste tot de minst beveiligde.

Bijvoorbeeld:

- Een gebruiker is ingeschakeld voor SSPR. Het SSPR-beleid vereiste twee methoden om te resetten en heeft mobiele app-code, e-mail en telefoon ingeschakeld.
   - Deze gebruiker is verplicht om twee methoden te registreren.
      - De gebruiker wordt standaard weergegeven authenticator app en telefoon.
      - De gebruiker kan ervoor kiezen om e-mail te registreren in plaats van authenticator app of telefoon.

In dit stroomdiagram worden beschreven welke methoden aan een gebruiker worden getoond wanneer deze wordt onderbroken om zich tijdens het aanmelden te registreren:

![Gecombineerd stroomschema voor beveiligingsgegevens](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

Als u zowel Multi-Factor Authentication als SSPR hebt ingeschakeld, raden we u aan de registratie van multifactorauthenticatie af te dwingen.

Als het SSPR-beleid vereist dat gebruikers hun beveiligingsgegevens regelmatig controleren, worden gebruikers onderbroken tijdens het aanmelden en worden al hun geregistreerde methoden weergegeven. Ze kunnen de huidige informatie bevestigen als deze up-to-date is, of ze kunnen wijzigingen aanbrengen als dat nodig is. Gebruikers moeten multi-factor authenticatie uitvoeren bij het openen van deze pagina.

### <a name="manage-mode"></a>Modus beheren

Gebruikers hebben toegang tot [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) de beheermodus door naar of door **Beveiligingsgegevens** te selecteren in Mijn profiel. Van daaruit kunnen gebruikers methoden toevoegen, bestaande methoden verwijderen of wijzigen, de standaardmethode wijzigen en meer.

## <a name="key-usage-scenarios"></a>Sleutelgebruiksscenario's

### <a name="set-up-security-info-during-sign-in"></a>Beveiligingsgegevens instellen tijdens aanmelding

Een beheerder heeft de registratie afgedwongen.

Een gebruiker heeft niet alle vereiste beveiligingsgegevens ingesteld en gaat naar de Azure-portal. Na het invoeren van de gebruikersnaam en het wachtwoord wordt de gebruiker gevraagd beveiligingsgegevens in te stellen. De gebruiker volgt vervolgens de stappen in de wizard om de vereiste beveiligingsgegevens in te stellen. Als uw instellingen dit toestaan, kan de gebruiker ervoor kiezen om andere methoden in te stellen dan die standaard worden weergegeven. Nadat gebruikers de wizard hebben voltooid, controleren ze de methoden die ze hebben ingesteld en hun standaardmethode voor multifactorverificatie. Om het installatieproces te voltooien, bevestigt de gebruiker de informatie en gaat hij verder naar de Azure-portal.

### <a name="set-up-security-info-from-my-profile"></a>Beveiligingsgegevens instellen vanuit Mijn profiel

Een beheerder heeft de registratie niet afgedwongen.

Een gebruiker die nog niet alle vereiste beveiligingsgegevens heeft ingesteld, gaat naar [https://myprofile.microsoft.com](https://myprofile.microsoft.com). De gebruiker selecteert **Beveiligingsgegevens** in het linkerdeelvenster. Van daaruit kiest de gebruiker ervoor om een methode toe te voegen, selecteert een van de beschikbare methoden en volgt de stappen om die methode in te stellen. Wanneer de gebruiker klaar is, ziet hij de methode die zojuist is ingesteld op de pagina Beveiligingsgegevens.

### <a name="delete-security-info-from-my-profile"></a>Beveiligingsgegevens verwijderen uit Mijn profiel

Een gebruiker die eerder ten minste één [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)methode heeft ingesteld, navigeert naar . De gebruiker kiest ervoor om een van de eerder geregistreerde methoden te verwijderen. Als de gebruiker klaar is, ziet hij die methode niet meer op de pagina Beveiligingsgegevens.

### <a name="change-the-default-method-from-my-profile"></a>De standaardmethode wijzigen in Mijn profiel

Een gebruiker die eerder ten minste één methode heeft ingesteld die kan [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)worden gebruikt voor multifactorverificatie, navigeert naar . De gebruiker wijzigt de huidige standaardmethode in een andere standaardmethode. Wanneer de gebruiker klaar is, ziet hij de nieuwe standaardmethode op de pagina Beveiligingsgegevens.

## <a name="next-steps"></a>Volgende stappen

[Gebruikers dwingen om verificatiemethoden opnieuw te registreren](howto-mfa-userdevicesettings.md#manage-user-authentication-options)

[Gecombineerde registratie inschakelen in uw tenant](howto-registration-mfa-sspr-combined.md)

[SSPR- en MFA-gebruik en inzichtenrapportage](howto-authentication-methods-usage-insights.md)

[Beschikbare methoden voor multi-factor authenticatie en SSPR](concept-authentication-methods.md)

[Self-service voor wachtwoord opnieuw instellen configureren](howto-sspr-deployment.md)

[Azure Multi-Factor Authentication configureren](howto-mfa-getstarted.md)
