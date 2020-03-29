---
title: Microsoft Azure StorSimple Data Manager-gebruikersinterface
description: Beschrijft hoe u de gebruikersinterface van de StorSimple Data Manager-service gebruikt
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 85be49ad88ac62d90235c3da6b89b0da6a11487c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78933749"
---
# <a name="manage-the-storsimple-data-manager-service-in-azure-portal"></a>De StorSimple Data Manager-service beheren in Azure-portal

In dit artikel wordt uitgelegd hoe u de StorSimple Data Manager-gebruikersinterface gebruiken om de gegevens die zich op de Apparaten uit de StorSimple 8000-serie bevinden, te transformeren. De getransformeerde gegevens kunnen vervolgens worden verbruikt door andere Azure-services, zoals Azure Media Services, Azure HDInsight, Azure Machine Learning en Azure Cognitive Search.


## <a name="use-storsimple-data-transformation"></a>StorSimple-gegevenstransformatie gebruiken

De StorSimple Data Manager is de bron waarbinnen gegevenstransformatie wordt geinstantieerd. Met de service Gegevenstransformatie u gegevens transformeren van de Indeling StorSimple naar native indeling in blobs of Azure-bestanden. Als u de gegevens van de StorSimple-native indeling wilt transformeren, moet u de details opgeven over uw StorSimple 8000-serieapparaat en de gegevens die u wilt transformeren.

### <a name="create-a-storsimple-data-manager-service"></a>Een StorSimple Data Manager-service maken

Voer de volgende stappen uit om een StorSimple Data Manager-service te maken.

1. Gebruik de referenties van uw Microsoft-account om u aan te melden bij [Azure Portal](https://portal.azure.com/).

2. Klik **op + Maak een bron** en zoek naar StorSimple Data Manager.

    ![Een StorSimple Data Manager-service maken 1](./media/storsimple-data-manager-ui/create-service-1.png)

3. Klik op StorSimple Data Manager en klik vervolgens op **Maken.**
    
    ![Een StorSimple Data Manager-service maken 2](./media/storsimple-data-manager-ui/create-service-3.png)

3. Geef voor de nieuwe service het volgende op:

   1. Geef een unieke **servicenaam** voor uw StorSimple Data Manager. Dit is een beschrijvende naam die kan worden gebruikt om de service te identificeren. De naam kan tussen 3 en 24 tekens bevatten (letters, cijfers en afbreekstreepjes). De naam moet beginnen en eindigen met een letter of cijfer.

   2. Kies een **abonnement** in de vervolgkeuzelijst. Het abonnement is gekoppeld aan uw factureringsrekening. Dit veld wordt automatisch ingevuld (en niet selecteerbaar) als u slechts één abonnement hebt.

   3. Selecteer een bestaande resourcegroep of maak een nieuwe groep. Zie [Azure-resourcegroepen](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/) voor meer informatie.

   4. Geef de **locatie** voor uw service op waarin uw opslagaccounts en uw StorSimple Data Manager-service zijn opgenomen. Uw StorSimple Device Manager-service, de Data Manager-service en het bijbehorende opslagaccount moeten zich allemaal in de ondersteunde regio's bevinden.
    
   5. Als u een koppeling naar deze service op uw dashboard wilt krijgen, selecteert u **Vastmaken aan dashboard**.
    
   6. Klik **op Maken**.

      ![Een StorSimple Data Manager-service maken 3](./media/storsimple-data-manager-ui/create-service-4.png)

Het maken van de service duurt enkele minuten. U ziet een melding nadat de service is gemaakt en de nieuwe service wordt weergegeven.

### <a name="create-a-data-transformation-job-definition"></a>Een taakdefinitie voor gegevenstransformatie maken

Binnen een StorSimple Data Manager-service moet u een taakdefinitie voor gegevenstransformatie maken. Een taakdefinitie geeft details op van de StorSimple-gegevens die u wilt verplaatsen naar een opslagaccount in de oorspronkelijke indeling. Zodra u een taakdefinitie hebt gemaakt, u deze taak opnieuw uitvoeren met verschillende runtime-instellingen.

Voer de volgende stappen uit om een taakdefinitie te maken.

1. Navigeer naar de service die u hebt gemaakt. Ga naar **management > functiedefinities**.

2. Klik **op + Taakdefinitie**.

    ![Klik op +Taakdefinitie](./media/storsimple-data-manager-ui/create-job-definition-1.png)

3. Geef een naam op voor uw taakdefinitie. De naam kan tussen de 3 en 63 tekens liggen. De naam kan hoofdletters, cijfers en koppeltekens bevatten.

4. Geef een locatie op waar uw taak wordt uitgevoerd. Deze locatie kan afwijken van de locatie waar de service wordt geïmplementeerd.

5. Klik op **Bron** om de brongegevensopslagplaats op te geven.

    ![Repo brongegevens configureren](./media/storsimple-data-manager-ui/create-job-definition-2.png)

6. Aangezien dit een nieuwe Data Manager-service is, zijn er geen gegevensopslagplaatsen geconfigureerd. Geef in **Gegevensbron configureren**de details op van uw StorSimple 8000-serie apparaat en de gegevens van belang.

   Als u uw StorSimple-apparaatbeheer als gegevensopslagplaats wilt toevoegen, klikt u op **Nieuw toevoegen** in de vervolgkeuzelijst van de gegevensopslagplaats en klikt u vervolgens op **Gegevensopslagplaats toevoegen.**

    ![Nieuwe gegevensrepo toevoegen](./media/storsimple-data-manager-ui/create-job-definition-3.png)
  
   1. Kies **StorSimple 8000-serie Manager** als gegevensopslagplaatstype.
    
   2. Voer een vriendelijke naam in voor uw brongegevensopslagplaats.
    
   3. Kies in de vervolgkeuzelijst een abonnement dat is gekoppeld aan uw StorSimple Device Manager-service.
    
   4. Geef de naam op van de StorSimple Device Manager voor de **resource.**

   5. Voer de **versleutelingssleutel servicegegevens** in voor de StorSimple Device Manager-service. 

      ![Brongegevens repo 1 configureren](./media/storsimple-data-manager-ui/create-job-definition-4.png)

      Klik op **OK** als u klaar bent. Dit slaat uw data repository op. Gebruik deze StorSimple Device Manager opnieuw in andere taakdefinities zonder deze parameters opnieuw in te voeren. Het duurt een paar seconden nadat u op **OK** hebt geklikt voor de nieuw gemaakte brongegevensopslagplaats die wordt weergegeven in de vervolgkeuzelijst.

7. Selecteer in de vervolgkeuzelijst voor **gegevensopslagplaats**de gegevensopslagplaats die u hebt gemaakt. 

   1. Voer de naam in van het apparaat uit de StorSimple 8000-serie dat de gegevens bevat die van belang zijn.

   2. Geef de naam op van het volume dat zich op het StorSimple-apparaat bevindt met uw gegevens van belang.

   3. Voer in de subsectie **Filter** de hoofdmap in die uw gegevens bevat die van belang zijn in _de indeling \MyRootDirectory\Data._ Stationsletters zoals _\C:\Gegevens_ worden niet ondersteund. U hier ook bestandsfilters toevoegen.

   4. De gegevenstransformatieservice werkt alleen op de nieuwste momentopname van de gegevens die naar Azure worden gepusht.

   5. Klik op **OK**.

      ![Brongegevens repo 2 configureren](./media/storsimple-data-manager-ui/create-job-definition-8.png)

8. Vervolgens moet de doelgegevensopslagplaats worden geconfigureerd. Kies opslagaccounts om bestanden in blobs in dat account te plaatsen. Selecteer in de vervolgkeuzelijst **Nieuw toevoegen** en **configureer**vervolgens instellingen .

9. Selecteer het type doelopslagplaats dat u wilt toevoegen en de andere parameters die aan de opslagplaats zijn gekoppeld.

    Als u een opslagaccounttypedoel selecteert, u een vriendelijke naam, abonnement (kies hetzelfde als die van de service of een andere) en een opslagaccount opgeven.
        ![Doelgegevens repo 1 configureren](./media/storsimple-data-manager-ui/create-job-definition-10.png)

    Er wordt een opslagwachtrij gemaakt wanneer de taak wordt uitgevoerd. Deze wachtrij wordt gevuld met berichten over getransformeerde blobs wanneer deze gereed zijn. De naam van deze wachtrij is hetzelfde als de naam van de taakdefinitie.
    
10. Nadat u de gegevensopslagplaats hebt toegevoegd, wacht u een paar minuten.
    
    1. Selecteer de opslagplaats die u als doel hebt gemaakt in de vervolgkeuzelijst in de **naam van het doelaccount**.

    2. Kies het opslagtype als blobs of bestanden. Geef de naam op van de opslagcontainer waar de getransformeerde gegevens zich bevinden. Klik op **OK**.

        ![Repo-opslagaccount voor doelgegevens configureren](./media/storsimple-data-manager-ui/create-job-definition-16.png)

11. U ook de optie controleren om een schatting van de duur van de taak weer te geven voordat u de taak uitvoert. Klik op **OK** om de taakdefinitie te maken. Uw taakdefinitie is nu voltooid. U deze taakdefinitie meerdere keren gebruiken via de gebruikersinterface met verschillende runtime-instellingen.

    ![Volledige taakdefinitie](./media/storsimple-data-manager-ui/create-job-definition-13.png)

    De nieuw gemaakte taakdefinitie wordt toegevoegd aan de lijst met taakdefinities voor deze service.

### <a name="run-the-job-definition"></a>De taakdefinitie uitvoeren

Wanneer u gegevens van StorSimple moet verplaatsen naar het opslagaccount dat u in de taakdefinitie hebt opgegeven, moet u deze uitvoeren. Bij runtime kunnen sommige parameters anders worden opgegeven. De stappen zijn als volgt:

1. Selecteer uw StorSimple Data Manager-service en ga naar **Management > Job definities**. Selecteer en klik op de taakdefinitie die u wilt uitvoeren.
     
     ![Taakrun starten 1](./media/storsimple-data-manager-ui/start-job-run1.png)

2. Klik **op Nu uitvoeren**.
     
     ![Taakloop 2 starten](./media/storsimple-data-manager-ui/start-job-run2.png)

3. Klik **op Instellingen uitvoeren** om alle instellingen te wijzigen die u mogelijk wilt wijzigen voor deze taaktaak. Klik op **OK** en klik vervolgens op **Uitvoeren** om uw taak te starten.

    ![Taakrun 3 starten](./media/storsimple-data-manager-ui/start-job-run3.png)

4. Om deze taak te controleren, ga je naar **Vacatures** in je StorSimple Data Manager. Naast controle in het **blade Van Jobs,** u ook luisteren in de opslagwachtrij waar een bericht wordt toegevoegd telkens wanneer een bestand wordt verplaatst van StorSimple naar het opslagaccount.

    ![Taakloop starten 4](./media/storsimple-data-manager-ui/start-job-run4.png)

### <a name="view-logs-after-job-completion"></a>Logboeken weergeven na voltooiing van de taak

Na het voltooien van een taak u de status van de taak bekijken. Functiestatus kan worden **opgevolgd,** **gedeeltelijk geslaagd** en **mislukt**. U de lijst met bestanden bekijken die zijn gekopieerd en bestanden die niet zijn gekopieerd. Deze lijsten zijn beschikbaar in een container genaamd **"storsimple-data-manager-joblogs"** binnen uw doelopslagaccount. In deze container u zoeken naar een map met dezelfde naam als uw taakdefinitie. Hierin wordt een map gemaakt voor elke taakrun die uw lijsten bevat. De naam van deze map is de GUID van de taak, die u krijgen van de pagina taakgegevens. Als alternatief ziet u in de meeste gevallen een koppeling voor de kopieerlogboeken binnen de banenpagina zelf.
Er zijn 2 set csv-bestanden die u in deze map ziet. Alle bestanden die beginnen met **gekopieerde bestandslijst ...** bevat de lijst met met succes gekopieerde bestanden. Alle bestanden die beginnen met **mislukte filelist ...** bevatten bestanden die niet konden worden gekopieerd, samen met een foutmelding.


## <a name="next-steps"></a>Volgende stappen

[Gebruik .NET SDK om StorSimple Data Manager-taken te starten.](storsimple-data-manager-dotnet-jobs.md)
