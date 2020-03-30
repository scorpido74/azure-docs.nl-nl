---
title: BuildingBlocks
titleSuffix: Azure AD B2C
description: Geef het element BuildBlocks van een aangepast beleid op in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cf9c333af48e1c148dcd20ccf5ce91b284bd728b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189868"
---
# <a name="buildingblocks"></a>BuildingBlocks

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Het element **Bouwstenen** wordt toegevoegd in het element [TrustFrameworkPolicy.](trustframeworkpolicy.md)

```XML
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="mytenant.onmicrosoft.com"
  PolicyId="B2C_1A_TrustFrameworkBase"
  PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">

  <BuildingBlocks>
    <ClaimsSchema>
      ...
    </ClaimsSchema>
    <Predicates>
    ...
    </Predicates>
    <PredicateValidations>
    ...
    </PredicateValidations>
    <ClaimsTransformations>
      ...
    </ClaimsTransformations>
    <ContentDefinitions>
      ...
    </ContentDefinitions>
    <Localization>
      ...
    </Localization>
    <DisplayControls>
      ...
    </DisplayControls>
 </BuildingBlocks>
```

Het element **Bouwstenen** bevat de volgende elementen die moeten worden gespecificeerd in de gedefinieerde volgorde:

- [ClaimsSchema](claimsschema.md) - Definieert de claimtypen waarnaar kan worden verwezen als onderdeel van het beleid. Het claimschema is de plaats waar u uw claimtypen declareert. Een claimtype is vergelijkbaar met een variabele in veel programmatische talen. U het claimtype gebruiken om gegevens van de gebruiker van uw toepassing te verzamelen, claims van aanbieders van sociale identiteit te ontvangen, gegevens uit een aangepaste REST-API te verzenden en te ontvangen of interne gegevens op te slaan die door uw aangepaste beleid worden gebruikt.

- [Predicaten en PredicateValidationsInput](predicates.md) - Stelt u in staat om een validatieproces uit te voeren om ervoor te zorgen dat alleen goed gevormde gegevens worden ingevoerd in een claim.

- [ClaimsTransformaties](claimstransformations.md) - Bevat een lijst met claimstransformaties die in uw beleid kunnen worden gebruikt.  Een claimtransformatie zet de ene claim om in de andere. In de claimtransformatie geeft u een transformatiemethode op, zoals:
  - Het geval van een tekenreeksclaim wijzigen in de opgegeven. Bijvoorbeeld het wijzigen van een tekenreeks van kleine letters naar hoofdletters.
  - Het vergelijken van twee claims en het retourneren van een claim met ware aangeeft dat de vorderingen overeenkomen, anders onwaar.
  - Een tekenreeksclaim maken op basis van de opgegeven parameter in het beleid.
  - Het maken van een willekeurige string met behulp van de random number generator.
  - Een claim opmaken volgens de tekenreeks voor de opgegeven indeling. Deze transformatie maakt `String.Format` gebruik van de C#-methode.

- InputValidation - Met dit element u booleaanse aggregaties uitvoeren die vergelijkbaar zijn met *en* en *of*.

- [Inhouddefinities](contentdefinitions.md) - Bevat URL's voor HTML5-sjablonen die u gebruiken in uw gebruikersreis. In een aangepast beleid definieert een inhoudsdefinitie de HTML5-pagina URI die wordt gebruikt voor een bepaalde stap in het gebruikerstraject. Bijvoorbeeld de aanmelding of aanmelding, wachtwoordreset of foutpagina's. U het uiterlijk wijzigen door de LoadUri voor het HTML5-bestand te overschrijven. U ook nieuwe inhoudsdefinities maken op basis van uw behoeften. Dit element kan een verwijzing naar gelokaliseerde bronnen bevatten met behulp van een lokalisatie-id.

- [Lokalisatie](localization.md) - Hiermee u meerdere talen ondersteunen. Met de ondersteuning voor lokalisatie in beleid u de lijst met ondersteunde talen instellen in een beleid en een standaardtaal kiezen. Taalspecifieke tekenreeksen en collecties worden ook ondersteund.

- [DisplayControls](display-controls.md) - Hiermee definieert u de besturingselementen die op een pagina moeten worden weergegeven. Displaybesturingselementen hebben speciale functionaliteit en werken samen met technische back-endvalidatieprofielen. Weergavebesturingselementen zijn momenteel in **preview**.
