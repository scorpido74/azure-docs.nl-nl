---
title: Overzicht van Microsoft Azure StorSimple Data Manager | Microsoft Documenten
description: Geeft een overzicht van de StorSimple Data Manager-service
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/21/2018
ms.author: alkohli
ms.openlocfilehash: 2ffe17bf7ef4f01c18d2c26f4a045add7302272d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67876106"
---
# <a name="storsimple-data-manager-solution-overview"></a>Overzicht van StorSimple Data Manager-oplossing

## <a name="overview"></a>Overzicht

Microsoft Azure StorSimple gebruikt cloudopslag als een uitbreiding van de on-premises oplossing en brengt gegevens automatisch op on-premises opslag en de cloud. Gegevens worden opgeslagen in de cloud in een deduped en gecomprimeerd formaat voor maximale efficiëntie en om de kosten te verlagen. Aangezien de gegevens worden opgeslagen in het StorSimple-formaat, is het niet gemakkelijk te gebruiken voor andere cloudtoepassingen die u mogelijk wilt gebruiken.

Met de StorSimple Data Manager u naadloos toegang krijgen tot de StorSimple-indelingsgegevens in de cloud en deze gebruiken. Dit gebeurt door de StorSimple-indeling om te zetten in native blobs en bestanden, die u gebruiken met andere services zoals Azure Media Services, Azure HDInsights en Azure Machine Learning.

Dit artikel geeft een overzicht van de StorSimple Data Manager oplossing. Het legt ook uit hoe u deze service gebruiken om toepassingen te schrijven die StorSimple-gegevens en andere Azure-services in de cloud gebruiken.

## <a name="how-it-works"></a>Hoe werkt het?

De StorSimple Data Manager-service identificeert StorSimple-gegevens in de cloud van een on-premises apparaat uit de StorSimple 8000-serie. De StorSimple-gegevens in de cloud worden verwijderd, gecomprimeerd storsimple-formaat. De Data Manager-service biedt API's om de StorSimple-indelingsgegevens te extraheren en om te zetten in andere indelingen, zoals Azure-blobs en Azure-bestanden. Deze getransformeerde gegevens worden vervolgens gemakkelijk verbruikt door Azure HDInsight- en Azure Media-services. De gegevenstransformatie stelt deze services dus in staat om te werken op de getransformeerde StorSimple-gegevens van storSimple 8000-serie on-premises apparaten. Deze stroom wordt geïllustreerd in het volgende diagram.

![Diagram op hoog niveau](./media/storsimple-data-manager-overview/storsimple-data-manager-overview2.png)


## <a name="data-manager-use-cases"></a>Gebruiksvoorbeelden van Gegevensbeheer

U Gegevensbeheer met Azure-functies, Azure Automation en Azure Data Factory gebruiken om werkstromen op uw gegevens te laten uitvoeren zoals deze in StorSimple worden geleverd. U uw media-inhoud verwerken die u op StorSimple opslaat met Azure Media Services, of een Machine Learning-algoritme op die gegevens uitvoeren of een Hadoop-cluster weergeven om de gegevens te analyseren die u op StorSimple opslaat. Met het uitgebreide scala aan services dat beschikbaar is op Azure in combinatie met de gegevens op StorSimple, u de kracht van uw gegevens ontgrendelen.


## <a name="region-availability"></a>Beschikbaarheid in regio’s

De StorSimple Data Manager is beschikbaar in de volgende 7 regio's:

 - Azië - zuidoost
 - VS - oost
 - VS - west
 - VS - west 2
 - VS - west-centraal
 - Europa - noord
 - Europa -west

De StorSimple Data Manager kan echter worden gebruikt om gegevens in de volgende regio's te transformeren. 

![Regio's beschikbaar voor gegevens](./media/storsimple-data-manager-overview/data-manager-job-definition-different-regions-m.png)

Deze set is groter omdat de implementatie van resources in een van de bovenstaande regio's in staat is om het transformatieproces in de onderstaande regio's te stimuleren. Zolang uw gegevens zich dus in een van de 19 regio's bevinden, u uw gegevens transformeren met behulp van deze service.


## <a name="choosing-a-region"></a>Een regio kiezen

We raden aan dat:
 - Uw bronopslagaccount (het account dat is gekoppeld aan uw StorSimple-apparaat) en het doelopslagaccount (waar u de gegevens in native indeling wilt) bevinden zich in dezelfde Azure-regio.
 - U brengt uw Data Manager en taakdefinitie naar boven in de regio die het Opslagaccount StorSimple bevat. Als dit niet mogelijk is, brengt u gegevensbeheer weer in de dichtstbijzijnde Azure-regio en maakt u de taakdefinitie in dezelfde regio als uw StorSimple-opslagaccount. 

    Als uw StorSimple-opslagaccount zich niet in de 26 regio's bevindt die het maken van taakdefinitie ondersteunen, raden we u aan StorSimple Data Manager niet uit te voeren omdat u lange latencies en potentiële uitgaande kosten ziet.
    
Microsoft streeft ernaar ervoor te zorgen dat Azure-services altijd beschikbaar zijn in alle regio's. Ongeplande serviceonderbrekingen kunnen echter voor korte perioden in een bepaalde regio optreden. In dergelijke gevallen u een gegevensbeheer- en taakdefinitie weergeven in een regio die niet wordt beïnvloed door de storing en de transformatietaak uitvoeren. U in een dergelijk scenario een extra latentie tegenkomen, maar dit kan uw herstelstrategie zijn in het zeldzame geval van een regionale storing.

## <a name="security-considerations"></a>Beveiligingsoverwegingen

De StorSimple Data Manager heeft de encryptiesleutel voor servicegegevens nodig om van StorSimple-indeling naar native formaat te transformeren. De versleutelingssleutel voor servicegegevens wordt gegenereerd wanneer het eerste apparaat zich registreert met de StorSimple-service. Ga voor meer informatie over deze sleutel naar [StorSimple security.](storsimple-8000-security.md)

De versleutelingssleutel voor servicegegevens die als invoer wordt geleverd, wordt opgeslagen in een sleutelkluis die wordt gemaakt wanneer u een gegevensbeheer maakt. De kluis bevindt zich in dezelfde Azure-regio als uw StorSimple Data Manager. Deze sleutel wordt verwijderd wanneer u uw Data Manager-service verwijdert.

Deze sleutel wordt door de rekenbronnen gebruikt om de transformatie uit te voeren. Deze rekenbronnen bevinden zich in dezelfde Azure-regio als uw taakdefinitie. Deze regio kan wel of niet hetzelfde zijn als de regio waar u uw Gegevensbeheer ter sprake brengt.

Als uw regio Gegevensbeheer verschilt van uw taakdefinitiegebied, is het belangrijk dat u begrijpt welke gegevens/metagegevens zich in elk van deze regio's bevinden. In het volgende diagram wordt het effect van verschillende regio's voor Gegevensbeheer en taakdefinitie geïllustreerd.

![Service- en taakdefinitie in verschillende regio's](./media/storsimple-data-manager-overview/data-manager-job-different-regions.png)

## <a name="managing-personal-information"></a>Persoonlijke informatie beheren

De StorSimple Data Manager verzamelt of geeft geen persoonlijke informatie. Bekijk het Microsoft-privacybeleid in het [Vertrouwenscentrum](https://www.microsoft.com/trustcenter) voor meer informatie.

## <a name="known-limitations"></a>Bekende beperkingen

De service heeft momenteel de volgende beperkingen:
- De StorSimple Data Manager werkt momenteel niet met volumes die bitlocker versleuteld zijn. U ziet taakfouten als u probeert de service uit te voeren met een versleuteld station.
- Sommige metagegevens van bestanden (inclusief ACL's) worden niet bewaard in de getransformeerde gegevens.
- Deze service werkt alleen met NTFS-volumes.
- De lengtes van het bestandspad moeten minder dan 256 tekens bevatten, anders mislukt de taak.

## <a name="next-steps"></a>Volgende stappen

[Gebruik de gebruikersinterface van StorSimple Data Manager om uw gegevens te transformeren.](storsimple-data-manager-ui.md)
