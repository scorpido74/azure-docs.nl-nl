---
title: Data base-instellingen configureren in azure API voor FHIR
description: In dit artikel wordt beschreven hoe u de data base-instellingen in azure API kunt configureren voor FHIR
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/15/2019
ms.author: matjazl
ms.openlocfilehash: adc6fdf144927d10f811a00aa33f244cfdc25042
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "84871762"
---
# <a name="configure-database-settings"></a>Data base-instellingen configureren 

De Azure-API voor FHIR gebruikt data base om de gegevens op te slaan. De prestaties van de onderliggende Data Base zijn afhankelijk van het aantal aanvraag eenheden (RU) dat is geselecteerd tijdens het inrichten van services of in de data base-instellingen nadat de service is ingericht.

Azure API voor FHIR leent het concept van RUs van Cosmos DB (Zie [aanvraag eenheden in azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/request-units)) bij het instellen van de prestaties van de onderliggende data base. 

De door voer moet worden ingericht om ervoor te zorgen dat er te allen tijde voldoende systeem bronnen beschikbaar zijn voor uw data base. Hoeveel RUs u nodig hebt voor uw toepassing is afhankelijk van de bewerkingen die u uitvoert. Bewerkingen kunnen variëren van eenvoudige Lees-en schrijf bewerkingen naar complexere query's. 

> [!NOTE]
> Als verschillende bewerkingen een verschillend aantal RU gebruiken, retour neren we het werkelijke aantal dat is gebruikt in elke API-aanroep in de reactie header. Op deze manier kunt u het aantal door uw toepassing verbruikte RUs-profielen.

## <a name="update-throughput"></a>Door Voer bijwerken
Als u deze instelling in de Azure Portal wilt wijzigen, gaat u naar de Azure API voor FHIR en opent u de Blade data base. Wijzig vervolgens de ingerichte door voer naar de gewenste waarde, afhankelijk van uw prestatie behoeften. U kunt de waarde wijzigen tot een maximum van 10.000 RU/s. Als u een hogere waarde nodig hebt, neemt u contact op met de ondersteuning van Azure.

> [!NOTE] 
> Hoe hoger de waarde, hoe hoger de Azure-API voor FHIR-door Voer en hogere kosten voor de service.

![Cosmos DB configuratie](media/database/database-settings.png)

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u de RUs voor Azure API voor FHIR bijwerkt. Implementeer daarna een volledig beheerde Azure-API voor FHIR:
 
>[!div class="nextstepaction"]
>[Azure API for FHIR implementeren](fhir-paas-portal-quickstart.md)