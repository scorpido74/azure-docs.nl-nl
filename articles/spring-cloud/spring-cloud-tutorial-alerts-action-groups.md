---
title: 'Zelfstudie: Azure Spring Cloud-bronnen controleren met waarschuwingen en actiegroepen | Microsoft Documenten'
description: Meer informatie over het gebruik van Voorjaarswolk-waarschuwingen.
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 12/29/2019
ms.openlocfilehash: cf7e075244a83190b83e629ef300a4903b114a6d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77920073"
---
# <a name="tutorial-monitor-spring-cloud-resources-using-alerts-and-action-groups"></a>Zelfstudie: Cloudbronnen voor de lente controleren met waarschuwingen en actiegroepen

Azure Spring Cloud-waarschuwingen ondersteunen monitoringbronnen op basis van voorwaarden zoals beschikbare opslag, snelheid van aanvragen of gegevensgebruik. Een waarschuwing stuurt een melding wanneer tarieven of voorwaarden voldoen aan de gedefinieerde specificaties.

Er zijn twee stappen om een waarschuwingspijplijn in te stellen: 
1. Stel een actiegroep in met de acties die moeten worden uitgevoerd wanneer een waarschuwing wordt geactiveerd, zoals e-mail, sms, runbook of Webhook. Actiegroepen kunnen worden hergebruikt tussen verschillende waarschuwingen.
2. Waarschuwingsregels instellen. De regels binden metrische patronen met de actiegroepen op basis van doelbron, metrische, voorwaarde, tijdaggregatie, enz.

## <a name="prerequisites"></a>Vereisten
Naast de Azure Spring-vereisten is deze zelfstudie afhankelijk van de volgende resources.

* Een geïmplementeerde Azure Spring Cloud-instantie.  Volg onze [quickstart](spring-cloud-quickstart-launch-app-cli.md) om aan de slag te gaan.

* Een Azure-bron die u moet controleren. In dit voorbeeld wordt een instantie Spring Cloud bewaakt.
 
De volgende procedures initialiseren zowel **actiegroep** als **Waarschuwing** vanaf de optie **Waarschuwingen** in het linkernavigatiedeelvenster van een instantie Voor de voorjaarswolk. (De procedure kan ook beginnen vanaf de pagina **Monitoroverzicht** van de Azure-portal.) 

Navigeer vanuit een resourcegroep naar uw instantie Spring Cloud. Selecteer **Waarschuwingen** in het linkerdeelvenster en selecteer **Acties beheren:**

![Pagina met een groep van portalbron](media/alerts-action-groups/action-1-a.png)

## <a name="set-up-action-group"></a>Actiegroep instellen

Als u wilt beginnen met de procedure om een nieuwe **actiegroep**te initialiseren, selecteert u **+ Actiegroep toevoegen**.

![Schermafbeelding van de actiegroep Toevoegen van de portal](media/alerts-action-groups/action-1.png)

Ga op de pagina **Actiegroep toevoegen:**

 1. Geef een **groepsnaam** en **korte naam op.**

 1. **Abonnementsgroep** en **Resourcegroep opgeven**.

 1. **Actienaam**opgeven .

 1. Selecteer **Actietype**.  Hiermee wordt een ander deelvenster aan de rechterkant geopend om de actie te definiëren die bij activering wordt uitgevoerd.

 1. Definieer de actie met de opties in het rechterdeelvenster.  Deze aanvraag maakt gebruik van e-mailmelding.

 1. Klik op **OK** in het juiste actiedeelvenster.

 1. Klik op **OK** in het dialoogvenster **Actiegroep toevoegen.** 

  ![Schermafbeelding van de actie Definiëren van een schermafbeelding van portal](media/alerts-action-groups/action-2.png)

## <a name="set-up-alert"></a>Waarschuwing instellen 

In de vorige stappen is een **actiegroep gemaakt** die e-mail gebruikt. U ook telefoonmeldingen, webhooks, Azure-functies, enz.  

Als u een **waarschuwing wilt**configureren, navigeert u terug naar de pagina **Waarschuwingen** en klikt u op **Waarschuwingsregels beheren**.

  ![Schermafbeelding van waarschuwing voor het definiëren van een portal](media/alerts-action-groups/alerts-2.png)

1. Selecteer de **bron** voor de waarschuwing.

1. Klik **op + Nieuwe waarschuwingsregel**.

   ![Schermafbeelding van nieuwe waarschuwingsregel van Portal](media/alerts-action-groups/alerts-3.png)

1. Geef op de pagina **Regel maken** de **RESOURCE**op .

1. De **INSTELLING VOORWAARDE** biedt veel opties voor het bewaken van uw Spring **Cloud-bronnen.**  Klik **op Toevoegen** om het deelvenster Signaallogica configureren te **openen.**

1. Selecteer een voorwaarde. In dit voorbeeld wordt **gebruikgenomen van het systeem-CPU-gebruikspercentage**.

   ![Schermafbeelding van nieuwe waarschuwingsregel van Portal](media/alerts-action-groups/alerts-3-1.png)

1. Schuif omlaag in het deelvenster **Signaallogica configureren** om de **drempelwaarde** in te stellen die moet worden gecontroleerd.

   ![Schermafbeelding van nieuwe waarschuwingsregel van Portal](media/alerts-action-groups/alerts-3-2.png)

1. Klik op **Gereed**.

Zie Opties voor [gebruikersportalstatistieken](spring-cloud-concept-metrics.md#user-metrics-options)voor meer informatie over de beschikbare voorwaarden om te controleren.

 Klik **onder ACTIES**op Actiegroep **selecteren**. Selecteer in het deelvenster **HANDELINGEN** de eerder gedefinieerde **actiegroep**.

   ![Schermafbeelding van nieuwe waarschuwingsregel van Portal](media/alerts-action-groups/alerts-3-3.png) 

1. Scroll naar beneden en geef onder **WAARSCHUWINGSGEGEVENS**de naam van de waarschuwingsregel.

1. Stel de **ernst in.**

1. Klik **op Waarschuwingsregel maken**.

   ![Schermafbeelding van nieuwe waarschuwingsregel van Portal](media/alerts-action-groups/alerts-3-4.png)

Controleer of de nieuwe waarschuwingsregel is ingeschakeld.

   ![Schermafbeelding van nieuwe waarschuwingsregel van Portal](media/alerts-action-groups/alerts-4.png)

Er kan ook een regel worden gemaakt met de pagina **Statistieken:**

   ![Schermafbeelding van nieuwe waarschuwingsregel van Portal](media/alerts-action-groups/alerts-5.png)

## <a name="next-steps"></a>Volgende stappen
* [Opties voor statistieken van gebruikersportalen](spring-cloud-concept-metrics.md#user-metrics-options)
* [Actiegroepen maken en beheren in de Azure-portal](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)
* [Sms-waarschuwingsgedrag in actiegroepen](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-sms-behavior)
* [Zelfstudie: Gedistribueerde tracering gebruiken met Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing)
