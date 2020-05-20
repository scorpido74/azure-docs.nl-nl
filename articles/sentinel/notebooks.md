---
title: Notebooks gebruiken met Azure Sentinel voor beveiligings jacht
description: In dit artikel wordt beschreven hoe u notebooks gebruikt met de Azure Sentinel-jacht-mogelijkheden.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/25/2019
ms.openlocfilehash: bf63d5c8cb46fd791508af40dcefd7b39d4ba9de
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83652026"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>Jupyter-notebooks gebruiken om te zoeken naar beveiligings Risico's

De basis van Azure Sentinel is het gegevens archief. de oplossing biedt een combi natie van hoge prestaties voor query's, dynamische schema's en schalen naar enorme gegevens volumes. De Azure Portal en alle Azure Sentinel-hulpprogram ma's gebruiken een gemeen schappelijke API voor toegang tot deze gegevens opslag. Dezelfde API is ook beschikbaar voor externe hulpprogram ma's, zoals [Jupyter](https://jupyter.org/) -notebooks en python. Hoewel veel algemene taken kunnen worden uitgevoerd in de portal, breidt Jupyter het bereik uit van wat u met deze gegevens kunt doen. Hiermee wordt volledige programmeer baarheid gecombineerd met een enorme verzameling bibliotheken voor machine learning, visualisatie en gegevens analyse. Deze kenmerken maken het Jupyter een fascinerend hulp programma voor beveiligings onderzoek en jacht.

![voor beeld van notebook](./media/notebooks/sentinel-notebooks-map.png)

We hebben de Jupyter-ervaring geïntegreerd in de Azure Portal, waardoor u eenvoudig notitie blokken kunt maken en uitvoeren om uw gegevens te analyseren. De *Kqlmagic* -bibliotheek biedt de lijm waarmee u query's kunt uitvoeren vanuit Azure Sentinel en deze rechtstreeks in een notebook uitvoert. Query's gebruiken de [Kusto-query taal](https://kusto.azurewebsites.net/docs/query/index.html). Verschillende notebooks, ontwikkeld door enkele van de beveiligings analisten van micro soft, worden geleverd met Azure Sentinel. Sommige van deze notitie blokken zijn gebouwd voor een specifiek scenario en kunnen worden gebruikt als-is. Andere zijn bedoeld als voor beelden voor het illustreren van technieken en functies die u kunt kopiëren of aanpassen voor gebruik in uw eigen notitie blokken. Andere notitie blokken kunnen ook worden geïmporteerd vanuit de Azure Sentinel Community GitHub.

De geïntegreerde Jupyter-ervaring maakt gebruik van [Azure notebooks](https://notebooks.azure.com/) om notebooks op te slaan, te delen en uit te voeren. U kunt deze notitie blokken ook lokaal uitvoeren als u een python-omgeving hebt en Jupyter op uw computer of in andere JupterHub-omgevingen, zoals Azure Databricks.

Notebooks hebben twee onderdelen:

- De browser interface waar u query's en code invoert en uitvoert, en waar de resultaten van de uitvoering worden weer gegeven.
- Een *kernel* die verantwoordelijk is voor het parseren en uitvoeren van de code zelf. 

In Azure Notebooks wordt deze kernel standaard uitgevoerd op de *gratis Cloud Compute en Storage*van Azure. Als uw Notebooks complexe machine learning modellen of visualisaties bevatten, kunt u overwegen om krachtige, toegewezen reken resources zoals [Data Science virtual machines](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) (DSVM) te gebruiken. Notitie blokken in uw account worden privé bewaard, tenzij u ervoor kiest om deze te delen.

De Azure Sentinel-notebooks gebruiken veel populaire python-Bibliotheken, zoals Panda, matplotlib, bokeh en anderen. Er zijn een groot aantal andere Python-pakketten waaruit u kunt kiezen. Dit omvat gebieden zoals:

- Visualisaties en afbeeldingen
- Gegevens verwerking en-analyse
- Statistieken en numerieke computing
- Machine learning en diep gaande lessen

Daarnaast hebben we een aantal open-source Jupyter-beveiligings hulpprogramma's uitgebracht in een pakket met de naam [msticpy](https://github.com/Microsoft/msticpy/). Dit pakket wordt gebruikt in veel van de meegeleverde notitie blokken. Msticpy-hulpprogram ma's zijn speciaal ontworpen voor het maken van notitie blokken voor jacht en onderzoek en we werken op dit moment met nieuwe functies en verbeteringen.

De eerste notitie blokken bevatten:

- **Begeleid onderzoek-proces waarschuwingen**: Hiermee kunt u snel waarschuwingen sorteren door de activiteit op de betrokken host of hosts te analyseren.
- **Begeleide jacht-Windows host Explorer**: Hiermee kunt u de account activiteit, uitvoeringen van processen, netwerk activiteit en andere gebeurtenissen op een host verkennen.
- **Begeleide jacht-Office365**: zoeken naar verdachte Office 365-activiteiten in meerdere Office 365-gegevens sets.

De [Azure Sentinel Community github-opslag plaats](https://github.com/Azure/Azure-Sentinel) is de locatie voor toekomstige Azure-Sentinel-notebooks die door micro soft zijn gebouwd of die zijn bijgedragen van de community.

Als u de notitie blokken wilt gebruiken, moet u een Azure Notebooks-account hebben. Zie [Quick Start: Meld u aan en stel een gebruikers-id](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks) in vanuit de Azure notebooks documentatie voor meer informatie. Als u dit account wilt maken, kunt u de optie **registreren voor Azure notebooks** gebruiken vanaf de opdracht balk in **Azure Sentinel-notebooks**:

> [!div class="mx-imgBorder"]
>![Registreren voor Azure Notebooks optie](./media/notebooks/sentinel-azure-sign-up-azure-notebooks.png)

U kunt een notitie blok direct vanuit Azure Sentinel uitvoeren of alle Azure Sentinel-notebooks klonen naar een nieuw Azure Notebooks-project.

## <a name="run-a-notebook-from-azure-sentinel"></a>Een notitie blok uitvoeren vanuit Azure Sentinel
 
1. Ga vanuit het Azure Portal naar **Azure Sentinel**  >  **Threat Management**  >  -**notebooks**, waar u notitie blokken kunt zien die Azure Sentinel biedt. 

2. Selecteer afzonderlijke notitie blokken om de bijbehorende beschrijvingen, vereiste gegevens typen en gegevens bronnen te lezen. Bijvoorbeeld:
    
    > [!div class="mx-imgBorder"]
    > ![notitie blok starten](./media/notebooks/sentinel-azure-notebooks-launch.png)

3. Selecteer het notitie blok dat u wilt gebruiken en selecteer vervolgens **notebook starten (preview)** om het notitie blok te klonen en te configureren in een nieuw Azure notebooks project dat verbinding maakt met uw Azure Sentinel-werk ruimte. Wanneer het proces is voltooid, wordt het notitie blok in Azure Notebooks geopend, zodat u het kunt uitvoeren.

## <a name="clone-azure-sentinel-notebooks-to-a-new-azure-notebooks-project"></a>Azure Sentinel notebooks klonen voor een nieuw Azure Notebooks project

Met deze procedure maakt u een Azure Notebooks project voor u, dat de Azure Sentinel-notebooks bevat. U kunt de notitie blokken vervolgens uitvoeren als-is of wijzigingen aanbrengen en vervolgens uitvoeren.

1. Navigeer vanuit het Azure Portal naar **Azure Sentinel**  >  **Threat Management**-  >  **notebooks** en selecteer **notebooks klonen** in de opdracht balk:
  
    > [!div class="mx-imgBorder"]
    >![Optie notebooks klonen](./media/notebooks/sentinel-azure-clone-notebooks.png)

2. Wanneer het volgende dialoog venster wordt weer gegeven, selecteert u **importeren** om de GitHub opslag plaats te klonen in uw Azure notebooks-project. Als u geen bestaand Azure Notebooks account hebt, wordt u gevraagd om er een te maken en u aan te melden.

   ![Notitie blok importeren](./media/notebooks/sentinel-notebooks-clone.png)

3. In het dialoog venster **github-opslag plaats uploaden** selecteert u **recursief klonen** , omdat deze optie verwijst naar gekoppelde github opslag plaatsen. Voor de project naam gebruikt u de standaard naam of typt u een nieuwe. Klik vervolgens op **importeren** om te beginnen met het klonen van de GitHub-inhoud. Dit kan enkele minuten duren.

   ![Notitie blok importeren](./media/notebooks/sentinel-create-project.png)

4. Open het project dat u zojuist hebt gemaakt en open vervolgens de map **notitie blokken** om de notitie blokken weer te geven. Bijvoorbeeld:

   ![Opslag plaats importeren](./media/notebooks/sentinel-open-notebook1.png)

U kunt de notitie blokken vervolgens uit Azure Notebooks uitvoeren. Als u wilt terugkeren naar deze notitie blokken vanuit Azure Sentinel, selecteert **u naar uw notitie blokken gaan** vanaf de opdracht balk in **Azure Sentinel-notebooks**:

> [!div class="mx-imgBorder"]
>![Ga naar uw notitieblok opties](./media/notebooks/sentinel-azure-to-go-notebooks.png)


## <a name="using-notebooks-to-hunt"></a>Notitie blokken gebruiken om te jacht

Elk notebook begeleidt u stapsgewijs door de stappen voor het uitvoeren van een opsporing of onderzoek. Bibliotheken en andere afhankelijkheden die nodig zijn voor de notebook, kunnen worden geïnstalleerd vanuit het notitie blok zelf of via een eenvoudige configuratie procedure. De configuratie die uw notitieblok project aan uw Azure Sentinel-abonnement vastmaakt, wordt automatisch in de voor gaande stappen ingericht.

1. Als u zich nog niet in Azure Notebooks bevindt, kunt u de optie **Ga naar uw notitie blokken** gebruiken vanaf de opdracht balk in **Azure Sentinel-notebooks**:
    
    > [!div class="mx-imgBorder"]
    >![Ga naar uw notitieblok opties](./media/notebooks/sentinel-azure-to-go-notebooks.png)
    
    In Azure Notebooks selecteert u **Mijn projecten**, vervolgens het project met de Azure Sentinel-notebooks en tot slot de map met **notitie blokken** .
    
2. Houd er rekening mee dat u bij het openen van een notitie blok standaard gratis Compute hebt geselecteerd voor het uitvoeren van de notitie blokken:
    
   ![notitie Blok selecteren](./media/notebooks/sentinel-open-notebook2.png)
    
    Als u een Data Science Virtual Machines (DSVM) hebt geconfigureerd om te gebruiken zoals wordt uitgelegd in de inleiding, selecteert u de DSVM en authenticatie voordat u het eerste notitie blok opent. 

3. Selecteer een notitie blok om het te openen.
    
    De eerste keer dat u een notitie blok opent, wordt u mogelijk gevraagd om een versie van de kernel te selecteren. Als u niet wordt gevraagd, selecteert u **de kernel-versie van kernel**  >   **Change kernel**en selecteert u vervolgens een versie die ten minste 3,6 is. De geselecteerde versie van de kernel wordt weer gegeven in de rechter bovenhoek van het notitieblok venster:
    
   ![notitie Blok selecteren](./media/notebooks/sentinel-select-kernel.png)

4. Voordat u wijzigingen aanbrengt in een notitie blok dat u hebt gedownload, is het een goed idee om een kopie van het oorspronkelijke notitie blok te maken en te werken aan de kopie. Hiertoe selecteert u **bestand**  >  **een kopie maken**. Door aan de slag te gaan, kunt u in toekomstige versies van notitie blokken veilig bijwerken zonder uw gegevens te overschrijven.
    
    U bent nu klaar om het geselecteerde notitie blok uit te voeren of te bewerken.

Vereisten

- Voor een snelle inleiding op het uitvoeren van query's in azure Sentinel, kijkt u naar de [GetStarted](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/345cf9f7c8f6137f5af4593a3f9d7568acd6cbc2/DeprecatedNotebooks/Get%20Started.ipynb) -notebook in de hoofdmap van de map met **notitie blokken** . 

- U vindt aanvullende voor beelden van notitie blokken in de submap voor **beeld-notebooks** . Deze voorbeeld notitieblokken zijn met gegevens opgeslagen, zodat de beoogde uitvoer gemakkelijker te zien is. U wordt aangeraden deze notitie blokken weer te geven in [nbviewer](https://nbviewer.jupyter.org/). 

- De map **HOWTOs** bevat notebooks, zoals het instellen van de standaard Python-versie, het configureren van een DSVM, het maken van Azure Sentinel-blad wijzers van een notitie blok en andere onderwerpen.

De meegeleverde notebooks zijn bedoeld als zowel nuttige hulpprogram ma's als illustraties en code voorbeelden die u kunt gebruiken in de ontwikkeling van uw eigen notitie blokken.

We geven feedback, of suggesties, aanvragen voor functies, bijgedragen notitie blokken, fout rapporten of verbeteringen en toevoegingen aan bestaande notitie blokken. Ga naar de [Azure Sentinel Community github](https://github.com/Azure/Azure-Sentinel) om een probleem op te lossen of Fork te maken en een bijdrage te uploaden.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u aan de slag kunt met Jupyter-notebooks in azure Sentinel. Raadpleeg de volgende artikelen voor meer informatie over Azure Sentinel:

- [Proactief zoeken naar bedreigingen](hunting.md)
- [Gebruik blad wijzers voor het opslaan van interessante informatie tijdens het jacht](bookmarks.md)
