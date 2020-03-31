---
title: Richtlijnen voor noodherstel voor Azure Data Lake Analytics
description: Meer informatie over het plannen van noodherstel voor uw Azure Data Lake Analytics-accounts.
services: data-lake-analytics
author: MikeRys
ms.author: mrys
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: f9b22e6b806f76189134ec63c83d48f48bf95587
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73889763"
---
# <a name="disaster-recovery-guidance-for-azure-data-lake-analytics"></a>Richtlijnen voor noodherstel voor Azure Data Lake Analytics

Azure Data Lake Analytics is een service op aanvraag voor het uitvoeren van analysetaken, om de analyse van big data te vereenvoudigen. U hoeft geen hardware te implementeren, configureren en optimaliseren, maar kunt query's schrijven om uw data te transformeren en waardevolle inzichten te verkrijgen. De analyseservice kan taken van elke schaal onmiddellijk verwerken, door de hoeveelheid rekenkracht die u nodig hebt aan te passen. U betaalt alleen voor de tijd die nodig is voor het uitvoeren van uw taak, wat het gebruik van deze service zeer rendabel maakt. In dit artikel vindt u richtlijnen voor het beschermen van uw taken tegen zeldzame regiostoringen of onbedoelde verwijderingen.

## <a name="disaster-recovery-guidance"></a>Richtlijnen voor herstel na noodgevallen

Wanneer u Azure Data Lake Analytics gebruikt, is het van cruciaal belang dat u uw eigen noodherstelplan voorbereidt. Dit artikel helpt u bij het maken van een noodherstelplan. Er zijn extra bronnen waarmee u uw eigen plan maken:
- [Herstel na fouten en noodgevallen voor Azure-toepassingen](/azure/architecture/reliability/disaster-recovery)
- [Technische richtlijnen voor flexibiliteit van Azure](/azure/architecture/checklist/resiliency-per-service)

## <a name="best-practices-and-scenario-guidance"></a>Aanbevolen procedures en scenariorichtlijnen

U een terugkerende U-SQL-taak uitvoeren in een ADLA-account in een regio die U-SQL-tabellen en ongestructureerde gegevens leest en schrijft.  Bereid je voor op een ramp door de volgende stappen te nemen:

1. Maak ADLA- en ADLS-accounts in de secundaire regio die tijdens een storing worden gebruikt.

   > [!NOTE]
   > Aangezien accountnamen wereldwijd uniek zijn, gebruikt u een consistent naamgevingsschema dat aangeeft welk account secundair is.

2. Voor ongestructureerde gegevens, referentie [Disaster recovery guidance voor gegevens in Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

3. Maak kopieën van de metagegevensartefacten zoals databases, tabellen, functies met tabelwaarde en samenstellingen voor gestructureerde gegevens die zijn opgeslagen in ADLA-tabellen en databases. U moet deze artefacten periodiek opnieuw synchroniseren wanneer er wijzigingen in de productie plaatsvinden. Nieuw ingevoegde gegevens moeten bijvoorbeeld worden gerepliceerd naar het secundaire gebied door de gegevens te kopiëren en in de secundaire tabel in te voegen.

   > [!NOTE]
   > Deze objectnamen zijn ingesteld op het secundaire account en zijn niet wereldwijd uniek, zodat ze dezelfde namen kunnen hebben als in het primaire productieaccount.

Tijdens een storing moet u uw scripts bijwerken, zodat de invoerpaden naar het secundaire eindpunt wijzen. Vervolgens dienen de gebruikers hun vacatures in bij het ADLA-account in de secundaire regio. De uitvoer van de taak wordt vervolgens naar het ADLA- en ADLS-account in de secundaire regio geschreven.

## <a name="next-steps"></a>Volgende stappen

[Richtlijnen voor noodherstel voor gegevens in Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)
