---
title: Overzicht van Azure Active Directory-verificatie en-synchronisatie Protocol
description: Richt lijnen voor architectuur over het bereiken van dit verificatie patroon
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d881dc3fe3e3caa1058cf97834735910b0de1d9
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114276"
---
# <a name="azure-active-directory-integrations-with-legacy-authentication-and-synchronization-protocols"></a>Azure Active Directory integraties met verouderde verificatie-en synchronisatie protocollen

Microsoft Azure Active Directory (Azure AD) maakt integratie mogelijk met veel verificatie-en synchronisatie protocollen. Met de verificatie integratie kunt u Azure AD en de beveiligings-en beheer functies gebruiken met weinig of geen wijzigingen in uw toepassingen die gebruikmaken van verouderde verificatie methoden. Met de synchronisatie integratie kunt u gebruikers-en groeps gegevens synchroniseren naar Azure AD en vervolgens de beheer mogelijkheden van Azure AD. Bij sommige synchronisatie patronen wordt geautomatiseerde inrichting ook ingeschakeld.

## <a name="authentication-patterns"></a>Verificatiepatronen

De volgende tabel geeft verificatie patronen en de mogelijkheden ervan. Selecteer de naam van een verificatie patroon om te zien

* Een gedetailleerde beschrijving

* Wanneer te gebruiken

* Architectuur diagram

* Uitleg van systeem onderdelen

* Koppelingen voor het implementeren van de integratie

 

| Verificatiepatronen| Verificatie| Autorisatie| Multi-Factor Authentication| Voorwaardelijke toegang |
| - |- | - | - | - |
| [Verificatie op basis van koptekst](auth-header-based.md)|![vinkje](./media/authentication-patterns/check.png)| ![vinkje](./media/authentication-patterns/check.png)| ![vinkje](./media/authentication-patterns/check.png)| ![vinkje](./media/authentication-patterns/check.png) |
| [LDAP-verificatie](auth-ldap.md)| ![vinkje](./media/authentication-patterns/check.png)| | |  |
| [OAuth 2,0-verificatie](auth-oauth2.md)| ![vinkje](./media/authentication-patterns/check.png)| ![vinkje](./media/authentication-patterns/check.png)| ![vinkje](./media/authentication-patterns/check.png)| ![vinkje](./media/authentication-patterns/check.png) |
| [OIDC-verificatie](auth-oidc.md)| ![vinkje](./media/authentication-patterns/check.png)| ![vinkje](./media/authentication-patterns/check.png)| ![vinkje](./media/authentication-patterns/check.png)| ![vinkje](./media/authentication-patterns/check.png) |
| [Op wacht woord gebaseerde SSO-verificatie](auth-password-based-sso.md )| ![vinkje](./media/authentication-patterns/check.png)| ![vinkje](./media/authentication-patterns/check.png)| ![vinkje](./media/authentication-patterns/check.png)| ![vinkje](./media/authentication-patterns/check.png) |
| [RADIUS-verificatie]( auth-radius.md)| ![vinkje](./media/authentication-patterns/check.png)| | ![vinkje](./media/authentication-patterns/check.png)| ![vinkje](./media/authentication-patterns/check.png) |
| [Extern bureaublad-gateway services](auth-remote-desktop-gateway.md)| ![vinkje](./media/authentication-patterns/check.png)| ![vinkje](./media/authentication-patterns/check.png)| ![vinkje](./media/authentication-patterns/check.png)| ![vinkje](./media/authentication-patterns/check.png) |
| [SAML-verificatie](auth-saml.md)| ![vinkje](./media/authentication-patterns/check.png)| ![vinkje](./media/authentication-patterns/check.png)| ![vinkje](./media/authentication-patterns/check.png)| ![vinkje](./media/authentication-patterns/check.png) |
| [Windows-verificatie-Kerberos-beperkte overdracht](auth-kcd.md)| ![vinkje](./media/authentication-patterns/check.png)| ![vinkje](./media/authentication-patterns/check.png)| ![vinkje](./media/authentication-patterns/check.png)| ![vinkje](./media/authentication-patterns/check.png) |


 
## <a name="synchronization-patterns"></a>Synchronisatie patronen

De volgende tabel bevat synchronisatie patronen en de mogelijkheden ervan. De naam van een patroon selecteren om te zien

* Een gedetailleerde beschrijving

* Wanneer te gebruiken

* Architectuur diagram

* Uitleg van systeem onderdelen

* Koppelingen voor het implementeren van de integratie



| Synchronisatie patroon| Adreslijstsynchronisatie| Inrichten van gebruikers |
| - | - | - |
| [Adreslijstsynchronisatie](sync-directory.md)| ![vinkje](./media/authentication-patterns/check.png)|  |
| [LDAP-synchronisatie](sync-ldap.md)| ![vinkje](./media/authentication-patterns/check.png)|  |
| [SCIM-synchronisatie](sync-scim.md)| ![vinkje](./media/authentication-patterns/check.png)| ![vinkje](./media/authentication-patterns/check.png) |

