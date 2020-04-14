---
author: vladvino
ms.service: api-management
ms.topic: include
ms.date: 11/09/2018
ms.author: vlvinogr
ms.openlocfilehash: 3b42d5fbcfb19f08b46241dbe92e6a300bec1df6
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81275031"
---
## <a name="how-apim-proxy-server-responds-with-ssl-certificates-in-the-tls-handshake"></a>Hoe APIM Proxy Server reageert met SSL-certificaten in de TLS-handshake

### <a name="clients-calling-with-sni-header"></a>Clients die bellen met SNI-header
Als de klant een of meerdere aangepaste domeinen heeft geconfigureerd voor Proxy, kan APIM reageren op HTTPS-verzoeken van het aangepaste domein (bijvoorbeeld contoso.com) en het standaarddomein (bijvoorbeeld apim-service-name.azure-api.net). Op basis van de informatie in de SNI-header (Server Name Indication) reageert APIM met het juiste servercertificaat.

### <a name="clients-calling-without-sni-header"></a>Clients die bellen zonder SNI-header
Als de klant een client gebruikt, die de [SNI-header](https://tools.ietf.org/html/rfc6066#section-3) niet verzendt, maakt APIM antwoorden op basis van de volgende logica:

* Als de service slechts één aangepast domein heeft geconfigureerd voor Proxy, is het standaardcertificaat het certificaat dat is uitgegeven aan het aangepaste proxydomein.
* Als de service meerdere aangepaste domeinen voor Proxy heeft geconfigureerd (ondersteund in de laag **Ontwikkelaars** en **Premium),** kan de klant aanwijzen welk certificaat het standaardcertificaat moet zijn. Als u het standaardcertificaat wilt instellen, moet de eigenschap [defaultSslBinding](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/apimanagementservice/createorupdate#hostnameconfiguration) worden ingesteld op true ('defaultSslBinding':"true"). Als de klant de eigenschap niet instelt, is het standaardcertificaat het certificaat dat is uitgegeven aan het standaardproxydomein dat wordt gehost op *.azure-api.net.

## <a name="support-for-putpost-request-with-large-payload"></a>Ondersteuning voor PUT/POST-aanvraag met grote payload

APIM Proxy server ondersteunt aanvraag met grote payload bij het gebruik van client-side certificaten in HTTPS (bijvoorbeeld payload > 40 KB). Om te voorkomen dat het verzoek van de server wordt bevroren, kunnen klanten de eigenschap ["negotiateClientCertificate" instellen: 'true'](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/ApiManagementService/CreateOrUpdate#hostnameconfiguration) op de hostnaam Proxy. Als de eigenschap is ingesteld op true, wordt het clientcertificaat aangevraagd op SSL/TLS-verbindingstijd, voordat een HTTP-aanvraag wordt uitgewisseld. Aangezien de instelling van toepassing is op het niveau **proxyhostnaam,** vragen alle verbindingsaanvragen om het clientcertificaat. Klanten kunnen maximaal 20 aangepaste domeinen configureren voor **Premium** Proxy (alleen ondersteund in de Premium-laag) en deze beperking omzeilen.

