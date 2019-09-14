---
title: Instellingen configureren-persoonlijker
titleSuffix: Azure Cognitive Services
description: Service configuratie bevat de manier waarop de service beloningen behandelt, hoe vaak de service bekijkt, hoe vaak het model opnieuw wordt getraind en hoeveel gegevens worden opgeslagen.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: diberry
ms.openlocfilehash: 376c2efbf3269092d0534870108ef6d753f8743e
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70962526"
---
# <a name="personalizer-settings"></a>Persoonlijke instellingen

Service configuratie bevat de manier waarop de service beloningen behandelt, hoe vaak de service bekijkt, hoe vaak het model opnieuw wordt getraind en hoeveel gegevens worden opgeslagen.

## <a name="create-personalizer-resource"></a>Een persoonlijke resource maken

Maak een persoonlijke resource voor elke feedback-lus. 

1. Meld u aan bij de [Azure-portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). Met de vorige koppeling gaat u naar de pagina **maken** voor de personaler-service. 
1. Voer uw service naam in, selecteer een abonnement, locatie, prijs categorie en resource groep.
1. Selecteer de bevestiging en selecteer **maken**.

## <a name="configure-service-settings-in-the-azure-portal"></a>Service-instellingen configureren in de Azure Portal

1. Meld u aan bij [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer).
1. Zoek uw persoonlijke resource. 
1. Selecteer in de sectie **resource management** de optie **instellingen**.

    Kopieer voordat u de Azure Portal verlaat een van de bron sleutels van de pagina **sleutels** . U hebt dit nodig om de [personaler SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer)te gebruiken.

### <a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>Belonings instellingen voor de feedback-lus configureren op basis van use case

Configureer de service-instellingen voor het gebruik van beloningen voor uw feedback-lus. Als u de volgende instellingen wijzigt, wordt het huidige personalisatie model opnieuw ingesteld en wordt het opnieuw getraind met de laatste 2 dagen aan gegevens:

![De belonings instellingen voor de feedback-lus configureren](media/settings/configure-model-reward-settings.png)

|Instelling|Doel|
|--|--|
|Wacht tijd op beloning|Hiermee stelt u de periode in waarin Personaler belonings waarden voor een classificatie oproep verzamelt, vanaf het moment dat de classificatie oproep plaatsvindt. Deze waarde wordt ingesteld door de volgende vraag: "Hoe lang moet Personaler wachten op de aanroepen van beloningen?" Elke beloning die wordt binnengekomen nadat dit venster wordt geregistreerd, wordt vastgelegd, maar niet voor Learning.|
|Standaard beloning|Als er geen belonings oproep wordt ontvangen door Personaler tijdens het venster belonings wachttijd dat is gekoppeld aan een rang nummer, zal Personaler de standaard beloning toewijzen. In de meeste scenario's is de standaard beloning standaard nul.|
|Belonings aggregatie|Als er meerdere beloningen worden ontvangen voor dezelfde positie-API-aanroep, wordt deze samenvoegings methode gebruikt: **Sum** of **oudste**. De eerste keer kiest de eerste ontvangen Score en wordt de rest verwijderd. Dit is handig als u een unieke beloning wilt hebben tussen mogelijk dubbele aanroepen. |

Nadat u deze instellingen hebt gewijzigd, moet u **Opslaan**selecteren.

### <a name="exploration-setting"></a>Instelling voor verkennen 

Personalisatie kan nieuwe patronen ontdekken en de wijzigingen van gebruikers gedrag in de loop van de tijd aanpassen door alternatieven te verkennen. De instellingen voor **verkennen** bepalen welk percentage van de rangings aanroepen met verkennen worden beantwoord. 

Als u deze instelling wijzigt, wordt het huidige personalisatie model opnieuw ingesteld en wordt het opnieuw getraind met de laatste 2 dagen aan gegevens.

![De instelling voor verkennen bepaalt welk percentage van de Rangings aanroepen met verkennen wordt beantwoord](media/settings/configure-exploration-setting.png)

Wanneer u deze instelling hebt gewijzigd, selecteert u **Opslaan**.

### <a name="model-update-frequency"></a>Frequentie van model updates

Het meest recente model, dat is getraind vanuit belonings-API-aanroepen van elke actieve gebeurtenis, wordt niet automatisch gebruikt door de rang orde van Personaler. Met de frequentie van de **model updates** wordt ingesteld hoe vaak het model dat wordt gebruikt door de classificatie oproep up-to-date wordt bijgewerkt. 

High model-update frequenties zijn nuttig voor situaties waarin u de wijzigingen in het gedrag van gebruikers nauw keurig wilt bijhouden. Voor beelden zijn onder andere sites die worden uitgevoerd op Live nieuws, virale inhoud of Live-product biedingen. In deze scenario's kunt u een frequentie van 15 minuten gebruiken. Voor de meeste gebruiks voorbeelden is een lagere update frequentie van kracht. Update frequenties van één minuut zijn handig bij het opsporen van fouten in de code van een toepassing met behulp van Personaler, het uitvoeren van demo's of het interactief testen van machine learning aspecten.

![Met de model update frequentie stelt u in hoe vaak een nieuw Personalr model opnieuw wordt getraind.](media/settings/configure-model-update-frequency-settings-15-minutes.png)

Wanneer u deze instelling hebt gewijzigd, selecteert u **Opslaan**.

### <a name="data-retention"></a>Bewaartijd van gegevens

**Gegevens retentie periode** instellen hoeveel dagen persoonlijke gegevens logboeken worden bewaard. Eerdere gegevens logboeken zijn vereist voor het uitvoeren van [offline-evaluaties](concepts-offline-evaluation.md), die worden gebruikt om de effectiviteit van personaler te meten en het trainings beleid te optimaliseren.

Wanneer u deze instelling hebt gewijzigd, selecteert u **Opslaan**.

## <a name="export-the-personalizer-model"></a>Het Personalr model exporteren

Bekijk in het gedeelte resource beheer voor **model en beleid**het maken van het model en de datum van de laatste update en exporteer het huidige model. U kunt de Azure Portal of de Personaler-Api's gebruiken voor het exporteren van een model bestand voor archiverings doeleinden. 

![Huidig Personal model exporteren](media/settings/export-current-personalizer-model.png)

## <a name="import-and-export-learning-policy"></a>Leer beleid importeren en exporteren

Importeer vanuit het gedeelte resource beheer voor **model en beleid**een nieuw trainings beleid of Exporteer het huidige leer beleid.
U kunt Learning-beleids bestanden van eerdere export aanvragen of de geoptimaliseerde beleids regels downloaden die zijn gedetecteerd tijdens offline-evaluaties. Het maken van hand matige wijzigingen in deze bestanden is van invloed op machine learning prestaties en nauw keurigheid van offline-evaluaties, en micro soft kan niet op de nauw keurigheid van machine learning en evaluaties of service-uitzonde ringen die het gevolg zijn van hand matig bewerkings beleid

## <a name="next-steps"></a>Volgende stappen

<!--
[How to use the Personalizer container](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409)
-->
[Meer informatie over de beschik baarheid van regio's](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
