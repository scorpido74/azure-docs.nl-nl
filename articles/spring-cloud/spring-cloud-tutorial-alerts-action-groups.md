---
title: 'Zelf studie: Azure lente-cloud resources bewaken met behulp van waarschuwingen en actie groepen | Microsoft Docs'
description: Meer informatie over het gebruik van Lente-Cloud waarschuwingen.
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 12/29/2019
ms.openlocfilehash: cf7e075244a83190b83e629ef300a4903b114a6d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77920073"
---
# <a name="tutorial-monitor-spring-cloud-resources-using-alerts-and-action-groups"></a>Zelf studie: lente-cloud resources bewaken met behulp van waarschuwingen en actie groepen

Azure lente-Cloud waarschuwingen bieden ondersteuning voor het bewaken van resources op basis van voor waarden zoals de beschik bare opslag, de frequentie van aanvragen of het gegevens gebruik. Er wordt een melding verzonden wanneer tarieven of voor waarden voldoen aan de gedefinieerde specificaties.

Er zijn twee stappen voor het instellen van een waarschuwings pijplijn: 
1. Stel een actie groep in met de acties die moeten worden uitgevoerd wanneer een waarschuwing wordt geactiveerd, zoals e-mail, SMS, Runbook of webhook. Actie groepen kunnen opnieuw worden gebruikt tussen verschillende waarschuwingen.
2. Waarschuwings regels instellen. De regels binden metrische patronen met de actie groepen op basis van de doel resource, metrische gegevens, condities, tijd aggregatie, enzovoort.

## <a name="prerequisites"></a>Vereisten
Naast de vereisten voor Azure lente, is deze zelf studie afhankelijk van de volgende resources.

* Een geïmplementeerd Azure veer Cloud-instantie.  Volg onze [Snelstartgids](spring-cloud-quickstart-launch-app-cli.md) om aan de slag te gaan.

* Een Azure-resource die moet worden bewaakt. In dit voor beeld wordt een lente-Cloud instantie bewaakt.
 
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

1. Geef op de pagina **regel maken** de **resource**op.

1. De instelling **voor waarde** biedt veel opties voor het bewaken van uw **lente-Cloud** resources.  Klik op **toevoegen** om het deel venster **signalerings logica configureren** te openen.

1. Selecteer een voor waarde. In dit voor beeld wordt het **CPU-gebruiks percentage**van het systeem gebruikt.

   ![Nieuwe waarschuwings regel voor de scherm opname Portal](media/alerts-action-groups/alerts-3-1.png)

1. Schuif omlaag in het deel venster **signaal logica configureren** om de **drempel waarde** in te stellen die moet worden bewaakt.

   ![Nieuwe waarschuwings regel voor de scherm opname Portal](media/alerts-action-groups/alerts-3-2.png)

1. Klik op **Gereed**.

Zie Opties voor metrische gegevens van de [gebruikers Portal](spring-cloud-concept-metrics.md#user-metrics-options)voor meer informatie over de beschik bare voor waarden om te controleren.

 Klik onder **acties**op **actie groep selecteren**. Selecteer in het deel venster **acties** de eerder gedefinieerde **actie groep**.

   ![Nieuwe waarschuwings regel voor de scherm opname Portal](media/alerts-action-groups/alerts-3-3.png) 

1. Schuif omlaag en geef onder **WAARSCHUWINGS Details**de naam van de waarschuwings regel op.

1. Stel de **Ernst**in.

1. Klik op **waarschuwings regel maken**.

   ![Nieuwe waarschuwings regel voor de scherm opname Portal](media/alerts-action-groups/alerts-3-4.png)

Controleer of de nieuwe waarschuwings regel is ingeschakeld.

   ![Nieuwe waarschuwings regel voor de scherm opname Portal](media/alerts-action-groups/alerts-4.png)

Een regel kan ook worden gemaakt met behulp van de pagina **metrische gegevens** :

   ![Nieuwe waarschuwings regel voor de scherm opname Portal](media/alerts-action-groups/alerts-5.png)

## <a name="next-steps"></a>Volgende stappen
* [Opties voor metrische gegevens van gebruikers Portal](spring-cloud-concept-metrics.md#user-metrics-options)
* [Actie groepen maken en beheren in de Azure Portal](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)
* [Gedrag van SMS-waarschuwingen in actie groepen](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-sms-behavior)
* [Zelf studie: gedistribueerde tracering met Azure lente-Cloud gebruiken](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing)
