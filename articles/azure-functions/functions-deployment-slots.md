---
title: Implementatie sleuven Azure Functions
description: Meer informatie over het maken en gebruiken van implementatie sleuven met Azure Functions
author: craigshoemaker
ms.topic: reference
ms.date: 08/12/2019
ms.author: cshoe
ms.openlocfilehash: 0e8c93ea6d5c2b525ccbea2af900f100afcc3d93
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75769214"
---
# <a name="azure-functions-deployment-slots"></a>Implementatie sleuven Azure Functions

Met Azure Functions implementatie sleuven kan uw functie-app verschillende instanties met de naam ' sleuven ' uitvoeren. Sleuven zijn verschillende omgevingen die worden weer gegeven via een openbaar beschikbaar eind punt. Een app-exemplaar wordt altijd toegewezen aan de productie sleuf en u kunt instanties die zijn toegewezen aan een sleuf op aanvraag, vervangen. Functie-apps die onder het service plan apps worden uitgevoerd, kunnen meerdere sleuven hebben, terwijl het verbruiks abonnement slechts één sleuf is toegestaan.

Hieronder ziet u hoe de functies worden beïnvloed door de wisselende sleuven:

- Het omleiden van verkeer is naadloos; Er worden geen aanvragen verwijderd vanwege een wissel.
- Als tijdens een swap een functie wordt uitgevoerd, worden de uitvoeringen voortgezet en worden de volgende triggers doorgestuurd naar het verwisselde app-exemplaar.

> [!NOTE]
> Er zijn momenteel geen sleuven beschikbaar voor het verbruiks abonnement voor Linux.

## <a name="why-use-slots"></a>Waarom sleuven gebruiken?

Er zijn een aantal voor delen voor het gebruik van implementatie sites. In de volgende scenario's worden veelvoorkomende toepassingen voor sleuven beschreven:

- **Verschillende omgevingen voor verschillende doel einden**: het gebruik van verschillende sleuven biedt u de mogelijkheid om app-exemplaren te onderscheiden voordat u naar de productie-of staging-sleuf overgaat.
- Voor bereiding: als u in plaats van rechtstreeks naar productie een sleuf implementeert, kan de app worden **opgewarmd**voordat u live gaat. Daarnaast vermindert het gebruik van sleuven de latentie voor HTTP-geactiveerde workloads. Instanties worden vóór de implementatie gewarmd, waardoor de gedistribueerde start voor nieuwe functies wordt verminderd.
- **Eenvoudige terugvals**: na een swap met productie heeft de sleuf met een eerder gefaseerde app nu de vorige productie-app. Als de wijzigingen die zijn gewisseld naar de productie sleuf, niet naar verwachting zijn, kunt u de wisseling onmiddellijk omkeren om de laatste bekende juiste instantie terug te halen.

## <a name="swap-operations"></a>Swap bewerkingen

Tijdens een swap wordt één sleuf beschouwd als de bron en het andere doel. De bron sleuf heeft het exemplaar van de toepassing die wordt toegepast op de doel site. De volgende stappen zorgen ervoor dat de doel sleuf geen downtime ondervindt tijdens een wissel:

1. **Instellingen Toep assen:** Instellingen van de doel sleuf worden toegepast op alle exemplaren van de bron sleuf. De productie-instellingen worden bijvoorbeeld toegepast op het staging-exemplaar. De toegepaste instellingen zijn onder andere de volgende categorieën:
    - [Sleuf-specifieke app-](#manage-settings) instellingen en verbindings reeksen (indien van toepassing)
    - Instellingen voor [continue implementatie](../app-service/deploy-continuous-deployment.md) (indien ingeschakeld)
    - [Verificatie](../app-service/overview-authentication-authorization.md) -instellingen app service (indien ingeschakeld)

1. **Wachten op opnieuw opstarten en beschik baarheid:** De wissel wordt gewacht op elke instantie in de bron sleuf om de computer opnieuw op te starten en voor aanvragen beschikbaar te stellen. Als een exemplaar niet opnieuw wordt opgestart, worden alle wijzigingen in de bron sleuf door de wissel bewerking hersteld en wordt de bewerking gestopt.

1. **Route ring bijwerken:** Als alle exemplaren op de bron sleuf zijn opgewarmd, wordt de wisseling door de twee sleuven voltooid door middel van routerings regels te scha kelen. Na deze stap heeft de doel sleuf (bijvoorbeeld het productie gebied) de app die eerder is opgewarmd in de bron sleuf.

1. **Herhaal bewerking:** Nu de bron sleuf de pre-swap-app eerder in de doel sleuf heeft, voert u dezelfde bewerking uit door alle instellingen toe te passen en de instanties voor de bron sleuf opnieuw te starten.

Houd rekening met de volgende belangrijke punten:

- Op een wille keurig punt van de wissel bewerking wordt de initialisatie van de verwisselde apps uitgevoerd op de bron site. De doel sleuf blijft online terwijl de bron sleuf wordt voor bereid, of de wisseling slaagt of mislukt.

- Als u een faserings sleuf wilt vervangen door de productie sleuf, moet u ervoor zorgen dat de productie sleuf *altijd* de doel sleuf is. Op deze manier heeft de swap bewerking geen invloed op uw productie-app.

- Instellingen met betrekking tot gebeurtenis bronnen en bindingen moeten worden geconfigureerd als [implementatie sleuf instellingen](#manage-settings) *voordat u een swap initieert*. Als u deze voor het eerst markeert, zorgt u ervoor dat gebeurtenissen en uitvoer naar de juiste instantie worden gestuurd.

## <a name="manage-settings"></a>Instellingen beheren

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

### <a name="create-a-deployment-setting"></a>Een implementatie-instelling maken

U kunt instellingen markeren als een implementatie-instelling waarmee het ' Sticky ' wordt. Een plak instelling wordt niet verwisseld met het app-exemplaar.

Als u een implementatie-instelling in één sleuf maakt, moet u ervoor zorgen dat u dezelfde instelling maakt met een unieke waarde in een andere sleuf die bij een wissel betrokken is. Op die manier blijft de naam van de instelling consistent tussen sleuven, terwijl de waarde van een instelling niet verandert. Deze naam consistentie zorgt ervoor dat uw code geen toegang probeert te krijgen tot een instelling die in een sleuf is gedefinieerd, maar niet een andere.

Gebruik de volgende stappen om een implementatie-instelling te maken:

- Navigeer naar *sleuven* in de functie-app
- Klik op de naam van de sleuf
- Klik onder *platform functies > algemene instellingen*op **configuratie**
- Klik op de naam van de instelling die u wilt aanhouden met de huidige sleuf
- Klik op het selectie vakje **implementatie sleuf**
- Klik op **OK**
- Zodra de Blade is ingesteld verdwijnt, klikt u op **Opslaan** om de wijzigingen te bewaren

![Implementatie site-instelling](./media/functions-deployment-slots/azure-functions-deployment-slots-deployment-setting.png)

## <a name="deployment"></a>Implementatie

Sleuven zijn leeg wanneer u een sleuf maakt. U kunt een van de [ondersteunde implementatie technologieën](./functions-deployment-technologies.md) gebruiken om uw toepassing in een sleuf te implementeren.

## <a name="scaling"></a>Schalen

Alle sleuven worden geschaald naar hetzelfde aantal werk rollen als de productie site.

- Voor verbruiks abonnementen wordt de sleuf geschaald als de functie-app wordt geschaald.
- Voor App Service plannen wordt de app geschaald naar een vast aantal werk rollen. Sleuven worden uitgevoerd op hetzelfde aantal werk rollen als het app-plan.

## <a name="add-a-slot"></a>Een sleuf toevoegen

U kunt een sleuf toevoegen via de [cli](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-create) of via de portal. De volgende stappen laten zien hoe u een nieuwe sleuf maakt in de portal:

1. Navigeer naar de functie-app en klik op het **plus teken** naast *sleuven*.

    ![Azure Functions implementatie sleuf toevoegen](./media/functions-deployment-slots/azure-functions-deployment-slots-add.png)

1. Voer een naam in het tekstvak in en klik op de knop **maken** .

    ![Implementatie sleuf van naam Azure Functions](./media/functions-deployment-slots/azure-functions-deployment-slots-add-name.png)

## <a name="swap-slots"></a>Wisselings sleuven

U kunt sleuven wisselen via de [cli](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-swap) of via de portal. De volgende stappen laten zien hoe u sleuven in de portal kunt wisselen:

1. Ga naar de functie-app
1. Klik op de naam van de bron sleuf die u wilt wisselen
1. Klik op het tabblad *overzicht* op de wissel **knop** ![wisseling Azure functions implementatie sleuf](./media/functions-deployment-slots/azure-functions-deployment-slots-swap.png)
1. Controleer de configuratie-instellingen voor uw swap en **Klik op wisselen** ![swap Azure functions implementatie sleuf](./media/functions-deployment-slots/azure-functions-deployment-slots-swap-config.png)

De bewerking kan even duren wanneer de wissel bewerking wordt uitgevoerd.

## <a name="roll-back-a-swap"></a>Een swap terugdraaien

Als een wissel resulteert in een fout of als u simpelweg een wissel wilt herstellen, kunt u teruggaan naar de begin status. Als u wilt terugkeren naar de voorgewisselde status, voert u een andere swap uit om de wisseling om te keren.

## <a name="remove-a-slot"></a>Een sleuf verwijderen

U kunt een sleuf verwijderen via de [cli](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-delete) of via de portal. De volgende stappen laten zien hoe u een sleuf in de portal kunt verwijderen:

1. Ga naar het overzicht van de functie-app

1. Klik op de knop **verwijderen**

    ![Azure Functions implementatie sleuf toevoegen](./media/functions-deployment-slots/azure-functions-deployment-slots-delete.png)

## <a name="automate-slot-management"></a>Sleuf beheer automatiseren

Met de [Azure cli](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest)kunt u de volgende acties voor een sleuf automatiseren:

- [creëren](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-create)
- [verwijderd](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-delete)
- [list](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-list)
- [ruil](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-swap)
- [automatisch wisselen](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-auto-swap)

## <a name="change-app-service-plan"></a>App Service plan wijzigen

Met een functie-app die wordt uitgevoerd onder een App Service-abonnement, hebt u de mogelijkheid om het onderliggende App Service plan voor een sleuf te wijzigen.

> [!NOTE]
> U kunt het App Service plan van een sleuf onder het verbruiks abonnement niet wijzigen.

Gebruik de volgende stappen om het App Service plan van een sleuf te wijzigen:

1. Naar een sleuf navigeren

1. Klik onder *platform functies*op **alle instellingen**

    ![App service-abonnement wijzigen](./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-settings.png)

1. Klik op **app service plan**

1. Een nieuw App Service plan selecteren of een nieuw abonnement maken

1. Klik op **OK**

    ![App service-abonnement wijzigen](./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-select.png)


## <a name="limitations"></a>Beperkingen

Azure Functions implementatie sleuven hebben de volgende beperkingen:

- Het aantal beschik bare sleuven voor een app is afhankelijk van het plan. Het verbruiks abonnement is slechts één implementatie sleuf toegestaan. Er zijn extra sleuven beschikbaar voor apps die worden uitgevoerd onder het App Service-abonnement.
- Als u een sleuf verwisselt, worden sleutels voor `AzureWebJobsSecretStorageType` apps waarvan de app `files`-instelling gelijk is aan ingesteld.
- Er zijn geen sleuven beschikbaar voor het verbruiks abonnement voor Linux.

## <a name="support-levels"></a>Ondersteunings niveaus

Er zijn twee ondersteunings niveaus voor implementatie sites:

- **Algemene Beschik baarheid (ga)**: volledig ondersteund en goedgekeurd voor productie gebruik.
- **Preview**: nog niet ondersteund, maar er wordt naar verwachting de Ga-status in de toekomst bereikt.

| Besturings systeem/hosting abonnement           | Ondersteunings niveau     |
| ------------------------- | -------------------- |
| Windows-verbruik       | Algemene beschikbaarheid |
| Windows Premium           | Algemene beschikbaarheid  |
| Windows toegewezen         | Algemene beschikbaarheid |
| Linux-verbruik         | Niet ondersteund          |
| Linux Premium             | Algemene beschikbaarheid  |
| Speciaal voor Linux           | Algemene beschikbaarheid |

## <a name="next-steps"></a>Volgende stappen

- [Implementatie technologieën in Azure Functions](./functions-deployment-technologies.md)
