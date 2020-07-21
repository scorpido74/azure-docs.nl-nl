---
title: Voorbeelden van Azure API Management-beleid | Microsoft Docs
description: Meer informatie over het beschikbare beleid voor gebruik in Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cflower
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 10/31/2017
ms.author: apimpm
ms.custom: mvc
ms.openlocfilehash: b5b8c82aa420b62e3b6e68ee53352eb9f77988f2
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86506686"
---
# <a name="api-management-policy-samples"></a>Voorbeelden van API Management-beleid

[Beleidsregels](../api-management-howto-policies.md) zijn een krachtige mogelijkheid van API Management waarmee de uitgever het gedrag van de API via configuratie kan wijzigen. Beleidsregels zijn een verzameling instructies die sequentieel worden uitgevoerd op de aanvraag of het antwoord van een API. De volgende tabel bevat koppelingen naar voorbeelden en geeft u een korte beschrijving van elk voorbeeld.

| Inkomende beleidsregels | Beschrijving |
| ---------------- | ----------- |
| [Voegt een Forwarded-koptekst zodat de back-end API de juiste URL's kan maken](./set-header-to-enable-backend-to-construct-urls.md) | Demonstreert hoe u een Forwarded-koptekst toevoegt in de binnenkomende aanvraag zodat de back-end-API de juiste URL's kan maken.                                                                                                        |
| [Een header met een correlatie-id toevoegen](./add-correlation-id.md)                                                             | Demonstreert hoe u een koptekst met een correlatie-id kunt toevoegen aan de binnenkomende aanvraag.                                                                                                                                        |
| [Mogelijkheden toevoegen aan een back-endservice en de reactie in cache opslaan](./cache-response.md)                                             | Laat zien hoe u mogelijkheden kunt toevoegen aan een back-endservice. Bijvoorbeeld, een naam van de locatie accepteren in plaats van de breedtegraad en lengtegraad in een weerprognose-API.                                                                    |
| [Toegang verlenen op basis van JWT-claims ](./authorize-request-based-on-jwt-claims.md)                                              | Laat zien hoe u toegang verleent aan specifieke HTTP-methoden op een API op basis van JWT-claims.                                                                                                                                       |
| [Aanvragen via externe authorizer autoriseren](./authorize-request-using-external-authorizer.md)                                                   | Hiermee kunt u zien hoe u een externe authorizer gebruikt voor het beveiligen van API-toegang.                                                                                                                                                               |
| [Toestaan van toegang met behulp van Google-OAuth-token](./use-google-as-oauth-token-provider.md)                                            | Laat zien hoe u toegang verleent aan uw eindpunten met Google als een provider van OAuth-token.                                                                                                                                    |
| [IP-adressen filteren bij het gebruik van een toepassingsgateway](./filter-ip-addresses-when-using-appgw.md) | Laat zien hoe u in beleid op IP filtert wanneer het API Management-exemplaar wordt geopend via een toepassingsgateway
| [Shared Access Signature genereren en aanvraag doorsturen naar Azure-opslag](./generate-shared-access-signature.md)                  | Laat zien hoe u [Shared Access Signature](../../storage/common/storage-sas-overview.md) genereert met expressies en de aanvraag doorstuurt naar Azure-opslag met beleid voor herschrijven van URI. |
| [OAuth2 toegangstoken ophalen uit AAD en dit doorsturen naar de back-end](./use-oauth2-for-authorization.md)                             | Biedt een voorbeeld van het gebruik van OAuth2 voor autorisatie tussen de gateway en een back-end. Er wordt weergegeven hoe u een toegangstoken van AAD kunt verkrijgen en dit door kunt sturen naar de back-end.                                                    |
| [X-CSRF-token ophalen uit SAP-gateway met behulp van beleid voor verbindingsaanvragen verzenden](./get-x-csrf-token-from-sap-gateway.md)                           | Laat zien hoe u een X-CSRF-patroon implementeert dat wordt gebruikt door veel API's. Dit voorbeeld is specifiek voor SAP-Gateway.                                                                                                                           |
| [De aanvraag op basis van de grootte van de hoofdtekst routeren](./route-requests-based-on-size.md)                                            | Demonstreert hoe u aanvragen op basis van de grootte van hun hoofdtekst routeert.                                                                                                                                                       |
| [Aanvraag contextinformatie sturen naar de back-endservice](./send-request-context-info-to-backend-service.md)                    | Laat zien hoe u bepaalde contextgegevens kunt verzenden naar de back-endservice voor logboekregistratie of voor verwerking.                                                                                                                                |
| [Duur antwoordcache instellen](./set-cache-duration.md)                                                                          | Demonstreert hoe u de duur van het antwoord instelt met de maxAge-waarde in Cache-Control-header die door de back-end is verstuurd.                                                                                                             |
| **Uitgaand beleid** | **Beschrijving** |
| [Antwoordinhoud filteren](./filter-response-content.md)                                                                         | Demonstreert hoe u gegevenselementen van de nettolading van de reactie moet filteren op basis van het product dat is gekoppeld aan de aanvraag.                                                                                                        |
| **Beleid op fout** | **Beschrijving** |
| [Fouten registreren in Stackify](./log-errors-to-stackify.md)                                                                           | Laat zien hoe u een beleid voor logboekregistratie van fouten toevoegt voor het verzenden van fouten naar Stackify voor logboekregistratie.                                                                                                                                            |
