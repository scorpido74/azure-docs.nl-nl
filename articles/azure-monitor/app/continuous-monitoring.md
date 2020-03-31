---
title: Continue bewaking van uw DevOps-releasepijplijn met Azure Pipelines en Azure Application Insights | Microsoft Documenten
description: Geeft instructies om snel continue monitoring in te stellen met Application Insights
ms.topic: conceptual
ms.date: 07/16/2019
ms.openlocfilehash: e565101218b975ef2bd29b8a32a4aa1bf4300b6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655392"
---
# <a name="add-continuous-monitoring-to-your-release-pipeline"></a>Continue bewaking toevoegen aan uw releasepijplijn

Azure Pipelines integreert met Azure Application Insights om continue bewaking van uw DevOps-releasepijplijn mogelijk te maken gedurende de hele levenscyclus van softwareontwikkeling. 

Met continue bewaking kunnen releasepijplijnen bewakingsgegevens van Application Insights en andere Azure-bronnen bevatten. Wanneer de releasepijplijn een Application Insights-waarschuwing detecteert, kan de pijplijn de implementatie poortof terugdraaien totdat de waarschuwing is opgelost. Als alle controles verlopen, kunnen implementaties automatisch doorgaan van de test tot de productie, zonder handmatige interventie. 

## <a name="configure-continuous-monitoring"></a>Continue bewaking configureren

1. Selecteer in [Azure DevOps](https://dev.azure.com)een organisatie en project.
   
1. Selecteer in het linkermenu van de projectpagina de optie > **Pijplijnreleases**. **Pipelines** 
   
1. Plaats de pijl naast **Nieuw** en selecteer **Nieuwe releasepijplijn**. Als u nog geen pijplijn hebt, selecteert u **Nieuwe pijplijn** op de pagina die wordt weergegeven.
   
1. Zoek **in** het deelvenster Een sjabloon selecteren naar en selecteer **Azure App Service-implementatie met continue bewaking**en selecteer Vervolgens **Toepassen**. 

   ![Releasepijplijn voor nieuwe Azure Pipelines](media/continuous-monitoring/001.png)

1. Selecteer **in het** vak Fase 1 de hyperlink naar **Fasetaken weergeven.**

   ![Fasetaken weergeven](media/continuous-monitoring/002.png)

1. Voer in **het configuratievenster van fase 1** de volgende velden in: 

    | Parameter        | Waarde |
   | ------------- |:-----|
   | **Fasenaam**      | Geef een artiestennaam op of laat deze achter in **fase 1**. |
   | **Azure-abonnement** | Devalerplaats en selecteer het gekoppelde Azure-abonnement dat u wilt gebruiken.|
   | **App-type** | Val neer en selecteer het type app. |
   | **Naam app-service** | Voer de naam van uw Azure App Service in. |
   | **Naam resourcegroep voor toepassingsinzichten**    | Laat de brongroep die u wilt gebruiken en selecteer de brongroep. |
   | **Naam van application Insights-bron** | Devallijst en selecteer de resource Application Insights voor de door u geselecteerde resourcegroep.

1. Als u de pijplijn wilt opslaan met standaardinstellingen voor waarschuwingsregels, selecteert **u Opslaan** rechtsboven in het azure devOps-venster. Voer een beschrijvende opmerking in en selecteer **OK**.

## <a name="modify-alert-rules"></a>Waarschuwingsregels wijzigen

De **Azure App Service-implementatie met sjabloon voor continue bewaking** heeft vier waarschuwingsregels: **Beschikbaarheid,** **Mislukte aanvragen,** **serverresponstijd**en **serveruitzonderingen**. U meer regels toevoegen of de regelinstellingen wijzigen om aan uw serviceniveaubehoeften te voldoen. 

Ga als u de instellingen van de waarschuwingsregel wijzigen:

1. Selecteer in het linkerdeelvenster van de releasepijplijnpagina de optie **Waarschuwingen voor toepassingsinzichten configureren**.

1. Selecteer in het deelvenster **Azure Monitor Alerts** de ellips **...** naast **Waarschuwingsregels**.
   
1. Selecteer in het dialoogvenster **Waarschuwingsregels** het vervolgkeuzesymbool naast een waarschuwingsregel, zoals **Beschikbaarheid**. 
   
1. Wijzig de **drempelwaarde** en andere instellingen om aan uw vereisten te voldoen.
   
   ![Waarschuwing wijzigen](media/continuous-monitoring/003.png)
   
1. Selecteer **OK**en selecteer **Opslaan** rechtsboven in het azure devOps-venster. Voer een beschrijvende opmerking in en selecteer **OK**.

## <a name="add-deployment-conditions"></a>Implementatievoorwaarden toevoegen

Wanneer u implementatiepoorten toevoegt aan uw releasepijplijn, voorkomt een waarschuwing die de ingestelde drempelwaarden overschrijdt, ongewenste releasepromotie. Zodra u de waarschuwing hebt opgelost, kan de implementatie automatisch worden gestart.

Ga als u implementatiepoorten toevoegen:

1. Selecteer op de hoofdpijplijnpagina onder **Stadia**het symbool **Voorimplementatievoorwaarden** of **voorwaarden na implementatie,** afhankelijk van welke fase een continue bewakingspoort nodig heeft.
   
   ![Voorwaarden voor implementatie](media/continuous-monitoring/004.png)
   
1. Stel **gates** in het configuratievenster **voor de randvan de implementatie** in op **Ingeschakeld**.
   
1. Selecteer naast **Implementatiepoorten**de optie **Toevoegen**.
   
1. Selecteer **Query Azure Monitor-waarschuwingen** in het vervolgkeuzemenu. Met deze optie hebt u toegang tot zowel Azure Monitor- als Application Insights-waarschuwingen.
   
   ![Azure-monitorwaarschuwingen voor Query](media/continuous-monitoring/005.png)
   
1. Voer **onder Evaluatieopties**de gewenste waarden in voor instellingen zoals De tijd tussen de herevaluatie van poorten en De **time-out** **waarna gates mislukken.** 

## <a name="view-release-logs"></a>Releaselogboeken weergeven

U het gedrag van de implementatiepoort en andere releasestappen zien in de releaselogboeken. Ga als u de logboeken openen:

1. Selecteer **Releases** in het linkermenu van de pijplijnpagina. 
   
1. Selecteer een release. 
   
1. Selecteer **onder Stadia**een willekeurig stadium om een releasesamenvatting weer te geven. 
   
1. Als u logboeken wilt weergeven, selecteert **u Logboeken weergeven** in het releaseoverzicht, selecteert u de hyperlink **Geslaagd** of **Mislukt** in een willekeurig stadium of zweeft u over een fase en selecteert **u Logboeken**. 
   
   ![Releaselogboeken weergeven](media/continuous-monitoring/006.png)

## <a name="next-steps"></a>Volgende stappen

Zie de [azure pipelines-documentatie](https://docs.microsoft.com/azure/devops/pipelines)voor meer informatie over Azure Pipelines.
