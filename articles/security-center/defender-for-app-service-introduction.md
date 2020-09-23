---
title: 'Azure Defender voor App Service: de voor delen en functies'
description: Meer informatie over de voor delen en functies van Azure Defender voor App Service.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: d15e881ca1246d463d8054fe5bd094efaff0cdd5
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90977174"
---
# <a name="introduction-to-azure-defender-for-app-service"></a>Inleiding tot Azure Defender voor App Service

Azure App Service is een volledig beheerd platform voor het bouwen en hosten van uw web-apps en Api's zonder dat u zich zorgen hoeft te maken over het beheer van de infra structuur. Het biedt beheer, bewaking en operationele inzichten om te voldoen aan de vereisten voor prestaties, beveiliging en naleving van de bedrijfs kwaliteit. Zie [Azure app service](https://azure.microsoft.com/services/app-service/)voor meer informatie.

**Azure Defender voor app service** gebruikt de schaal van de cloud om aanvallen te identificeren die gericht zijn op toepassingen die meer dan app service. Aanvallers kunnen webtoepassingen testen om zwakke plekken te vinden en misbruik te maken. Aanvragen voor toepassingen die in Azure worden uitgevoerd, worden voordat ze naar specifieke omgevingen worden doorgestuurd via verschillende gateways, waar ze worden geïnspecteerd en geregistreerd. Deze gegevens worden vervolgens gebruikt om aanvallen en aanvallers te identificeren en om nieuwe patronen te leren die later zullen worden gebruikt.

Door de zicht baarheid van Azure als een Cloud provider te gebruiken, Security Center analyseert App Service interne Logboeken om een aanvals methodologie op meerdere doelen te identificeren. Zo omvat de methodologie uitgebreide scans en gedistribueerde aanvallen. Dit type aanval is doorgaans afkomstig uit een kleine subset van IP-adressen en toont patronen van het verkennen naar vergelijk bare eind punten op meerdere hosts. De aanvallen zoeken naar een kwets bare pagina of invoeg toepassing en kunnen niet worden geïdentificeerd op basis van het standpunt van één host.


## <a name="availability"></a>Beschikbaarheid

|Aspect|Details|
|----|:----|
|Release status:|Preview|
|Koers|[Azure Defender voor app service](azure-defender.md) wordt gefactureerd zoals weer gegeven op [de pagina met prijzen](security-center-pricing.md)|
|Ondersteunde App Service plannen:|![Ja ](./media/icons/yes-icon.png) Basic, Standard, Premium, geïsoleerd of Linux<br>![Geen ](./media/icons/no-icon.png) gratis, gedeeld of verbruik<br>[Meer informatie over App Service-abonnementen](https://azure.microsoft.com/pricing/details/app-service/plans/)|
|Clouds|![Yes](./media/icons/yes-icon.png) Commerciële Clouds<br>![No](./media/icons/no-icon.png) National/soeverein (US Gov, China gov, andere gov)|
|||

## <a name="what-does-azure-defender-for-app-service-protect"></a>Wat beschermt Azure Defender voor App Service?

Wanneer het App Service-abonnement is ingeschakeld, bepaalt Security Center de resources die door uw App Service plan worden gedekt en worden beveiligings aanbevelingen gegenereerd op basis van de bevindingen. Security Center beschermt het VM-exemplaar waarin uw App Service wordt uitgevoerd en de beheer interface. Er worden ook aanvragen en antwoorden gecontroleerd die worden verzonden naar en van uw apps die worden uitgevoerd in App Service.

Als u een App Service-abonnement op basis van Windows uitvoert, heeft Security Center ook toegang tot de onderliggende sandboxes en virtuele machines. Samen met de hierboven vermelde logboek gegevens kan de infra structuur het verhaal vertellen van een nieuwe aanval die in het wild wordt circuleren om inbreuk te kunnen vormen op de klant machines. Daarom, zelfs als Security Center wordt geïmplementeerd nadat een web-app is misbruikt, kan het mogelijk worden doorlopende aanvallen te detecteren.


## <a name="protect-your-azure-app-service-web-apps-and-apis"></a>Web-apps en API's van Azure App Service beveiligen
U kunt als volgt uw Azure App Service plan beveiligen met Azure Defender voor App Service:

- Zorg ervoor dat u een ondersteund App Service-abonnement hebt dat is gekoppeld aan toegewezen machines. Ondersteunde abonnementen worden hierboven vermeld in de [Beschik baarheid](#availability).

- Schakel **Azure Defender** in voor uw abonnement (u kunt optioneel alleen het **Azure Defender for app service** -abonnement inschakelen), zoals wordt beschreven in de [prijs van Azure Security Center](security-center-pricing.md)

Security Center is systeem eigen geïntegreerd met App Service, waardoor implementatie en onboarding niet meer nodig is. de integratie is dus transparant.

>[!NOTE]
> De pagina prijs informatie en instellingen bevat een aantal exemplaren voor het **aantal resources**. Hiermee wordt het totale aantal Compute-exemplaren aangegeven, in alle App Service plannen voor dit abonnement, dat wordt uitgevoerd op het moment dat u de pagina prijs categorie hebt geopend.
>
> Azure App Service biedt diverse plannen. Uw App Service-abonnement definieert de set reken resources voor het uitvoeren van een web-app. Deze zijn gelijk aan server farms in conventionele webhosting. Een of meer apps kunnen worden geconfigureerd om te worden uitgevoerd op dezelfde computer bronnen (of in hetzelfde App Service-abonnement).
>
>Voor het valideren van het aantal, het hoofd naar ' App Service plannen ' in de Azure-Portal, waar u het aantal reken instanties kunt zien dat door elk abonnement wordt gebruikt. 



## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over Azure Defender voor App Service. 

Raadpleeg de volgende artikelen voor gerelateerde materialen: 

- Of een waarschuwing wordt gegenereerd door Security Center, of door Security Center van een ander beveiligings product is ontvangen, kunt u het exporteren. Als u uw waarschuwingen wilt exporteren naar Azure Sentinel, eventuele SIEM van derden of een ander extern hulp programma, volgt u de instructies in [waarschuwingen exporteren naar een Siem](continuous-export.md).
- Zie de [naslag tabel met waarschuwingen](alerts-reference.md#alerts-azureappserv)voor een overzicht van de waarschuwingen voor Azure app service.
- Zie [app service-plannen](https://azure.microsoft.com/pricing/details/app-service/plans/)voor meer informatie over het plannen van app service.
- > [!div class="nextstepaction"]
    > [Azure Defender inschakelen](security-center-pricing.md)