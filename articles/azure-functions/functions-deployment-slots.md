---
title: Implementatiesleuven voor Azure-functies
description: Meer informatie over het maken en gebruiken van implementatiesleuven met Azure-functies
author: craigshoemaker
ms.topic: reference
ms.date: 08/12/2019
ms.author: cshoe
ms.openlocfilehash: 0e8c93ea6d5c2b525ccbea2af900f100afcc3d93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75769214"
---
# <a name="azure-functions-deployment-slots"></a>Implementatiesleuven voor Azure-functies

Met de implementatiesleuven voor Azure Functions kan de functie-app verschillende exemplaren uitvoeren, de zogenaamde 'slots'. Slots zijn verschillende omgevingen blootgesteld via een openbaar beschikbaar eindpunt. Eén app-exemplaar wordt altijd toegewezen aan de productiesleuf en u instanties die zijn toegewezen aan een sleuf op aanvraag wisselen. Functie-apps die onder het Apps Service-abonnement worden uitgevoerd, kunnen meerdere sleuven hebben, terwijl onder het verbruiksplan slechts één sleuf is toegestaan.

Het volgende geeft aan hoe functies worden beïnvloed door het verwisselen van slots:

- De omleiding van het verkeer verloopt naadloos; geen aanvragen worden verwijderd vanwege een swap.
- Als een functie wordt uitgevoerd tijdens een swap, wordt de uitvoering voortgezet en worden de volgende triggers doorgestuurd naar de verwisselde app-instantie.

> [!NOTE]
> Slots zijn momenteel niet beschikbaar voor het Linux-verbruiksplan.

## <a name="why-use-slots"></a>Waarom slots gebruiken?

Er zijn een aantal voordelen aan het gebruik van implementatieslots. In de volgende scenario's worden veelvoorkomende toepassingen voor sleuven beschreven:

- **Verschillende omgevingen voor verschillende doeleinden:** Het gebruik van verschillende sleuven geeft u de mogelijkheid om app-exemplaren te differentiëren voordat u overwisselt naar productie of een faseringssleuf.
- **Prewarming**: Door te implementeren in een sleuf in plaats van rechtstreeks naar de productie, kan de app opwarmen voordat u live gaat. Bovendien vermindert het gebruik van sleuven de latentie voor HTTP-geactiveerde workloads. Instanties worden voor de implementatie opgewarmd, waardoor de koude start voor nieuw geïmplementeerde functies wordt verminderd.
- **Eenvoudige terugval**: Na een swap met productie heeft de sleuf met een eerder geënsceneerde app nu de vorige productie-app. Als de wijzigingen die zijn omgeruild in de productiesleuf niet zijn zoals u verwacht, u de swap onmiddellijk omkeren om uw laatst bekende goede instantie terug te krijgen.

## <a name="swap-operations"></a>Swapbewerkingen

Tijdens een swap wordt de ene sleuf beschouwd als de bron en het andere het doel. De bronsleuf heeft de instantie van de toepassing die wordt toegepast op de doelsleuf. De volgende stappen zorgen ervoor dat de doelsleuf geen downtime ervaart tijdens een swap:

1. **Instellingen toepassen:** Instellingen vanuit de doelsleuf worden toegepast op alle exemplaren van de bronsleuf. De productie-instellingen worden bijvoorbeeld toegepast op de faseringsinstantie. De toegepaste instellingen omvatten de volgende categorieën:
    - [Sleufspecifieke app-instellingen](#manage-settings) en verbindingstekenreeksen (indien van toepassing)
    - [Instellingen voor continue implementatie](../app-service/deploy-continuous-deployment.md) (indien ingeschakeld)
    - [Verificatie-instellingen voor App-service](../app-service/overview-authentication-authorization.md) (indien ingeschakeld)

1. **Wacht op herstart en beschikbaarheid:** De swap wacht op elk exemplaar in de bronsleuf om de herstart te voltooien en beschikbaar te zijn voor aanvragen. Als een instantie niet opnieuw wordt opgestart, wordt alle wijzigingen in de bronsleuf teruggezet en wordt de bewerking gestopt.

1. **Routering bijwerken:** Als alle instanties op de bronsleuf met succes worden opgewarmd, voltooien de twee sleuven de swap door van routeringsregels te wisselen. Na deze stap heeft de doelsleuf (bijvoorbeeld de productiesleuf) de app die eerder is opgewarmd in de bronsleuf.

1. **Herhaalde bewerking:** Nu de bronsleuf de pre-swap-app eerder in de doelsleuf heeft, voert u dezelfde bewerking uit door alle instellingen toe te passen en de instanties voor de bronsleuf opnieuw te starten.

Houd rekening met de volgende belangrijke punten:

- Op elk punt van de swapbewerking vindt initialisatie van de geruilde apps plaats op de bronsleuf. De doelsleuf blijft online terwijl de bronsleuf wordt voorbereid, ongeacht of de swap slaagt of mislukt.

- Als u een faseringssleuf wilt verwisselen met de productiesleuf, moet u ervoor zorgen dat de productiesleuf *altijd* de doelsleuf is. Op deze manier heeft de swapbewerking geen invloed op uw productie-app.

- Instellingen met betrekking tot gebeurtenisbronnen en bindingen moeten worden geconfigureerd als [instellingen voor implementatiesleuf](#manage-settings) *voordat u een swap start.* Markering ze als "sticky" van tevoren zorgt ervoor dat gebeurtenissen en uitgangen worden gericht op de juiste instantie.

## <a name="manage-settings"></a>Instellingen beheren

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

### <a name="create-a-deployment-setting"></a>Een implementatieinstelling maken

U instellingen markeren als een implementatie-instelling waardoor het plakkerig is. Een plakkerige instelling wisselt niet met de app-instantie.

Als u een implementatieinstelling maakt in één sleuf, moet u dezelfde instelling maken met een unieke waarde in een andere sleuf die betrokken is bij een swap. Op deze manier, terwijl de waarde van een instelling niet verandert, blijven de instellingsnamen consistent tussen sleuven. Deze naamconsistentie zorgt ervoor dat uw code geen toegang probeert te krijgen tot een instelling die in de ene sleuf is gedefinieerd, maar niet in een andere.

Gebruik de volgende stappen om een implementatieinstelling te maken:

- Navigeren naar *Sleuven* in de functie-app
- Klik op de sleufnaam
- Klik *onder Platformfuncties > algemene instellingen*op **Configuratie**
- Klik op de instellingsnaam die u bij de huidige sleuf wilt houden
- Klik op het **selectievakje Instelling implementatiesleuf**
- Klik **op OK**
- Zodra het instelblad is verdwenen, klikt u op **Opslaan** om de wijzigingen te behouden

![Instelling voor implementatiesleuf](./media/functions-deployment-slots/azure-functions-deployment-slots-deployment-setting.png)

## <a name="deployment"></a>Implementatie

Slots zijn leeg wanneer u een sleuf maakt. U een van de [ondersteunde implementatietechnologieën](./functions-deployment-technologies.md) gebruiken om uw toepassing in een sleuf te implementeren.

## <a name="scaling"></a>Schalen

Alle sleuven schalen naar hetzelfde aantal werknemers als de productiesleuf.

- Voor verbruiksplannen wordt de sleuf geschaald als de functie-app.
- Voor App Service-abonnementen wordt de app geschaald naar een vast aantal werknemers. Slots worden uitgevoerd op hetzelfde aantal werknemers als het app-abonnement.

## <a name="add-a-slot"></a>Een sleuf toevoegen

U een sleuf toevoegen via de [CLI](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-create) of via de portal. De volgende stappen laten zien hoe u een nieuwe sleuf in de portal maakt:

1. Navigeer naar de functie-app en klik op het **plusteken** naast *Slots.*

    ![Implementatiesleuf voor Azure-functies toevoegen](./media/functions-deployment-slots/azure-functions-deployment-slots-add.png)

1. Voer een naam in het tekstvak in en druk op de knop **Maken.**

    ![Implementatiesleuf Azure Functions een naam geven](./media/functions-deployment-slots/azure-functions-deployment-slots-add-name.png)

## <a name="swap-slots"></a>Slots ruilen

U slots wisselen via de [CLI](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-swap) of via de portal. De volgende stappen laten zien hoe u slots in de portal verwisselen:

1. Navigeren naar de functie-app
1. Klik op de naam van het bronslot die u wilt verwisselen
1. Klik op het tabblad *Overzicht* ![op **de** implementatiesleuf Azure-functies swap](./media/functions-deployment-slots/azure-functions-deployment-slots-swap.png)
1. Controleer de configuratie-instellingen voor uw swap en klik op Azure-implementatiesleuf **swapswap** ![](./media/functions-deployment-slots/azure-functions-deployment-slots-swap-config.png)

De bewerking kan even duren voordat de swapbewerking wordt uitgevoerd.

## <a name="roll-back-a-swap"></a>Een swap terugdraaien

Als een swap resulteert in een fout of als u gewoon een swap wilt ongedaan maken, u terugdraaien naar de oorspronkelijke status. Als u wilt terugkeren naar de vooraf geruilde status, doet u een andere swap om de swap om te keren.

## <a name="remove-a-slot"></a>Een sleuf verwijderen

U een sleuf verwijderen via de [CLI](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-delete) of via de portal. De volgende stappen laten zien hoe u een sleuf in de portal verwijdert:

1. Navigeren naar het overzicht van de functie-app

1. Klik op de knop **Verwijderen**

    ![Implementatiesleuf voor Azure-functies toevoegen](./media/functions-deployment-slots/azure-functions-deployment-slots-delete.png)

## <a name="automate-slot-management"></a>Slotbeheer automatiseren

Met de [Azure CLI](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest)u de volgende acties voor een sleuf automatiseren:

- [Maken](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-create)
- [Verwijderen](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-delete)
- [list](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-list)
- [Swap](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-swap)
- [auto-swap](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-auto-swap)

## <a name="change-app-service-plan"></a>App-serviceplan wijzigen

Met een functie-app die wordt uitgevoerd onder een App Service-abonnement, hebt u de mogelijkheid om het onderliggende App Service-abonnement voor een sleuf te wijzigen.

> [!NOTE]
> U het App-serviceplan van een sleuf niet wijzigen onder het abonnement Verbruik.

Gebruik de volgende stappen om het App Service-abonnement van een sleuf te wijzigen:

1. Navigeren naar een sleuf

1. Klik *onder Platformfuncties*op **Alle instellingen**

    ![App-serviceplan wijzigen](./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-settings.png)

1. Klik op **App Service-abonnement**

1. Selecteer een nieuw App Service-abonnement of maak een nieuw abonnement

1. Klik **op OK**

    ![App-serviceplan wijzigen](./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-select.png)


## <a name="limitations"></a>Beperkingen

Implementatiesleuven voor Azure Functions hebben de volgende beperkingen:

- Het aantal slots dat beschikbaar is voor een app is afhankelijk van het abonnement. Het verbruiksplan is slechts één implementatiesleuf toegestaan. Er zijn extra sleuven beschikbaar voor apps die worden uitgevoerd onder het App-service-abonnement.
- Als u een sleuf verwisselt, `AzureWebJobsSecretStorageType` worden de `files`toetsen opnieuw ingesteld voor apps met een app-instelling die gelijk is aan .
- Slots zijn niet beschikbaar voor het Linux-verbruiksplan.

## <a name="support-levels"></a>Ondersteuningsniveaus

Er zijn twee ondersteuningsniveaus voor implementatieslots:

- **Algemene beschikbaarheid (GA)**: Volledig ondersteund en goedgekeurd voor productiegebruik.
- **Preview**: Nog niet ondersteund, maar zal naar verwachting ga-status te bereiken in de toekomst.

| OS/Hosting-abonnement           | Ondersteuningsniveau     |
| ------------------------- | -------------------- |
| Windows-verbruik       | Algemene beschikbaarheid |
| Windows Premium           | Algemene beschikbaarheid  |
| Windows Dedicated         | Algemene beschikbaarheid |
| Linux-verbruik         | Niet ondersteund          |
| Linux Premium             | Algemene beschikbaarheid  |
| Linux Dedicated           | Algemene beschikbaarheid |

## <a name="next-steps"></a>Volgende stappen

- [Implementatietechnologieën in Azure-functies](./functions-deployment-technologies.md)
