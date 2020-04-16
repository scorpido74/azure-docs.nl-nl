---
title: Azure Security Control - Incident Response
description: Reactie op Azure Security Control Incident
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 993793d21e6253188dfc199d8701cbe117503517
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408419"
---
# <a name="security-control-incident-response"></a>Beveiligingscontrole: reactie bij incidenten

Bescherm de informatie van de organisatie, evenals haar reputatie, door het ontwikkelen en implementeren van een incident response infrastructuur (bijvoorbeeld plannen, gedefinieerde rollen, training, communicatie, management toezicht) voor het snel ontdekken van een aanval en vervolgens effectief in perken de schade, het uitroeien van de aanwezigheid van de aanvaller, en het herstel van de integriteit van het netwerk en systemen.

## <a name="101-create-an-incident-response-guide"></a>10.1: Een gids voor incidentrespons maken

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 10.1 | 19.1, 19.2, 19.3 | Klant |

Bouw een incidentresponseguide voor uw organisatie. Zorg ervoor dat er geschreven incidentresponseplannen zijn die alle rollen van personeel definiëren, evenals fasen van incidentafhandeling/beheer, van detectie tot beoordeling na incidenten.  

- [Richtlijnen voor het bouwen van uw eigen beveiligingsincidentresponsproces](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [De anatomie van een incident door het Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Maak gebruik van NIST's Computer Security Incident Handling Guide om te helpen bij het maken van uw eigen incident response plan](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Een incidentscore- en prioriteringsprocedure maken

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 10.2 | 19.8 | Klant |

Security Center wijst een ernst toe aan elke waarschuwing om u te helpen prioriteiten te stellen welke waarschuwingen eerst moeten worden onderzocht. De ernst is gebaseerd op hoe zeker Security Center is in de bevinding of de analytische gebruikt om de waarschuwing en het betrouwbaarheidsniveau dat er kwaadaardige bedoelingen achter de activiteit die leidde tot de waarschuwing. 

Bovendien, duidelijk markeren abonnementen (voor bijvoorbeeld. productie, niet-prod) met behulp van tags en maak een naamgevingssysteem om Azure-bronnen duidelijk te identificeren en te categoriseren, met name die welke gevoelige gegevens verwerken.  Het is uw verantwoordelijkheid om prioriteit te geven aan het herstel van waarschuwingen op basis van de kritiek van de Azure-resources en -omgeving waar het incident zich heeft voorgedaan.

- [Beveiligingswaarschuwingen in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

- [Tags gebruiken om uw Azure-bronnen te ordenen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="103-test-security-response-procedures"></a>10.3: Procedures voor beveiligingsrespons testen

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 10.3 | 19 | Klant |

Voer oefeningen uit om de incidentresponsemogelijkheden van uw systemen te testen op een regelmatige cadans om uw Azure-bronnen te beschermen. Identificeer zwakke punten en hiaten en herzie het plan indien nodig.

- [NIST's publicatie - Gids voor test-, trainings- en trainingsprogramma's voor IT-plannen en -mogelijkheden](https://csrc.nist.gov/publications/detail/sp/800-84/final)

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Contactgegevens beveiligingsincidenten verstrekken en waarschuwingsmeldingen configureren voor beveiligingsincidenten

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 10.4 | 19.5 | Klant |

Contactgegevens van beveiligingsincidenten worden door Microsoft gebruikt om contact met u op te nemen als het Microsoft Security Response Center (MSRC) ontdekt dat uw gegevens zijn geopend door een onwettige of onbevoegde partij. Bekijk incidenten na het feit om ervoor te zorgen dat problemen worden opgelost.

- [De beveiligingscontactpersoon van Azure Security Center instellen](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

## <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Beveiligingswaarschuwingen opnemen in uw incidentresponsesysteem

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 10.5 | 19.6 | Klant |

Exporteer uw Azure Security Center-waarschuwingen en -aanbevelingen met de functie Continue export om risico's voor Azure-resources te identificeren. Met Continue export u waarschuwingen en aanbevelingen handmatig of doorlopend exporteren. U de Azure Security Center-gegevensconnector gebruiken om de waarschuwingen naar Azure Sentinel te streamen.

- [Continue export configureren](https://docs.microsoft.com/azure/security-center/continuous-export)

- [Waarschuwingen streamen naar Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

## <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatiseer de reactie op beveiligingswaarschuwingen

| Azure-id | CIS-iD's | Verantwoordelijkheid |
|--|--|--|
| 10.6 | 19 | Klant |

Gebruik de functie Werkstroomautomatisering in Azure Security Center om automatisch reacties te activeren via 'Logic Apps' over beveiligingswaarschuwingen en aanbevelingen om uw Azure-bronnen te beschermen.

- [Workflowautomatisering en logische apps configureren](https://docs.microsoft.com/azure/security-center/workflow-automation)


## <a name="next-steps"></a>Volgende stappen

- Bekijk de volgende security control: [penetratietests en rode teamoefeningen](security-control-penetration-tests-red-team-exercises.md)