---
title: Wachtwoord complexiteit-Azure Active Directory B2C | Microsoft Docs
description: Complexiteits vereisten configureren voor wacht woorden die door consumenten worden verstrekt in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 642621e2e04d3e96dc6886b1bdb28c161560b28e
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065462"
---
# <a name="configure-complexity-requirements-for-passwords-in-azure-active-directory-b2c"></a>Complexiteits vereisten configureren voor wacht woorden in Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) ondersteunt het wijzigen van de complexiteits vereisten voor wacht woorden die door een eind gebruiker worden verstrekt bij het maken van een account. Azure AD B2C maakt standaard gebruik `Strong` van wacht woorden. Azure AD B2C biedt ook ondersteuning voor configuratie opties voor het beheren van de complexiteit van wacht woorden die klanten kunnen gebruiken.

## <a name="password-rule-enforcement"></a>Wachtwoord regel afdwingen

Tijdens het registreren of opnieuw instellen van wacht woorden moet een eind gebruiker een wacht woord opgeven dat voldoet aan de complexiteits regels. Regels voor wachtwoord complexiteit worden afgedwongen per gebruikers stroom. Het is mogelijk dat er voor één gebruikers stroom een pincode van vier cijfers is vereist tijdens het aanmelden terwijl een andere gebruikers stroom een teken reeks van acht tekens vereist tijdens de registratie. U kunt bijvoorbeeld een gebruikers stroom met verschillende wachtwoord complexiteit voor volwassenen gebruiken dan voor kinderen.

Wachtwoord complexiteit wordt nooit afgedwongen tijdens het aanmelden. Gebruikers worden nooit gevraagd tijdens het aanmelden om hun wacht woord te wijzigen, omdat het niet voldoet aan de huidige complexiteits vereisten.

Wachtwoord complexiteit kan worden geconfigureerd in de volgende typen gebruikers stromen:

- Gebruikers stroom registreren of aanmelden
- Gebruikers stroom voor wacht woord opnieuw instellen

Als u aangepast beleid gebruikt, kunt u[de complexiteit van het wacht woord configureren in een aangepast beleid](active-directory-b2c-reference-password-complexity-custom.md).

## <a name="configure-password-complexity"></a>Wachtwoord complexiteit configureren

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Zorg ervoor dat u de map met uw Azure AD B2C-Tenant gebruikt door het filter **Directory + abonnement** te selecteren in het bovenste menu en de map te kiezen die uw Tenant bevat.
3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
4. Selecteer **gebruikers stromen**.
2. Selecteer een gebruikers stroom en klik op **Eigenschappen**.
3. Wijzig onder **wachtwoord complexiteit**de complexiteit van het wacht woord voor deze gebruikers stroom naar **eenvoudig**, **sterk**of **aangepast**.

### <a name="comparison-chart"></a>Vergelijkings grafiek

| Complexiteit | Description |
| --- | --- |
| Simpel | Een wacht woord van ten minste 8 tot 64 tekens. |
| Sterk | Een wacht woord van ten minste 8 tot 64 tekens. Hiervoor zijn 3 van de 4 van kleine letters, hoofd letters, cijfers of symbolen vereist. |
| Aanpassen | Deze optie biedt de meeste controle over regels voor wachtwoord complexiteit.  Hiermee kan een aangepaste lengte worden geconfigureerd.  Ook kunt u alleen aantal wacht woorden (pincodes) accepteren. |

## <a name="custom-options"></a>Aangepaste opties

### <a name="character-set"></a>Tekenset

Hiermee kunt u alleen cijfers (pincodes) of de volledige tekenset accepteren.

- **Getallen mogen alleen** cijfers (0-9) bevatten tijdens het invoeren van een wacht woord.
- **Alle** letter, cijfer of symbool worden toegestaan.

### <a name="length"></a>Hoogte

Hiermee kunt u de lengte vereisten van het wacht woord bepalen.

- De **minimum lengte** moet mini maal 4 zijn.
- De **maximum lengte** moet groter zijn dan of gelijk zijn aan de minimum lengte en mag maxi maal 64 tekens lang zijn.

### <a name="character-classes"></a>Teken klassen

Hiermee kunt u de verschillende teken typen beheren die in het wacht woord worden gebruikt.

- **2 van 4: Kleine letters, hoofd letters, cijfer (0-9), symbool** zorgt ervoor dat het wacht woord ten minste twee teken typen bevat. Bijvoorbeeld een numeriek teken en een kleine letter.
- **3 van 4: Kleine letters, hoofd letters, cijfer (0-9), symbool** zorgt ervoor dat het wacht woord ten minste twee teken typen bevat. Bijvoorbeeld een getal, een kleine letter en een hoofd letter.
- **4 van 4: Kleine letters, hoofd letters, cijfer (0-9), symbool** zorgt ervoor dat het wacht woord alle tekens voor teken typen bevat.

    > [!NOTE]
    > Het vereisen **van 4 van 4** kan leiden tot frustraties van eind gebruikers. Sommige onderzoeken hebben laten zien dat deze vereiste de wachtwoord entropie niet verbetert. Zie [NIST-wachtwoord richtlijnen](https://pages.nist.gov/800-63-3/sp800-63b.html#appA)
