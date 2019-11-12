---
title: Aangepaste analytische regels maken voor het detecteren van verdachte dreigingen met Azure Sentinel | Microsoft Docs
description: Gebruik deze zelf studie voor meer informatie over het maken van aangepaste analyse regels voor het detecteren van verdachte dreigingen met Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 9c4ba09c7e3eca4482ed56b0b337124aeec5b838
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73928255"
---
# <a name="tutorial-create-custom-analytic-rules-to-detect-suspicious-threats"></a>Zelf studie: aangepaste analyse regels maken om verdachte bedreigingen te detecteren

Nadat u [uw gegevens bronnen](quickstart-onboard.md) aan Azure Sentinel hebt gekoppeld, kunt u aangepaste regels maken waarmee kan worden gezocht naar specifieke criteria in uw omgeving en incidenten worden gegenereerd wanneer de criteria worden vergeleken, zodat u ze kunt onderzoeken. Deze zelf studie helpt u bij het maken van aangepaste regels voor het detecteren van bedreigingen met Azure Sentinel.

Deze zelf studie helpt u bij het detecteren van bedreigingen met Azure Sentinel.
> [!div class="checklist"]
> * Analytische regels maken
> * Bedreigings reacties automatiseren

## <a name="create-custom-analytic-rules"></a>Aangepaste analytische regels maken

U kunt aangepaste analyse regels maken om u te helpen bij het zoeken naar de typen bedreigingen en afwijkingen die in uw omgeving worden verdacht. De regel zorgt ervoor dat u meteen op de hoogte wordt gebracht, zodat u de bedreigingen kunt sorteren, onderzoeken en oplossen.

1. Selecteer in de Azure Portal onder Azure Sentinel **Analytics**.

1. Selecteer in de bovenste menu balk **+ maken** en selecteer **geplande query regel**. Hiermee opent u de **wizard aangepaste regel maken**.

    ![Geplande query maken](media/tutorial-detect-threats-custom/create-scheduled-query.png)

1. Geef op het tabblad **Algemeen** een beschrijvende naam en een beschrijving op. Stel de **Ernst** van de waarschuwing in als dat nodig is. Wanneer u de regel maakt, kunt u deze inschakelen, waardoor deze onmiddellijk wordt uitgevoerd nadat u deze hebt gemaakt. Als u deze functie als uitgeschakeld maakt, wordt de regel toegevoegd aan het tabblad **actieve regels** en kunt u deze weer inschakelen wanneer u deze nodig hebt.

    ![Beginnen met het maken van een aangepaste analytische regel](media/tutorial-detect-threats-custom/general-tab.png)

1. Op het tabblad **instellingen** kunt u rechtstreeks een query schrijven of de query in log Analytics maken en deze vervolgens in het **Zoek query** veld plakken. Wanneer u uw query wijzigt en configureert, simuleert Azure Sentinel de query resultaten in het **voorbeeld** venster met resultaten aan de rechter kant. Zo kunt u inzicht krijgen in hoeveel gegevens er worden gegenereerd over een bepaald tijds interval voor de waarschuwing die u maakt. De hoeveelheid is afhankelijk van de waarde die u hebt ingesteld voor **query uitvoeren elke** en **het opzoeken van gegevens van de laatste**. Als u ziet dat uw waarschuwing gemiddeld waarschuwingen zou activeren, kunt u het aantal resultaten hoger instellen, zodat het boven uw gemiddelde basis lijn komt.

   ![Query maken in de Azure-Sentinel](media/tutorial-detect-threats-custom/settings-tab.png)

   Hier volgt een voorbeeld query waarmee u wordt gewaarschuwd wanneer een afwijkend aantal resources wordt gemaakt in azure activity.

    `AzureActivity
    \| where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
    \| where ActivityStatus == "Succeeded"
    \| make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller`

   > [!NOTE]
   > De lengte van de query moet tussen 1 en 10.000 tekens lang zijn en mag geen ' Search \*' of ' Union \*' bevatten.

    1. Stel onder **query planning**de volgende para meters in:

        1.  Stel de **query run elke** in om de **frequentie** in te stellen voor hoe vaak de query wordt uitgevoerd, zo vaak elke vijf minuten of als niet-regel matig als één keer per dag.

        1.  Stel **opzoek gegevens van de laatste** in om het tijd venster in te stellen voor het aantal gegevens dat door de query wordt uitgevoerd. Dit kan bijvoorbeeld elk uur, ongeveer 60 minuten aan gegevens, worden uitgevoerd.

        1. U kunt Azure Sentinel zo instellen dat **de query niet meer wordt uitgevoerd nadat de waarschuwing is gegenereerd** als u alleen een waarschuwing wilt ontvangen wanneer deze zich voordoet. U moet het venster instellen waarin de query moet worden gestopt, tot 24 uur.

    1. Definieer de voor waarden voor waarschuwings Triggers onder **waarschuwings trigger**. Onder **entiteits toewijzing**kunt u de kolommen in uw query toewijzen aan entiteits velden die worden herkend door Azure Sentinel. Wijs voor elk veld de relevante kolom toe aan de query die u hebt gemaakt in Log Analytics naar het juiste entiteits veld. Elke entiteit bevat meerdere velden, zoals SID en GUID. U kunt de entiteit toewijzen op basis van alle velden, niet alleen de entiteit op het hoogste niveau.

1.  Op het tabblad **reacties automatiseren** selecteert u de playbooks die u automatisch wilt uitvoeren wanneer een waarschuwing wordt gegenereerd door de aangepaste regel. Zie [reageren op bedreigingen](tutorial-respond-threats-playbook.md)voor meer informatie over het maken en automatiseren van playbooks.

    ![Reactie op bedreigingen in azure Sentinel automatiseren](media/tutorial-detect-threats-custom/response-automation-custom.png)

1. Selecteer **controleren** om alle instellingen voor de nieuwe waarschuwings regel te controleren en selecteer vervolgens **maken om uw waarschuwings regel te initialiseren**.

   ![Uw aangepaste query controleren](media/tutorial-detect-threats-custom/review-tab.png)

1.  Nadat de waarschuwing is gemaakt, wordt er een aangepaste regel toegevoegd aan de tabel onder **actieve regels**. In deze lijst kunt u elke regel inschakelen, uitschakelen of verwijderen.

1.  Als u de resultaten van de waarschuwings regels die u maakt, wilt weer geven, gaat u naar de pagina **incidenten** , waar u de [incidenten](tutorial-investigate-cases.md)kunt sorteren, onderzoeken en de bedreigingen herstelt.


> [!NOTE]
> Waarschuwingen die zijn gegenereerd in azure Sentinel zijn beschikbaar via [Microsoft Graph beveiliging](https://aka.ms/securitygraphdocs). Zie de [documentatie over Microsoft Graph Security Alerts](https://aka.ms/graphsecurityreferencebetadocs)(Engelstalig) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u aan de slag gaat met het detecteren van bedreigingen met behulp van Azure Sentinel.

Als u wilt weten hoe u uw reacties op bedreigingen kunt automatiseren, [stelt u automatische reacties op dreigingen in azure Sentinel in](tutorial-respond-threats-playbook.md).

