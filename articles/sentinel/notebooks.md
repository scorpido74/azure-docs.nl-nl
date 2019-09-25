---
title: Jacht-mogelijkheden met behulp van notitie blokken in azure Sentinel | Microsoft Docs
description: In dit artikel wordt beschreven hoe u notebooks gebruikt met de Azure Sentinel-jacht-mogelijkheden.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 5b90ecc1db686b698668b07bd839304b425445ca
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240531"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>Jupyter-notebooks gebruiken om te zoeken naar beveiligings Risico's

De basis van Azure Sentinel is het gegevens archief. de oplossing biedt een combi natie van hoge prestaties voor query's, dynamische schema's en schalen naar enorme gegevens volumes. De Azure Sentinel-Portal en alle Azure Sentinel-hulpprogram ma's gebruiken een gemeen schappelijke API voor toegang tot deze gegevens opslag. Dezelfde API is ook beschikbaar voor externe hulpprogram ma's, zoals [Jupyter](https://jupyter.org/) -notebooks en python. Hoewel veel algemene taken kunnen worden uitgevoerd in de portal, breidt Jupyter het bereik uit van wat u met deze gegevens kunt doen. Hiermee wordt volledige programmeer baarheid gecombineerd met een enorme verzameling bibliotheken voor machine learning, visualisatie en gegevens analyse. Deze kenmerken maken het Jupyter een fascinerend hulp programma voor beveiligings onderzoek en jacht.

![Voor beeld van notebook](./media/notebooks/sentinel-notebooks-map.png)

We hebben de Jupyter-ervaring geïntegreerd in de Azure-Sentinel-Portal, waardoor u eenvoudig notitie blokken kunt maken en uitvoeren om uw gegevens te analyseren. De *Kqlmagic* -bibliotheek biedt de lijm waarmee u query's kunt uitvoeren vanuit Azure Sentinel en deze rechtstreeks in een notebook uitvoert. Query's gebruiken de [Kusto-query taal](https://kusto.azurewebsites.net/docs/query/index.html). Verschillende notebooks, ontwikkeld door enkele van de beveiligings analisten van micro soft, worden geleverd met Azure Sentinel. Sommige van deze notitie blokken zijn gebouwd voor een specifiek scenario en kunnen worden gebruikt als-is. Andere zijn bedoeld als voor beelden voor het illustreren van technieken en functies die u kunt kopiëren of aanpassen voor gebruik in uw eigen notitie blokken. Andere notitie blokken kunnen ook worden geïmporteerd vanuit de Azure Sentinel Community GitHub.

De geïntegreerde Jupyter-ervaring maakt gebruik van [Azure notebooks](https://notebooks.azure.com/) om notebooks op te slaan, te delen en uit te voeren. U kunt deze notitie blokken ook lokaal uitvoeren (als u een python-omgeving hebt en Jupyter op uw computer) of in andere JupterHub-omgevingen, zoals Azure Databricks.

Notebooks hebben twee onderdelen:

- de browser interface waar u query's en code invoert en uitvoert, en waar de resultaten van de uitvoering worden weer gegeven.
- een *kernel* die verantwoordelijk is voor het parseren en uitvoeren van de code zelf. 

In Azure Notebooks wordt deze kernel standaard uitgevoerd op Azure *Free Cloud Compute en Storage* . Als uw Notebooks complexe machine learning modellen of visualisaties bevatten, kunt u overwegen om krachtige, toegewezen reken resources zoals [Data Science virtual machines](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) (DSVM) te gebruiken. Notitie blokken in uw account worden privé bewaard, tenzij u ervoor kiest om deze te delen.

De Azure Sentinel-notebooks gebruiken veel populaire python-Bibliotheken, zoals Panda, matplotlib, bokeh en anderen. Er zijn een groot aantal andere Python-pakketten waaruit u kunt kiezen. Dit omvat gebieden zoals:

- visualisaties en afbeeldingen
- gegevens verwerking en-analyse
- statistieken en numerieke computing
- machine learning en diep gaande lessen

Daarnaast hebben we een aantal open-source Jupyter-beveiligings hulpprogramma's uitgebracht in een pakket met de naam [msticpy](https://github.com/Microsoft/msticpy/). Dit pakket wordt gebruikt in veel van de meegeleverde notitie blokken. Msticpy-hulpprogram ma's zijn speciaal ontworpen voor het maken van notitie blokken voor jacht en onderzoek en we werken op dit moment met nieuwe functies en verbeteringen.

De eerste notitie blokken bevatten:

- **Begeleide onderzoeken-waarschuwingen verwerken**: Met kunt u waarschuwingen snel sorteren door de activiteit op de betrokken host (s) te analyseren.
- **Begeleide jacht-Windows host Explorer**: Hiermee kunt u de account activiteit, uitvoeringen van processen, netwerk activiteiten en andere gebeurtenissen op een host verkennen.  
- **Begeleide jacht-Office365**: Zoeken naar verdachte Office 365-activiteiten in meerdere O365-gegevens sets.

De [Azure Sentinel Community github-opslag plaats](https://github.com/Azure/Azure-Sentinel) is de locatie voor toekomstige Azure-Sentinel-notebooks die door micro soft zijn gebouwd of die zijn bijgedragen van de community.

## <a name="run-a-notebook"></a>Een notitie blok uitvoeren

In het volgende voor beeld maken we een Azure Notebooks-project van de Azure-Sentinel-Portal, waarin het project wordt gevuld met notitie blokken. Voordat u deze notitie blokken kunt gebruiken, is het een goed idee om een kopie van het notitie blok te maken en te werken met de kopie. Door aan de slag te gaan, kunt u in toekomstige versies van notitie blokken veilig bijwerken zonder uw gegevens te overschrijven.

1. Klik in de Azure-Sentinel-Portal op **notitie blokken** in het navigatie menu. Als u een nieuw Azure Notebooks project wilt maken, klikt u op **Azure Sentinel-notebooks klonen** of om uw bestaande notitieblok projecten te openen klikt **u op Ga naar uw notitie blokken**.
  
   ![notitie blokken selecteren](./media/notebooks/sentinel-azure-notebooks-home.png)

2. Als u **Azure Sentinel notebooks** in de vorige stap hebt gekozen, wordt het volgende dialoog venster weer gegeven. Klik op **importeren** om de GitHub opslag plaats te klonen in uw Azure notebooks-project. Als u geen bestaand Azure Notebooks account hebt, wordt u gevraagd om er een te maken en u aan te melden.

   ![Notitie blok importeren](./media/notebooks/sentinel-notebooks-clone.png)

3. Wanneer u een nieuw project maakt, moet u een naam voor het project opgeven, de standaard naam gebruiken of een nieuwe maken. Controleer niet de optie **recursief klonen** : deze optie verwijst naar gekoppelde github opslag plaatsen. Wanneer u op **importeren** klikt, wordt het klonen van de GitHub-inhoud gestart. Dit kan enkele minuten duren.

   ![Notitie blok importeren](./media/notebooks/sentinel-create-project.png)

4. Open de map met **notitie blokken** om de notitie blokken weer te geven. Elk notebook begeleidt u stapsgewijs door de stappen voor het uitvoeren van een opsporing of onderzoek. Bibliotheken en andere afhankelijkheden die nodig zijn voor de notebook, kunnen worden geïnstalleerd vanuit het notitie blok zelf of via een eenvoudige configuratie procedure. De configuratie die uw notitieblok project aan uw Azure Sentinel-abonnement vastmaakt, wordt automatisch in de voor gaande stappen ingericht. Uw Notebooks zijn klaar om te worden uitgevoerd met uw Azure Sentinel Log Analytics-werk ruimte.

   ![Opslag plaats importeren](./media/notebooks/sentinel-open-notebook1.png)

5. Open een notitie blok. Gratis Compute is standaard geselecteerd voor het uitvoeren van de notitie blokken (gemarkeerd). Als u een DSVM hebt geconfigureerd die u wilt gebruiken (zie hierboven), selecteert u de DSVM en verifieert u voordat u het eerste notitie blok opent. Klik op een notitie blok om het te openen.

   ![notitie Blok selecteren](./media/notebooks/sentinel-open-notebook2.png)

6. De python-versie selecteren. Wanneer u een notitie blok voor het eerst opent, wordt u mogelijk gevraagd om een versie van de kernel te selecteren. Als dat niet het geval is, selecteert u de kernel die u wilt gebruiken als volgt. Python 3,6 of hoger moet de geselecteerde kernel zijn (in de rechter bovenhoek van het venster voor het notitie blok).

   ![notitie Blok selecteren](./media/notebooks/sentinel-select-kernel.png)

Voor een snelle inleiding op het uitvoeren van query's in azure Sentinel, kijkt u naar de [GetStarted](https://github.com/Azure/Azure-Sentinel/blob/master/Notebooks/Get%20Started.ipynb) -notebook in de hoofdmap van de map met notitie blokken. Aanvullende voor beelden van notitie blokken vindt u in de submap voor **beeld-notebooks** . De voorbeeld notitieblokken zijn met gegevens opgeslagen, zodat de beoogde uitvoer gemakkelijker te zien is (we raden u aan deze weer te geven in [nbviewer](https://nbviewer.jupyter.org/)). De map **HOWTOs** bevat notebooks, zoals het instellen van de standaard Python-versie, het configureren van een DSVM, het maken van Azure Sentinel-blad wijzers van een notitie blok en andere onderwerpen.

Deze notebooks zijn bedoeld als zowel nuttige hulpprogram ma's als illustraties en code voorbeelden die u kunt gebruiken in de ontwikkeling van uw eigen notitie blokken.

We geven feedback, of suggesties, aanvragen voor functies, bijgedragen notitie blokken, fout rapporten of verbeteringen en toevoegingen aan bestaande notitie blokken. Ga naar de [Azure Sentinel Community github](https://github.com/Azure/Azure-Sentinel) om een probleem op te lossen of Fork te maken en een bijdrage te uploaden.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u aan de slag kunt met Jupyter-notebooks in azure Sentinel. Raadpleeg de volgende artikelen voor meer informatie over Azure Sentinel:

- [Proactief zoeken naar bedreigingen](hunting.md)
- [Gebruik blad wijzers voor het opslaan van interessante informatie tijdens het jacht](bookmarks.md)
