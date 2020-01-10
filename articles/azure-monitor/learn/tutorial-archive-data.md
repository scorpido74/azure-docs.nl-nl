---
title: Metrische gegevens en logboekgegevens van Azure archiveren met behulp van Azure Storage
description: Logboek-en metrische gegevens die door Azure-resources zijn gegenereerd, archiveren in een opslag account.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: tutorial
ms.date: 09/25/2017
ms.author: johnkem
ms.custom: mvc
ms.subservice: metrics
ms.openlocfilehash: 86f042a451583759aa15e886b3261700335a5285
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75748548"
---
# <a name="archive-azure-metric-and-log-data-using-azure-storage"></a>Metrische gegevens en logboekgegevens van Azure archiveren met behulp van Azure Storage

Verschillende lagen van uw Azure-omgeving genereren logboekgegevens en metrische gegevens die in een Azure Storage-account kunnen worden gearchiveerd. U kunt hier bijvoorbeeld gebruik van maken als u bewakingsgegevens waarvan de bewaarperiode is verstreken langere tijd wilt bewaren in een voordelig, niet-doorzoekbaar archief. 

- De metrische gegevens van het Azure Monitor-platform worden gedurende 93 dagen bewaard. 
- Resourcelogboeken met diagnostische gegevens worden alleen weergegeven als ze worden gerouteerd naar Log Analytics - hier worden ze minimaal 30 dagen bewaard. De definitieve bewaarperiode kan worden ingesteld. 
- Vermeldingen in het activiteitenlogboek worden gedurende 90 dagen bewaard.  

In deze zelfstudie beschrijven we hoe u uw Azure-omgeving configureert voor het archiveren van gegevens in een opslagaccount.

> [!div class="checklist"]
> * Een opslagaccount maken om bewakingsgegevens op te slaan
> * Abonnementlogboeken naar het opslagaccount doorsturen
> * Resourcegegevens naar het opslagaccount doorsturen
> * Gegevens van virtuele machines (als gast-OS) naar het opslagaccount doorsturen
> * De bewakingsgegevens in het opslagaccount weergeven
> * Uw resources opschonen

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure Portal](https://portal.azure.com/).

## <a name="create-a-storage-account"></a>Maak een opslagaccount

U moet eerst een opslagaccount instellen waarin de bewakingsgegevens worden gearchiveerd. Daarvoor [volgt u deze stappen](../../storage/common/storage-quickstart-create-account.md).

## <a name="route-subscription-logs-to-the-storage-account"></a>Abonnementlogboeken naar het opslagaccount doorsturen

U bent nu klaar om uw Azure-omgeving in te stellen zodat bewakingsgegevens worden doorgestuurd naar een opslagaccount. We gaan eerst gegevens op abonnementsniveau (in het Azure-activiteitenlogboek) configureren om deze door te sturen naar het opslagaccount. Het [**Azure-activiteitenlogboek**](../../azure-monitor/platform/platform-logs-overview.md) bevat een geschiedenis van gebeurtenissen op abonnementsniveau in Azure. U kunt ernaar zoeken in Azure Portal om te bepalen *wie* *welke* resources heeft gemaakt, bijgewerkt of verwijderd en *wanneer* dat is gebeurd.

1. Klik op de knop **Controle** in de linkernavigatielijst en klik op **Activiteitenlogboek**.

   ![Sectie Activiteitenlogboek](media/tutorial-archive-data/activity-log-home.png)

2. Klik in de weergegeven sectie Activiteitenlogboek op de knop **Exporteren**.

3. Schakel in de sectie **Activiteitenlogboek exporteren** het selectievakje voor **Exporteren naar een opslagaccount** in en klik op **Een opslagaccount selecteren**.

   ![Activiteitenlogboek exporteren](media/tutorial-archive-data/activity-log-export.png)

4. Gebruik in de weergegeven sectie de vervolgkeuzelijst **Opslagaccount** om de naam te selecteren van het opslagaccount dat u in de voorgaande stap, **Een opslagaccount maken**, hebt gemaakt. Klik vervolgens op **OK**.

   ![Een opslagaccount kiezen](media/tutorial-archive-data/activity-log-storage.png)

5. Stel de schuifregelaar **Bewaarperiode (dagen)** in op 30. Met deze schuifregelaar stelt u in hoeveel dagen u de bewakingsgegevens wilt bewaren in het opslagaccount. Gegevens die ouder zijn dan het aantal opgegeven dagen, worden automatisch verwijderd. Bij een bewaarperiode van nul dagen worden de gegevens voor onbepaalde tijd opgeslagen.

6. Klik op **Opslaan** en sluit deze sectie.

Bewakingsgegevens uit uw abonnement worden nu doorgestuurd naar het opslagaccount.

## <a name="route-resource-data-to-the-storage-account"></a>Resourcegegevens doorsturen naar het opslagaccount

Nu gaan we gegevens op resource niveau configureren (resource metrieken en resource Logboeken) om te worden doorgestuurd naar het opslag account door **Diagnostische instellingen voor bronnen**in te stellen.

1. Klik op de knop **Controle** in de linkernavigatielijst en klik op **Diagnostische instellingen**. Hier kunt u een lijst zien van alle resources in uw abonnement die bewakingsgegevens via Azure Monitor genereren. Als er geen resources in deze lijst staan, kunt u [een logische app maken](../../logic-apps/quickstart-create-first-logic-app-workflow.md) voordat u verdergaat. Zodoende beschikt u over een resource waarvoor u een diagnostische instelling kunt configureren.

2. Klik op een resource in de lijst en klik op **Diagnostische gegevens inschakelen**.

   ![Diagnostische gegevens inschakelen](media/tutorial-archive-data/diagnostic-settings-turn-on.png)

   Als er al een instelling is geconfigureerd, ziet u in plaats daarvan de bestaande instellingen, samen met de knop **Diagnostische instelling toevoegen**. Klik op deze knop.

   De diagnostische instelling van een resource is een definitie van *welke* bewakingsgegevens moeten worden doorgestuurd vanuit een bepaalde resource en *waar* die bewakingsgegevens naartoe moeten worden gestuurd.

3. Geef in de weergegeven sectie de instelling een **naam** en schakel het selectievakje in voor **Archiveren naar een opslagaccount**.

   ![Sectie Diagnostische instellingen](media/tutorial-archive-data/diagnostic-settings-home.png)

4. Klik op de knop **Configureren** onder **Archiveren naar een opslagaccount** en selecteer het opslagaccount dat u in de voorgaande sectie hebt gemaakt. Klik op **OK**.

   ![Opslagaccount voor diagnostische instellingen](media/tutorial-archive-data/diagnostic-settings-storage.png)

5. Schakel alle selectievakjes onder **Logboek** en **Metrische gegevens** in. Afhankelijk van het resourcetype is er mogelijk maar één van deze opties beschikbaar. Met deze selectievakjes bepaalt u welke categorieën beschikbare logboekgegevens en metrische gegevens voor een bepaald resourcetype worden verzonden naar de bestemming die u hebt geselecteerd (in dit geval: een opslagaccount).

   ![Categorieën van diagnostische instellingen](media/tutorial-archive-data/diagnostic-settings-categories.png)

6. Stel de schuifregelaar **Bewaarperiode (dagen)** in op 30. Met deze schuifregelaar stelt u in hoeveel dagen u de bewakingsgegevens wilt bewaren in het opslagaccount. Gegevens die ouder zijn dan het aantal opgegeven dagen, worden automatisch verwijderd. Bij een bewaarperiode van nul dagen worden de gegevens voor onbepaalde tijd opgeslagen.

7. Klik op **Opslaan**.

Bewakingsgegevens uit uw resource worden nu doorgestuurd naar het opslagaccount.

> [!NOTE]
> Het verzenden van multidimensionale metrische gegevens via diagnostische instellingen wordt momenteel niet ondersteund. Metrische gegevens met dimensies worden geëxporteerd als platte eendimensionale metrische gegevens, als totaal van alle dimensiewaarden.
>
> *Een voorbeeld*: de meetwaarde 'Binnenkomende berichten' voor een Event Hub kan worden verkend en uitgezet op wachtrijniveau. Wanneer de waarde wordt geëxporteerd via diagnostische instellingen, wordt deze echter voorgesteld als alle binnenkomende berichten voor alle wachtrijen in de Event Hub.
>
>

## <a name="route-virtual-machine-guest-os-data-to-the-storage-account"></a>Gegevens van virtuele machines (als gast-OS) doorsturen naar het opslagaccount

1. Als u nog geen een virtuele machine in uw abonnement hebt, [maakt u een virtuele machine](../../virtual-machines/windows/quick-create-portal.md).

2. Klik in de linker navigatielijst in de portal op **Virtuele machines**.

3. Klik in de weergegeven lijst met virtuele machines op de virtuele machine die u hebt gemaakt.

4. Klik in de weergegeven sectie op **Diagnostische instellingen** in de linkernavigatielijst. In deze sectie kunt u de standaard bewakingsextensie uit Azure Monitor instellen voor uw virtuele machine en gegevens die worden gegenereerd door Windows of Linux doorsturen naar een opslagaccount.

   ![Navigeren naar diagnostische instellingen](media/tutorial-archive-data/guest-navigation.png)

5. Klik op **Bewaking op gastniveau inschakelen** in de sectie die wordt weergegeven.

   ![Diagnostische instellingen inschakelen](media/tutorial-archive-data/guest-enable.png)

6. Nadat de diagnostische instelling correct is opgeslagen, laat het tabblad **Overzicht** een overzicht zien van de gegevens die worden verzameld en waar deze worden opgeslagen. Klik op de sectie **Prestatiemeteritems** om de verzamelde set Windows-prestatiemeteritems te bekijken.

   ![Instellingen van prestatiemeteritems](media/tutorial-archive-data/guest-perf-counters.png)

7. Klik op het tabblad **Logboeken** en schakel de selectievakjes in voor logboeken op **Informatie**niveau voor Toepassings- en Systeemlogboeken.

   ![Instellingen voor logboeken](media/tutorial-archive-data/guest-logs.png)

8. Klik op het tabblad **Agent** en klik onder **Opslagaccount** op de naam van het weergegeven opslagaccount.

   ![Opslagaccount bijwerken](media/tutorial-archive-data/guest-storage-home.png)

9. Kies in de weergegeven sectie het opslagaccount dat u in de voorgaande stap, **Een opslagaccount maken**, hebt gemaakt.

10. Klik op **Opslaan**.

Bewakingsgegevens uit uw virtuele machines worden nu doorgestuurd naar het opslagaccount.

## <a name="view-the-monitoring-data-in-the-storage-account"></a>De bewakingsgegevens in het opslagaccount weergeven

> [!WARNING]
> De indeling van de logboekgegevens in het opslagaccount wordt op 1 november 2018 gewijzigd in JSON Lines. [Raadpleeg dit artikel voor een beschrijving van de gevolgen en hoe u uw tooling kunt bijwerken om de nieuwe indeling te verwerken. ](./../../azure-monitor/platform/diagnostic-logs-append-blobs.md) 
>
> 

Als u de voorgaande stappen hebt gevolgd, worden gegevens nu doorgestuurd naar uw opslagaccount.

1. Voor bepaalde gegevenstypen, zoals het activiteitenlogboek, moet er activiteit zijn die een gebeurtenis in het opslagaccount genereert. Volg [deze instructies](./../../azure-monitor/platform/quick-audit-notify-action-subscription.md) om activiteit in het activiteitenlogboek te genereren. Wellicht moet u vijf minuten wachten voordat de gebeurtenis in het opslagaccount wordt weergegeven.

2. Navigeer in de portal naar de sectie **Opslagaccounts** vanuit de linker navigatiebalk.

3. Identificeer het opslagaccount dat u in de voorgaande sectie hebt gemaakt en klik erop.

4. Klik op **Blobs**, vervolgens op de container met het label **insights-operational-logs** en ten slotte op de container met het label **name=default**. Dit is de container waarin het activiteitenlogboek is opgenomen. Bewakingsgegevens worden onderverdeeld in containers op basis van resource-id (alleen de abonnements-id voor het activiteitenlogboek) en vervolgens op basis van datum en tijd. De volledige notatie voor deze blobs is:

   insights-operational-logs/name=default/resourceId=/SUBSCRIPTIONS/{abonnements-id}/y={jaar als vier cijfers}/m={maand als twee cijfers}/d={dag als twee cijfers}/h={uur als twee cijfers in 24-uurs notatie}/m=00/PT1H.json

5. Ga naar het bestand PT1H.json door te klikken in de containers voor resource-id, datum en tijd. Klik op het bestand PT1H.json en op **Downloaden**. Elke PT1H.json-blob bevat een JSON-blob van gebeurtenissen die hebben plaatsgevonden binnen het uur dat is opgegeven in de blob-URL (bijvoorbeeld, h=12). Tijdens het huidige uur worden gebeurtenissen toegevoegd aan het bestand PT1H.json wanneer deze zich voordoen. De minuutwaarde (m=00) is altijd 00 omdat logboekgebeurtenissen worden onderverdeeld in afzonderlijke blobs per uur.

   U kunt nu de JSON-gebeurtenis zien die in het opslagaccount werd opgeslagen. Voor resource-Resource Logboeken is de indeling voor de blobs:

   insights-logs-{categorienaam logboek}/resourceId=/{resource-id}/y={jaar als vier cijfers}/m={maand als twee cijfers}/d={dag als twee cijfers}/h={uur als twee cijfers in 24-uurs notatie}/m=00/PT1H.json

6. Bewakingsgegevens voor het gast-OS worden opgeslagen in tabellen. Ga terug naar het oorspronkelijke opslagaccount en klik op **Tabellen**. Er zijn tabellen voor metrische gegevens, prestatiemeteritems en gebeurtenislogboeken.

U hebt nu ingesteld dat bewakingsgegevens in een opslagaccount kunnen worden gearchiveerd.

## <a name="clean-up-resources"></a>Resources opschonen

1. Ga terug naar de sectie **Activiteitenlogboek exporteren** in de voorgaande stap, **Abonnementslogboeken naar het opslagaccount doorsturen** en klik op **Opnieuw instellen**.

2. Navigeer naar de sectie **Diagnostische instellingen**, klik op de resource waarvoor u een diagnostische instelling hebt gemaakt in de voorgaande stap, **Resourcegegevens doorsturen naar het opslagaccount**, zoek naar de instelling die u hebt gemaakt, klik op de knop **Instelling bewerken** en klik op **Verwijderen**.

3. Navigeer naar de sectie **Diagnostische instellingen** op de virtuele machine die u hebt geconfigureerd in de voorgaande stap, **Gegevens van virtuele machines (als gast-OS) doorsturen naar het opslagaccount**, en klik op het tabblad **Agent** op **Verwijderen** (onder de sectie **Azure Diagnostics-agent verwijderen**).

4. Navigeer naar het opslagaccount dat u in de voorgaande stap, **Een opslagaccount maken**, hebt gemaakt en klik op **Opslagaccount verwijderen**. Voer de naam in van het opslagaccount en klik op **Verwijderen**.

5. Als u voor de voorgaande stappen een virtuele machine of logische app hebt gemaakt, moet u die ook verwijderen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u gegevens uit uw Azure-omgeving (abonnement, resource en gastbesturingssysteem) instelt voor archivering naar een opslagaccount.


> [!div class="checklist"]
> * Een opslagaccount maken om bewakingsgegevens op te slaan
> * Abonnementlogboeken naar het opslagaccount doorsturen
> * Resourcegegevens naar het opslagaccount doorsturen
> * Gegevens van virtuele machines (als gast-OS) naar het opslagaccount doorsturen
> * De bewakingsgegevens in het opslagaccount weergeven
> * Uw resources opschonen

Om meer uit uw gegevens te halen en extra inzichten af te leiden, moet u uw gegevens ook naar Log Analytics verzenden.

> [!div class="nextstepaction"]
> [Aan de slag met Log Analytics](../../azure-monitor/log-query/log-query-overview.md)

