---
title: Problemen oplossen-QnA Maker
titleSuffix: Azure Cognitive Services
description: De lijst met alle meest gestelde vragen met betrekking tot de QnA Maker-service helpt u de service sneller en met betere resultaten te gebruiken.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 09/26/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 7e98fd089ce8ec1285232840a40bb42ac5b81446
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795447"
---
# <a name="troubleshooting-for-qna-maker"></a>Problemen oplossen voor QnA Maker

De lijst met alle meest gestelde vragen met betrekking tot de QnA Maker-service helpt u de service sneller en met betere resultaten te gebruiken.

<a name="how-to-get-the-qnamaker-service-hostname"></a>

## <a name="how-to-get-the-qnamaker-service-endpoint"></a>Het QnAMaker-service-eind punt ophalen

QnAMaker service-eind punt is handig voor fout opsporing wanneer u contact opneemt met QnAMaker-ondersteuning of UserVoice. Het eind punt is een URL in dit formulier: https://your-resource-name.azurewebsites.net.
    
1. Ga naar de QnAMaker-service (resource groep) in het [Azure Portal](https://portal.azure.com)

    ![QnAMaker Azure-resource groep in Azure Portal](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Selecteer de App Service die aan de QnA Maker resource is gekoppeld. Normaal gesp roken zijn de namen hetzelfde.

     ![QnAMaker App Service selecteren](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. De eind punt-URL is beschikbaar in het gedeelte Overzicht

    ![QnAMaker-eind punt](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)
    

## <a name="use-the-help-bot-in-the-qna-maker-portal"></a>De Help-bot gebruiken in de QnA Maker Portal

QnA Maker biedt een **Help** -bot binnen de QnA Maker Portal om u te helpen. De Help-bot is beschikbaar op elke webpagina. De bot maakt gebruik van QnA Maker om antwoorden te geven en geeft het [ C# bot Framework-code project](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-support) aan de bot zodat u snel aan de slag kunt met uw eigen antwoord bot. 

![! [QnA Maker biedt een * * Help * * bot in de QnA Maker Portal om u te helpen.] (./media/qnamaker-faq/use-qna-maker-help-bot-to-learn-qna-maker-service.png)](./media/qnamaker-faq/use-qna-maker-help-bot-to-learn-qna-maker-service.png#lightbox)

## <a name="manage-the-knowledge-base"></a>De Knowledge Base beheren

### <a name="i-accidentally-deleted-a-part-of-my-qna-maker-what-should-i-do"></a>Ik heb per ongeluk een deel van mijn QnA Maker verwijderd. Wat moet ik doen? 

Verwijder geen van de Azure-Services die zijn gemaakt samen met de QnA Maker resource, zoals een zoek-of web-app. Deze zijn nodig om QnA Maker te kunnen werken, als u er een verwijdert, QnA Maker niet meer goed werkt.

Alle verwijderingen zijn permanent, inclusief vraag-en antwoord paren, bestanden, Url's, aangepaste vragen en antwoorden, kennis slagen of Azure-resources. Zorg ervoor dat u de Knowledge Base exporteert vanaf de pagina **instellingen** voordat u een deel van de Knowledge Base verwijdert. 

### <a name="why-is-my-urlsfiles-not-extracting-question-answer-pairs"></a>Waarom worden mijn URL ('s)/file (en) niet de vraag/antwoord paren uitgepakt?

Het is mogelijk dat QnA Maker bepaalde QnA-inhoud (vraag-en antwoord) niet automatisch kan ophalen uit geldige Url's voor veelgestelde vragen. In dergelijke gevallen kunt u de QnA-inhoud in een txt-bestand plakken en zien of het hulp programma deze kan opnemen. U kunt ook inhoud toevoegen aan uw Knowledge Base via de [QnA Maker Portal](https://qnamaker.ai).

### <a name="how-large-a-knowledge-base-can-i-create"></a>Hoe groot mag de Knowledge Base zijn die ik maak?

De grootte van de kennis basis is afhankelijk van de SKU van Azure Search die u kiest bij het maken van de QnA Maker service. Lees [hier](./Tutorials/choosing-capacity-qnamaker-deployment.md) meer informatie.

### <a name="why-cant-i-see-anything-in-the-drop-down-when-i-try-to-create-a-new-knowledge-base"></a>Waarom kan er niets in de vervolg keuzelijst worden weer geven wanneer ik een nieuwe Knowledge Base probeer te maken?

U hebt nog geen QnA Maker Services in azure gemaakt. Lees [hier](./How-To/set-up-qnamaker-service-azure.md) meer informatie over hoe u dit doet.

### <a name="how-do-i-share-a-knowledge-base-with-others"></a>Hoe kan ik een kennis database delen met anderen?

Delen werkt op het niveau van een QnA Maker service, dat wil zeggen dat alle kennis grondslagen in de service worden gedeeld. Lees [hier](./How-To/collaborate-knowledge-base.md) hoe u kunt samen werken aan een Knowledge Base.

### <a name="can-you-share-a-knowledge-base-with-a-contributor-that-is-not-in-the-same-aad-tenant-to-modify-a-knowledge-base"></a>Kunt u een Knowledge Base delen met een Inzender die zich niet in dezelfde AAD-Tenant bevindt, om een Knowledge Base te wijzigen? 

Delen is gebaseerd op op rollen gebaseerd toegangs beheer (RBAC) van Azure. Als u _een_ resource in azure kunt delen met een andere gebruiker, kunt u ook QnA Maker delen.

### <a name="if-you-have-an-app-service-plan-with-5-qnamaker-knowledge-bases-can-you-assign-readwrite-rights-to-5-different-users-so-each-of-them-can-access-only-1-qnamaker-knowledge-base"></a>Als u een abonnement hebt App Service met 5 QnAMaker Knowledge bases. Kunt u lees-en schrijf rechten toewijzen aan vijf verschillende gebruikers, zodat ze slechts één QnAMaker Knowledge Base kunnen openen?

U kunt een volledige QnAMaker-service delen, niet individuele kennis bases.

### <a name="how-can-i-change-the-default-message-when-no-good-match-is-found"></a>Hoe kan ik het standaard bericht wijzigen wanneer er geen goede overeenkomst wordt gevonden?

Het standaard bericht is onderdeel van de instellingen in de app service.
- Ga naar de app service-resource in de Azure Portal

![qnamaker appservice](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- Klik op de optie **instellingen**

![qnamaker appservice-instellingen](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- Wijzig de waarde van de instelling **DefaultAnswer**
- Uw app service opnieuw starten

![qnamaker appservice opnieuw opstarten](./media/qnamaker-faq/qnamaker-appservice-restart.png)

### <a name="why-is-my-sharepoint-link-not-getting-extracted"></a>Waarom wordt mijn share point-koppeling niet opgehaald?

Zie [gegevens bron locaties](./Concepts/data-sources-supported.md#data-source-locations) voor meer informatie.

### <a name="the-updates-that-i-made-to-my-knowledge-base-are-not-reflected-on-publish-why-not"></a>De updates die ik heb aangebracht in mijn Knowledge Base, worden niet weer gegeven bij het publiceren. Waarom niet?

Elke bewerkings bewerking, ongeacht of deze in een tabel wordt bijgewerkt, getest of ingesteld, moet worden opgeslagen voordat deze kan worden gepubliceerd. Zorg ervoor dat u na elke bewerking op de knop **opslaan en trainen** klikt.

### <a name="does-the-knowledge-base-support-rich-data-or-multimedia"></a>Ondersteunt de Knowledge Base uitgebreide gegevens of multi media?

De Knowledge Base ondersteunt Markdown. De automatische extractie van Url's heeft echter de mogelijkheid om de conversie van HTML naar korting te beperken. Als u een volledige prijs verlaging wilt gebruiken, kunt u uw inhoud rechtstreeks in de tabel wijzigen of een Knowledge Base uploaden met de Rich Content.

Multi media, zoals afbeeldingen en Video's, wordt op dit moment niet ondersteund.

### <a name="does-qna-maker-support-non-english-languages"></a>Ondersteunt QnA Maker andere talen dan het Engels?

Meer informatie over [ondersteunde talen](./Overview/languages-supported.md).

Als u inhoud in meerdere talen hebt, moet u ervoor zorgen dat u een afzonderlijke service maakt voor elke taal.

## <a name="manage-service"></a>Services beheren

### <a name="when-should-i-restart-my-app-service"></a>Wanneer moet ik mijn app service opnieuw starten? 

Vernieuw uw app service wanneer het waarschuwings pictogram naast de versie waarde voor de Knowledge Base is ingesteld in de tabel **eindpunt sleutels** op de [pagina](https://www.qnamaker.ai/UserSettings) **gebruikers instellingen** .

### <a name="i-deleted-my-existing-search-service-how-can-i-fix-this"></a>Ik heb mijn bestaande zoek service verwijderd. Hoe kan ik dit oplossen?

Als u een Azure Cognitive Search-index verwijdert, is de bewerking definitief en kan de index niet worden hersteld. 

### <a name="i-deleted-my-testkb-index-in-my-search-service-how-can-i-fix-this"></a>Ik heb mijn `testkb`-index verwijderd uit mijn zoek service. Hoe kan ik dit oplossen? 

De oude gegevens kunnen niet worden hersteld. Maak een nieuwe QnA Maker resource en maak uw Knowledge Base opnieuw.

### <a name="when-should-i-refresh-my-endpoint-keys"></a>Wanneer moet ik mijn eindpunt sleutels vernieuwen?

Vernieuw uw eindpunt sleutels als u vermoedt dat er is geknoeid.

### <a name="can-i-use-the-same-azure-cognitive-search-resource-for-knowledge-bases-using-multiple-languages"></a>Kan ik dezelfde Azure Cognitive Search Resource gebruiken voor Knowledge bases met behulp van meerdere talen?

Als u meerdere talen en meerdere kennis bronnen wilt gebruiken, moet de gebruiker voor elke taal een QnA Maker resource maken. Hiermee maakt u een afzonderlijke Azure Search-service per taal. Het mixen van verschillende taal kennis bases in één Azure Search-service resulteert in een gedegradeerde relevantie van de resultaten.

### <a name="how-can-i-change-the-name-of-the-azure-cognitive-search-resource-used-by-qna-maker"></a>Hoe kan ik de naam wijzigen van de Azure Cognitive Search-resource die wordt gebruikt door QnA Maker?

De naam van de Azure Cognitive Search resource is de naam van de QnA Maker resource met enkele wille keurige letters die aan het einde worden toegevoegd. Dit maakt het moeilijk om onderscheid te maken tussen meerdere zoek bronnen voor QnA Maker. Maak een afzonderlijke zoek service (noem deze zoals u dat wilt) en verbind deze met uw QnA-service. De stappen zijn vergelijkbaar met de stappen die u moet uitvoeren om [een upgrade van Azure Search](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service)uit te voeren.

### <a name="when-qna-maker-returns-runtime-core-is-not-initialized-how-do-i-fix-it"></a>Als QnA Maker `Runtime core is not initialized,` retourneert hoe kan ik het herstellen?

De schijf ruimte voor uw app-service is mogelijk vol. Stappen om uw schijf ruimte te herstellen:

1. Selecteer in de [Azure Portal](https://portal.azure.com)de app service van uw QnA maker en Stop vervolgens de service.
1. Terwijl u nog steeds op de app-service klikt, selecteert u **ontwikkelingsprogram ma's**en **Geavanceerde hulpprogram Ma's**. vervolgens **gaat u naar**. Hiermee opent u een nieuw browser venster.
1. Selecteer **console fout opsporing**en vervolgens **cmd** om een opdracht regel programma te openen. 
1. Ga naar de _site/wwwroot/data/QnAMaker/_ map.
1. Verwijder alle mappen waarvan de naam begint met `rd`. 

    **Verwijder niet** het volgende:

    * KbIdToRankerMappings. txt-bestand
    * Bestand EndpointSettings. json
    * Map EndpointKeys 

1. Start de app service.
1. Open uw kennis database om te controleren of deze nu werkt. 


## <a name="integrate-with-other-services-including-bots"></a>Integreren met andere services, met inbegrip van bots

### <a name="do-i-need-to-use-bot-framework-in-order-to-use-qna-maker"></a>Moet ik Bot Framework gebruiken om QnA Maker te kunnen gebruiken?

Nee, u hoeft niet het [bot-Framework](https://github.com/Microsoft/botbuilder-dotnet) met QnA Maker te gebruiken. QnA Maker wordt echter aangeboden als een van de verschillende sjablonen in [Azure bot service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0). Met de bot-service kunt u snel slimme bot-ontwikkeling maken via micro soft bot Framework en wordt uitgevoerd in een omgeving zonder servers.

### <a name="how-can-i-create-a-new-bot-with-qna-maker"></a>Hoe kan ik een nieuwe bot maken met QnA Maker?

Volg de instructies in [deze](./Tutorials/create-qna-bot.md) documentatie om uw bot te maken met Azure bot service.

### <a name="how-do-i-use-a-different-knowledge-base-with-an-existing-azure-bot-service"></a>Hoe kan ik een andere Knowledge Base gebruiken met een bestaande Azure bot-service?

U moet over de volgende informatie beschikken over uw Knowledge Base:

* De Knowledge Base-ID.
* De aangepaste subdomeinnaam van het gepubliceerde endpoint op eind punt, ook wel `host`, gevonden op de pagina **instellingen** nadat u het bestand hebt gepubliceerd.
* De gepubliceerde eindpunt sleutel van de Knowledge Base-gevonden op de pagina **instellingen** nadat u deze hebt gepubliceerd. 

Met deze informatie gaat u naar de app-service van uw bot in de Azure Portal. Wijzig de waarden onder **instellingen-> configuratie-> toepassings instellingen**.  

De eindpunt sleutel van de kennis basis heeft de naam `QnAAuthkey` in de ABS-service. 

### <a name="can-two-or-more-client-applications-share-a-knowledge-base"></a>Kunnen twee of meer client toepassingen een Knowledge Base delen? 

Ja, de Knowledge Base kan vanuit elk wille keurig aantal clients worden opgevraagd. Als de reactie van de Knowledge Base langzaam of time-out lijkt, kunt u overwegen om de servicelaag bij te werken voor de app service die is gekoppeld aan de Knowledge Base.

### <a name="how-do-i-embed-the-qna-maker-service-in-my-website"></a>Hoe sluit ik de QnA Maker-service in op mijn website?

Volg deze stappen om de QnA Maker-service in te sluiten als een besturings element voor chatten op uw website:

1. Maak uw FAQ-bot door de volgende [instructies te volgen.](./Tutorials/create-qna-bot.md)
2. Schakel de volgende stappen uit om Web Chat [in te scha](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat) kelen

## <a name="data-storage"></a>Gegevensopslag

### <a name="what-data-is-stored-and-where-is-it-stored"></a>Welke gegevens worden opgeslagen en waar wordt deze opgeslagen? 

Wanneer u uw QnA Maker-service maakt, hebt u een Azure-regio geselecteerd. Uw kennis-en logboek bestanden worden opgeslagen in deze regio. 
