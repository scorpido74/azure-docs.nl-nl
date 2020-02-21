---
title: Ondersteunde Microsoft Graph bewerkingen
titleSuffix: Azure AD B2C
description: Een index van de Microsoft Graph bewerkingen die worden ondersteund voor het beheer van Azure AD B2C resources, waaronder gebruikers, gebruikers stromen, id-providers, aangepast beleid, beleids sleutels en meer.
services: B2C
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/20/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 5eeae1ab6866435311eeec944b4a51ecf2793dee
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/21/2020
ms.locfileid: "77522965"
---
# <a name="microsoft-graph-operations-available-for-azure-ad-b2c"></a>Microsoft Graph bewerkingen die beschikbaar zijn voor Azure AD B2C

De volgende Microsoft Graph API-bewerkingen worden ondersteund voor het beheer van Azure AD B2C resources, waaronder gebruikers, id-providers, gebruikers stromen, aangepaste beleids regels en beleids sleutels.

Elke koppeling in de volgende secties is gericht op de corresponderende pagina binnen de Microsoft Graph API-verwijzing voor die bewerking.

## <a name="user-management"></a>Gebruikersbeheer

- [Gebruikers weer geven](https://docs.microsoft.com/graph/api/user-list)
- [Een consument gebruiker maken](https://docs.microsoft.com/graph/api/user-post-users)
- [Een gebruiker ophalen](https://docs.microsoft.com/graph/api/user-get)
- [Een gebruiker bijwerken](https://docs.microsoft.com/graph/api/user-update)
- [Een gebruiker verwijderen](https://docs.microsoft.com/graph/api/user-delete)

Zie voor meer informatie over het beheren van Azure AD B2C gebruikers accounts met de Microsoft Graph-API [Azure AD B2C gebruikers accounts beheren met Microsoft Graph](manage-user-accounts-graph-api.md).

## <a name="identity-providers-user-flow"></a>Id-providers (gebruikers stroom)

De id-providers beheren die beschikbaar zijn voor uw gebruikers stromen in uw Azure AD B2C-Tenant.

- [Id-providers weer geven die zijn geregistreerd in de Azure AD B2C Tenant](https://docs.microsoft.com/graph/api/identityprovider-list)
- [Een id-provider maken](https://docs.microsoft.com/graph/api/identityprovider-post-identityproviders)
- [Een id-provider ophalen](https://docs.microsoft.com/graph/api/identityprovider-get)
- [ID-provider bijwerken](https://docs.microsoft.com/graph/api/identityprovider-update)
- [Een id-provider verwijderen](https://docs.microsoft.com/graph/api/identityprovider-delete)

## <a name="user-flow"></a>Gebruikersstroom

Vooraf gemaakte beleids regels configureren voor aanmelding, aanmelden, gecombineerde registratie en aanmelding, wacht woord opnieuw instellen en profiel update.

- [Gebruikers stromen weer geven](https://docs.microsoft.com/graph/api/identityuserflow-list)
- [Een gebruikers stroom maken](https://docs.microsoft.com/graph/api/identityuserflow-post-userflows)
- [Een gebruikers stroom ophalen](https://docs.microsoft.com/graph/api/identityuserflow-get)
- [Een gebruikers stroom verwijderen](https://docs.microsoft.com/graph/api/identityuserflow-delete)

## <a name="custom-policies"></a>Aangepast beleid

Met de volgende bewerkingen kunt u uw Azure AD B2C Trust Framework-beleid beheren, ook wel [aangepast beleid](custom-policy-overview.md)genoemd.

- [Alle beleids regels voor vertrouwens relaties die zijn geconfigureerd in een Tenant weer geven](https://docs.microsoft.com/graph/api/trustframework-list-trustframeworkpolicies)
- [Vertrouwens raamwerk beleid maken](https://docs.microsoft.com/graph/api/trustframework-post-trustframeworkpolicy)
- [Eigenschappen van een bestaand vertrouwens raamwerk beleid lezen](https://docs.microsoft.com/graph/api/trustframeworkpolicy-get)
- [Het vertrouwens raamwerk beleid bijwerken of maken.](https://docs.microsoft.com/graph/api/trustframework-put-trustframeworkpolicy)
- [Een bestaand vertrouwens raamwerk beleid verwijderen](https://docs.microsoft.com/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>Beleids sleutels

In het Framework voor identiteits ervaring worden de geheimen opgeslagen waarnaar wordt verwezen in een aangepast beleid om de vertrouwens relatie tussen onderdelen tot stand te brengen. Deze geheimen kunnen symmetrische of asymmetrische sleutels/waarden zijn. In de Azure Portal worden deze entiteiten als **beleids sleutels**weer gegeven.

De resource op het hoogste niveau voor beleids sleutels in de Microsoft Graph-API is de [betrouw bare Framework sleutel](https://docs.microsoft.com/graph/api/resources/trustframeworkkeyset). Elke **sleutelset** bevat ten minste één **sleutel**. Als u een sleutel wilt maken, moet u eerst een lege sleutelset maken en vervolgens een sleutel genereren in de sleutelset. U kunt een hand matig geheim maken, een certificaat uploaden of een PKCS12/pfx-profiel-sleutel. De sleutel kan een gegenereerd geheim zijn, een teken reeks die u definieert (zoals het Facebook-toepassings geheim) of een certificaat dat u uploadt. Als een sleutelset meerdere sleutels heeft, is slechts een van de sleutels actief.

### <a name="trust-framework-policy-keyset"></a>Sleutelset van vertrouwens raamwerk beleid

- [De set vertrouwens raamwerk-Series weer geven](https://docs.microsoft.com/graph/api/trustframework-list-keysets)
- [Een vertrouwens raamwerk-Series maken](https://docs.microsoft.com/graph/api/trustframework-post-keysets)
- [Een sleutelset ophalen](https://docs.microsoft.com/graph/api/trustframeworkkeyset-get)
- [Een vertrouwens raamwerk-Series bijwerken](https://docs.microsoft.com/graph/api/trustframeworkkeyset-update)
- [Een vertrouwens raamwerk-Series verwijderen](https://docs.microsoft.com/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>Beleids sleutel vertrouwens raamwerk

- [Momenteel actieve sleutel in de sleutelset ophalen](https://docs.microsoft.com/graph/api/trustframeworkkeyset-getactivekey)
- [Een sleutel in sleutelset genereren](https://docs.microsoft.com/graph/api/trustframeworkkeyset-generatekey)
- [Een geheim op basis van een teken reeks uploaden](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadsecret)
- [Een X. 509-certificaat uploaden](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadcertificate)
- [Een certificaat voor PKCS12/pfx-profiel-indeling uploaden](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>Toepassingen

- [Toepassingen weergeven](https://docs.microsoft.com/graph/api/application-list)
- [Een toepassing maken](https://docs.microsoft.com/graph/api/resources/application)
- [Toepassing bijwerken](https://docs.microsoft.com/graph/api/application-update)
- [ServicePrincipal maken](https://docs.microsoft.com/graph/api/resources/serviceprincipal)
- [Oauth2Permission-toekenning maken](https://docs.microsoft.com/graph/api/resources/oauth2permissiongrant)
- [Toepassing verwijderen](https://docs.microsoft.com/graph/api/application-delete)

## <a name="application-extension-properties"></a>Eigenschappen van toepassings uitbreiding

- [Uitbrei ding-eigenschappen weer geven](https://docs.microsoft.com/graph/api/application-list-extensionproperty)

Azure AD B2C biedt een map die aangepaste kenmerken van 100 kan bevatten per gebruiker. Voor gebruikers stromen worden deze extensie-eigenschappen [beheerd met behulp van de Azure Portal](custom-policy-custom-attributes.md). Voor aangepaste beleids regels maakt Azure AD B2C de eigenschap voor u de eerste keer dat het beleid een waarde naar de extensie-eigenschap schrijft.

## <a name="audit-logs"></a>Auditlogboeken

- [Audit logboeken weer geven](https://docs.microsoft.com/graph/api/directoryaudit-list)

Zie [toegang tot Azure AD B2C audit logboeken](view-audit-logs.md)voor meer informatie over het openen van Azure AD B2C controle logboeken met de Microsoft Graph-API.
