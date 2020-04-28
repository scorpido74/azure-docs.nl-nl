---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 03/11/2020
ms.author: mimart
ms.openlocfilehash: 4e03d076fc9ed7d87326a4c8b4f8bf6c0b21c750
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79126736"
---
## <a name="disable-email-verification"></a>E-mailverificatie uitschakelen

Azure Active Directory B2C (Azure AD B2C) controleert standaard het e-mail adres van uw klant voor lokale accounts (accounts voor gebruikers die zich aanmelden met een e-mail adres of gebruikers naam). Azure AD B2C zorgt voor geldige e-mail adressen door klanten te verplichten te verifiëren tijdens het registratie proces. Ook wordt voor komen dat kwaad aardige actors geautomatiseerde processen gebruiken om frauduleuze accounts in uw toepassingen te genereren.

Sommige toepassings ontwikkelaars geven de voor keur aan een e-mail verificatie overs Laan tijdens het registratie proces. ook kunnen klanten hun e-mail adres later verifiëren. Ter ondersteuning hiervan kan Azure AD B2C worden geconfigureerd om e-mail verificatie uit te scha kelen. Dit maakt een probleemloos registratie proces en biedt ontwikkel aars de flexibiliteit om klanten te onderscheiden die hun e-mail adres van klanten hebben gecontroleerd.

> [!WARNING]
> Als u e-mail verificatie in het registratie proces uitschakelt, kan dit leiden tot spam. Als u de standaard verificatie van e-mail berichten voor Azure AD B2C uitschakelt, wordt u aangeraden een vervangend verificatie systeem te implementeren.
