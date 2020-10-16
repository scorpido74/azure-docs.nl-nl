---
title: Directory synchronisatie met Azure Active Directory
description: Richt lijnen voor architectuur over het bereiken van dit synchronisatie patroon
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
ms.openlocfilehash: 862d17948cb09c18f9372f8b8b981e5efa6be71b
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114181"
---
# <a name="directory-synchronization"></a>Adreslijstsynchronisatie

Veel organisaties hebben een hybride infra structuur die zowel on-premises als Cloud onderdelen omvat. Door gebruikers identiteiten te synchroniseren tussen lokale en Cloud directory's, kunnen gebruikers toegang krijgen tot bronnen met één set referenties. 

Synchronisatie is het proces van 

* een object maken op basis van bepaalde voor waarden
* het object bijgewerkt houden
* het object verwijderen als niet meer aan de voor waarden wordt voldaan. 

On-premises inrichting omvat het inrichten van on-premises bronnen (zoals Active Directory) tot Azure Active Directory (Azure AD). 

## <a name="use-when"></a>Wanneer gebruiken

U moet identiteits gegevens van uw on-premises Active Directory omgevingen synchroniseren met Azure AD.

![architectuur diagram](./media/authentication-patterns/dir-sync-auth.png)

## <a name="components-of-system"></a>Onderdelen van systeem

* **Gebruiker**: opent een toepassing met Azure AD.

* **Webbrowser**: het onderdeel waarmee de gebruiker communiceert om toegang te krijgen tot de externe URL van de toepassing.

* **Toepassing**: Web-app die afhankelijk is van het gebruik van Azure AD voor verificatie en autorisatie.

* **Azure AD**: synchroniseert identiteits gegevens van de on-premises Directory van de organisatie via Azure AD Connect. 

* **Azure AD Connect**: een hulp programma voor het verbinden van een on-premises identiteits infrastructuur naar Microsoft Azure AD. Met de wizard en de begeleide ervaringen kunt u vereisten en onderdelen implementeren en configureren die vereist zijn voor de verbinding, waaronder synchronisatie en aanmelding vanuit Active Directory naar Azure AD. 

* **Active Directory**: Active Directory is een directory service die deel uitmaakt van de meeste Windows Server-besturings systemen. Servers met Active Directory Domain Services (AD DS) worden domein controllers genoemd. Ze verifiëren en autoriseren alle gebruikers en computers in het domein.

## <a name="implement-directory-synchronization-with-azure-ad"></a>Adreslijst synchronisatie implementeren met Azure AD

* [Wat is identiteitsinrichting?](https://docs.microsoft.com/azure/active-directory/cloud-provisioning/what-is-provisioning) 

* [Hulp middelen voor de integratie van hybride identiteits mappen](https://docs.microsoft.com/azure/active-directory/hybrid/plan-hybrid-identity-design-considerations-tools-comparison) 

* [Installatie schema voor Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-roadmap)
