---
title: Microsoft Azure Data Explorer Flow-connector (voorbeeld)
description: Meer informatie over het gebruik van de Microsoft Flow-connector om stromen van automatisch geplande of geactiveerde taken te maken.
author: orspod
ms.author: orspodek
ms.reviewer: dorcohen
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/15/2020
ms.openlocfilehash: 7745888dcaa1324d4a9d956e93d0504c8da8c026
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501779"
---
# <a name="microsoft-flow-connector-preview"></a>Microsoft Flow-connector (voorbeeld)

Met de Stroomconnector azure Data Explorer kan Azure Data Explorer de [stroommogelijkheden van Microsoft Power Automate](https://flow.microsoft.com/) gebruiken om Kusto-query's en -opdrachten automatisch uit te voeren als onderdeel van een geplande of geactiveerde taak.

Veelvoorkomende gebruiksscenario's zijn:

* Dagelijkse rapporten verzenden met tabellen en grafieken
* Meldingen instellen op basis van queryresultaten
* Besturingselementopdrachten plannen voor clusters
* Gegevens exporteren en importeren tussen Azure Data Explorer en andere databases 

Zie [voorbeelden voor het gebruik van Microsoft Flow-connectoren](flow-usage.md)voor meer informatie .

##  <a name="log-in"></a>Aanmelden 

1. Meld u aan bij [Microsoft Power Automate](https://flow.microsoft.com/).

1. Wanneer u voor de eerste keer verbinding maakt, wordt u gevraagd zich aan te melden.

1. Selecteer **Aanmelden** en voer uw referenties in.

![Het dialoogvenster Aanmelden](./media/flow/flow-signin.png)

## <a name="authentication"></a>Authentication

U verifiëren met gebruikersreferenties of een AAD-toepassing.

> [!Note]
> Zorg ervoor dat uw toepassing een [AAD-toepassing](https://docs.microsoft.com/azure/kusto/management/access-control/how-to-provision-aad-app) is en bevoegd is om query's uit te voeren op uw cluster.

1. De drie puntjes rechtsboven in de Microsoft ![Flow-connector selecteren: een verbinding toevoegen](./media/flow/flow-addconnection.png)

1. Selecteer **Nieuwe verbinding toevoegen** en selecteer Verbinding maken met **serviceprincipal**.
![Het dialoogvenster Aanmelden](./media/flow/flow-signin.png)

1. Voer de vereiste gegevens in:
    * Verbindingsnaam: een beschrijvende en betekenisvolle naam voor de nieuwe verbinding
    * Client-id: uw toepassings-id
    * Client geheim: uw toepassingssleutel
    * Tenant: de id van de AAD-map waarin u de toepassing hebt gemaakt. De Microsoft-tenant-id is bijvoorbeeld: 72f988bf-86f1-41af-91ab-2d7cd011db47

![Toepassingsverificatie](./media/flow/flow-appauth.png)

Wanneer de verificatie is voltooid, ziet u dat uw stroom de nieuw toegevoegde verbinding gebruikt.

![Voltooide toepassingsverificatie](./media/flow/flow-appauthcomplete.png)

Vanaf nu wordt deze stroom uitgevoerd met behulp van deze toepassingsreferenties.

## <a name="find-the-azure-kusto-connector"></a>De Azure Kusto-connector zoeken

Als u de Microsoft Flow-connector wilt gebruiken, moet u eerst een trigger toevoegen. Een trigger kan worden gedefinieerd op basis van een terugkerende periode of als reactie op een eerdere stroomactie.

1. [Maak een nieuwe stroom](https://flow.microsoft.com/manage/flows/new) of selecteer op de startpagina de actie **Mijn stromen** en selecteer **+ Nieuw**.

    ![Een nieuwe stroom maken](./media/flow/flow-newflow.png)

1. Geplande lege toevoegen.

    ![Nieuwe geplande stroom](./media/flow/flow-scheduled-from-blank.png)

1. Voer de vereiste informatie in op de pagina Een geplande stroom maken.
    ![Een geplande stroom bouwen](./media/flow/flow-build-scheduled-flow.png)
1. Selecteer **Maken**.
1. Selecteer **+ Nieuwe stap**.
1. Voer in het zoekvak 'Kusto' in.

    ![Stroomacties selecteren](./media/flow/flow-actions.png)

1. Selecteer **Azure Data Explorer**.

## <a name="flow-actions"></a>Stroomacties

Wanneer u de Azure Data Explorer-connector opent, zijn er drie mogelijke acties die u aan uw stroom toevoegen.

In deze sectie worden de mogelijkheden en parameters voor elke Microsoft Flow-actie beschreven.

![Acties voor Azure Data Explorer](./media/flow/flow-adx-actions.png)

### <a name="run-control-command-and-visualize-results"></a>Besturingsopdracht uitvoeren en resultaten visualiseren

Gebruik de opdracht Besturingselement uitvoeren en visualiseer resultaten actie om een [besturingsopdracht](https://docs.microsoft.com/azure/kusto/management/index)uit te voeren .

1. Geef de cluster-URL op. Bijvoorbeeld: https://clusterName.eastus.kusto.windows.net
1. Voer de naam van de database in.
1. Geef de besturingsopdracht op:
    * Dynamische inhoud selecteren uit de apps en connectoren die in de stroom worden gebruikt
    * Een expressie toevoegen om waarden te openen, converteren en vergelijken
1. Als u de resultaten van deze actie per e-mail wilt verzenden als tabel of grafiek, geeft u het grafiektype op, dat kan zijn:
    * Een HTML-tabel
    * Een cirkeldiagram
    * Een tijddiagram
    * Een staafdiagram

![Uitvoeren Van stroombesturingselement, opdracht](./media/flow/flow-runcontrolcommand.png)

> [!IMPORTANT]
> Voer in het veld *Clusternaam* de cluster-URL in.

### <a name="run-query-and-list-results"></a>Query- en lijstresultaten uitvoeren

> [!Note]
> Als uw query begint met een punt (wat betekent dat het een [besturingsopdracht](https://docs.microsoft.com/azure/kusto/management/index)is), gebruikt u [De opdracht Besturingselement uitvoeren en de resultaten visualiseren](#run-control-command-and-visualize-results)

Met deze actie wordt een query naar het Kusto-cluster verzenden. De acties die daarna worden toegevoegd, herhalen over elke regel van de resultaten van de query.

In het volgende voorbeeld wordt elke minuut een query geactiveerd en wordt een e-mail gestuurd op basis van de queryresultaten. De query controleert het aantal regels in de database en stuurt vervolgens alleen een e-mail als het aantal regels groter is dan 0. 

![Querylijstresultaten uitvoeren](./media/flow/flow-runquerylistresults-2.png)

> [!Note]
> Als de kolom meerdere regels heeft, wordt de connector voor elke regel in de kolom uitgevoerd.

### <a name="run-query-and-visualize-results"></a>Query's uitvoeren en resultaten visualiseren
        
> [!Note]
> Als uw query begint met een punt (wat betekent dat het een [besturingsopdracht](https://docs.microsoft.com/azure/kusto/management/index)is), gebruikt u [De opdracht Besturingselement uitvoeren en de resultaten visualiseren](#run-control-command-and-visualize-results)
        
Gebruik de actie Query uitvoeren en visualiseer resultaten om het resultaat van Kusto-query's te visualiseren als een tabel of grafiek. Gebruik deze stroom bijvoorbeeld om dagelijkse ICM-rapporten per e-mail te ontvangen. 
    
In dit voorbeeld worden de resultaten van de query geretourneerd als een HTML-tabel.
            
![Query's uitvoeren en resultaten visualiseren](./media/flow/flow-runquery.png)

> [!IMPORTANT]
> Voer in het veld *Clusternaam* de cluster-URL in.

## <a name="email-kusto-query-results"></a>Kusto-queryresultaten e-mailen

U een stap in elke stroom opnemen om rapporten per e-mail naar elk e-mailadres te verzenden. 

1. Selecteer **+ Nieuwe stap** om een nieuwe stap aan uw stroom toe te voegen.
1. Voer in het zoekveld Office 365 in en selecteer **Office 365 Outlook**.
1. Selecteer **Een e-mailbericht verzenden**.
1. Voer het e-mailadres in waar u het e-mailrapport wilt verzenden.
1. Voer het onderwerp van de e-mail in.
1. Selecteer In het veld *Lichaam* in het veld Dynamische inhoud de optie **Lichaam**.
1. Selecteer **Geavanceerde opties weergeven**.
1. Selecteer *bijlagenaam* in het veld **Attachment Name**Naam van bijlagen -1 .
1. Selecteer *bijlageinhoud* in het **Attachment Content**veld Inhoud bijlagen .
1. Stel indien nodig het belangniveau in.
1. Selecteer **Opslaan**.

![Een e-mailbericht versturen](./media/flow/flow-sendemail.png)

## <a name="check-if-your-flow-succeeded"></a>Controleren of uw stroom is geslaagd

Zie de rungeschiedenis van de stroom om te controleren of uw stroom is geslaagd:
1. Ga naar de [startpagina van Microsoft Flow](https://flow.microsoft.com/).
1. Selecteer In het hoofdmenu [Mijn flows](https://flow.microsoft.com/manage/flows).
    ![Pagina Mijn stromen](./media/flow/flow-myflows.png)
1. Selecteer in de rij van de stroom die u wilt onderzoeken het pictogram Meer opdrachten en **voer de geschiedenis uit**.

    ![Geschiedenismenu uitvoeren](./media/flow//flow-runhistory.png)

    Alle stroomuitvoeringen worden weergegeven met begintijd, duur en status.
    ![Pagina Geschiedenisresultaten uitvoeren](./media/flow/flow-runhistoryresults.png)

    Selecteer op de pagina [Mijn stromen](https://flow.microsoft.com/manage/flows) de stroom die u wilt onderzoeken voor meer informatie over de stroom.

    ![Pagina Geschiedenis volledige resultaten uitvoeren](./media/flow/flow-fulldetails.png) 

Als u wilt zien waarom een run is mislukt, selecteert u de begintijd van de run. De stroom wordt weergegeven en de stap van de stroom die is mislukt, wordt aangegeven door een rood uitroepteken. Vouw de mislukte stap uit om de details te bekijken. Het rechterdeelvenster bevat informatie over de fout, zodat u dit oplossen.

![Stroomfout](./media/flow/flow-error.png)

## <a name="timeout-exceptions"></a>Uitzonderingen op time-outs

Uw stroom kan mislukken en een uitzondering op 'RequestTimeout' retourneren als deze langer dan zeven minuten wordt uitgevoerd.

Meer informatie over [Microsoft Flow-beperkingen](#limitations).
    
Dezelfde query kan worden uitgevoerd in Azure Data Explorer, waar de tijd niet beperkt is en kan worden gewijzigd.
            
De uitzondering 'RequestTimeout' wordt weergegeven in de onderstaande afbeelding:
    
![Uitzonderingsfout time-out van stroomaanvraag](./media/flow/flow-requesttimeout.png)
    
Als u een time-outprobleem wilt oplossen, probeert u uw query efficiënter te maken, zodat deze sneller wordt uitgevoerd of in brokken te scheiden. Elk segment kan op een ander deel van de query worden uitgevoerd.

Lees voor meer informatie over [aanbevolen procedures voor query's](https://docs.microsoft.com/azure/kusto/query/best-practices).

## <a name="limitations"></a>Beperkingen

* De resultaten die aan de klant worden geretourneerd, zijn beperkt tot 500.000 records. Het totale geheugen voor deze records mag niet hoger zijn dan 64 MB en zeven minuten uitvoeringstijd.
* De connector ondersteunt de [vork-](https://docs.microsoft.com/azure/kusto/query/forkoperator) en [facetoperatoren](https://docs.microsoft.com/azure/kusto/query/facetoperator) niet.
* Flow werkt het beste op Microsoft Edge en Chrome.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de [Microsoft Azure Explorer Logic App-connector,](https://docs.microsoft.com/azure/kusto/tools/logicapps) een andere manier om Kusto-query's en -opdrachten automatisch uit te voeren als onderdeel van een geplande of geactiveerde taak.
