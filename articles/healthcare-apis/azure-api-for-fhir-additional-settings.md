---
title: Aanvullende instellingen voor de Azure-API voor FHIR
description: Overzicht van de aanvullende instellingen die u kunt instellen voor Azure API voor FHIR
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.reviewer: mihansen
ms.author: cavoeg
author: CaitlinV39
ms.date: 11/22/2019
ms.openlocfilehash: 896d5bafd879ff3ba09bd5b8922cde4cd8345689
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "84871950"
---
# <a name="additional-settings-for-azure-api-for-fhir"></a>Aanvullende instellingen voor de Azure-API voor FHIR

In deze hand leiding gaan we de aanvullende instellingen bekijken die u mogelijk wilt instellen in uw Azure API voor FHIR. Er zijn nog meer pagina's die meer details kunnen bekijken.

## <a name="configure-database-settings"></a>Data base-instellingen configureren

De Azure-API voor FHIR gebruikt data base om de gegevens op te slaan. De prestaties van de onderliggende Data Base zijn afhankelijk van het aantal aanvraag eenheden (RU) dat is geselecteerd tijdens het inrichten van services of in de data base-instellingen nadat de service is ingericht.

De door voer moet worden ingericht om ervoor te zorgen dat er te allen tijde voldoende systeem bronnen beschikbaar zijn voor uw data base. Hoeveel RUs u nodig hebt voor uw toepassing is afhankelijk van de bewerkingen die u uitvoert. Bewerkingen kunnen variëren van eenvoudige Lees-en schrijf bewerkingen naar complexere query's.

Zie [Configure Data Base Settings](configure-database.md)(Engelstalig) voor meer informatie over het wijzigen van de standaard instellingen.

## <a name="access-control"></a>Toegangsbeheer

Met de Azure-API voor FHIR kunnen geautoriseerde gebruikers alleen toegang krijgen tot de FHIR-API. U kunt geautoriseerde gebruikers configureren via twee verschillende mechanismen. De primaire en aanbevolen manier om toegangs beheer te configureren, maakt gebruik [van Azure Role Based Access Control (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/), die toegankelijk is via de Blade **toegangs beheer (IAM)** . Azure RBAC werkt alleen als u de toegang tot het gegevens vlak wilt beveiligen met behulp van de Azure Active Directory Tenant die aan uw abonnement is gekoppeld. Als u een andere Tenant wilt gebruiken, biedt de Azure API voor FHIR een lokaal mechanisme voor het toegangs beheer van FHIR-gegevens. De configuratie opties zijn niet zo uitgebreid wanneer het lokale RBAC-mechanisme wordt gebruikt. Kies een van de volgende opties voor meer informatie:

* [Azure RBAC voor FHIR-gegevens vlak](configure-azure-rbac.md). Dit is de voorkeurs optie wanneer u de Azure Active Directory Tenant gebruikt die aan uw abonnement is gekoppeld.
* [Toegangs beheer voor lokaal FHIR-gegevens](configure-local-rbac.md). Gebruik deze optie alleen wanneer u een externe Azure Active Directory Tenant moet gebruiken voor het toegangs beheer voor gegevens vlak. 

## <a name="enable-diagnostic-logging"></a>registratie in het diagnoselogboek inschakelen
Mogelijk wilt u diagnostische logboek registratie inschakelen als onderdeel van uw installatie, zodat u uw service kunt bewaken en nauw keurige rapportage voor nalevings doeleinden hebt. Zie voor meer informatie over het instellen van diagnostische logboek registratie onze [hand leiding](enable-diagnostic-logging.md) voor het instellen van diagnostische logboek registratie en enkele voorbeeld query's. 

## <a name="use-custom-headers-to-add-data-to-audit-logs"></a>Aangepaste kopteksten gebruiken om gegevens toe te voegen aan audit logboeken
In de Azure-API voor FHIR wilt u mogelijk aanvullende informatie toevoegen aan de logboeken die afkomstig zijn van het aanroepende systeem. Als u deze informatie wilt opnemen, kunt u aangepaste kopteksten gebruiken.

U kunt aangepaste kopteksten gebruiken om verschillende soorten informatie vast te leggen. Bijvoorbeeld:

* Identiteits-of autorisatie-informatie
* Oorsprong van de oproepende functie
* Oorspronkelijke organisatie
* Details van client systeem (elektronische gezondheids record, patiënten-Portal)

Als u deze gegevens wilt toevoegen aan uw audit logboeken, raadpleegt u de hand leiding [aangepaste HTTP-headers gebruiken om gegevens toe te voegen aan audit logboeken](use-custom-headers.md) .

## <a name="next-steps"></a>Volgende stappen

In deze hand leiding kunt u aanvullende instellingen voor de Azure API voor FHIR instellen.

Bekijk vervolgens de reeks zelf studies voor het maken van een webtoepassing die FHIR gegevens leest.

>[!div class="nextstepaction"]
>[JavaScript-toepassing implementeren](tutorial-web-app-fhir-server.md)