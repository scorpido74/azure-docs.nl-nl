---
title: Azure-beveiligings beheer-reactie op incidenten
description: Reactie op incidenten voor beveiligings controle
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: b027a668403f47e9ffb824179ae54b89cded7a0c
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75564229"
---
# <a name="security-control-incident-response"></a>Beveiligings beheer: respons op incidenten

Beveilig de gegevens van de organisatie en de reputatie ervan door een infra structuur voor incidenten te ontwikkelen en implementeren (zoals plannen, gedefinieerde rollen, training, communicatie, beheer toezicht) om snel een aanval te detecteren en vervolgens effectief met de schade, uitroeiing van de aanwezigheid van de aanvaller en het herstellen van de integriteit van het netwerk en de systemen.

## <a name="101-create-an-incident-response-guide"></a>10,1: een hand leiding voor reactie op incidenten maken

| Azure-ID | CIS-Id's | Verantwoordelijkheid |
|--|--|--|
| 10.1 | 19,1, 19,2, 19,3 | Klant |

Maak een antwoord gids voor incidenten voor uw organisatie. Zorg ervoor dat er schriftelijke incidenten abonnementen zijn die alle werk rollen definiëren, evenals fasen van incident handling/management van detectie tot een beoordeling van het incident.

Werk stroom automatisering configureren in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Richt lijnen voor het bouwen van uw eigen beveiligings incident antwoord proces:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Micro soft Security Response Center anatomie van een incident:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

De klant kan ook gebruikmaken van de hand leiding voor de verwerking van het computer beveiligings incident van het NIST om hulp te bieden bij het maken van een eigen reactie plan voor incidenten:

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: een beoordelings procedure voor incidenten en prioriteits procedures maken

| Azure-ID | CIS-Id's | Verantwoordelijkheid |
|--|--|--|
| 10.2 | 19,8 | Klant |

Security Center wijst aan elke waarschuwing een Ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of het analyse programma dat wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau dat er schadelijke bedoelingen zijn achter de activiteit die tot de waarschuwing heeft geleid.

Daarnaast kunt u ook duidelijk abonnementen markeren (voor bijvoorbeeld productie, niet-productie) en maak een naamgevings systeem om Azure-resources duidelijk te identificeren en te categoriseren.

## <a name="103-test-security-response-procedures"></a>10,3: procedures voor beveiligings antwoorden testen

| Azure-ID | CIS-Id's | Verantwoordelijkheid |
|--|--|--|
| 10,3 | 19 | Klant |

Voer oefeningen uit om de respons mogelijkheden van uw systeem op een gewone uitgebracht te testen. Identificeer zwakke punten en tussen ruimten en wijzig zo nodig het schema.

Raadpleeg de publicatie van het NIST: hand leiding voor het testen, trainen en uitoefenen van Program Ma's voor IT-plannen en-mogelijkheden:

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-nbspfor-security-incidents"></a>10,4: contact gegevens van het beveiligings incident opgeven en waarschuwings meldingen &nbsp;voor beveiligings incidenten configureren

| Azure-ID | CIS-Id's | Verantwoordelijkheid |
|--|--|--|
| 10,4 | 19,5 | Klant |

Contact gegevens van beveiligings incidenten worden door micro soft gebruikt om contact met u op te nemen als het micro soft Security Response Center (MSRC) detecteert dat de gegevens van de klant zijn geopend door een onrecht matige of niet-gemachtigde partij.  Bekijk incidenten na het feit om te controleren of de problemen zijn opgelost.

De Azure Security Center Security-contact persoon instellen:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

## <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: beveiligings waarschuwingen opnemen in uw reactie systeem van uw incident

| Azure-ID | CIS-Id's | Verantwoordelijkheid |
|--|--|--|
| 10.5 | 19,6 | Klant |

Exporteer uw Azure Security Center waarschuwingen en aanbevelingen met behulp van de functie continue export. Met doorlopend exporteren kunt u waarschuwingen en aanbevelingen hand matig of op een doorlopende manier exporteren. U kunt de Azure Security Center Data Connector gebruiken om de Sentinel van waarschuwingen te streamen.

Continue export configureren:

https://docs.microsoft.com/azure/security-center/continuous-export

Waarschuwingen streamen naar Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

## <a name="106-automate-the-response-to-security-alerts"></a>10,6: de reactie op beveiligings waarschuwingen automatiseren

| Azure-ID | CIS-Id's | Verantwoordelijkheid |
|--|--|--|
| 10,6 | 19 | Klant |

Gebruik de functie werk stroom automatisering in Azure Security Center om automatisch reacties te activeren via &quot;Logic Apps&quot; over beveiligings waarschuwingen en aanbevelingen.

Werk stroom automatisering en Logic Apps configureren:

https://docs.microsoft.com/azure/security-center/workflow-automation

## <a name="next-steps"></a>Volgende stappen

Zie het volgende beveiligings beheer: [Indringings tests en rode team oefeningen](security-control-penetration-tests-red-team-exercises.md)