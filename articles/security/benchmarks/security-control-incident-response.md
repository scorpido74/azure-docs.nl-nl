---
title: Azure Security Control - Incident Response
description: Reactie op beveiligingscontrole-incidenten
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: fb3560aa2d3fbf48ab63c4da4d3a8d69cb677209
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934416"
---
# <a name="security-control-incident-response"></a>Beveiligingscontrole: reactie bij incidenten

Bescherm de informatie van de organisatie, evenals haar reputatie, door het ontwikkelen en implementeren van een incident response infrastructuur (bijvoorbeeld plannen, gedefinieerde rollen, training, communicatie, management toezicht) voor het snel ontdekken van een aanval en vervolgens effectief het beperken van de schade, het uitroeien van de aanwezigheid van de aanvaller, en het herstel van de integriteit van het netwerk en systemen.

## <a name="101-create-an-incident-response-guide"></a>10.1: Een gids voor incidentrespons maken

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 10.1 | 19.1, 19.2, 19.3 | Klant |

Bouw een incidentresponseguide voor uw organisatie. Zorg ervoor dat er geschreven incidentresponseplannen zijn die alle rollen van personeel definiëren, evenals fasen van incidentafhandeling/beheer, van detectie tot beoordeling na incidenten.

Werkstroomautomatiseringen configureren binnen Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Richtlijnen voor het bouwen van uw eigen beveiligingsincidentresponsproces:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

De anatomie van een incident door het Microsoft Security Response Center:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

De klant kan ook gebruikmaken van NIST's Computer Security Incident Handling Guide om te helpen bij het maken van hun eigen incident response plan:

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Een incidentscore- en prioriteringsprocedure maken

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 10.2 | 19.8 | Klant |

Security Center wijst een ernst toe aan elke waarschuwing om u te helpen prioriteiten te stellen welke waarschuwingen eerst moeten worden onderzocht. De ernst is gebaseerd op hoe zeker Security Center is in de bevinding of de analytische gebruikt om de waarschuwing en het betrouwbaarheidsniveau dat er kwaadaardige bedoelingen achter de activiteit die leidde tot de waarschuwing.

Bovendien, duidelijk markeren abonnementen (voor bijvoorbeeld. productie, niet-prod) en maak een naamgevingssysteem om Azure-resources duidelijk te identificeren en te categoriseren.

## <a name="103-test-security-response-procedures"></a>10.3: Procedures voor beveiligingsrespons testen

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 10.3 | 19 | Klant |

Voer oefeningen uit om de incidentresponsemogelijkheden van uw systemen op een regelmatige cadans te testen. Identificeer zwakke punten en hiaten en herzie het plan indien nodig.

Raadpleeg de publicatie van NIST: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities:

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Contactgegevens beveiligingsincidenten verstrekken en waarschuwingsmeldingen configureren voor beveiligingsincidenten

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 10.4 | 19.5 | Klant |

Contactgegevens van beveiligingsincidenten worden door Microsoft gebruikt om contact met u op te nemen als het Microsoft Security Response Center (MSRC) ontdekt dat de gegevens van de klant zijn geopend door een onwettige of onbevoegde partij.  Bekijk incidenten na het feit om ervoor te zorgen dat problemen worden opgelost.

De beveiligingscontactpersoon azure security center instellen:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

## <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Beveiligingswaarschuwingen opnemen in uw incidentresponsesysteem

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 10.5 | 19.6 | Klant |

Exporteer uw Azure Security Center-waarschuwingen en -aanbevelingen met de functie Continue exporteren. Met Continue export u waarschuwingen en aanbevelingen handmatig of doorlopend exporteren. U de Azure Security Center-gegevensconnector gebruiken om de waarschuwingen sentinel te streamen.

Continue export configureren:

https://docs.microsoft.com/azure/security-center/continuous-export

Waarschuwingen streamen naar Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

## <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatiseer de reactie op beveiligingswaarschuwingen

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 10.6 | 19 | Klant |

Gebruik de functie Workflowautomatisering in Azure Security &quot;Center&quot; om automatisch reacties via Logic Apps te activeren op beveiligingswaarschuwingen en aanbevelingen.

Workflowautomatisering en logische apps configureren:

https://docs.microsoft.com/azure/security-center/workflow-automation

## <a name="next-steps"></a>Volgende stappen

Bekijk de volgende beveiligingscontrole: [penetratietests en oefeningen van het Rode Team](security-control-penetration-tests-red-team-exercises.md)