---
title: Notitieblokken gebruiken met Azure Sentinel voor beveiligingsjacht
description: In dit artikel wordt beschreven hoe u notitieblokken gebruikt met de Azure Sentinel-jachtmogelijkheden.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/25/2019
ms.openlocfilehash: 84b72a71ed2de910bce44b0c3f3309782f096680
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77581834"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>Jupyter-notitieblokken gebruiken om te zoeken naar beveiligingsbedreigingen

De basis van Azure Sentinel is het gegevensarchief; het combineert hoge prestaties query's, dynamisch schema en schalen tot enorme gegevensvolumes. De Azure-portal en alle Azure Sentinel-hulpprogramma's gebruiken een algemene API om toegang te krijgen tot dit gegevensarchief. Dezelfde API is ook beschikbaar voor externe hulpprogramma's zoals [Jupyter-notebooks](https://jupyter.org/) en Python. Hoewel veel voorkomende taken in het portaal kunnen worden uitgevoerd, breidt Jupyter het bereik uit van wat u met deze gegevens doen. Het combineert volledige programmeerbaarheid met een enorme verzameling bibliotheken voor machine learning, visualisatie en data-analyse. Deze attributen maken Jupyter een overtuigend instrument voor veiligheidsonderzoek en jacht.

![voorbeeldnotitieblok](./media/notebooks/sentinel-notebooks-map.png)

We hebben de Jupyter-ervaring geïntegreerd in de Azure-portal, waardoor u eenvoudig notitieblokken maken en uitvoeren om uw gegevens te analyseren. De *Kqlmagic-bibliotheek* biedt de lijm waarmee u query's van Azure Sentinel uitvoeren en deze rechtstreeks in een notitieblok uitvoeren. Query's gebruiken de [Kusto Querytaal](https://kusto.azurewebsites.net/docs/query/index.html). Verschillende notebooks, ontwikkeld door enkele beveiligingsanalisten van Microsoft, zijn verpakt met Azure Sentinel. Sommige van deze notitieblokken zijn gebouwd voor een specifiek scenario en kunnen worden gebruikt zoals het is. Andere zijn bedoeld als voorbeelden om technieken en functies te illustreren die u kopiëren of aanpassen voor gebruik in uw eigen notitieblokken. Andere notitieblokken kunnen ook worden geïmporteerd uit de GitHub van de Azure Sentinel-community.

De geïntegreerde Jupyter-ervaring maakt gebruik van [Azure-notitieblokken](https://notebooks.azure.com/) om notitieblokken op te slaan, te delen en uit te voeren. U deze notitieblokken ook lokaal uitvoeren als u een Python-omgeving en Jupyter op uw computer hebt, of in andere JupterHub-omgevingen zoals Azure Databricks.

Notitieblokken hebben twee componenten:

- De browserinterface waar u query's en code invoert en uitvoert en waar de resultaten van de uitvoering worden weergegeven.
- Een *kernel* die verantwoordelijk is voor het ontwijsen en uitvoeren van de code zelf. 

In Azure-notitieblokken wordt deze kernel standaard uitgevoerd op Azure *Free Cloud Compute and Storage.* Als uw notitieblokken complexe machine learning-modellen of visualisaties bevatten, u overwegen krachtigere, specifiekere rekenbronnen te gebruiken, zoals [DSVM (Data Science Virtual Machines).](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) Notitieblokken in uw account worden privé gehouden, tenzij u ervoor kiest ze te delen.

De Azure Sentinel-notitieblokken maken gebruik van veel populaire Python-bibliotheken, zoals panda's, matplotlib, bokeh en anderen. Er zijn een groot aantal andere Python-pakketten voor u om uit te kiezen, die gebieden zoals:

- Visualisaties en afbeeldingen
- Gegevensverwerking en -analyse
- Statistieken en numerieke computing
- Machine learning en deep learning

We hebben ook een aantal open-source Jupyter security tools in een pakket genaamd [msticpy](https://github.com/Microsoft/msticpy/). Dit pakket wordt gebruikt in veel van de meegeleverde notebooks. Msticpy-tools zijn speciaal ontworpen om te helpen bij het maken van notitieblokken voor jacht en onderzoek en we werken actief aan nieuwe functies en verbeteringen.

De eerste notitieblokken zijn:

- **Rondleidingen - Proceswaarschuwingen:** Hiermee u snel waarschuwingen triage door het analyseren van de activiteit op de getroffen host of hosts.
- **Begeleide jacht - Windows Host Explorer:** Hiermee u accountactiviteit, procesuitvoeringen, netwerkactiviteit en andere gebeurtenissen op een host verkennen.
- Rondleiding en rondleiding **- Office365-Exploring:** Zoeken naar verdachte Office 365-activiteiten in meerdere Office 365-gegevenssets.

De [GitHub-repository van de Azure Sentinel-community](https://github.com/Azure/Azure-Sentinel) is de locatie voor toekomstige Azure Sentinel-notebooks die door Microsoft zijn gebouwd of vanuit de community zijn bijgedragen.

Als u de notitieblokken wilt gebruiken, moet u een Azure Notebooks-account hebben. Zie [Snelstart: Meld u aan en stel een gebruikersnaam in](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks) vanuit de documentatie van Azure Notebooks voor meer informatie. Als u dit account wilt maken, u de optie **Aanmelden voor Azure-notitieblokken** gebruiken via de opdrachtbalk in **Azure Sentinel - Notitieblokken:**

> [!div class="mx-imgBorder"]
>![Aanmelden voor de optie Azure Notebooks](./media/notebooks/sentinel-azure-sign-up-azure-notebooks.png)

U een notitieblok rechtstreeks vanuit Azure Sentinel uitvoeren of alle Azure Sentinel-notitieblokken klonen naar een nieuw Azure Notebooks-project.

## <a name="run-a-notebook-from-azure-sentinel"></a>Een notitieblok uitvoeren vanuit Azure Sentinel
 
1. Navigeer vanuit de Azure-portal naar **Azure Sentinel Threat** > **Management** > **Notebooks,** waar u notitieblokken zien die Azure Sentinel biedt. 

2. Selecteer afzonderlijke notitieblokken om hun beschrijvingen, vereiste gegevenstypen en gegevensbronnen te lezen. Bijvoorbeeld:
    
    > [!div class="mx-imgBorder"]
    > ![notitieblok starten](./media/notebooks/sentinel-azure-notebooks-launch.png)

3. Selecteer het notitieblok dat u wilt gebruiken en selecteer **Vervolgens Notitieblok starten (voorbeeld)** om het notitieblok te klonen en te configureren in een nieuw Azure Notebooks-project dat verbinding maakt met uw Azure Sentinel-werkruimte. Wanneer het proces is voltooid, wordt het notitieblok geopend in Azure-notitieblokken, waar u uitvoeren.

## <a name="clone-azure-sentinel-notebooks-to-a-new-azure-notebooks-project"></a>Azure Sentinel-notitieblokken klonen naar een nieuw Azure Notebooks-project

Met deze procedure wordt een Azure Notebooks-project voor u gemaakt, dat de Azure Sentinel-notitieblokken bevat. U de notitieblokken vervolgens uitvoeren zoals deze is, of er wijzigingen in aanbrengen en ze vervolgens uitvoeren.

1. Navigeer in de **Azure-portal**naar Azure Sentinel > **Threat Management** > **Notebooks** en selecteer **Vervolgens Kloonnotitieblokken** in de opdrachtbalk:
  
    > [!div class="mx-imgBorder"]
    >![Kloonnotitieblokken, optie](./media/notebooks/sentinel-azure-clone-notebooks.png)

2. Wanneer het volgende dialoogvenster wordt weergegeven, selecteert u **Importeren** om de GitHub-repo te klonen in uw Azure-notitieblokkenproject. Als u geen bestaand Azure Notebooks-account hebt, wordt u gevraagd er een te maken en u aan te melden.

   ![Notitieblok importeren](./media/notebooks/sentinel-notebooks-clone.png)

3. Selecteer in het dialoogvenster **GitHub-opslagplaats uploaden** niet opnieuw **cursief** klonen omdat deze optie verwijst naar gekoppelde GitHub-repo's. Gebruik voor de projectnaam de standaardnaam of typ in een nieuwe naam. Klik vervolgens op **Importeren** om te beginnen met het klonen van de GitHub-inhoud, wat enkele minuten kan duren.

   ![Notitieblok importeren](./media/notebooks/sentinel-create-project.png)

4. Open het project dat u zojuist hebt gemaakt en open de map **Notitieblokken** om de notitieblokken weer te geven. Bijvoorbeeld:

   ![Repo importeren](./media/notebooks/sentinel-open-notebook1.png)

U de notitieblokken vervolgens uitvoeren vanuit Azure-notitieblokken. Als u vanuit Azure Sentinel naar deze notitieblokken wilt terugkeren, selecteert **u Naar uw notitieblokken** gaan via de opdrachtbalk in **Azure Sentinel - Notitieblokken:**

> [!div class="mx-imgBorder"]
>![Ga naar de optie Notitieblokken](./media/notebooks/sentinel-azure-to-go-notebooks.png)


## <a name="using-notebooks-to-hunt"></a>Notitieblokken gebruiken om te jagen

Elke notebook loopt u door de stappen voor het uitvoeren van een jacht of onderzoek. Bibliotheken en andere afhankelijkheden die het notitieblok nodig heeft, kunnen worden geïnstalleerd vanaf het notitieblok zelf of via een eenvoudige configuratieprocedure. De configuratie die uw notebookproject koppelt aan uw Azure Sentinel-abonnement, wordt automatisch in de voorgaande stappen ingericht.

1. Als u zich nog niet in Azure-notitieblokken bevindt, u de optie **Ga naar uw notitieblokken** gebruiken vanaf de opdrachtbalk in **Azure Sentinel - Notitieblokken:**
    
    > [!div class="mx-imgBorder"]
    >![Ga naar de optie Notitieblokken](./media/notebooks/sentinel-azure-to-go-notebooks.png)
    
    Selecteer in Azure-notitieblokken **Mijn projecten,** vervolgens het project dat de Azure Sentinel-notitieblokken bevat en ten slotte de map **Notitieblokken.**
    
2. Voordat u een notitieblok opent, moet u er rekening mee houden dat Vrije Gegevenskracht standaard is geselecteerd om de notitieblokken uit te voeren:
    
   ![notitieblok selecteren](./media/notebooks/sentinel-open-notebook2.png)
    
    Als u een DSVM (Data Science Virtual Machines) hebt geconfigureerd om te gebruiken zoals uitgelegd in de inleiding, selecteert u de DSVM en verifieert u voordat u het eerste notitieblok opent. 

3. Selecteer een notitieblok om het te openen.
    
    De eerste keer dat u een notitieblok opent, wordt u mogelijk gevraagd een kernelversie te selecteren. Als u niet wordt gevraagd, u de kernelversie selecteren in >  **kernelchangekernel**en vervolgens een versie selecteren die ten minste 3.6 is. **Kernel** De geselecteerde kernelversie wordt rechtsboven in het notitieblokvenster weergegeven:
    
   ![notitieblok selecteren](./media/notebooks/sentinel-select-kernel.png)

4. Voordat u wijzigingen aanbrengt in het notitieblok dat u hebt gedownload, is het een goed idee om een kopie van het originele notitieblok te maken en aan de kopie te werken. Selecteer Hiervoor **Bestand** > **Een kopie maken**. Met het werken aan kopieën u veilig updaten naar toekomstige versies van notitieblokken zonder uw gegevens te overschrijven.
    
    U bent nu klaar om het geselecteerde notitieblok uit te voeren of te bewerken.

Aanbevelingen:

- Voor een snelle inleiding tot het opvragen van gegevens in Azure Sentinel, kijkt u naar het [GetStarted-notitieblok](https://github.com/Azure/Azure-Sentinel/blob/master/Notebooks/Get%20Started.ipynb) in de map **Hoofdnotitieblokken.** 

- U vindt extra voorbeeldnotitieblokken in de submap **Voorbeeldnotitieblok's.** Deze voorbeeldnotitieblokken zijn opgeslagen met gegevens, zodat de beoogde uitvoer gemakkelijker kan worden weergegeven. We raden u aan deze notitieblokken in [nbviewer te bekijken.](https://nbviewer.jupyter.org/) 

- De **map HowTos** bevat notitieblokken die bijvoorbeeld beschrijven: U instellen in de standaardPython-versie, het configureren van een DSVM, het maken van Azure Sentinel-bladwijzers vanuit een notitieblok en andere onderwerpen.

De meegeleverde notitieblokken zijn bedoeld als zowel nuttige hulpmiddelen als als illustraties en codevoorbeelden die u gebruiken bij de ontwikkeling van uw eigen notitieblokken.

We zijn blij met feedback, of het nu gaat om suggesties, verzoeken om functies, bijgedragen notitieblokken, bugrapporten of verbeteringen en toevoegingen aan bestaande notitieblokken. Ga naar de [GitHub van de Azure Sentinel-community](https://github.com/Azure/Azure-Sentinel) om een probleem of vork te maken en een bijdrage te uploaden.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u aan de slag met Jupyter-notitieblokken in Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:

- [Proactief op zoek naar bedreigingen](hunting.md)
- [Gebruik bladwijzers om interessante informatie op te slaan tijdens de jacht](bookmarks.md)
