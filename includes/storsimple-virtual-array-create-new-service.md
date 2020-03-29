---
title: bestand opnemen
description: bestand opnemen
services: storage
author: alkohli
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: 4ba5c8b69776b39d8a6640744b0c24600f3a0d5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67176222"
---
#### <a name="to-create-a-new-service"></a>Een nieuwe service maken

1.  Meld u op deze URL aan bij de Azure-portal: <https://portal.azure.com/>. Als u het apparaat implementeert in de portal van de overheid, meldt u zich aan bij:<https://portal.azure.us/>

2.  Klik in de Azure-portal op + Een **storSimple-serie** **voor resourcesopslag** &gt; **Storage** &gt; maken .

    ![Nieuwe service maken](./media/storsimple-virtual-array-create-new-service/createnewservice2.png) 

3.  Ga als volgt te werk in het **StorSimple Device Manager-blad** dat wordt geopend:

    1.  Geef een unieke **resourcenaam** voor uw service op. De naam van de resource is een vriendelijke naam die kan worden gebruikt om de service te identificeren. De naam kan tussen 2 en 50 tekens bevatten (letters, cijfers en afbreekstreepjes). De naam moet beginnen en eindigen met een letter of cijfer.

    2.  Kies een **abonnement** in de vervolgkeuzelijst. Het abonnement is gekoppeld aan uw factureringsrekening. Dit veld wordt niet weergegeven als u slechts één abonnement hebt.

    3.  Selecteer **voor resourcegroep**een bestaande bestaande groep of maak een nieuwe groep. Zie [Azure-resourcegroepen](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/) voor meer informatie.

    4.  Geef een **locatie** voor uw service op. Zie [Azure-regio's](https://azure.microsoft.com/regions/#services) voor meer informatie over welke services beschikbaar zijn in welke regio. Kies over het algemeen een **locatie die** het dichtst bij het geografische gebied ligt waar u uw apparaat wilt implementeren. U kunt ook rekening houden met het volgende:

        -   Als u bestaande workloads in Azure hebt die u ook wilt implementeren met uw StorSimple-apparaat, raden we u aan dat datacenter te gebruiken.

        -   Uw StorSimple-apparaatbeheer en Azure-opslag kunnen zich op twee afzonderlijke locaties bevinden. In dat geval moet u de StorSimple--apparaatbeheerfunctie en het Azure Storage-account afzonderlijk maken. Als u een Azure-opslagaccount wilt maken, navigeert u naar Azure Storage in de Azure-portal en volgt u de stappen die zijn beschreven in [Een opslagaccount maken.](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) Nadat u dit account hebt gemaakt, voegt u het toe aan de StorSimple-apparaatbeheerfunctie met de stappen in [Een nieuw opslagaccount voor de service maken](https://azure.microsoft.com/documentation/articles/storsimple-deployment-walkthrough/#configure-a-new-storage-account-for-the-service).

        -   Als u het virtuele apparaat implementeert in het overheidsportaal, is de StorSimple Device Manager-service beschikbaar op locaties in Iowa en in de VS. Virginia.

    5.  Selecteer **Een nieuw Azure-opslagaccount maken** om automatisch een opslagaccount met de service te maken. Geef een **naam van een opslagaccount op**. Als u een andere locatie voor uw gegevens wilt kiezen, schakelt u dit vakje uit.

    6.  Schakel **Aan dashboard vastmaken** in als u een snelkoppeling naar deze service op uw dashboard wilt.

    7.  Klik op **Maken** om de StorSimple-apparaatbeheerfunctie te maken.

        ![Nieuwe service maken](./media/storsimple-virtual-array-create-new-service/createnewservice4.png)  

U wordt doorverwezen naar de **bestemmingspagina van** de service. Het maken van de service duurt enkele minuten. Nadat de service is gemaakt, krijgt u hierover een melding en wordt de status van de service gewijzigd in **Actief**.


