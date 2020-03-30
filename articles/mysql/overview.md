---
title: Overzicht - Azure Database voor MySQL
description: Meer informatie over de Azure Database for MySQL-service, een relationele databaseservice in de Microsoft-cloud op basis van de MySQL Community Edition.
author: ajlam
ms.service: mysql
ms.author: andrela
ms.custom: mvc
ms.topic: overview
ms.date: 3/18/2020
ms.openlocfilehash: 8f49811ad0d40c70933d32227cfb17a5144b857a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80067826"
---
# <a name="what-is-azure-database-for-mysql"></a>Wat is Azure Database for MySQL?

Azure Database for MySQL is een relationele databaseservice in de Microsoft-cloud op basis van de [MySQL Community Edition](https://www.mysql.com/products/community/) (beschikbaar onder de GPLv2-licentie) databaseengine, versies 5.6, 5.7 en 8.0. Azure Database voor MySQL levert:

- Ingebouwde hoge beschikbaarheid zonder extra kosten.
- Voorspelbare prestaties, tegen all-inclusive prijzen op basis van betalen per gebruik.
- Schalen naar behoefte, binnen enkele seconden.
- Ter bescherming van gevoelige niet-actieve en actieve gegevens.
- Automatische back-ups en herstel naar een tijdstip tot 35 dagen geleden.
- Beveiliging en naleving van bedrijfskwaliteit.

Deze mogelijkheden vereisen nauwelijks beheer, en alles wordt zonder extra kosten geleverd. Dankzij deze mogelijkheden kunt u zich richten op het sneller ontwikkelen en op de markt brengen van apps, in plaats van kostbare tijd en middelen in te zetten voor het beheer van virtuele machines en infrastructuur. U kunt bovendien de open-source hulpprogramma's en het platform van uw keuze blijven gebruiken om uw toepassing verder te ontwikkelen, en deze te leveren met de snelheid en efficiëntie die uw bedrijf vereist zonder dat u nieuwe vaardigheden hoeft te leren.

![Azure Database voor MySQL-conceptueel diagram](media/overview/1-azure-db-for-mysql-conceptual-diagram.png)

Dit artikel is een inleiding tot Azure Database voor MySQL-kernconcepten en -functies met betrekking tot prestaties, schaalbaarheid en beheerbaarheid, met koppelingen om details te verkennen. Zie deze Quick Starts om snel aan de slag te gaan:

- [Een Azure-database voor MySQL-server maken met behulp van Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md)
- [Een Azure-database voor MySQL-server maken met behulp van Azure CLI](quickstart-create-mysql-server-database-using-azure-cli.md)

Zie de volgende artikelen voor een reeks Azure CLI-voorbeelden:

- [Azure CLI-voorbeelden voor Azure Database for MySQL](sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-within-seconds"></a>Binnen een paar seconden prestaties en schaal aanpassen
De Azure Database for MySQL-service biedt verschillende servicelagen: Basic, General Purpose en Memory Optimized. Elke laag biedt verschillende prestaties en mogelijkheden voor lichte tot zware workloads van databases. U kunt uw eerste app op een kleine database bouwen voor een paar euro met maand en vervolgens de schaal ervan aanpassen om aan de vereisten van uw oplossing te voldoen. Doordat de schaalbaarheid dynamisch is, kan uw database op een transparante manier reageren op snel veranderende resourcevereisten. U betaalt alleen voor de resources die u nodig hebt op het moment dat u ze nodig hebt. Zie  [Prijscategorieën](concepts-service-tiers.md) voor meer informatie.

## <a name="monitoring-and-alerting"></a>Bewaking en waarschuwingen
Hoe bepaalt u wanneer u omhoog of omlaag moet schalen? U maakt gebruik van de ingebouwde functies voor prestatiebewaking en waarschuwingen, in combinatie met de prestatiebeoordelingen op basis van vCores. Met behulp van deze tools kunt u snel beoordelen wat de impact is van het aanpassen van de schaal van vCores op basis van uw huidige of verwachte prestatiebehoeften. Zie [Waarschuwingen](howto-alert-on-metric.md) voor meer details.

## <a name="keep-your-app-and-business-running"></a>Continuïteit van uw app en uw bedrijf
De toonaangevende serviceovereenkomst (SLA) van Azure met 99,99% beschikbaarheid dankzij een wereldwijd netwerk van door Microsoft beheerde datacenters, zorgt ervoor dat uw app continu (24 uur per dag, 7 dagen per week) beschikbaar blijft. Bij elke Azure Database voor MySQL-server profiteert u van de ingebouwde beveiliging, fouttolerantie en gegevensbescherming die u anders zou moeten kopen, ontwerpen, bouwen en beheren. Met Azure Database voor MySQL u point-in-time restore gebruiken om een server tot een eerdere status te herstellen, al 35 dagen terug.

## <a name="secure-your-data"></a>Uw gegevens beveiligen
Azure-databaseservices hebben een traditie van gegevensbeveiliging die Azure Database voor MySQL handhaaft, met functies die de toegang beperken, gegevens in rust en in beweging beschermen en u helpen activiteiten te controleren. Bezoek het [Vertrouwenscentrum van Azure](https://www.microsoft.com/trustcenter/security) voor informatie over de beveiliging van het Azure-platform. Zie het [beveiligingsoverzicht](concepts-security.md)voor meer informatie over Azure Database voor MySQL-beveiligingsfuncties.

## <a name="contacts"></a>Contactpersonen
Voor vragen of suggesties die u hebt over het werken met Azure Database voor MySQL, stuurt u een e-mail naar de Azure Database for MySQL Team[ @Ask (Azure DB for MySQL).](mailto:AskAzureDBforMySQL@service.microsoft.com) Dit e-mailadres is geen alias voor technische ondersteuning.

Overweeg ook de volgende aanspreekpunten, indien van toepassing:

- Als u contact wilt opnemen met Azure-ondersteuning, kunt u een [ticket indienen vanuit Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Als u een probleem met uw account wilt oplossen, kunt u een [ondersteuningsaanvraag](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) indienen in Azure Portal.
- Als u feedback wilt geven of een nieuwe functie wilt aanvragen, maakt u een vermelding via [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).

## <a name="next-steps"></a>Volgende stappen
Nu u een inleiding tot Azure Database voor MySQL hebt gelezen en de vraag hebt beantwoord "Wat is Azure Database voor MySQL?" je bent klaar om:

- Zie de pagina met prijzen voor kostenvergelijkingen en calculators. [Prijzen](https://azure.microsoft.com/pricing/details/mysql/)
- Ga aan de slag met het maken van uw eerste server. [Een Azure-database voor MySQL-server maken met behulp van Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md)
- Bouw je eerste app met je voorkeurstaal: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md)
