---
title: Overzicht - Azure Database for MySQL
description: Meer informatie over de Azure Database for MySQL-service, een relationele databaseservice in de Microsoft Cloud die is gebaseerd op de MySQL Community Edition.
author: ajlam
ms.service: mysql
ms.author: andrela
ms.custom: mvc
ms.topic: overview
ms.date: 3/18/2020
ms.openlocfilehash: 37bc99d9f83f185a5372fd45634351987b85e20b
ms.sourcegitcommit: e2b36c60a53904ecf3b99b3f1d36be00fbde24fb
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/24/2020
ms.locfileid: "88763657"
---
# <a name="what-is-azure-database-for-mysql"></a>Wat is Azure Database for MySQL?

Azure Database for MySQL is een relationele databaseservice in de Microsoft Cloud die is gebaseerd op de [MySQL Community Edition](https://www.mysql.com/products/community/)-database-engine (beschikbaar onder de GPLv2-licentie, versies 5.6, 5.7 en 8.0). Azure Database for MySQL biedt het volgende:

- Ingebouwde hoge beschikbaarheid zonder extra kosten.
- Voorspelbare prestaties, tegen all-inclusive prijzen op basis van betalen per gebruik.
- Schalen naar behoefte, binnen enkele seconden.
- Ter bescherming van gevoelige niet-actieve en actieve gegevens.
- Automatische back-ups en herstel naar een tijdstip tot 35 dagen geleden.
- Beveiliging en naleving van bedrijfskwaliteit.

Deze mogelijkheden vereisen nauwelijks beheer, en alles wordt zonder extra kosten geleverd. Dankzij deze mogelijkheden kunt u zich richten op het sneller ontwikkelen en op de markt brengen van apps, in plaats van kostbare tijd en middelen in te zetten voor het beheer van virtuele machines en infrastructuur. U kunt bovendien de open-source hulpprogramma's en het platform van uw keuze blijven gebruiken om uw toepassing verder te ontwikkelen, en deze te leveren met de snelheid en efficiëntie die uw bedrijf vereist zonder dat u nieuwe vaardigheden hoeft te leren.

![Conceptueel diagram van Azure Database for MySQL](media/overview/1-azure-db-for-mysql-conceptual-diagram.png)

Dit artikel bevat een inleiding tot de belangrijkste concepten en functies van Azure Database for MySQL met betrekking tot prestaties, schaalbaarheid en beheerbaarheid, met koppelingen naar meer gedetailleerde informatie. Zie deze Quick Starts om snel aan de slag te gaan:

- [Een Azure-database voor een MySQL-server maken met behulp van Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md)
- [Een Azure-database voor een MySQL-server maken met behulp van Azure CLI](quickstart-create-mysql-server-database-using-azure-cli.md)

Zie de volgende artikelen voor een reeks Azure CLI-voorbeelden:

- [Azure CLI-voorbeelden voor Azure Database for MySQL](sample-scripts-azure-cli.md)

## <a name="automated-patching"></a>Automatisch patchen
De service voert automatische patches uit van de onderliggende hardware, het besturingssysteem en de database-engine. De patches bestaan uit beveiligings- en software-updates voor de onderliggende hardware, het besturingssysteem en de database-engine. Voor de MySQL-engine worden kleine versie-upgrades automatisch uitgevoerd. Deze zijn onderdeel van de patchrelease. Wanneer de community een kleine versie uitbrengt, wordt deze automatisch geïntegreerd als onderdeel van de testcyclus voor de service. Het testen van de kleine versie wordt op een aantal canonieke werkbelastingen voor MySQL uitgevoerd. De kleine versies van de MySQL-engine die worden uitgebracht, worden op betrouwbaarheid (geen vastlopers), beschikbaarheid, beveiliging en prestaties geëvalueerd. Niet elke kleine versie wordt voor productie uitgebracht in de service, maar wordt wel geëvalueerd op basis van de ernst van de opgeloste fouten en de nieuwe incrementele waarde. De reden hiervoor is een juiste balans te vinden tussen een nieuwe incrementele waarde en het minimaliseren van het aantal variabelen in het systeem om stabiliteit te krijgen. Gebruikers hoeven zelf geen actie te ondernemen en er zijn geen configuratie-instellingen vereist voor patches. De frequentie waarbij patches worden uitgevoerd, wordt beheerd door de service op basis van de ernst van de nettolading. Over het algemeen volgt de service de maandelijkse releaseplanning, als onderdeel van de continue integratie en releases. Gebruikers kunnen zich abonneren op [meldingen voor gepland onderhoud](concepts-monitoring.md). Zij ontvangen dan 72 uur voorafgaand aan het geplande onderhoud een melding.

## <a name="adjust-performance-and-scale-within-seconds"></a>Binnen een paar seconden prestaties en schaal aanpassen
De Azure Database for MySQL-service biedt verschillende servicelagen: Basic, Algemeen gebruik en Geoptimaliseerd voor geheugen. Elke laag biedt verschillende prestaties en mogelijkheden voor lichte tot zware workloads van databases. U kunt uw eerste app op een kleine database bouwen voor een paar euro met maand en vervolgens de schaal ervan aanpassen om aan de vereisten van uw oplossing te voldoen. Doordat de schaalbaarheid dynamisch is, kan uw database op een transparante manier reageren op snel veranderende resourcevereisten. U betaalt alleen voor de resources die u nodig hebt op het moment dat u ze nodig hebt. Zie  [Prijscategorieën](concepts-service-tiers.md) voor meer informatie.

## <a name="monitoring-and-alerting"></a>Bewaking en waarschuwingen
Hoe bepaalt u wanneer u omhoog of omlaag moet schalen? U maakt gebruik van de ingebouwde functies voor prestatiebewaking en waarschuwingen, in combinatie met de prestatiebeoordelingen op basis van vCores. Met behulp van deze tools kunt u snel beoordelen wat de impact is van het aanpassen van de schaal van vCores op basis van uw huidige of verwachte prestatiebehoeften. Zie [Waarschuwingen](howto-alert-on-metric.md) voor meer details.

## <a name="keep-your-app-and-business-running"></a>Continuïteit van uw app en uw bedrijf
De toonaangevende serviceovereenkomst (SLA) van Azure met 99,99% beschikbaarheid dankzij een wereldwijd netwerk van door Microsoft beheerde datacenters, zorgt ervoor dat uw app continu (24 uur per dag, 7 dagen per week) beschikbaar blijft. Elke Azure Database for MySQL-server is voorzien van ingebouwde beveiliging, fouttolerantie en gegevensbeveiliging die u anders zelf zou moeten kopen of ontwerpen, ontwikkelen en beheren. Met Azure Database for MySQL kunt u met behulp van herstelpunten een server terugzetten naar een eerdere toestand, tot 35 dagen geleden.

## <a name="secure-your-data"></a>Uw gegevens beveiligen
Azure-databaseservices hebben traditiegetrouw een uitstekende gegevensbeveiliging. Deze traditie wordt voortgezet in Azure Database for MySQL, met functies voor toegangsbeperking, het beveiligen van niet-actieve en actieve gegevens en activiteitsbewaking. Bezoek het [Vertrouwenscentrum van Azure](https://www.microsoft.com/trustcenter/security) voor informatie over de beveiliging van het Azure-platform. Zie het [beveiligingsoverzicht](concepts-security.md) voor meer informatie over de beveiligingsfuncties van Azure Database for MySQL.

## <a name="contacts"></a>Contactpersonen
Voor eventuele vragen of suggesties over het werken met Azure Database for MySQL stuurt u een e-mailbericht naar het Azure Database for MySQL-team ([@AskAzure DB for MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com)). Dit e-mailadres is geen alias voor technische ondersteuning.

Overweeg ook de volgende aanspreekpunten, indien van toepassing:

- Als u contact wilt opnemen met Azure-ondersteuning, kunt u een [ticket indienen vanuit Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Als u een probleem met uw account wilt oplossen, kunt u een [ondersteuningsaanvraag](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) indienen in Azure Portal.
- Als u feedback wilt geven of een nieuwe functie wilt aanvragen, maakt u een vermelding via [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).

## <a name="next-steps"></a>Volgende stappen
Nu u de inleiding tot Azure Database for MySQL hebt gelezen en weet wat Azure Database for MySQL is, bent u klaar voor de volgende stappen:

- Zie de pagina met prijzen voor kostenvergelijkingen en calculators. [Prijzen](https://azure.microsoft.com/pricing/details/mysql/)
- Ga aan de slag met het maken van uw eerste server. [Een Azure-database voor een MySQL-server maken met behulp van Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md)
- Bouw uw eerste app in de taal van uw voorkeur:
  - [Python](./connect-python.md)
  - [Node.JS](./connect-nodejs.md)
  - [Java](./connect-java.md)
  - [Ruby](./connect-ruby.md)
  - [PHP](./connect-php.md)
  - [.NET (C#)](./connect-csharp.md)
  - [Go](./connect-go.md)
