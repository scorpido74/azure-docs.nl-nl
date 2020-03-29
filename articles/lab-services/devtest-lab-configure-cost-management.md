---
title: Bekijk de maandelijkse geschatte trend in labkosten in Azure DevTest Labs
description: In dit artikel vindt u informatie over het bijhouden van de kosten van uw lab (het maandelijks geschatte kostentrenddiagram) in Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 1f46fdc5-d917-46e3-a1ea-f6dd41212ba4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 2e4fe55fac5edf73e16df05bd38cc2712a94377d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721724"
---
# <a name="track-costs-associated-with-a-lab-in-azure-devtest-labs"></a>Kosten bijhouden die zijn gekoppeld aan een lab in Azure DevTest Labs
In dit artikel vindt u informatie over het bijhouden van de kosten van uw lab. Het toont u hoe u de geschatte kostentrent voor de huidige kalendermaand voor het lab bekijken. In het artikel ziet u ook hoe u de kosten per resource in het lab weergeven.

## <a name="view-the-monthly-estimated-lab-cost-trend"></a>Bekijk de maandelijkse geschatte trend van labkosten 
In deze sectie leert u hoe u het grafiek **Monthly Estimated Cost Trend** gebruikt om de geschatte kosten-tot-datum van de huidige kalendermaand en de verwachte kosten aan het einde van de maand voor de huidige kalendermaand weer te geven. U leert ook hoe u labkosten beheren door bestedingsdoelen en drempelwaarden in te stellen die, wanneer ze worden bereikt, DevTest Labs activeren om de resultaten aan u te rapporteren.

Voer de volgende stappen uit om het grafiek Monthly Estimated Cost Trend te bekijken: 

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **Alle services**en selecteer Vervolgens **DevTest Labs** in de lijst.
3. Selecteer in de lijst met labs uw lab.  
4. Selecteer **Configuratie en beleid** in het linkermenu.  
4. Selecteer **Kostentrend** in de sectie **Kostenbijhouden** in het linkermenu. De volgende schermafbeelding toont een voorbeeld van een kostengrafiek. 
   
    ![Kostendiagram](./media/devtest-lab-configure-cost-management/graph.png)

    De **geschatte kostenwaarde** is de geschatte kosten-tot-datum van de huidige kalendermaand. De **verwachte kosten** zijn de geschatte kosten voor de volledige huidige kalendermaand, berekend aan de hand van de labkosten voor de afgelopen vijf dagen.

    De kostenbedragen worden naar boven afgerond op het volgende hele nummer. Bijvoorbeeld: 

   * 5.01 ronden tot 6 
   * 5,50 ronden tot 6
   * 5,99 ronden tot 6

     Zoals boven de grafiek staat, zijn de kosten die u standaard in de grafiek ziet *geschatte* kosten met [behulp van pay-as-you-go-aanbiedingstarieven.](https://azure.microsoft.com/offers/ms-azr-0003p/) U ook uw eigen bestedingsdoelen instellen die in de grafieken worden weergegeven door [de kostendoelen voor uw lab te beheren.](#managing-cost-targets-for-your-lab)

     De volgende kosten zijn *niet* opgenomen in de kostenberekening:

   * CSP- en Dreamspark-abonnementen worden momenteel niet ondersteund omdat Azure DevTest Labs de [Azure-facturerings-API's](../cost-management-billing/manage/usage-rate-card-overview.md) gebruikt om de labkosten te berekenen, die geen CSP- of Dreamspark-abonnementen ondersteunen.
   * Uw aanbiedingstarieven. Momenteel u de aanbiedingstarieven (weergegeven onder uw abonnement) die u met Microsoft- of Microsoft-partners hebt onderhandeld, niet gebruiken. Er worden alleen pay-as-you-go-tarieven gebruikt.
   * Uw belastingen
   * Uw kortingen
   * Uw factureringsvaluta. Momenteel worden de labkosten alleen weergegeven in USD-valuta.

### <a name="managing-cost-targets-for-your-lab"></a>Kostendoelen voor uw lab beheren
Met DevTest Labs u de kosten in uw lab beter beheren door een bestedingsdoel in te stellen dat u vervolgens weergeven in het grafiek Monthly Estimated Cost Trend. DevTest Labs kan u ook een melding sturen wanneer de opgegeven doeluitgaven of drempelwaarde is bereikt. 

1. Selecteer op de pagina **Kostentrend** de optie **Doel beheren**.

    ![Knop Doel beheren](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)
2. Geef op de **pagina Doel beheren** een bestedingsdoel en drempelwaarden op. U ook instellen of elke geselecteerde drempel wordt gerapporteerd in het kostentrenddiagram of via een webhook-melding.

    ![Doelvenster beheren](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - Selecteer een periode waarin u kostendoelen wilt bijhouden.
      - **Maandelijks**: kostendoelen worden per maand bijgehouden.
      - **Opgelost:** kostendoelen worden bijgehouden voor het datumbereik dat u opgeeft in de begin- en einddatum. Deze waarden geven doorgaans aan hoe lang uw project moet worden uitgevoerd.
   - Geef **doelkosten op.** Bijvoorbeeld hoeveel u van plan bent te besteden aan dit lab in de periode die u hebt gedefinieerd.
   - Selecteer om de gewenste drempelwaarde in of uit te schakelen , in stappen van 25%, tot 125% van de opgegeven **doelkosten.**
      - **Melding:** Wanneer deze drempel is bereikt, wordt u op de hoogte gesteld door een webhook-URL die u opgeeft.
      - **Plot in grafiek:** Wanneer deze drempel is bereikt, worden de resultaten uitgezet op de grafiek van de kostentrend die u weergeven, zoals beschreven in Het overzicht van de trend van de maandelijkse geschatte kosten bekijken.
   - Als u op **de hoogte wilt stellen** wanneer de drempelwaarde is bereikt, moet u een url van de webhook opgeven. Selecteer hier in het gebied Kostenintegraties **Klik hier om een integratie toe te voegen.** Voer een **webhook-URL** in het systeemvan de melding seinen in en selecteer **OK**.

       ![Meldingsvenster configureren](./media/devtest-lab-configure-cost-management/configure-notification.png)

     - Als u **Notify opgeeft,** moet u een webhook-URL definiëren.
     - Als u een webhook-URL definieert, moet u **melding** instellen op **Aan** in het deelvenster Kostendrempel.
     - U moet een webhook maken voordat u deze hier invoert.  

       Zie [Een webhook- of API Azure-functie maken](../azure-functions/functions-create-a-web-hook-or-api-function.md)voor meer informatie over webhooks. 

## <a name="view-cost-by-resource"></a>Kosten per resource weergeven 
Met de maandelijkse kostentrendfunctie in labs u zien hoeveel u in de huidige kalendermaand hebt uitgegeven. Het toont ook de projectie van de uitgaven tot het einde van de maand, op basis van uw uitgaven in de afgelopen zeven dagen. Om u te helpen begrijpen waarom de uitgaven in het lab in een vroeg stadium voldoen aan drempelwaarden, u de **kosten per resourcefunctie** gebruiken die u de kosten **per resource per resource** in een tabel weergeeft.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **Alle services**en selecteer Vervolgens **DevTest Labs** in de lijst.
3. Selecteer in de lijst met laboratoria het gewenste lab.  
4. Selecteer **Configuratie en beleid** in het linkermenu.
5. Selecteer **Kosten per resource** in de sectie **Kostenbijhouden** in het linkermenu. U ziet de kosten die zijn gekoppeld aan elke resource die aan een lab is gekoppeld. 

    ![Kosten per resource](./media/devtest-lab-configure-cost-management/cost-by-resource.png)

Met deze functie u eenvoudig de resources identificeren die het meest kosten, zodat u acties ondernemen om de uitgaven in het lab te verminderen. De kosten van een vm zijn bijvoorbeeld gebaseerd op de grootte van de VM. Hoe groter de grootte van de VM, meer is de kosten. U eenvoudig de grootte van een VM en de eigenaar vinden, zodat u met de VM-eigenaar praten om te begrijpen waarom een dergelijke VM-grootte nodig is en of er een kans is om de grootte te verlagen.

[Het beleid voor automatisch afsluiten](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy) helpt u de kosten te verlagen door labVM's op een bepaald tijdstip van de dag af te sluiten. Een labgebruiker kan zich echter afmelden voor het afsluitbeleid, waardoor de kosten van het uitvoeren van de VM stijgen. U een vm in de tabel selecteren om te zien of deze is afgemeld voor het beleid voor automatisch afsluiten. Als dat het geval is, u met de eigenaar van de virtuele machine praten om te achterhalen waarom de vm is afgemeld voor het beleid.
 
## <a name="next-steps"></a>Volgende stappen
Hier zijn een aantal dingen om te proberen volgende:

* [Labbeleid definiëren](devtest-lab-set-lab-policy.md) : meer informatie over het instellen van de verschillende beleidsregels die worden gebruikt om te bepalen hoe uw lab en de vm's worden gebruikt. 
* [Aangepaste afbeelding maken](devtest-lab-create-template.md) - Wanneer u een vm maakt, geeft u een basis op, die een aangepaste afbeelding of een Marketplace-afbeelding kan zijn. In dit artikel wordt uitgelegd hoe u een aangepaste afbeelding maakt op basis van een VHD-bestand.
* [Marketplace-afbeeldingen configureren](devtest-lab-configure-marketplace-images.md) - DevTest Labs ondersteunt het maken van VM's op basis van Azure Marketplace-afbeeldingen. In dit artikel wordt uitgelegd hoe u opgeven welke, indien van toepassing, Azure Marketplace-afbeeldingen kunnen worden gebruikt bij het maken van VM's in een lab.
* [Maak een VM in een lab](devtest-lab-add-vm.md) - illustreert hoe u een VM maakt op basisafbeelding (aangepast of Marketplace) en hoe u werkt met artefacten in uw VM.

