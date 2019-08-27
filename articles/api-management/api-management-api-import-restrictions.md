---
title: Beperkingen en bekende problemen in azure API Management-API importeren | Microsoft Docs
description: Details over bekende problemen en beperkingen bij het importeren in azure API Management met behulp van de open API-, WSDL-of WADL-indelingen.
services: api-management
documentationcenter: ''
author: vladvino
manager: vlvinogr
editor: ''
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2019
ms.author: apimpm
ms.openlocfilehash: bf39e508b8e4c883934b51fdc99eaef96caf1235
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2019
ms.locfileid: "70018213"
---
# <a name="api-import-restrictions-and-known-issues"></a>API-import beperkingen en bekende problemen

## <a name="about-this-list"></a>Over deze lijst

Bij het importeren van een API kunt u enkele beperkingen opleveren of problemen identificeren die moeten worden verholpen voordat u kunt importeren. In dit artikel worden deze documenten geordend op basis van de import indeling van de API.

## <a name="open-api"> </a>OpenAPI/Swagger

Als u fouten ontvangt bij het importeren van uw OpenAPI-document, moet u ervoor zorgen dat u het hebt gevalideerd. U kunt dit doen met behulp van de designer in de Azure Portal (OpenAPI specificatie-editor voor ontwerp vooraf) of met een hulp programma van derden, zoals <a href="https://editor.swagger.io">Swagger editor</a>.

### <a name="open-api-general"> </a>Algemeen

-   De vereiste para meters voor beide paden en query's moeten unieke namen hebben. (In OpenAPI moet een parameter naam alleen uniek zijn binnen een locatie, bijvoorbeeld pad, query, koptekst. In API Management we echter toestaan dat bewerkingen worden doorzocht met behulp van pad-en query parameters (wat niet wordt ondersteund door OpenAPI). Daarom moeten parameter namen uniek zijn binnen de volledige URL-sjabloon.)
-   Ref pointers kunnen niet verwijzen naar externe bestanden.  **\$**
-   **x-MS-paden** en **x-servers** zijn de enige ondersteunde uitbrei dingen.
-   Aangepaste extensies worden genegeerd bij het importeren en worden niet opgeslagen of bewaard om te worden geëxporteerd.
-   **Recursie** -API Management biedt geen ondersteuning voor definities die recursief zijn gedefinieerd (bijvoorbeeld schema's die naar zichzelf verwijzen).
-   De URL van het bron bestand (indien beschikbaar) wordt toegepast op relatieve server-Url's.
-   Beveiligings definities worden genegeerd.

### <a name="open-api-v2"> </a>OpenAPI versie 2

-   Alleen JSON-indeling wordt ondersteund.

### <a name="open-api-v3"> </a>OpenAPI-versie 3

-   Als er veel **servers** zijn opgegeven, probeert API Management de eerste HTTPS-URL te selecteren. Als er geen HTTPs-Url's zijn, de eerste HTTP-URL. Als er geen HTTP-Url's zijn, de URL van de server is leeg.
-   **Voor beelden** worden niet ondersteund, maar het **voor beeld** is.
-   **Meerdelige/formulier-gegevens** worden niet ondersteund.

> [!IMPORTANT]
> Zie dit [document](https://blogs.msdn.microsoft.com/apimanagement/2018/04/11/important-changes-to-openapi-import-and-export/) voor belangrijke informatie en tips die betrekking hebben op OpenAPI importeren.

## <a name="wsdl"> </a>WSDL

WSDL-bestanden worden gebruikt voor het maken van SOAP-Pass-Through-en SOAP-to-REST-Api's.

-   **SOAP** -bindingen: alleen SOAP-bindingen met de stijl document en letterlijke code ring worden ondersteund. Er is geen ondersteuning voor "RPC"-stijl of SOAP-encoding.
-   **WSDL: import** -dit kenmerk wordt niet ondersteund. Klanten moeten de Imports samen voegen in één document.
-   **Berichten met meerdere onderdelen** : deze typen berichten worden niet ondersteund.
-   **WCF wsHttpBinding** -SOAP-services die zijn gemaakt met Windows Communication Foundation moeten BasicHttpBinding-wsHttpBinding gebruiken. dit wordt niet ondersteund.
-   **MTOM** -services die gebruikmaken van MTOM <em>kunnen</em> werken. Er wordt op dit moment geen officiële ondersteuning aangeboden.
-   **Recursie** -typen die recursief worden gedefinieerd (bijvoorbeeld verwijzen naar een matrix zelf) worden niet ondersteund door APIM.
-   **Meerdere naam ruimten** : meerdere naam ruimten kunnen worden gebruikt in een schema, maar alleen de doel naam ruimte kan worden gebruikt voor het definiëren van bericht delen. Andere naam ruimten dan het doel die worden gebruikt voor het definiëren van andere invoer-of uitvoer elementen blijven niet behouden. Hoewel een dergelijk WSDL-document kan worden geïmporteerd, is bij het exporteren van alle bericht onderdelen de doel naam ruimte van de WSDL.
-   **Matrices** -SOAP-to-rest trans formatie ondersteunt alleen ingepakte matrices die in het onderstaande voor beeld worden weer gegeven:

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

## <a name="wadl"> </a>WADL

Er zijn momenteel geen bekende problemen met de WADL-import.
