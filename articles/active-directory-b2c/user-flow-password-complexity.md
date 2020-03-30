---
title: Vereisten voor wachtwoordcomplexiteit configureren
titleSuffix: Azure AD B2C
description: Complexiteitsvereisten configureren voor wachtwoorden die door consumenten worden geleverd in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c5ef550af0c7e19531ea19093ea937880f7dcf14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78185638"
---
# <a name="configure-complexity-requirements-for-passwords-in-azure-active-directory-b2c"></a>Complexiteitsvereisten configureren voor wachtwoorden in Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) ondersteunt het wijzigen van de complexiteitsvereisten voor wachtwoorden die door een eindgebruiker worden geleverd bij het maken van een account. Azure AD B2C gebruikt `Strong` standaard wachtwoorden. Azure AD B2C ondersteunt ook configuratieopties om de complexiteit van wachtwoorden te beheren die klanten kunnen gebruiken.

## <a name="password-rule-enforcement"></a>Handhaving van wachtwoordregels

Tijdens het opnieuw aanmelden of het opnieuw instellen van wachtwoorden moet een eindgebruiker een wachtwoord opgeven dat voldoet aan de complexiteitsregels. Regels voor complexiteit van wachtwoorden worden per gebruikersstroom afgedwongen. Het is mogelijk om een gebruikersstroom een viercijferige pin te laten vereisen tijdens het aanmelden, terwijl een andere gebruikersstroom een tekenreeks met acht tekens vereist tijdens het aanmelden. U bijvoorbeeld een gebruikersstroom gebruiken met een andere wachtwoordcomplexiteit voor volwassenen dan voor kinderen.

Complexiteit van wachtwoorden wordt nooit afgedwongen tijdens het aanmelden. Gebruikers worden nooit gevraagd tijdens het aanmelden om hun wachtwoord te wijzigen omdat het niet voldoet aan de huidige complexiteitseis.

De complexiteit van wachtwoorden kan worden geconfigureerd in de volgende typen gebruikersstromen:

- Aanmelden of gebruikersstroom aanmelden
- Gebruikersstroom voor het opnieuw instellen van wachtwoorden

Als u aangepaste beleidsregels gebruikt, u[(wachtwoordcomplexiteit configureren in een aangepast beleid).](custom-policy-password-complexity.md)

## <a name="configure-password-complexity"></a>Complexiteit van wachtwoorden configureren

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer het pictogram **Directory + Abonnement** op de werkbalk van de portal en selecteer vervolgens de map met uw Azure AD B2C-tenant.
3. Zoek en selecteer **Azure AD B2C**in de Azure-portal .
4. Selecteer **Gebruikersstromen (beleidsregels)**.
2. Selecteer een gebruikersstroom en klik op **Eigenschappen**.
3. Wijzig **onder Complexiteit van het wachtwoord**de complexiteit van het wachtwoord voor deze gebruikersstroom in **Eenvoudig,** **Sterk**of **Aangepast**.

### <a name="comparison-chart"></a>Vergelijkingstabel

| Complexiteit | Beschrijving |
| --- | --- |
| Eenvoudig | Een wachtwoord dat ten minste 8 tot 64 tekens is. |
| Sterk | Een wachtwoord dat ten minste 8 tot 64 tekens is. Het vereist 3 van de 4 van kleine letters, hoofdletters, getallen of symbolen. |
| Aangepast telefoonnummer | Deze optie biedt de meeste controle over regels voor wachtwoordcomplexiteit.  Hiermee u een aangepaste lengte configureren.  Het maakt het ook mogelijk het accepteren van nummer-only wachtwoorden (pins). |

## <a name="custom-options"></a>Aangepaste opties

### <a name="character-set"></a>Tekenset

Hiermee u alleen cijfers (pins) of de volledige tekenset accepteren.

- **Met Getallen** worden alleen cijfers (0-9) toegestaan bij het invoeren van een wachtwoord.
- **Alle** staat elke letter, nummer of symbool.

### <a name="length"></a>Lengte

Hiermee u de lengtevereisten van het wachtwoord beheren.

- **De minimumlengte** moet ten minste 4 bedragen.
- **De maximale lengte** moet groter of gelijk zijn aan de minimumlengte en kan maximaal 64 tekens zijn.

### <a name="character-classes"></a>Tekenklassen

Hiermee u de verschillende tekentypen beheren die in het wachtwoord worden gebruikt.

- **2 van 4: Hoofdletterteken, Hoofdletterteken, Getal (0-9), Symbool** zorgt ervoor dat het wachtwoord ten minste twee tekentypen bevat. Bijvoorbeeld een getal en een kleine letters.
- **3 van 4: Kleine letters teken, Hoofdletter teken, Nummer (0-9), Symbool** zorgt ervoor dat het wachtwoord bevat ten minste drie tekentypen. Bijvoorbeeld een getal, een kleine letters en een hoofdletterteken.
- **4 van 4: Hoofdletterteken, Hoofdletterteken, Getal (0-9), Symbool** zorgt ervoor dat het wachtwoord alles bevat voor tekentypen.

    > [!NOTE]
    > Het vereisen **van 4 van 4** kan resulteren in frustratie van de eindgebruiker. Sommige studies hebben aangetoond dat deze eis wachtwoordentropie niet verbetert. Zie [NIST-wachtwoordrichtlijnen](https://pages.nist.gov/800-63-3/sp800-63b.html#appA)
