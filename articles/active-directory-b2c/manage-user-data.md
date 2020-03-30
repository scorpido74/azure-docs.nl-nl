---
title: Gebruikersgegevens beheren in Azure Active Directory B2C | Microsoft Documenten
description: Meer informatie over het verwijderen of exporteren van gebruikersgegevens in Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/06/2018
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: e245b58449ab773914fc60be056082b82f05035a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184482"
---
# <a name="manage-user-data-in-azure-active-directory-b2c"></a>Gebruikersgegevens beheren in Azure Active Directory B2C

 In dit artikel wordt besproken hoe u de gebruikersgegevens in Azure Active Directory B2C (Azure AD B2C) beheren met behulp van de bewerkingen die worden geleverd door de [Microsoft Graph API.](https://docs.microsoft.com/graph/use-the-api) Het beheren van gebruikersgegevens omvat het verwijderen of exporteren van gegevens uit controlelogboeken.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="delete-user-data"></a>Gebruikersgegevens verwijderen

Gebruikersgegevens worden opgeslagen in de Azure AD B2C-map en in de controlelogboeken. Alle controlegegevens van gebruikers worden 7 dagen bewaard in Azure AD B2C. Als u gebruikersgegevens binnen die periode van zeven dagen wilt verwijderen, u de [bewerking Een gebruiker verwijderen](https://docs.microsoft.com/graph/api/user-delete) gebruiken. Er is een delete-bewerking vereist voor elk van de Azure AD B2C-tenants waar gegevens zich kunnen bevinden.

Elke gebruiker in Azure AD B2C krijgt een object-id toegewezen. De object-id biedt een ondubbelzinnige id die u gebruiken om gebruikersgegevens in Azure AD B2C te verwijderen. Afhankelijk van uw architectuur kan de object-id een nuttige correlatie-id zijn voor andere services, zoals databases voor financiële, marketing- en klantrelatiebeheer.

De meest nauwkeurige manier om de object-id voor een gebruiker te verkrijgen, is door deze te verkrijgen als onderdeel van een verificatiereis met Azure AD B2C. Als u een geldig verzoek om gegevens van een gebruiker ontvangt met behulp van andere methoden, kan een offline proces, zoals een zoekopdracht door een medewerker van de klantenservice, nodig zijn om de gebruiker te vinden en de bijbehorende object-id te noteren.

In het volgende voorbeeld ziet u een mogelijke gegevensverwijderingsstroom:

1. De gebruiker meldt zich aan en selecteert **Mijn gegevens verwijderen**.
2. De toepassing biedt een optie om de gegevens binnen een beheersectie van de toepassing te verwijderen.
3. De toepassing dwingt een verificatie naar Azure AD B2C. Azure AD B2C biedt een token met de object-id van de gebruiker terug naar de toepassing.
4. Het token wordt ontvangen door de toepassing en de object-id wordt gebruikt om de gebruikersgegevens te verwijderen via een aanroep naar de Microsoft Graph-API. De Microsoft Graph API verwijdert de gebruikersgegevens en retourneert een statuscode van 200 OK.
5. De toepassing orkestreert de verwijdering van gebruikersgegevens in andere organisatiesystemen indien nodig met behulp van de object-id of andere id's.
6. De toepassing bevestigt het verwijderen van gegevens en biedt de volgende stappen aan de gebruiker.

## <a name="export-customer-data"></a>Klantgegevens exporteren

Het proces voor het exporteren van klantgegevens vanuit Azure AD B2C is vergelijkbaar met het verwijderingsproces.

Azure AD B2C-gebruikersgegevens zijn beperkt tot:

- **Gegevens die zijn opgeslagen in de Azure Active Directory:** u gegevens ophalen in een gebruikersreis voor Azure AD B2C-verificatie met behulp van de object-id of een aanmeldingsnaam, zoals een e-mailadres of gebruikersnaam.
- **Rapport voor gebruikersspecifieke controlegebeurtenissen**: U gegevens indexeren met behulp van de object-id.

In het volgende voorbeeld van een exportgegevensstroom kunnen de stappen die worden beschreven als worden uitgevoerd door de toepassing ook worden uitgevoerd door een backendproces of een gebruiker met een beheerdersrol in de map:

1. De gebruiker meldt zich aan bij de toepassing. Azure AD B2C dwingt verificatie af met Azure Multi-Factor Authentication indien nodig.
2. De toepassing gebruikt de gebruikersreferenties om een Microsoft Graph API-bewerking aan te roepen om de gebruikerskenmerken op te halen. De Microsoft Graph API biedt de kenmerkgegevens in JSON-indeling. Afhankelijk van het schema u de inhoud van de ID-token instellen op alle persoonlijke gegevens over een gebruiker.
3. De toepassing haalt de controleactiviteit van de gebruiker op. De Microsoft Graph API biedt de gebeurtenisgegevens aan de toepassing.
4. De toepassing verzamelt de gegevens en maakt deze beschikbaar voor de gebruiker.

## <a name="next-steps"></a>Volgende stappen

Zie [Gebruikerstoegang beheren](manage-user-access.md)voor meer informatie over hoe u beheren hoe gebruikers toegang tot uw toepassing krijgen.
