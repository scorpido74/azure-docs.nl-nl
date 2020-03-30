---
title: Azure CDN HTTP-raw-logboeken
description: In dit artikel worden de Azure CDN HTTP-raw-logboeken beschreven.
services: cdn
author: sohamnchatterjee
manager: danielgi
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2020
ms.author: sohamnc
ms.openlocfilehash: c6e8570746ae3dd0051dbec084c89d90580d28b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371639"
---
# <a name="azure-cdn-http-raw-logs"></a>Azure CDN HTTP-raw-logboeken
Raw-logboeken bieden uitgebreide informatie over bewerkingen en fouten die belangrijk zijn voor controle en probleemoplossing. Raw logs verschillen van activiteit logs. Activiteitslogboeken bieden inzicht in de bewerkingen die worden uitgevoerd op Azure-resources. Raw logs bieden een record van de activiteiten van uw resource.

> [!IMPORTANT]
> De functie HTTP raw logs is beschikbaar voor Azure CDN van Microsoft.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint. 

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij [https://portal.azure.com](https://portal.azure.com)de Azure-portal op .

## <a name="configuration"></a>Configuratie

Ga als lid van het Programma Voor Uw Azure CDN vanuit Microsoft-profiel: 

1. Selecteer in het menu van de Azure-portal **alle bronnen** >> **\<uw-CDN-profiel>**.

2. Selecteer onder **Bewaking** de optie **Diagnostische instellingen**.

3. Selecteer **+ Diagnostische instelling toevoegen**.

    ![CDN-diagnostische instelling](./media/cdn-raw-logs/raw-logs-01.png)

    > [!IMPORTANT]
    > Raw-logboeken zijn alleen beschikbaar in het profielniveau, terwijl geaggregeerde http-statuscodelogboeken beschikbaar zijn in het eindpuntniveau.

4. Voer **onder Diagnostische instellingen**een naam in voor de diagnostische instelling onder De naam Diagnostische **instellingen**.

5. Selecteer het **logboek** en stel de retentie in dagen in.

6. Selecteer de **doelgegevens**. Bestemmingsopties zijn:
    * **Verzenden naar Log Analytics**
        * Selecteer de werkruimte **Abonnement** en **Logboekanalyse**.
    * **Archiveren naar een opslagaccount**
        * Selecteer het **abonnement** en het **opslagaccount**.
    * **Streamen naar een gebeurtenishub**
        * Selecteer de **naamruimte van**de **gebeurtenishub,** **gebeurtenishub (optioneel)** en **de beleidsnaam van de gebeurtenishub**.

    ![CDN-diagnostische instelling](./media/cdn-raw-logs/raw-logs-02.png)

7. Selecteer **Opslaan**.

## <a name="raw-logs-properties"></a>Eigenschappen van raw-logboeken

Azure CDN van Microsoft Service biedt momenteel Raw-logboeken. Raw-logboeken bieden afzonderlijke API-aanvragen bij elke vermelding met het volgende schema: 

| Eigenschap              | Beschrijving                                                                                                                                                                                          |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| TrackingReferentie     | De unieke referentietekenreeks die een aanvraag van Front Door identificeert en ook als X-Azure-Ref-header naar de client wordt verzonden. Vereist voor het zoeken naar details in de toegangslogboeken voor een specifiek verzoek. |
| HttpMethod            | HTTP-methode die door de aanvraag wordt gebruikt.                                                                                                                                                                     |
| HttpVersie           | Type van de aanvraag of verbinding.                                                                                                                                                                   |
| RequestUri            | URI van het ontvangen verzoek.                                                                                                                                                                         |
| AanvragenBytes          | De grootte van het HTTP-aanvraagbericht in bytes, inclusief de aanvraagkoppen en de aanvraaginstantie.                                                                                                   |
| ResponseBytes         | Bytes die door de backendserver als antwoord worden verzonden.                                                                                                                                                    |
| Useragent             | Het browsertype dat de client heeft gebruikt.                                                                                                                                                               |
| ClientIp              | Het IP-adres van de klant die het verzoek heeft ingediend.                                                                                                                                                  |
| Tijdgenomen             | De tijdsduur die de actie heeft ondernomen, in milliseconden.                                                                                                                                            |
| SecurityProtocol (SecurityProtocol)      | De TLS/SSL-protocolversie die door het verzoek of null wordt gebruikt als er geen versleuteling is.                                                                                                                           |
| Eindpunt              | De CDN-eindpunthost is geconfigureerd onder het bovenliggende CDN-profiel.                                                                                                                                   |
| Naam back-endhost     | De naam van de backendhost of -oorsprong waar aanvragen worden verzonden.                                                                                                                                |
| Verzonden naar oorsprongschild | Als dit waar is, betekent dit dat het verzoek is beantwoord vanuit de cache van Origin Shield in plaats van de edge pop. Origin shield is een bovenliggende cache die wordt gebruikt om de hitratio in de cache te verbeteren.                                       |
| Httpstatuscode        | De HTTP-statuscode is geretourneerd van de proxy.                                                                                                                                                        |
| HttpStatusDetails     | Resulterende status op het verzoek. Betekenis van deze tekenreekswaarde is te vinden in een statusreferentietabel.                                                                                              |
| Pop                   | De randpop, die op het verzoek van de gebruiker reageerde. De afkortingen van POP's zijn luchthavencodes van hun respectievelijke metro's.                                                                                   |
| Cachestatus          | Betekent dat het object is geretourneerd uit de cache of afkomstig is van de oorsprong.                                                                                                             |
> [!IMPORTANT]
> De HTTP Raw-logboekenfunctie is automatisch beschikbaar voor profielen die na **25 februari 2020**zijn gemaakt of bijgewerkt. Voor eerder gemaakte CDN-profielen moet het CDN-eindpunt worden bijgewerkt na het instellen van logboekregistratie. Men kan bijvoorbeeld naar geofiltering onder CDN-eindpunten navigeren en elk land blokkeren dat niet relevant is voor hun werkbelasting en op opslaan drukken. 

> [!NOTE]
> De logboeken kunnen onder uw Log Analytics-profiel worden bekeken door een query uit te voeren. Een voorbeeldquery lijkt op AzureDiagnostics | waar categorie == "AzureCdnAccessLog"

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u HTTP raw-logboeken ingeschakeld voor de Microsoft CDN-service.

Zie voor meer informatie over Azure CDN en de andere Azure-services die in dit artikel worden genoemd:

* [Analyseren](cdn-log-analysis.md) Azure CDN-gebruikspatronen.

* Meer informatie over [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview).

* [Loganalytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)configureren in Azure Monitor .
