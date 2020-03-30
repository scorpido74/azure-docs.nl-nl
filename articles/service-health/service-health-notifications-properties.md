---
title: Wat zijn Azure-servicestatusmeldingen?
description: Met servicestatusmeldingen u servicestatusberichten bekijken die zijn gepubliceerd door Microsoft Azure.
ms.topic: conceptual
ms.date: 4/12/2018
ms.openlocfilehash: e40538ac98bbc7b79311d4fb0da7568d56a84e18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77653965"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Servicestatusmeldingen bekijken met de Azure-portal

Servicestatusmeldingen worden gepubliceerd door Azure en bevatten informatie over de bronnen onder uw abonnement. Deze meldingen zijn een subklasse van activiteitslogboekgebeurtenissen en zijn ook te vinden in het activiteitenlogboek. Servicestatusmeldingen kunnen informatief of bruikbaar zijn, afhankelijk van de klasse.

Er zijn verschillende klassen van service health meldingen:  

- **Vereiste actie:** Azure merkt mogelijk iets ongewoons op uw account en werkt met u samen om dit te verhelpen. Azure stuurt u een melding, waarin de acties worden beschreven die u moet uitvoeren of hoe u contact opnemen met Azure-engineering of -ondersteuning.  
- **Incident:** Een gebeurtenis die gevolgen heeft voor de service, heeft momenteel invloed op een of meer van de bronnen in uw abonnement.  
- **Onderhoud:** Een geplande onderhoudsactiviteit die van invloed kan zijn op een of meer van de resources onder uw abonnement.  
- **Informatie:** Mogelijke optimalisaties die kunnen helpen bij het verbeteren van uw resourcegebruik. 
- **Beveiliging:** Dringende beveiligingsgerelateerde informatie over uw oplossingen die op Azure worden uitgevoerd.

Elke servicestatusmelding bevat details over het bereik en de impact op uw resources. Details zijn onder meer:

Naam van eigenschap | Beschrijving
-------- | -----------
Kanalen | Een van de volgende waarden: **Beheerder** of **Bewerking**.
correlationId | Meestal een GUID in de tekenreeksindeling. Gebeurtenissen die tot dezelfde actie behoren, hebben meestal dezelfde correlationId.
eventDataId | De unieke id van een gebeurtenis.
gebeurtenisNaam | De titel van een evenement.
niveau | Het niveau van een evenement
resourceProviderName | De naam van de resourceprovider voor de getroffen resource.
resourceType| Het type resource van de getroffen resource.
subStatus | Meestal is de HTTP-statuscode van de bijbehorende REST-aanroep, maar kan ook andere tekenreeksen bevatten die een substatus beschrijven. Bijvoorbeeld: OK (HTTP-statuscode: 200), Gemaakt (HTTP-statuscode: 201), Geaccepteerd (HTTP-statuscode: 202), Geen inhoud (HTTP-statuscode: 204), Bad Request (HTTP-statuscode: 400), Niet gevonden (HTTP-statuscode: 404), Conflict (HTTP-statuscode: 409), Interne server Fout (HTTP-statuscode: 500), Service niet beschikbaar (HTTP-statuscode: 503) en gateway-time-out (HTTP-statuscode: 504).
eventTimestamp | Tijdstempel wanneer de gebeurtenis is gegenereerd door de Azure-service die de aanvraag verwerkt die overeenkomt met de gebeurtenis.
indieningTijdstempel | Tijdstempel toen de gebeurtenis beschikbaar kwam voor query's.
subscriptionId | Het Azure-abonnement waarin deze gebeurtenis is geregistreerd.
status | Tekenreeks die de status van de bewerking beschrijft. Enkele algemene waarden zijn: **Gestart**, **In uitvoering**, **Geslaagd**, **Mislukt**, **Actief**en **Opgelost**.
operationName | De naam van de bewerking.
category | Deze accommodatie is altijd **ServiceHealth.**
resourceId | De resource-id van de getroffen resource.
Eigenschappen.titel | De gelokaliseerde titel voor deze mededeling. Engels is de standaard.
Eigenschappen.communicatie | De gelokaliseerde details van de communicatie met HTML-markering. Engels is de standaard.
Properties.incidentType | Een van de volgende waarden: **ActionRequired**, **Informational**, **Incident**, **Maintenance**, or **Security**.
Properties.trackingId | Het incident waarmee deze gebeurtenis is gekoppeld. Gebruik dit om de gebeurtenissen met betrekking tot een incident te correleren.
Properties.impactedServices | Een ontsnapte JSON-blob die de services en regio's beschrijft die door het incident zijn beïnvloed. De eigenschap bevat een lijst met services, die elk een **ServiceName**hebben, en een lijst met beïnvloede regio's, die elk een **RegioNaam**hebben.
Properties.defaultLanguageTitle | De communicatie in het Engels.
Properties.defaultLanguageContent | De communicatie in het Engels als HTML-markering of platte tekst.
Eigenschappen.fase | De mogelijke waarden voor **Incident**en **Beveiliging** zijn **Actief,** **Opgelost** of **RCA**. Voor **ActionRequired** of **Informational** is de enige waarde **Actief.** Voor **onderhoud** zijn ze: **Actief**, **Gepland**, **InProgress**, **Geannuleerd**, **Opnieuw gepland,** **Opgelost**of **Voltooid**.
Properties.communicationId | De communicatie waarmee deze gebeurtenis is gekoppeld.

### <a name="details-on-service-health-level-information"></a>Details over informatie over servicestatusniveau

**Actie vereist** (properties.incidentType == ActieVereist)
- Informatieve - Beheerdersactie is vereist om impact op bestaande services te voorkomen.
    
**Onderhoud** (properties.incidentType == Onderhoud)
- Waarschuwing - Noodonderhoud
- Informatief - Standaard gepland onderhoud

**Informatie** (properties.incidentType == Informatie)
- Informatief - Beheerder kan worden verplicht om gevolgen voor bestaande services te voorkomen.

**Beveiliging** (properties.incidentType == Beveiliging)
- Waarschuwing - Beveiligingsadvies dat van invloed is op bestaande services en mogelijk beheerdersactie vereist.
- Informatief - Beveiligingsadvies dat van invloed is op bestaande services.

**Serviceproblemen** (properties.incidentType == Incident)
- Fout - Wijdverbreide problemen die toegang hebben tot meerdere services in meerdere regio's hebben gevolgen voor een breed scala aan klanten.
- Waarschuwing : problemen met de toegang tot specifieke services en/of specifieke regio's hebben gevolgen voor een subset van klanten.
- Informatief - Problemen die van invloed zijn op beheeractiviteiten en/of latentie, die geen invloed hebben op de beschikbaarheid van de service.
