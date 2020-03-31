---
title: Beperkingen en details van api-indelingen ondersteunen
titleSuffix: Azure API Management
description: Details over bekende problemen en beperkingen voor Open API-, WSDL- en WADL-indelingen ondersteunen in Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: vlvinogr
editor: ''
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/02/2020
ms.author: apimpm
ms.openlocfilehash: 61d43addfdf9008cb7aa8a073dcf3bb702cb55f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513368"
---
# <a name="api-import-restrictions-and-known-issues"></a>API-importbeperkingen en bekende problemen

## <a name="about-this-list"></a>Over deze lijst

Wanneer u een API importeert, u bepaalde beperkingen tegenkomen of problemen identificeren die moeten worden verholpen voordat u de import uitvoeren. In dit artikel worden deze beperkingen, georganiseerd door de importindeling van de API, documenteerd. Het beschrijft ook hoe OpenAPI-export werkt.

## <a name="openapiswagger-import-limitations"></a><a name="open-api"> </a>OpenAPI/Swagger importbeperkingen

Als u fouten ontvangt bij het importeren van uw OpenAPI-document, controleert u of u het vooraf hebt gevalideerd. U dat doen met behulp van de ontwerper in de Azure-portal (Design - Front End - OpenAPI Specification Editor), of met een hulpprogramma van derden, zoals <a href="https://editor.swagger.io">Swagger Editor.</a>

### <a name="general"></a><a name="open-api-general"> </a>Algemeen

-   Vereiste parameters voor zowel pad als query moeten unieke namen hebben. (In OpenAPI hoeft een parameternaam alleen uniek te zijn binnen een locatie, bijvoorbeeld pad, query, koptekst. In API-beheer staan we echter toe dat bewerkingen worden gediscrimineerd door zowel pad- als queryparameters (die OpenAPI niet ondersteunt). Daarom vereisen we dat parameternamen uniek zijn binnen de gehele URL-sjabloon.)
-   `\$ref`verwijzingen kunnen niet verwijzen naar externe bestanden.
-   `x-ms-paths`en `x-servers` zijn de enige ondersteunde extensies.
-   Aangepaste extensies worden genegeerd bij importeren en worden niet opgeslagen of bewaard voor export.
-   `Recursion`- API Management ondersteunt geen definities die recursief zijn gedefinieerd (bijvoorbeeld schema's die naar zichzelf verwijzen).
-   De URL van bronbestand (indien beschikbaar) wordt toegepast op relatieve server-URL's.
-   Beveiligingsdefinities worden genegeerd.
-   Inline-schemadefinities voor API-bewerkingen worden niet ondersteund. Schemadefinities worden gedefinieerd in het API-bereik en kunnen worden verwezen in api-bewerkingenaanvragen of antwoordscopes.
-   Een gedefinieerde URL-parameter moet deel uitmaken van de URL-sjabloon.
-   `Produces`trefwoord, waarin MIME-typen worden geretourneerd door een API, wordt niet ondersteund. 

### <a name="openapi-version-2"></a><a name="open-api-v2"> </a>OpenAPI versie 2

-   Alleen JSON-indeling wordt ondersteund.

### <a name="openapi-version-3"></a><a name="open-api-v3"> </a>OpenAPI versie 3

-   Als `servers` er veel zijn opgegeven, probeert API Management de eerste URL van HTTP's te selecteren. Als er geen HTTPs URL's zijn - de eerste HTTP-URL. Als er geen HTTP-URL's zijn, is de URL van de server leeg.
-   `Examples`wordt niet ondersteund, `example` maar wel.

## <a name="openapi-import-update-and-export-mechanisms"></a>OpenAPI-import-, update- en exportmechanismen

### <a name="add-new-api-via-openapi-import"></a>Nieuwe API toevoegen via OpenAPI-import

Voor elke bewerking in het OpenAPI-document wordt een nieuwe bewerking gemaakt `operationId` met `summary` respectievelijk de naam van de Azure-bron en de weergavenaam. `operationId`waarde wordt genormaliseerd volgens de onderstaande regels. `summary`waarde wordt geïmporteerd zoals het is en de lengte is beperkt tot 300 tekens.

Als `operationId` niet is opgegeven (dat wil `null`zeggen, niet aanwezig of leeg), wordt azure-resourcenaamwaarde gegenereerd `get-foo`door bijvoorbeeld HTTP-methode en padsjabloon te combineren.

Als `summary` de waarde niet is opgegeven `null`(dat wil `display name` zeggen, `operationId`niet aanwezig of leeg), wordt de waarde ingesteld op . Als `operationId` deze niet is opgegeven, wordt de waarde van de weergavenaam `Get - /foo`gegenereerd door bijvoorbeeld http-methode en padsjabloon te combineren.

### <a name="update-an-existing-api-via-openapi-import"></a>Een bestaande API bijwerken via OpenAPI-import

Tijdens het importeren van bestaande API wordt gewijzigd om api's te matchen die in het OpenAPI-document worden beschreven. Elke bewerking in het OpenAPI-document wordt gekoppeld `operationId` aan de bestaande bewerking door de waarde ervan te vergelijken met de naam van de Azure-bron van de bestaande bewerking.

Als er een overeenkomst wordt gevonden, worden de eigenschappen van bestaande bewerking 'in-place' bijgewerkt.

Als er geen overeenkomst wordt gevonden, wordt er een nieuwe bewerking gemaakt met behulp van de regels die in de bovenstaande sectie worden beschreven. Voor elke nieuwe bewerking probeert de import beleid te kopiëren van een bestaande bewerking met dezelfde HTTP-methode en padsjabloon.

Alle bestaande niet-overeenkomende bewerkingen worden verwijderd.

Volg de volgende richtlijnen om import voorspelbaarder te maken:

- Zorg ervoor `operationId` dat u voor elke bewerking de eigenschap opgeeft.
- Zich te `operationId` onthouden van verandering na de eerste import.
- Wijzig `operationId` nooit en http-methode of padsjabloon tegelijkertijd.

### <a name="export-api-as-openapi"></a>API exporteren als OpenAPI

Voor elke bewerking wordt de naam van `operationId`de Azure-bron geëxporteerd als `summary`een , en wordt de weergavenaam geëxporteerd als een .
Normalisatieregels voor de werkingId

- Converteren naar kleine letters.
- Vervang elke reeks niet-alfanumerieke tekens door een `GET-/foo/{bar}?buzz={quix}` enkel streepje, `get-foo-bar-buzz-quix-`bijvoorbeeld, zal worden omgezet in .
- Trim streepjes aan beide zijden, bijvoorbeeld, `get-foo-bar-buzz-quix-` zal worden`get-foo-bar-buzz-quix`
- Afgekapt om te passen bij 76 tekens, vier tekens minder dan de maximumlimiet voor een resourcenaam.
- Gebruik de overige vier tekens voor een deduplicatie achtervoegsel, indien nodig, in de vorm van `-1, -2, ..., -999`.


## <a name="wsdl"></a><a name="wsdl"> </a>WSDL

WSDL-bestanden worden gebruikt om SOAP-pass-through en SOAP-to-REST API's te maken.

-   **SOAP bindingen** -Alleen SOAP bindingen van stijl "document" en "letterlijke" codering worden ondersteund. Er is geen ondersteuning voor "rpc" stijl of SOAP-Encoding.
-   **WSDL:Import** - Dit kenmerk wordt niet ondersteund. Klanten moeten de invoer samenvoegen tot één document.
-   **Berichten met meerdere onderdelen** - Dit soort berichten worden niet ondersteund.
-   **WCF wsHttpBinding** - SOAP-services die zijn gemaakt met Windows Communication Foundation moeten basicHttpBinding - wsHttpBinding gebruiken.
-   **MTOM** - Diensten die gebruikmaken van MTOM <em>kunnen</em> werken. Officiële ondersteuning wordt op dit moment niet aangeboden.
-   **Recursie** - Typen die recursief worden gedefinieerd (bijvoorbeeld verwijzen naar een array van zichzelf) worden niet ondersteund door APIM.
-   **Meerdere naamruimten** - Meerdere naamruimten kunnen in een schema worden gebruikt, maar alleen de doelnaamruimte kan worden gebruikt om berichtonderdelen te definiëren. Andere naamruimten dan het doel, die worden gebruikt om andere invoer- of uitvoerelementen te definiëren, blijven niet behouden. Hoewel een dergelijk WSDL-document kan worden geïmporteerd, hebben bij export alle berichtonderdelen de doelnaamruimte van de WSDL.
-   **Arrays** - SOAP-to-REST-transformatie ondersteunt alleen verpakte arrays in het onderstaande voorbeeld:

```xml
    <complexType name="arrayTypeName">
        <sequence>
            <element name="arrayElementValue" type="arrayElementType" minOccurs="0" maxOccurs="unbounded"/>
        </sequence>
    </complexType>
    <complexType name="typeName">
        <sequence>
            <element name="element1" type="someTypeName" minOccurs="1" maxOccurs="1"/>
            <element name="element2" type="someOtherTypeName" minOccurs="0" maxOccurs="1" nillable="true"/>
            <element name="arrayElement" type="arrayTypeName" minOccurs="1" maxOccurs="1"/>
        </sequence>
    </complexType>
```

## <a name="wadl"></a><a name="wadl"> </a>WADL

Momenteel zijn er geen bekende WADL import problemen.
