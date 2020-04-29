---
title: Resource logboeken verzamelen van een Azure-resource en analyseren met Azure Monitor
description: Zelf studie voor het configureren van diagnostische instellingen om bron logboeken te verzamelen van een Azure-resource in een Log Analytics-werk ruimte, waar ze kunnen worden geanalyseerd met een logboek query.
ms.subservice: ''
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 12/15/2019
ms.openlocfilehash: d356042d65c419163de4951e64a635a22ea90e6d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "78269192"
---
# <a name="tutorial-collect-and-analyze-resource-logs-from-an-azure-resource"></a>Zelf studie: resource logboeken verzamelen en analyseren vanuit een Azure-resource

Bron logboeken bieden inzicht in de gedetailleerde werking van een Azure-resource en zijn nuttig voor het bewaken van hun status en beschik baarheid. Azure-resources genereren automatisch resource logboeken, maar u moet configureren waar ze moeten worden verzameld. In deze zelf studie wordt u begeleid bij het maken van een diagnostische instelling voor het verzamelen van resource logboeken voor een resource in uw Azure-abonnement en het analyseren van de gegevens met een logboek query.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Log Analytics-werk ruimte maken in Azure Monitor
> * Een diagnostische instelling maken om bron logboeken te verzamelen 
> * Een eenvoudige logboek query maken voor het analyseren van Logboeken


## <a name="prerequisites"></a>Vereisten

Voor het volt ooien van deze zelf studie hebt u een Azure-resource nodig om te bewaken. U kunt elke resource in uw Azure-abonnement gebruiken die Diagnostische instellingen ondersteunt. Als u wilt bepalen of een resource Diagnostische instellingen ondersteunt, gaat u naar het menu van het Azure Portal en controleert u of er een **Diagnostische instellingen** optie is in de sectie **bewaking** van het menu.


## <a name="log-in-to-azure"></a>Meld u aan bij Azure.
Meld u aan bij de Azure Portal [https://portal.azure.com](https://portal.azure.com)op.


## <a name="create-a-workspace"></a>Een werkruimte maken
Met een Log Analytics-werk ruimte in Azure Monitor worden logboek gegevens uit verschillende bronnen verzameld en geïndexeerd en kunnen geavanceerde analyses worden gebruikt met een krachtige query taal. De Log Analytics-werk ruimte moet bestaan voordat u een diagnostische instelling kunt maken om gegevens te verzenden. U kunt een bestaande werk ruimte in uw Azure-abonnement gebruiken of er een maken met de volgende procedure. 

> [!NOTE]
> U kunt werken met gegevens in Log Analytics werk ruimten in het menu **Azure monitor** door werk ruimten te maken en te beheren in het menu **log Analytics-werk ruimten** .

1. Selecteer in **alle services** **log Analytics-werk ruimten**.
2. Klik boven aan het scherm op **toevoegen** en geef de volgende details op voor de werk ruimte:
   - **Log Analytics werk ruimte**: naam voor de nieuwe werk ruimte. Deze naam moet globaal uniek zijn in alle Azure Monitor-abonnementen.
   - **Abonnement**: Selecteer het abonnement om de werk ruimte op te slaan. Dit hoeft niet hetzelfde abonnement te zijn als de resource die wordt bewaakt.
   - **Resource groep**: Selecteer een bestaande resource groep of klik op **Nieuw maken** om een nieuwe te maken. Dit hoeft niet dezelfde resource groep te zijn als de resource die wordt bewaakt.
   - **Locatie**: Selecteer een Azure-regio of maak een nieuwe. Dit hoeft niet dezelfde locatie te zijn als de bron die wordt bewaakt.
   - **Prijs categorie**: Selecteer *betalen per gebruik* als prijs categorie. U kunt deze prijs categorie later wijzigen. Klik op de **log Analytics prijs** koppeling voor meer informatie over de verschillende prijs categorieën.

    ![Nieuwe werkruimte](media/tutorial-resource-logs/new-workspace.png)

3. Klik op **OK** om de werk ruimte te maken.

## <a name="create-a-diagnostic-setting"></a>Een diagnostische instelling maken
[Diagnostische instellingen](../platform/diagnostic-settings.md) bepalen waar bron logboeken voor een bepaalde resource moeten worden verzonden. Eén diagnostische instelling kan meerdere [bestemmingen](../platform/diagnostic-settings.md#destinations)hebben, maar in deze zelf studie wordt alleen een log Analytics-werk ruimte gebruikt.

1. Selecteer **Diagnostische instellingen**in het gedeelte **bewaking** van het menu van de resource.
2. U moet een bericht ' geen diagnostische instellingen gedefinieerd ' hebben. Klik op **Diagnostische instelling toevoegen**.

    ![Diagnostische instellingen](media/tutorial-resource-logs/diagnostic-settings.png)

3. Elke diagnostische instelling heeft drie basis onderdelen:
 
   - **Naam**: dit heeft geen significant effect en moet alleen voor u een beschrijving zijn.
   - **Doelen**: een of meer bestemmingen voor het verzenden van de logboeken. Alle Azure-Services delen dezelfde set van drie mogelijke bestemmingen. Elke diagnostische instelling kan een of meer bestemmingen definiëren, maar niet meer dan één bestemming van een bepaald type. 
   - **Categorieën**: logboeken die moeten worden verzonden naar elke bestemming. De set categorieën varieert per Azure-service.

4. Selecteer **verzenden naar log Analytics werk ruimte** en selecteer vervolgens de werk ruimte die u hebt gemaakt.
5. Selecteer de categorieën die u wilt verzamelen. Raadpleeg de documentatie voor elke service voor een definitie van de beschik bare categorieën.

    ![Diagnostische instelling](media/tutorial-resource-logs/diagnostic-setting.png)

6. Klik op **Opslaan** om de diagnostische instellingen op te slaan.

    
 
 ## <a name="use-a-log-query-to-retrieve-logs"></a>Een logboek query gebruiken om logboeken op te halen
Gegevens worden opgehaald uit een Log Analytics-werk ruimte met behulp van een logboek query die is geschreven in Kusto query language (KQL). Inzichten en oplossingen in Azure Monitor bieden logboek query's om gegevens voor een bepaalde service op te halen, maar u kunt rechtstreeks werken met logboek query's en de resultaten daarvan in de Azure Portal met Log Analytics. 

1. Selecteer **Logboeken**onder de sectie **bewaking** van het menu van de resource.
2. Log Analytics wordt geopend met een leeg query venster met het bereik dat is ingesteld op uw resource. Alle query's bevatten alleen records van die resource.

    > [!NOTE]
    > Als u Logboeken vanuit het Azure Monitor menu hebt geopend, wordt de scope ingesteld op de Log Analytics-werk ruimte. In dit geval bevatten alle query's alle records in de werk ruimte.
   
    ![Logboeken](media/tutorial-resource-logs/logs.png)

4. Met de service die in het voor beeld wordt weer gegeven, worden bron logboeken naar de tabel **AzureDiagnostics** geschreven, maar andere services kunnen naar andere tabellen schrijven. Zie [ondersteunde services, schema's en categorieën voor Azure-resource logboeken](../platform/diagnostic-logs-schema.md) voor tabellen die worden gebruikt door verschillende Azure-Services.

    > [!NOTE]
    > Meerdere services schrijven resource logboeken naar de tabel AzureDiagnostics. Als u Log Analytics start vanuit het menu Azure Monitor, moet u een `where` instructie toevoegen met de `ResourceProvider` kolom om uw specifieke service op te geven. Wanneer u Log Analytics start vanuit het menu van een resource, wordt de scope ingesteld op alleen records van deze resource, zodat deze kolom niet vereist is. Raadpleeg de documentatie van de service voor voorbeeld query's.


5. Typ een query en klik op **uitvoeren** om de resultaten te controleren. 
6. Zie [aan de slag met logboek query's in azure monitor](../log-query/get-started-queries.md) voor een zelf studie over het schrijven van logboek query's.

    ![Logboek query](media/tutorial-resource-logs/log-query-1.png)




## <a name="next-steps"></a>Volgende stappen
Nu u hebt geleerd hoe u resource Logboeken kunt verzamelen in een Log Analytics-werk ruimte, voltooit u een zelf studie over het schrijven van logboek query's voor het analyseren van deze gegevens.

> [!div class="nextstepaction"]
> [Aan de slag met logboek query's in Azure Monitor](../log-query/get-started-queries.md)
