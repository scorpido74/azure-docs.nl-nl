---
title: Test stations configureren in micro soft Commercial Marketplace
description: Meer informatie over test stations. Met test stations kunnen nieuwe klanten uw aanbieding test drive voordat ze de aankoop door voeren.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/13/2019
ms.openlocfilehash: 8bd17858fa6866549088de8307f40d7b79daef6b
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82786543"
---
# <a name="learn-about-test-drive-for-your-offer"></a>Meer informatie over test drive voor uw aanbieding

Met de optie **test drive** in de micro soft Commercial Marketplace kunt u een zelf-doorgeleide rond leiding door de belangrijkste functies van uw product configureren. Met een test drive kunnen nieuwe klanten uw aanbieding uitproberen voordat ze zich vastleggen om deze aan te schaffen. Zie [Wat is een test station?](../cloud-partner-portal/test-drive/what-is-test-drive.md)voor meer informatie.

Als u geen test drive voor uw aanbieding wilt opgeven, keert u terug naar de pagina voor het instellen van de **aanbieding** en schakelt u **Test Drive inschakelen**in.

## <a name="technical-configuration"></a>Technische configuratie

De volgende typen test stations zijn beschikbaar, elk met hun eigen technische configuratie vereisten.

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [Logische app](#technical-configuration-for-logic-app-test-drive)
- [Power bi](#technical-configuration-not-required-for-power-bi-test-drives) (technische configuratie niet vereist)

### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Technische configuratie voor Azure Resource Manager test drive

Er is een implementatie sjabloon die alle Azure-resources bevat waaruit uw oplossing bestaat. Dit is geschikt voor producten die alleen Azure-resources gebruiken. Meer informatie over het instellen van een [Azure Resource Manager test drive](../cloud-partner-portal/test-drive/azure-resource-manager-test-drive.md).

- **Regio's** (vereist): momenteel zijn er 26 door Azure ondersteunde regio's waar uw test drive beschikbaar kan worden gemaakt. Normaal gesp roken wilt u uw test drive beschikbaar maken in de regio's waar u het grootste aantal klanten verwacht, zodat ze de dichtstbijzijnde regio voor de beste prestaties kunnen selecteren. U moet ervoor zorgen dat uw abonnement alle resources mag implementeren die nodig zijn voor elk van de regio's die u selecteert.

- **Instanties**: Selecteer het type (warme of koud) en het aantal beschik bare instanties dat wordt vermenigvuldigd met het aantal regio's waar uw aanbieding beschikbaar is.

  - **Dynamisch**: dit type exemplaar wordt geïmplementeerd en wacht op toegang per geselecteerde regio. Klanten hebben direct toegang tot *Hot* instances van een test drive in plaats van te wachten op een implementatie. De balans is dat deze instanties altijd worden uitgevoerd op uw Azure-abonnement, zodat de kosten voor de uptime groter worden. Het wordt ten zeerste aanbevolen om ten minste één *Hot* instance te hebben, aangezien de meeste klanten niet hoeven te wachten op volledige implementaties, wat leidt tot uitval van klant gebruik als er geen *Hot* instance beschikbaar is.

  - **Koud**: dit type exemplaar vertegenwoordigt het totale aantal instanties dat mogelijk per regio kan worden geïmplementeerd. Voor koude instanties is het hele test station Resource Manager-sjabloon vereist om te implementeren wanneer een klant de test drive aanvraagt, zodat *koude* instanties veel langzamer worden geladen dan *Hot* instances. Het bedrag dat u alleen hoeft te betalen voor de duur van de test drive, wordt *niet* altijd uitgevoerd op uw Azure-abonnement, net als bij een *Hot* -instance.

- **Azure Resource Manager sjabloon voor het station testen**: Upload de. zip met uw Azure Resource Manager sjabloon.  Meer informatie over het maken van een Azure Resource Manager sjabloon in het Quick Start-artikel [Azure Resource Manager sjablonen maken en implementeren met behulp van de Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

- **Duur van test drive** (vereist): Geef aan hoe lang het test station actief blijft, in aantal uren. Het test station wordt automatisch beëindigd nadat deze periode is verstreken. Deze duur mag alleen worden ingesteld met een geheel aantal uren (bijvoorbeeld ' 2 ' uur, ' 1,5 ' is niet geldig).

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Technische configuratie voor Dynamics 365 test drive

Micro soft kan de complexiteit van het instellen van een test drive verwijderen door de service-inrichting en-implementatie te hosten en te onderhouden met dit type test drive. De configuratie voor dit type gehoste test drive is hetzelfde, ongeacht of de test drive is gericht op een zakelijke centrale, klant afspraak of bewerkings publiek.

- **Maxi maal aantal gelijktijdige test schijven** (vereist): Stel het maximum in voor de klanten die uw test drive tegelijk kunnen gebruiken. Elke gelijktijdige gebruiker gebruikt een Dynamics 365-licentie terwijl de test drive actief is. u moet er dus voor zorgen dat er voldoende licenties beschikbaar zijn voor de ondersteuning van de maximum limiet. Aanbevolen waarde van 3-5.

- **Duur van test drive** (vereist): Geef aan hoe lang het test station actief blijft door het aantal uren te definiëren. Na dit aantal uur wordt de sessie beëindigd en wordt een van uw licenties niet meer verbruikt. We raden een waarde van 2-24 uur aan, afhankelijk van de complexiteit van uw aanbieding. Deze duur mag alleen worden ingesteld met een geheel aantal uren (bijvoorbeeld ' 2 ' uur, ' 1,5 ' is niet geldig).  De gebruiker kan een nieuwe sessie aanvragen als deze niet langer actief zijn en de test drive opnieuw willen gebruiken.

- **URL van exemplaar** (vereist): de URL waar de klant de test drive begint. Doorgaans de URL van uw Dynamics 365-exemplaar waarop uw app wordt uitgevoerd, waarbij voorbeeld gegevens zijn `https://testdrive.crm.dynamics.com`geïnstalleerd (bijvoorbeeld).

- **URL** van de Web-API voor het exemplaar (vereist): Haal de Web-API-URL voor uw Dynamics 365-exemplaar op door u aan te melden bij uw Microsoft 365-account en te navigeren naar **instellingen** \&gt; **Aanpassing** \&gt; **Bronnen** \&voor ontwikkel aars gt; **Web-API van het exemplaar (Service root URL)**, kopieer de URL die u hier `https://testdrive.crm.dynamics.com/api/data/v9.0`kunt vinden (bijvoorbeeld).

- **Rolnaam** (vereist): Geef de naam op van de beveiligingsrol die u hebt gedefinieerd in uw aangepaste Dynamics 365-test drive. Deze wordt toegewezen aan de gebruiker tijdens hun test drive (bijvoorbeeld test station-Role).

### <a name="technical-configuration-for-logic-app-test-drive"></a>Technische configuratie voor de test drive Logic-app

Aangepaste producten moeten gebruikmaken van dit type test drive-implementatie sjabloon dat een verscheidenheid aan complexe oplossings architecturen omvat. Ga voor meer informatie over het instellen van de test schijven voor logische apps naar [activiteiten](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) en [klanten betrokkenheid](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) op github.

- **Regio** (vereist, vervolg keuzelijst met één selectie): er zijn momenteel 26 door Azure ondersteunde regio's waar uw test drive beschikbaar kan worden gemaakt. De resources voor uw logische app worden geïmplementeerd in de regio die u selecteert. Als uw logische app aangepaste resources heeft die zijn opgeslagen in een bepaalde regio, moet u ervoor zorgen dat de regio hier wordt geselecteerd. De beste manier om dit te doen is om uw logische app lokaal te implementeren in uw Azure-abonnement in de portal en te controleren of deze correct werkt voordat u deze selectie maakt.

- **Maxi maal aantal gelijktijdige test schijven** (vereist): Stel het maximum in voor de klanten die uw test drive tegelijk kunnen gebruiken. Deze test stations zijn al geïmplementeerd, zodat klanten deze direct kunnen openen zonder te hoeven wachten op een implementatie.

- **Duur van test drive** (vereist): Geef aan hoe lang het test station actief blijft, in aantal uren. De test drive wordt automatisch beëindigd nadat deze periode is verstreken.

- **Naam van de Azure-resource groep** (vereist): Voer de naam in van de [Azure-resource groep](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) waar de logische app test drive wordt opgeslagen.

- **Naam van de Azure Logic-app** (vereist): Voer de naam in van de logische app die de test drive aan de gebruiker toewijst. Deze logische app moet worden opgeslagen in de bovenstaande Azure-resource groep.

- De naam van de **logische app** voor het ongedaan maken van de inrichting (vereist): Voer de naam in van de logische app die de test drive nadat de klant is voltooid. Deze logische app moet worden opgeslagen in de bovenstaande Azure-resource groep.

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Technische configuratie niet vereist voor Power BI test stations

Producten die een interactief Power BI visueel element willen demonstreren, kunnen een Inge sloten koppeling gebruiken om een aangepast dash board te delen als hun test drive, maar er is geen verdere technische configuratie vereist. Meer informatie over het instellen van [Power bi](https://docs.microsoft.com/power-bi/service-template-apps-overview) sjabloon-apps.

## <a name="deployment-subscription-details"></a>Details van implementatie abonnement

Als u het test station namens u wilt implementeren, maakt en geeft u een afzonderlijk, uniek Azure-abonnement. (Niet vereist voor Power BI test stations).

- **Azure-abonnements-id** (vereist voor Azure Resource Manager en Logic apps): Voer de abonnements-id in om toegang te verlenen tot uw Azure-account services voor rapportage en facturering van resource gebruik. Het is raadzaam om [een afzonderlijk Azure-abonnement te maken](https://docs.microsoft.com/azure/billing/billing-create-subscription) dat u voor test stations kunt gebruiken als u er nog geen hebt. U kunt uw Azure-abonnements-ID vinden door u aan te melden bij de [Azure Portal](https://portal.azure.com/) en te navigeren naar het tabblad **abonnementen** van het menu aan de linkerkant. Als u het tabblad selecteert, wordt uw abonnements-ID `a83645ac-1234-5ab6-6789-1h234g764ghty`weer gegeven (bijvoorbeeld).

- **Azure AD-Tenant-id** (vereist): voer uw Azure Active Directory (AD) [Tenant-id](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)in. Als u deze ID wilt vinden, meldt u zich aan bij de [Azure Portal](https://portal.azure.com/), selecteert u het tabblad Active Directory in het menu links, selecteert u **Eigenschappen** en zoekt u naar de weer gegeven `50c464d3-4930-494c-963c-1e951d15360e` **Directory-id** (bijvoorbeeld). U kunt ook de Tenant-ID van uw organisatie opzoeken met uw domein naam-URL [https://www.whatismytenantid.com](https://www.whatismytenantid.com)op:.

- **Naam van Azure AD-Tenant** (vereist voor dynamische 365): Voer de naam van uw Azure Active Directory (AD) in. Als u deze naam wilt vinden, meldt u zich aan bij de [Azure Portal](https://portal.azure.com/). in de rechter bovenhoek wordt de naam van de Tenant vermeld onder uw account naam.

- **Azure AD-App-ID** (vereist): voer uw Azure Active Directory (AD) [-toepassings-id](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)in. Als u deze ID wilt vinden, meldt u zich aan bij de [Azure Portal](https://portal.azure.com/), selecteert u het tabblad Active Directory in het menu links, selecteert u **app-registraties**en zoekt u naar het **toepassings-id-** nummer dat wordt weer gegeven (bijvoorbeeld `50c464d3-4930-494c-963c-1e951d15360e`).

- **Azure AD-App-client geheim** (vereist): Voer uw Azure AD-toepassings [client geheim](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)in. Als u deze waarde wilt vinden, meldt u zich aan bij de [Azure Portal](https://portal.azure.com/). Selecteer in het linkermenu het tabblad **Azure Active Directory** , selecteer **app-registraties**en selecteer vervolgens uw test drive-app. Selecteer vervolgens **certificaten en geheimen**, selecteer **Nieuw client geheim**, voer een beschrijving in, selecteer **nooit** onder **verlooptd**en kies vervolgens **toevoegen**. Zorg ervoor dat u de waarde kopieert. (Verlaat de pagina niet voordat u dit doet, anders hebt u geen toegang tot de waarde.)

Vergeet niet om te **slaan** voordat u verdergaat met de volgende sectie.

## <a name="test-drive-listings-optional"></a>Vermeldingen voor het test station (optioneel)

De optie voor het **testen van stations** die is gevonden op het tabblad **test station** bevat de talen (en markten) waar uw test drive beschikbaar is. op Verenigde Staten dit moment is de enige locatie beschikbaar. Daarnaast wordt op deze pagina de status weer gegeven van de taalspecifieke vermelding en de datum/tijd waarop deze is toegevoegd. U moet de test drive Details (beschrijving, gebruikers handleiding, Video's, enzovoort) definiëren voor elke taal/markt.

- **Beschrijving** (vereist): Geef een beschrijving van uw test drive, wat wordt aangetoond, wat de gebruiker kan doen om te experimenteren met, functies om te verkennen en alle relevante informatie om de gebruiker te helpen bepalen of u uw aanbieding moet verkrijgen. In dit veld kunnen Maxi maal 3.000 tekens tekst worden ingevoerd.

- **Toegangs gegevens** (vereist voor Azure Resource Manager en logische test stations): Leg uit wat een klant moet weten om deze test drive te kunnen openen en gebruiken. Door loop een scenario voor het gebruik van uw aanbieding en precies wat de klant moet weten voor toegang tot de functies in de test drive. In dit veld kunnen Maxi maal 10.000 tekens tekst worden ingevoerd.

- **Gebruikers handleiding** (vereist): een diep gaande overzicht van uw test drive ervaring. De gebruikers handleiding moet betrekking hebben op precies wat u wilt dat de klant het test drive verkrijgt en zich als referentie voor eventuele vragen bevindt. Het bestand moet een PDF-indeling hebben en een naam hebben (255 tekens Max) na het uploaden.

- **Video's: Video's toevoegen** (optioneel): Video's kunnen worden geüpload naar YouTube of Vimeo en hiernaar wordt verwezen met een koppeling en een miniatuur afbeelding (533 x 324 pixels), zodat een klant een overzicht van de informatie kan bekijken om hen meer inzicht te geven in de test drive, met inbegrip van de manier waarop u de functies van uw aanbieding kunt gebruiken en inzicht kunt krijgen in de voor delen.
  - **Naam** (vereist)
  - **URL (alleen YouTube of Vimeo)** (vereist)
  - **Miniatuur (533 x 324 px)** : het afbeeldings bestand moet de PNG-indeling hebben.

Selecteer **Opslaan** na het volt ooien van deze velden.

## <a name="next-steps"></a>Volgende stappen

[Een bestaande aanbieding bijwerken in Commerciële Marketplace](./update-existing-offer.md)
