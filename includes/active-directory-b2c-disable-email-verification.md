---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 03/11/2020
ms.author: mimart
ms.openlocfilehash: 4e03d076fc9ed7d87326a4c8b4f8bf6c0b21c750
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126736"
---
## <a name="disable-email-verification"></a>E-mailverificatie uitschakelen

Azure Active Directory B2C (Azure AD B2C) controleert standaard het e-mailadres van uw klant voor lokale accounts (accounts voor gebruikers die zich aanmelden met e-mailadres of gebruikersnaam). Azure AD B2C zorgt voor geldige e-mailadressen door klanten te verplichten deze te verifiëren tijdens het aanmeldingsproces. Het voorkomt ook dat kwaadwillende actoren geautomatiseerde processen gebruiken om frauduleuze accounts in uw toepassingen te genereren.

Sommige toepassingsontwikkelaars geven er de voorkeur aan e-mailverificatie over te slaan tijdens het aanmeldingsproces en in plaats daarvan laten klanten hun e-mailadres later verifiëren. Om dit te ondersteunen, kan Azure AD B2C worden geconfigureerd om e-mailverificatie uit te schakelen. Hierdoor wordt een soepeler aanmeldingsproces gemaakt en kunnen ontwikkelaars zich onderscheiden van klanten die hun e-mailadres hebben geverifieerd van klanten die dat niet hebben gedaan.

> [!WARNING]
> Het uitschakelen van e-mailverificatie in het aanmeldingsproces kan leiden tot spam. Als u de standaard e-mailverificatie met Azure AD B2C uitschakelt, raden we u aan een vervangend verificatiesysteem te implementeren.
