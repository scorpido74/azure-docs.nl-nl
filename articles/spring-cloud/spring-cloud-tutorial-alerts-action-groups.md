---
title: 'Zelf studie: Azure lente-cloud resources bewaken met behulp van waarschuwingen en actie groepen | Microsoft Docs'
description: Meer informatie over het gebruik van Lente-Cloud waarschuwingen.
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 11/18/2019
ms.openlocfilehash: 2be21b20c394ae8505ad18f2c411db7aab06215f
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74694003"
---
# <a name="tutorial-monitor-spring-cloud-resources-using-alerts-and-action-groups"></a>Zelf studie: lente-cloud resources bewaken met behulp van waarschuwingen en actie groepen

Azure lente-Cloud waarschuwingen bieden ondersteuning voor het bewaken van resources op basis van voor waarden zoals de beschik bare opslag, de frequentie van aanvragen of het gegevens gebruik. Er wordt een melding verzonden wanneer tarieven of voor waarden voldoen aan de gedefinieerde specificaties.

Er zijn twee stappen voor het instellen van een waarschuwings pijplijn: 
1. Stel een actie groep in met de acties die moeten worden uitgevoerd wanneer een waarschuwing wordt geactiveerd, zoals e-mail, SMS, Runbook of webhook. Actie groepen kunnen opnieuw worden gebruikt tussen verschillende waarschuwingen.
2. Waarschuwings regels instellen. De regels binden metrische patronen met de actie groepen op basis van de doel resource, metrische gegevens, condities, tijd aggregatie, enzovoort.

## <a name="prerequisites"></a>Vereisten
Naast de vereisten voor Azure lente, is deze zelf studie afhankelijk van de volgende resources.

* Een geïmplementeerd Azure veer Cloud-instantie.  Volg onze [Snelstartgids](spring-cloud-quickstart-launch-app-cli.md) om aan de slag te gaan.

* Een Azure-resource die u wilt bewaken, bijvoorbeeld de data base die in dit artikel wordt geïmplementeerd: [lente gegevens gebruiken Apache Cassandra-API met Azure Cosmos DB](https://docs.microsoft.com/azure/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
 
De volgende procedures worden beide **actie groepen** en **waarschuwingen** geïnitialiseerd, te beginnen met de optie **waarschuwingen** in het navigatie deel venster links van een lente-Cloud exemplaar. (De procedure kan ook worden gestart op de **overzichts** pagina van de monitor van de Azure Portal.) 

Navigeren van een resource groep naar het lente-Cloud exemplaar. Selecteer **waarschuwingen** in het linkerdeel venster en selecteer vervolgens **acties beheren**:

![Pagina met de resource groep van de scherm opname Portal](media/alerts-action-groups/action-1-a.png)

## <a name="set-up-action-group"></a>Actie groep instellen

Selecteer **+ actie groep toevoegen**om te beginnen met de procedure voor het initialiseren van een nieuwe **actie groep**.

![Scherm opname Portal actie groep toevoegen](media/alerts-action-groups/action-1.png)

Op de pagina **actie groep toevoegen** :

 1. Geef een naam en **korte naam**op voor de **actie groep** .

 1. Geef het **abonnement** en de **resource groep**op.

 1. **Actie naam**opgeven.

 1. Selecteer een **actie type**.  Hiermee opent u een ander deel venster aan de rechter kant om de actie te definiëren die tijdens de activering wordt uitgevoerd.

 1. Definieer de actie met behulp van de opties in het rechterdeel venster.  In dit geval wordt e-mail melding gebruikt.

 1. Klik op **OK** in het rechterdeel venster.

 1. Klik op **OK** in het dialoog venster **actie groep toevoegen** . 

  ![Actie voor het definiëren van de scherm opname Portal](media/alerts-action-groups/action-2.png)

## <a name="set-up-alert"></a>Waarschuwing instellen 

In de vorige stappen is een **actie groep** gemaakt die gebruikmaakt van e-mail. U kunt ook telefoon meldingen, webhooks, Azure functions, enzovoort gebruiken.  

Als u een **waarschuwing**wilt configureren, gaat u terug naar de pagina **waarschuwingen** en klikt u op **waarschuwings regels beheren**.

  ![Scherm opname Portal-waarschuwing definiëren](media/alerts-action-groups/alerts-2.png)

1. Selecteer de **resource** voor de waarschuwing.

1. Klik op **+ nieuwe waarschuwings regel**.

  ![Nieuwe waarschuwings regel voor de scherm opname Portal](media/alerts-action-groups/alerts-3.png)

1. Geef op de pagina **regel maken** de **resource**, **voor waarde**en **acties**op.  Selecteer in het deel venster **acties** de eerder gedefinieerde **actie groep**.

1. Geef onder **WAARSCHUWINGS Details**de naam van de waarschuwings regel op.

1. Klik op **waarschuwings regel maken**.

  ![Nieuwe waarschuwings regel voor de scherm opname Portal](media/alerts-action-groups/alerts-4.png)

Controleer of de nieuwe waarschuwings regel is ingeschakeld.

  ![Nieuwe waarschuwings regel voor de scherm opname Portal](media/alerts-action-groups/alerts-5.png)

## <a name="next-steps"></a>Volgende stappen
* [Actie groepen maken en beheren in de Azure Portal](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)
* [Gedrag van SMS-waarschuwingen in actie groepen](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-sms-behavior)
* [Zelf studie: gedistribueerde tracering met Azure lente-Cloud gebruiken](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing)
