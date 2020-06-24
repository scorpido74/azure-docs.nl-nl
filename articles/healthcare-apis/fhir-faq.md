---
title: Veelgestelde vragen over FHIR Services in azure-Azure API voor FHIR
description: Krijg antwoorden op veelgestelde vragen over de Azure API voor FHIR, zoals de opslag locatie van gegevens achter FHIR Api's en versie ondersteuning.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: cdec5ade4fc72fba6fcfba131b69ca9eb373874c
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2020
ms.locfileid: "84871776"
---
# <a name="frequently-asked-questions-about-the-azure-api-for-fhir"></a>Veelgestelde vragen over de Azure API voor FHIR

## <a name="what-is-fhir"></a>Wat is FHIR?
De snelle interoperabiliteits bronnen van de gezondheids zorg (FHIR-uitgesp roken "vuur") is een interoperabiliteits norm die is bedoeld om de uitwisseling van gezondheids zorg tussen verschillende Health-systemen mogelijk te maken. Deze standaard is ontwikkeld door de HL7-organisatie en wordt aangenomen door organisaties in de gezondheids zorg over de hele wereld. De meest recente versie van FHIR beschikbaar is R4 (Release 4). De Azure API voor FHIR ondersteunt R4 en biedt ook ondersteuning voor de vorige versie van STU3 (standaard voor proef versie 3). Ga naar [HL7.org](http://hl7.org/fhir/summary.html)voor meer informatie over FHIR.

## <a name="is-the-data-behind-the-fhir-apis-stored-in-azure"></a>Bevinden de gegevens zich achter de FHIR-Api's die zijn opgeslagen in azure?

Ja, de gegevens worden opgeslagen in beheerde data bases in Azure. De Azure API voor FHIR biedt geen directe toegang tot het onderliggende gegevens archief.

## <a name="what-identity-provider-do-you-support"></a>Welke id-provider ondersteunt u?

We ondersteunen momenteel Microsoft Azure Active Directory als id-provider.

## <a name="what-fhir-version-do-you-support"></a>Welke FHIR-versie wordt ondersteund?

We ondersteunen versies 4.0.0 en 3.0.1 op zowel de Azure API voor FHIR (PaaS) als de FHIR-server voor Azure (open source).

Zie [ondersteunde functies](fhir-features-supported.md)voor meer informatie. Meer informatie over wat er is gewijzigd tussen versies in de [versie geschiedenis van HL7 FHIR](https://hl7.org/fhir/R4/history.html).

## <a name="whats-the-difference-between-the-open-source-microsoft-fhir-server-for-azure-and-the-azure-api-for-fhir"></a>Wat is het verschil tussen de open-source micro soft FHIR-server voor Azure en de Azure API voor FHIR?

De Azure API voor FHIR is een gehoste en beheerde versie van de open-source micro soft FHIR-server voor Azure. In de beheerde service biedt micro soft alle onderhoud en updates. 

Wanneer u FHIR server voor Azure gebruikt, hebt u rechtstreeks toegang tot de onderliggende services. Maar u bent ook verantwoordelijk voor het onderhouden en bijwerken van de server en alle vereiste nalevings werkzaamheden als u PHI-gegevens opslaat.

Vanuit een ontwikkelings standpunt wordt elke functie eerst geïmplementeerd op de open-source micro soft FHIR-server voor Azure. Zodra de app is gevalideerd in open source, wordt deze vrijgegeven aan de PaaS Azure API voor de FHIR-oplossing. De tijd tussen de release in open source en PaaS is afhankelijk van de complexiteit van de functie en andere prioriteiten voor het schema. 

## <a name="what-is-smart-on-fhir"></a>Wat is slim op FHIR?

SLIMME (Vervang bare medische toepassingen en herbruikbare technologie) op FHIR is een set open specificaties voor het integreren van partner toepassingen met FHIR-servers en andere Health IT-systemen, zoals elektronische status records en informatie uitwisselingen van de status. Door een slimme on FHIR-toepassing te maken, kunt u ervoor zorgen dat uw toepassing toegankelijk is en kan worden gebruikt door een verdwaald van verschillende systemen.
Verificatie en Azure API voor FHIR. Ga voor meer informatie naar Smart [Health](https://smarthealthit.org/).

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u enkele veelgestelde vragen over de Azure API voor FHIR gelezen. Meer informatie over de ondersteunde functies in FHIR server voor Azure:
 
>[!div class="nextstepaction"]
>[Ondersteunde FHIR-functies](fhir-features-supported.md)