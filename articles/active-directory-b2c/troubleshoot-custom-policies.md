---
title: Aangepaste beleidsregels oplossen in Azure Active Directory B2C
description: Meer informatie over benaderingen voor het oplossen van fouten bij het werken met aangepaste beleidsregels in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2f65e98cec04991fe9edef1b81bcb3ecc3d93d76
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186354"
---
# <a name="troubleshoot-azure-ad-b2c-custom-policies-and-identity-experience-framework"></a>Problemen met het aangepaste Azure AD B2C-beleid en het Identity Experience Framework oplossen

Als u het aangepaste azure active directory B2C-beleid (Azure AD B2C) gebruikt, u problemen ondervinden bij het instellen van het Identity Experience Framework in de XML-indeling voor beleidstaal. Leren om aangepaste beleid te schrijven kan zijn als het leren van een nieuwe taal. In dit artikel beschrijven we een aantal tools en tips die u kunnen helpen bij het ontdekken en oplossen van problemen.

Dit artikel richt zich op het oplossen van problemen met de aangepaste beleidsconfiguratie van Azure AD B2C. Het gaat niet over de toepassing van de relying party of de identiteitsbibliotheek.

## <a name="xml-editing"></a>XML-bewerking

De meest voorkomende fout bij het instellen van aangepaste beleidsregels is onjuist opgemaakte XML. Een goede XML-editor is bijna essentieel. Het toont XML native, kleur-codes inhoud, pre-vult algemene termen, houdt XML-elementen geïndexeerd, en kan valideren tegen een XML-schema.

Twee van onze favoriete editors zijn [Visual Studio Code](https://code.visualstudio.com/) en [Notepad++](https://notepad-plus-plus.org/).

XML-schemavalidatie identificeert fouten voordat u uw XML-bestand uploadt. Download in de hoofdmap van het [startpakket](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack)het XML-schemadefinitiebestand *TrustFrameworkPolicy_0.3.0.0.xsd*. Als u wilt weten hoe u het XSD-schemabestand gebruiken voor validatie in uw editor, zoekt u *XML-hulpprogramma's* en *XML-validatie* of iets dergelijks in de documentatie van de editor.

Mogelijk is een overzicht van XML-regels nuttig. Azure AD B2C verwerpt eventuele XML-opmaakfouten die worden gedetecteerd. Af en toe kan onjuist opgemaakte XML foutberichten veroorzaken die misleidend zijn.

## <a name="upload-policies-and-policy-validation"></a>Uploadbeleid en beleidsvalidatie

Validatie van het XML-beleidsbestand wordt automatisch uitgevoerd bij het uploaden. De meeste fouten zorgen ervoor dat het uploaden mislukt. Validatie omvat het beleidsbestand dat u uploadt. Het bevat ook de keten van bestanden waarnaar het uploadbestand verwijst (het beleidsbestand van relying party, het extensiesbestand en het basisbestand).

Veelvoorkomende validatiefouten zijn onder andere:

> Foutfragment:`...makes a reference to ClaimType with id "displayName" but neither the policy nor any of its base policies contain such an element`

* De ClaimType-waarde kan verkeerd worden gespeld of bestaat niet in het schema.
* ClaimType-waarden moeten worden gedefinieerd in ten minste één van de bestanden in het beleid.
    Bijvoorbeeld: `<ClaimType Id="issuerUserId">`
* Als ClaimType is gedefinieerd in het extensiesbestand, maar het wordt ook gebruikt in een TechnicalProfile-waarde in het basisbestand, resulteert het uploaden van het basisbestand in een fout.

> Foutfragment:`...makes a reference to a ClaimsTransformation with id...`

* De oorzaken van deze fout kunnen hetzelfde zijn als voor de ClaimType-fout.

> Foutfragment:`Reason: User is currently logged as a user of 'yourtenant.onmicrosoft.com' tenant. In order to manage 'yourtenant.onmicrosoft.com', please login as a user of 'yourtenant.onmicrosoft.com' tenant`

* Controleer of de tenantid-waarde in de `<TrustFrameworkPolicy\>` en `<BasePolicy\>` elementen overeenkomen met uw azure AD B2C-tenant.

## <a name="troubleshoot-the-runtime"></a>Problemen met de runtime oplossen

* Gebruik **Nu** `https://jwt.ms` uitvoeren en test uw beleid onafhankelijk van uw web- of mobiele toepassing. Deze website fungeert als een relying party applicatie. Het toont de inhoud van het JSON-webtoken (JWT) dat wordt gegenereerd door uw Azure AD B2C-beleid.

    Ga als lid van het `https://jwt.ms` werk om een testtoepassing te maken die kan worden omgeleid naar token-inspectie:

    [!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

* Als u de uitwisseling van berichten tussen uw clientbrowser en Azure AD B2C wilt traceren, gebruikt u [Fiddler](https://www.telerik.com/fiddler). Het kan u helpen een indicatie te krijgen van waar uw gebruikersreis niet in uw orkestratiestappen faalt.

* Gebruik **in de ontwikkelingsmodus** [Application Insights](troubleshoot-with-application-insights.md) om de activiteit van uw gebruikersreis van Identity Experience Framework te traceren. In **de ontwikkelingsmodus**u de uitwisseling van claims tussen het Identity Experience Framework en de verschillende claimproviders observeren die worden gedefinieerd door technische profielen, zoals identiteitsproviders, API-gebaseerde services, de Azure AD B2C-gebruikersmap en andere services, zoals Azure Multi-Factor Authentication.

## <a name="recommended-practices"></a>Aanbevolen procedures

**Bewaar meerdere versies van uw scenario's. Groepeer ze in een project met uw aanvraag.** De basis-, extensie- en relying party-bestanden zijn direct afhankelijk van elkaar. Sla ze als een groep. Houd afzonderlijke werkversies bij wanneer er nieuwe functies aan uw beleid worden toegevoegd. Werkversies in uw eigen bestandssysteem met de toepassingscode waarmee ze communiceren. Uw toepassingen kunnen een beroep doen op veel verschillende beleid van relying party in een tenant. Ze kunnen afhankelijk worden van de claims die ze verwachten van uw Azure AD B2C-beleid.

**Ontwikkelen en testen van technische profielen met bekende gebruikersreizen.** Gebruik getest startpakketbeleid om uw technische profielen in te stellen. Test ze afzonderlijk voordat u ze opneemt in uw eigen gebruikersreizen.

**Ontwikkel en test gebruikersreizen met geteste technische profielen.** Wijzig de orkestratiestappen van een gebruikersreis stapsgewijs. Bouw geleidelijk aan uw beoogde scenario's.

## <a name="next-steps"></a>Volgende stappen

Download het [active-directory-b2c-custom-policy-starterpack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) .zip archief. U de repository ook klonen:

```
git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
```
