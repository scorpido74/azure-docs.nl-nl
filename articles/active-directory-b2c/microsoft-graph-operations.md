---
title: Ondersteunde Microsoft Graph-bewerkingen
titleSuffix: Azure AD B2C
description: Een index van de Microsoft Graph-bewerkingen die worden ondersteund voor het beheer van Azure AD B2C-resources, waaronder gebruikers, gebruikersstromen, identiteitsproviders, aangepaste beleidsregels, beleidssleutels en meer.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 27fe1a41365d96a4179f8c659b63dc22c7b9fc93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184245"
---
# <a name="microsoft-graph-operations-available-for-azure-ad-b2c"></a>Microsoft Graph-bewerkingen beschikbaar voor Azure AD B2C

De volgende Microsoft Graph API-bewerkingen worden ondersteund voor het beheer van Azure AD B2C-resources, waaronder gebruikers, identiteitsproviders, gebruikersstromen, aangepaste beleidsregels en beleidssleutels.

Elke koppeling in de volgende secties is gericht op de bijbehorende pagina binnen de Microsoft Graph API-verwijzing voor die bewerking.

## <a name="user-management"></a>Gebruikersbeheer

- [Gebruikers weergeven](https://docs.microsoft.com/graph/api/user-list)
- [Een consument maken](https://docs.microsoft.com/graph/api/user-post-users)
- [Een gebruiker krijgen](https://docs.microsoft.com/graph/api/user-get)
- [Een gebruiker bijwerken](https://docs.microsoft.com/graph/api/user-update)
- [Een gebruiker verwijderen](https://docs.microsoft.com/graph/api/user-delete)

Zie [Azure AD B2C-gebruikersaccounts](manage-user-accounts-graph-api.md)beheren met Microsoft Graph voor meer informatie over het beheren van Azure AD B2C-gebruikersaccounts met Microsoft Graph.

## <a name="identity-providers-user-flow"></a>Identiteitsproviders (gebruikersstroom)

Beheer de identiteitsproviders die beschikbaar zijn voor uw gebruikersstromen in uw Azure AD B2C-tenant.

- [Identiteitsproviders weergeven die zijn geregistreerd in de Azure AD B2C-tenant](https://docs.microsoft.com/graph/api/identityprovider-list)
- [Een identiteitsprovider maken](https://docs.microsoft.com/graph/api/identityprovider-post-identityproviders)
- [Een identiteitsprovider](https://docs.microsoft.com/graph/api/identityprovider-get)
- [Identiteitsprovider bijwerken](https://docs.microsoft.com/graph/api/identityprovider-update)
- [Een identiteitsprovider verwijderen](https://docs.microsoft.com/graph/api/identityprovider-delete)

## <a name="user-flow"></a>Gebruikersstroom

Vooraf gebouwd beleid configureren voor aanmelding, aanmelding, gecombineerde aanmelding en aanmelding, wachtwoordreset en profielupdate.

- [Gebruikersstromen weergeven](https://docs.microsoft.com/graph/api/identityuserflow-list)
- [Een gebruikersstroom maken](https://docs.microsoft.com/graph/api/identityuserflow-post-userflows)
- [Een gebruikersstroom](https://docs.microsoft.com/graph/api/identityuserflow-get)
- [Een gebruikersstroom verwijderen](https://docs.microsoft.com/graph/api/identityuserflow-delete)

## <a name="custom-policies"></a>Aangepast beleid

Met de volgende bewerkingen u uw Azure AD B2C-vertrouwenskaderbeleid beheren, ook wel [aangepast beleid genoemd.](custom-policy-overview.md)

- [Alle vertrouwenskaderbeleidsregels weergeven die zijn geconfigureerd in een tenant](https://docs.microsoft.com/graph/api/trustframework-list-trustframeworkpolicies)
- [Beleid voor vertrouwenskadermaken](https://docs.microsoft.com/graph/api/trustframework-post-trustframeworkpolicy)
- [Eigenschappen van een bestaand vertrouwenskaderbeleid lezen](https://docs.microsoft.com/graph/api/trustframeworkpolicy-get)
- [Het beleid voor vertrouwenskaderbijwerken of maken.](https://docs.microsoft.com/graph/api/trustframework-put-trustframeworkpolicy)
- [Een bestaand beleid voor vertrouwenskaderverwijderen](https://docs.microsoft.com/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>Beleidssleutels

Het Identity Experience Framework slaat de geheimen op waarnaar wordt verwezen in een aangepast beleid om vertrouwen tussen componenten te vestigen. Deze geheimen kunnen symmetrische of asymmetrische sleutels/waarden zijn. In de Azure-portal worden deze entiteiten weergegeven als **beleidssleutels**.

De bron op het hoogste niveau voor beleidssleutels in de Microsoft Graph-API is de [Trusted Framework Keyset](https://docs.microsoft.com/graph/api/resources/trustframeworkkeyset). Elke **toetsset** bevat ten minste één **sleutel.** Als u een sleutel wilt maken, maakt u eerst een lege toetsset en genereert u vervolgens een sleutel in de toetsset. U een handmatig geheim maken, een certificaat uploaden of een PKCS12-sleutel. De sleutel kan een gegenereerd geheim zijn, een tekenreeks die u definieert (zoals het geheim van de Facebook-toepassing) of een certificaat dat u uploadt. Als een toetsset meerdere toetsen heeft, is slechts één van de toetsen actief.

### <a name="trust-framework-policy-keyset"></a>Keyset voor beleid voor vertrouwenskader

- [De sleutelsets voor vertrouwenskaders weergeven](https://docs.microsoft.com/graph/api/trustframework-list-keysets)
- [Een sleutelsets voor vertrouwenskaders maken](https://docs.microsoft.com/graph/api/trustframework-post-keysets)
- [Een sleutelset ophalen](https://docs.microsoft.com/graph/api/trustframeworkkeyset-get)
- [Een sleutelsets voor vertrouwenskaders bijwerken](https://docs.microsoft.com/graph/api/trustframeworkkeyset-update)
- [Een sleutelsets voor vertrouwenskaders verwijderen](https://docs.microsoft.com/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>Beleidssleutel vertrouwenskader

- [Actieve sleutel op dit moment in de toetsset](https://docs.microsoft.com/graph/api/trustframeworkkeyset-getactivekey)
- [Een sleutel in toetsset genereren](https://docs.microsoft.com/graph/api/trustframeworkkeyset-generatekey)
- [Een op tekenreeks gebaseerd geheim uploaden](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadsecret)
- [Een X.509-certificaat uploaden](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadcertificate)
- [Een PKCS12-formaatcertificaat uploaden](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>Toepassingen

- [Toepassingen weergeven](https://docs.microsoft.com/graph/api/application-list)
- [Een toepassing maken](https://docs.microsoft.com/graph/api/resources/application)
- [Toepassing bijwerken](https://docs.microsoft.com/graph/api/application-update)
- [ServicePrincipal maken](https://docs.microsoft.com/graph/api/resources/serviceprincipal)
- [Oauth2Toestemmingsverlening maken](https://docs.microsoft.com/graph/api/resources/oauth2permissiongrant)
- [Toepassing verwijderen](https://docs.microsoft.com/graph/api/application-delete)

## <a name="application-extension-properties"></a>Eigenschappen van toepassingsextensie

- [Eigenschappen van lijstextensie](https://docs.microsoft.com/graph/api/application-list-extensionproperty)

Azure AD B2C biedt een map met 100 aangepaste kenmerken per gebruiker. Voor gebruikersstromen worden deze extensie-eigenschappen [beheerd met behulp van de Azure-portal.](custom-policy-custom-attributes.md) Voor aangepaste beleidsregels maakt Azure AD B2C de eigenschap voor u de eerste keer dat het beleid een waarde aan de extensieeigenschap schrijft.

## <a name="audit-logs"></a>Auditlogboeken

- [Controlelogboeken weergeven](https://docs.microsoft.com/graph/api/directoryaudit-list)

Zie Toegang tot [Azure AD B2C-controlelogboeken](view-audit-logs.md)voor meer informatie over toegang tot Azure AD B2C-controlelogboeken.
