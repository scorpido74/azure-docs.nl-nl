---
title: Een app schalen in ASE v1
description: Een app schalen in een app-serviceomgeving. Dit document is alleen bedoeld voor klanten die de verouderde v1 ASE gebruiken.
author: ccompy
ms.assetid: 78eb1e49-4fcd-49e7-b3c7-f1906f0f22e3
ms.topic: article
ms.date: 10/17/2016
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 43849ca7084f2237c37ad537c50f4e94ac4ea7c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74688682"
---
# <a name="scaling-apps-in-an-app-service-environment-v1"></a>Apps schalen in een App-serviceomgeving v1
In de Azure App Service zijn er normaal gesproken drie dingen die u schalen:

* prijsplan
* grootte van de werknemer 
* aantal exemplaren.

In een ASE is het niet nodig om het prijsplan te selecteren of te wijzigen.  In termen van mogelijkheden is het al op een Premium prijsmogelijkheden niveau.  

Met betrekking tot de grootte van werknemers kan de ASE-beheerder de grootte toewijzen van de rekenbron die voor elke werknemersgroep moet worden gebruikt.  Dat betekent dat u Worker Pool 1 met P4-rekenresources en Worker Pool 2 hebben met P1-rekenresources, indien gewenst.  Ze hoeven niet in grootte orde te zijn.  Zie [het][AppServicePricing]document hier voor meer informatie over de grootte en de prijzen ervan.  Hierdoor blijven de schalingsopties voor web-apps en app-serviceplannen in een App-serviceomgeving:

* selectie van de werknemersgroep
* aantal exemplaren

Het wijzigen van een van beide items gebeurt via de juiste gebruikersinterface die wordt weergegeven voor uw ASE-gehoste app-serviceplannen.  

![][1]

U uw ASP niet opschalen buiten het aantal beschikbare rekenbronnen in de werknemersgroep waarin uw ASP zich bevindt.  Als u rekenresources in die werknemersgroep nodig hebt, moet u de ASE-beheerder ertoe brengen deze toe te voegen.  Voor informatie over het opnieuw configureren van uw ASE lees de informatie hier: [Hoe configureer je een App Service-omgeving][HowtoConfigureASE].  U ook gebruik maken van de ASE-functies voor automatisch schalen om capaciteit toe te voegen op basis van planning of statistieken.  Zie [Autoscale configureren voor een App-serviceomgeving][ASEAutoscale]voor meer informatie over het configureren van automatisch schalen voor de ASE-omgeving zelf.

U meerdere app-serviceplannen maken met behulp van rekenresources uit verschillende werkgroepen of u dezelfde werknemersgroep gebruiken.  Als u bijvoorbeeld (10) beschikbare rekenresources in Worker Pool 1 hebt, u ervoor kiezen om één app-serviceplan te maken met behulp van (6) rekenresources en een tweede app-serviceplan dat (4) rekenresources gebruikt.

### <a name="scaling-the-number-of-instances"></a>Het aantal exemplaren schalen
Wanneer u uw web-app voor het eerst maakt in een App Service-omgeving, begint deze met 1 exemplaar.  U vervolgens uitschalen naar extra exemplaren om extra rekenbronnen voor uw app te bieden.   

Als uw ASE heeft genoeg capaciteit dan is dit vrij eenvoudig.  U gaat naar uw App Service Plan dat de sites bevat die u wilt opschalen en selecteer Schalen.  Hiermee wordt de gebruikersinterface geopend waar u de schaal voor uw ASP handmatig instellen of automatische schaalregels voor uw ASP configureren.  Als u uw app handmatig wilt schalen, stelt ***u schalen*** in op ***een instantietelling die ik handmatig invul.***  Vanaf hier sleept u de schuifregelaar naar de gewenste hoeveelheid of voert u deze in het vak naast de schuifregelaar in.  

![][2] 

De autoscale regels voor een ASP in een ASE werken hetzelfde als ze normaal doen.  U ***CPU-percentage*** selecteren onder ***Schalen op*** basis van uw ASP en autoscaleregels maken op basis van CPU-percentage of u complexere regels maken met behulp van ***plannings- en prestatieregels.***  Voor meer volledige details over het configureren van automatisch schalen raadpleegt u hier De handleiding Hier [Een app schalen in Azure App Service][AppScale]. 

### <a name="worker-pool-selection"></a>Selectie van werknemerspool
Zoals eerder vermeld, is de selectie van de werknemersgroep toegankelijk vanuit de ASP-gebruikersinterface.  Open het blad voor de ASP die u wilt schalen en selecteer de groep werknemers.  U ziet alle werknemersgroepen die u hebt geconfigureerd in uw app-serviceomgeving.  Als u slechts één werknemerspool hebt, ziet u alleen de genoemde pool.  Als u wilt wijzigen in welke werknemersgroep uw ASP zich bevindt, selecteert u gewoon de werknemersgroep waarnaar uw App-serviceplan moet worden verplaatst.  

![][3]

Voordat u uw ASP van de ene werknemerspool naar de andere verplaatst, is het belangrijk om ervoor te zorgen dat u voldoende capaciteit hebt voor uw ASP.  In de lijst met werknemersgroepen wordt niet alleen de naam van de werknemersgroep vermeld, maar u ook zien hoeveel werknemers beschikbaar zijn in die werknemersgroep.  Zorg ervoor dat er voldoende exemplaren beschikbaar zijn om uw App Service Plan te bevatten.  Als u meer rekenresources nodig hebt in de werknemersgroep waarnaar u wilt verplaatsen, laat u de ASE-beheerder deze toevoegen.  

> [!NOTE]
> Als u een ASP uit een groep werknemers verplaatst, worden de apps in die ASP koud gestart.  Dit kan ertoe leiden dat aanvragen langzaam worden uitgevoerd omdat uw app koud is gestart op de nieuwe rekenbronnen.  De koude start kan worden vermeden door de [opwarmmogelijkheid][AppWarmup] van de toepassing in Azure App Service te gebruiken.  De module Initialisatie van toepassingen die in het artikel wordt beschreven, werkt ook voor koude starts, omdat het initialisatieproces ook wordt aangeroepen wanneer apps koud worden gestart op nieuwe rekenbronnen. 
> 
> 

## <a name="getting-started"></a>Aan de slag
Zie [Een app-serviceomgeving maken][HowtoCreateASE] om aan de slag te gaan met app-serviceomgevingen

<!--Image references-->
[1]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-aspblade.png
[2]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-manualscale.png
[3]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-sizescale.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[ScaleWebapp]: ../manage-scale-up.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoConfigureASE]: app-service-web-configure-an-app-service-environment.md
[CreateWebappinASE]: app-service-web-how-to-create-a-web-app-in-an-ase.md
[Appserviceplans]: ../overview-hosting-plans.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 
[ASEAutoscale]: app-service-environment-auto-scale.md
[AppScale]: ../manage-scale-up.md
[AppWarmup]: https://ruslany.net/2015/09/how-to-warm-up-azure-web-app-during-deployment-slots-swap/
