---
title: Resourcelogboeken verzamelen van een Azure Resource en analyseren met Azure Monitor
description: Zelfstudie om diagnostische instellingen te configureren om bronlogboeken van een Azure-bron te verzamelen in een Log Analytics-werkruimte waar ze kunnen worden geanalyseerd met een logboekquery.
ms.subservice: ''
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 12/15/2019
ms.openlocfilehash: d356042d65c419163de4951e64a635a22ea90e6d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78269192"
---
# <a name="tutorial-collect-and-analyze-resource-logs-from-an-azure-resource"></a>Zelfstudie: Bronlogboeken verzamelen en analyseren vanuit een Azure-bron

Resourcelogboeken bieden inzicht in de gedetailleerde werking van een Azure-bron en zijn handig voor het bewaken van de status en beschikbaarheid ervan. Azure-bronnen genereren automatisch bronlogboeken, maar u moet configureren waar ze moeten worden verzameld. Deze zelfstudie neemt u mee door het proces van het maken van een diagnostische instelling om bronlogboeken voor een resource in uw Azure-abonnement te verzamelen en te analyseren met een logboekquery.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een werkruimte logboekanalyse maken in Azure Monitor
> * Een diagnostische instelling maken om bronlogboeken te verzamelen 
> * Een eenvoudige logboekquery maken om logboeken te analyseren


## <a name="prerequisites"></a>Vereisten

Om deze zelfstudie te voltooien, hebt u een Azure-bron nodig om te controleren. U elke bron in uw Azure-abonnement gebruiken die diagnostische instellingen ondersteunt. Als u wilt bepalen of een resource diagnostische instellingen ondersteunt, gaat u naar het menu in de Azure-portal en controleert u of er een optie **diagnostische instellingen** is in het gedeelte **Controle** van het menu.


## <a name="log-in-to-azure"></a>Meld u aan bij Azure.
Log in bij de [https://portal.azure.com](https://portal.azure.com)Azure-portal op .


## <a name="create-a-workspace"></a>Een werkruimte maken
Een Log Analytics-werkruimte in Azure Monitor verzamelt en indexeert logboekgegevens uit verschillende bronnen en maakt geavanceerde analyse mogelijk met behulp van een krachtige querytaal. De werkruimte Log Analytics moet bestaan voordat u een diagnostische instelling maakt om gegevens erin te verzenden. U een bestaande werkruimte gebruiken in uw Azure-abonnement of er een maken met de volgende procedure. 

> [!NOTE]
> Hoewel u werken met gegevens in Log Analytics-werkruimten in het menu **Azure Monitor,** maakt en beheert u werkruimten in het menu **Logboekanalyse-werkruimten.**

1. Selecteer **Log Analytics-werkruimten**in **Alle services**.
2. Klik boven aan het scherm op **Toevoegen** en geef de volgende details voor de werkruimte op:
   - **Werkruimte Log Analytics:** Naam voor de nieuwe werkruimte. Deze naam moet wereldwijd uniek zijn voor alle Azure Monitor-abonnementen.
   - **Abonnement**: Selecteer het abonnement om de werkruimte op te slaan. Dit hoeft niet hetzelfde abonnement te zijn als de resource die wordt gecontroleerd.
   - **Resourcegroep:** selecteer een bestaande resourcegroep of klik op **Nieuw maken** om een nieuwe groep te maken. Dit hoeft niet dezelfde resourcegroep te zijn die hetzelfde is als de resource die wordt gecontroleerd.
   - **Locatie**: Selecteer een Azure-gebied of maak een nieuw gebied. Dit hoeft niet dezelfde locatie te hebben als de resource die wordt gecontroleerd.
   - **Prijscategorie:** Selecteer *Betalen per gebruik* als prijscategorie. U deze prijscategorie later wijzigen. Klik op de **prijslink Log Analytics** voor meer informatie over verschillende prijsniveaus.

    ![Nieuwe werkruimte](media/tutorial-resource-logs/new-workspace.png)

3. Klik op **OK** om de werkruimte te maken.

## <a name="create-a-diagnostic-setting"></a>Een diagnostische instelling maken
[Diagnostische instellingen](../platform/diagnostic-settings.md) bepalen waar resourcelogboeken moeten worden verzonden voor een bepaalde bron. Eén diagnostische instelling kan meerdere [bestemmingen](../platform/diagnostic-settings.md#destinations)hebben, maar we gebruiken alleen een Log Analytics-werkruimte in deze zelfstudie.

1. Selecteer **diagnostische instellingen**onder het gedeelte **Controle** van het menu van uw resource .
2. U moet een bericht 'Geen diagnostische instellingen gedefinieerd' hebben. Klik **op Diagnostische instelling toevoegen**.

    ![Diagnostische instellingen](media/tutorial-resource-logs/diagnostic-settings.png)

3. Elke diagnostische instelling bestaat uit drie basisonderdelen:
 
   - **Naam**: Dit heeft geen significant effect en moet gewoon beschrijvend zijn voor jou.
   - **Bestemmingen**: Een of meer bestemmingen om de logboeken te verzenden. Alle Azure-services delen dezelfde set van drie mogelijke bestemmingen. Elke diagnostische instelling kan een of meer bestemmingen definiëren, maar niet meer dan één bestemming van een bepaald type. 
   - **Categorieën**: categorieën van logboeken die naar elk van de bestemmingen moeten worden verzonden. De set categorieën varieert voor elke Azure-service.

4. Selecteer **De werkruimte Verzenden naar Logboekanalyse** en selecteer vervolgens de werkruimte die u hebt gemaakt.
5. Selecteer de categorieën die u wilt verzamelen. Zie de documentatie voor elke service voor een definitie van de beschikbare categorieën.

    ![Diagnostische instelling](media/tutorial-resource-logs/diagnostic-setting.png)

6. Klik **op Opslaan** om de diagnostische instellingen op te slaan.

    
 
 ## <a name="use-a-log-query-to-retrieve-logs"></a>Een logboekquery gebruiken om logboeken op te halen
Gegevens worden opgehaald uit een log analytics-werkruimte met behulp van een logboekquery die is geschreven in Kusto Query Language (KQL). Inzichten en oplossingen in Azure Monitor bieden logboekquery's om gegevens voor een bepaalde service op te halen, maar u rechtstreeks werken met logboekquery's en hun resultaten in de Azure-portal met Log Analytics. 

1. Selecteer **Logboeken** onder het gedeelte Controle **Logs**van het menu van uw bron .
2. Log Analytics wordt geopend met een leeg queryvenster met de scope die is ingesteld op uw resource. Eventuele query's bevatten alleen records uit die bron.

    > [!NOTE]
    > Als u Logboeken hebt geopend in het menu Azure Monitor, wordt het bereik ingesteld op de werkruimte Log Analytics. In dit geval bevatten alle query's alle records in de werkruimte.
   
    ![Logboeken](media/tutorial-resource-logs/logs.png)

4. De service in het voorbeeld schrijft resourcelogboeken naar de **tabel AzureDiagnostics,** maar andere services kunnen naar andere tabellen schrijven. Zie [Ondersteunde services, schema's en categorieën voor Azure Resource Logs](../platform/diagnostic-logs-schema.md) voor tabellen die worden gebruikt door verschillende Azure-services.

    > [!NOTE]
    > Meerdere services schrijven bronlogboeken naar de tabel AzureDiagnostics. Als u Log Analytics start in het menu Azure `where` Monitor, `ResourceProvider` moet u een instructie met de kolom toevoegen om uw specifieke service op te geven. Wanneer u Log Analytics start vanuit het menu van een resource, wordt het bereik ingesteld op alleen records uit deze bron, zodat deze kolom niet vereist is. Zie de documentatie van de service voor voorbeeldquery's.


5. Typ een query en klik op **Uitvoeren** om de resultaten te inspecteren. 
6. Zie [Aan de slag met logboekquery's in Azure Monitor](../log-query/get-started-queries.md) voor een zelfstudie over het schrijven van logboekquery's.

    ![Logboekquery](media/tutorial-resource-logs/log-query-1.png)




## <a name="next-steps"></a>Volgende stappen
Nu u hebt geleerd hoe u bronlogboeken verzamelen in een werkruimte van Log Analytics, u een zelfstudie uitvoeren over het schrijven van logboekquery's om deze gegevens te analyseren.

> [!div class="nextstepaction"]
> [Aan de slag met logboekquery's in Azure Monitor](../log-query/get-started-queries.md)
