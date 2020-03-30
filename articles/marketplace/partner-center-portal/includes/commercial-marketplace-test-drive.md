---
title: bestand opnemen
description: bestand opnemen
documentationcenter: partner-center-commercial-marketplace
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
ms.date: 08/13/2019
ms.author: dsindona
ms.custom: include file
ms.openlocfilehash: f2446a924ceed37c51779efc9d9e94c0252a2067
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80321876"
---
Met het tabblad **Teststation** u een demonstratie (of testrit) instellen waarmee klanten uw aanbieding kunnen proberen voordat ze deze willen kopen. Meer informatie in het artikel [Wat is Test Drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive). Als u geen testrit meer wilt geven voor uw aanbieding, gaat u terug naar de pagina **Installatie aanbieden** en schakelt u de **testrit inschakelen uit.**

### <a name="technical-configuration"></a>Technische configuratie
De volgende typen testritten zijn beschikbaar, elk met hun eigen technische configuratievereisten.

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [Logische app](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives) (Technische configuratie niet vereist)

#### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Technische configuratie voor Azure Resource Manager-teststation

Een implementatiesjabloon die alle Azure-bronnen bevat die uw oplossing bevatten. Producten die in dit scenario passen, gebruiken alleen Azure-bronnen. Meer informatie over het instellen van een [Azure Resource Manager-teststation](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

- **Regio's** (vereist): Momenteel zijn er 26 door Azure ondersteunde regio's waar uw testrit beschikbaar kan worden gesteld. Normaal gesproken wilt u uw testrit beschikbaar maken in de regio's waar u anticipeert op het grootste aantal klanten, zodat ze de dichtstbijzijnde regio kunnen selecteren voor de beste prestaties. U moet ervoor zorgen dat uw abonnement alle benodigde resources kan implementeren in elk van de regio's die u selecteert.

- **Instanties**: Selecteer het type (warm of koud) en het aantal beschikbare exemplaren, dat wordt vermenigvuldigd met het aantal regio's waar uw aanbieding beschikbaar is.

**Hot:** Dit type instantie wordt geïmplementeerd en wacht op toegang per geselecteerde regio. Klanten hebben direct toegang tot *Hot-exemplaren* van een testrit, in plaats van te hoeven wachten op een implementatie. De afweging is dat deze exemplaren altijd worden uitgevoerd op uw Azure-abonnement, zodat ze een grotere uptime kosten. Het wordt ten zeerste aanbevolen om ten minste één *Hot-exemplaar* te hebben, omdat de meeste klanten niet willen wachten op volledige implementaties, wat resulteert in een drop-off in het gebruik van de klant als er geen *Hot-exemplaar* beschikbaar is.

**Koud:** dit type instantie vertegenwoordigt het totale aantal exemplaren dat mogelijk per regio kan worden geïmplementeerd. Koude instanties vereisen dat de volledige sjabloon Test Drive Resource Manager wordt geïmplementeerd wanneer een klant de testrit aanvraagt, zodat *cold* instances veel langzamer worden geladen dan *Hot-exemplaren.* De afweging is dat u alleen hoeft te betalen voor de duur van de testrit, het wordt *niet* altijd uitgevoerd op uw Azure-abonnement zoals bij een *Hot-exemplaar.*

- **Sjabloon Azure Resource Manager voor testsstation:** upload de .zip met uw Azure Resource Manager-sjabloon.  Meer informatie over het maken van een Azure Resource Manager-sjabloon in het quickstart-artikel [Azure Resource Manager-sjablonen maken en implementeren met behulp van de Azure-portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

- **Duur van de testrit** (vereist): Voer de tijdsduur in dat de testrit in aantal uren actief blijft. De testdrive eindigt automatisch nadat deze periode is afgelopen. Deze duur kan slechts worden ingesteld door een geheel aantal uren (bijvoorbeeld "2" uur, "1,5" is niet geldig).

#### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Technische configuratie voor Dynamics 365-testrit

Microsoft kan de complexiteit van het instellen van een teststation verwijderen door de servicevoorziening en -implementatie te hosten en te onderhouden met behulp van dit type teststation. De configuratie voor dit type gehoste testrit is hetzelfde, ongeacht of de testrit is gericht op een Business Central-, Customer Engagement- of Operations-doelgroep.

- **Maximale gelijktijdige testritten** (vereist): Stel het maximum aantal klanten in dat uw testrit in één keer kan gebruiken. Elke gelijktijdige gebruiker verbruikt een Dynamics 365-licentie terwijl de testrit actief is, dus u moet ervoor zorgen dat u voldoende licenties beschikbaar hebt om de ingestelde maximumlimiet te ondersteunen. Aanbevolen waarde van 3-5.

- **Duur van de testrit** (vereist): Voer de tijdsduur in dat de testrit actief blijft door het aantal uren te definiëren. Na deze vele uren wordt de sessie beëindigd en verbruikt de sessie geen van uw licenties meer. Afhankelijk van de complexiteit van uw aanbod raden wij u een waarde van 2-24 uur aan. Deze duur kan slechts worden ingesteld door een geheel aantal uren (bijvoorbeeld "2" uur, "1,5" is niet geldig).  De gebruiker kan een nieuwe sessie aanvragen als de tijd op raakt en de testrit opnieuw wil worden geopend.

- **Instantie-URL** (vereist): de URL waar de klant zijn testrit begint. Meestal is de URL van uw Dynamics 365-exemplaar waarop uw `https://testdrive.crm.dynamics.com`app wordt uitgevoerd met voorbeeldgegevens geïnstalleerd (bijvoorbeeld ).

- **Instance Web API URL** (vereist): Haal de URL van de web-API voor uw Dynamics 365-exemplaar op door in te loggen op uw Microsoft 365-account en door te navigeren naar **Instellingen** \&gt; **Aanpassing** \&gt; **Ontwikkelaarsbronnen** \&gt; **Instance Web API (Url van serviceroot)**, kopieer `https://testdrive.crm.dynamics.com/api/data/v9.0`de URL hier (bijv. ).

- **Rolnaam** (vereist): geef de beveiligingsrolnaam op die u hebt gedefinieerd in uw aangepaste Dynamics 365-testrit. Dit wordt tijdens de proefrit (bijv. test-drive-rol) aan de gebruiker toegewezen.

#### <a name="technical-configuration-for-logic-app-test-drive"></a>Technische configuratie voor het teststation van de Logic-app

Alle aangepaste producten moeten dit type implementatiesjabloon voor teststations gebruiken, die een verscheidenheid aan complexe oplossingsarchitecturen omvat. Ga voor meer informatie over het instellen van Logic App-testritten naar [Operations](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) en [Customer Engagement](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) op GitHub.

- **Regio** (vereiste vervolgkeuzelijst met één selectie): Momenteel zijn er 26 door Azure ondersteunde regio's waar uw testrit beschikbaar kan worden gesteld. De bronnen voor uw Logic-app worden geïmplementeerd in de regio die u selecteert. Als in uw Logic App aangepaste bronnen zijn opgeslagen in een specifieke regio, controleert u of dat gebied hier is geselecteerd. De beste manier om dit te doen is door uw Logic App lokaal volledig te implementeren op uw Azure-abonnement in de portal en te controleren of het correct functioneert voordat u deze selectie maakt.

- **Maximale gelijktijdige testritten** (vereist): Stel het maximum aantal klanten in dat uw testrit in één keer kan gebruiken. Deze testritten zijn al geïmplementeerd, zodat klanten er direct toegang toe hebben zonder te wachten op een implementatie.

- **Duur van de testrit** (vereist): Voer de tijdsduur in dat de testrit in aantal uren actief blijft. De testrit eindigt automatisch nadat deze periode is afgelopen.

- **Naam van azure-brongroepgroep** (vereist): voer de naam van de [Azure-brongroep](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) in waarin uw Logische App-teststation is opgeslagen.

- **Azure logic app name** (vereist): Voer de naam in van de Logic-app die de testrit aan de gebruiker toewijst. Deze Logic-app moet worden opgeslagen in de azure-bronnengroep hierboven.

- **Naam van de logische app deprovision** (vereist): voer de naam in van de Logic-app die de testrit deprovisioneert zodra de klant is voltooid. Deze Logic-app moet worden opgeslagen in de azure-bronnengroep hierboven.

#### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Technische configuratie niet vereist voor Power BI-testritten

Producten die een interactieve Power BI-visual willen demonstreren, kunnen een ingesloten koppeling gebruiken om een op maat gemaakt dashboard te delen als testrit, zonder verdere technische configuratie vereist. Meer informatie over het instellen van[Power BI-sjabloon-apps.](https://docs.microsoft.com/power-bi/service-template-apps-overview)

### <a name="deployment-subscription-details"></a>Details van het implementatieabonnement

Als u de testdrive namens u wilt implementeren, maakt en verstrekt u een afzonderlijk, uniek Azure-abonnement. (Niet vereist voor Power BI-testritten).

- **Azure-abonnements-ID** (vereist voor Azure Resource Manager- en Logic-apps): Voer de abonnements-id in om toegang te verlenen tot uw Azure-accountservices voor rapportage en facturering van resourcegebruik. We raden u aan om [een apart Azure-abonnement](https://docs.microsoft.com/azure/billing/billing-create-subscription) te maken dat u gebruiken voor testritten als u er nog geen hebt. U uw Azure-abonnements-id vinden door u aan te melden bij de [Azure-portal](https://portal.azure.com/) en te navigeren naar het tabblad **Abonnementen** van het linkermenu. Als u het tabblad selecteert, wordt uw abonnements-ID weergegeven (bijvoorbeeld "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **Azure AD-tenant-id** (vereist): voer uw Azure Active Directory (AD) [tenant ID in](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in). Als u deze id wilt vinden, meldt u zich aan bij de [Azure-portal,](https://portal.azure.com/)selecteert u het tabblad Active Directory in het linkermenu, selecteert u **Eigenschappen** en zoekt u vervolgens naar het vermelde **Directory-ID-nummer** (bijvoorbeeld 50c464d3-4930-494c-963c-1e951d15360e). U ook de tenant-id van uw organisatie [https://www.whatismytenantid.com](https://www.whatismytenantid.com)opzoeken met uw URL van uw domeinnaam op:.

- **Azure AD-tenantnaam** (vereist voor Dynamic 365): voer de naam van uw Azure Active Directory (AD) in. Als u deze naam wilt vinden, meldt u zich aan bij de [Azure-portal,](https://portal.azure.com/)in de rechterbovenhoek wordt uw tenantnaam vermeld onder uw accountnaam.

- **Azure AD-app-id** (vereist): voer uw Azure Active Directory (AD) [toepassings-id in](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in). Als u deze id wilt vinden, meldt u zich aan bij de [Azure-portal,](https://portal.azure.com/)selecteert u het tabblad Active Directory in het linkermenu, selecteert u **App-registraties**en zoekt u vervolgens naar het vermelde **toepassings-id-nummer** (bijvoorbeeld 50c464d3-4930-494c-963c-1e951d15360e).

- **Azure AD-app client geheim** (vereist): Voer het geheim van uw Azure AD-clientclient [in](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets). Als u deze waarde wilt vinden, meldt u zich aan bij de [Azure-portal.](https://portal.azure.com/) Selecteer het tabblad **Azure Active Directory** in het linkermenu, selecteer **App-registraties**en selecteer vervolgens de app voor een teststation. Selecteer vervolgens **Certificaten en geheimen,** selecteer **Nieuw clientgeheim,** voer een beschrijving in, selecteer **Nooit** onder **Verloopt**en kies **Vervolgens Toevoegen**. Zorg ervoor dat u de waarde naar beneden kopieert. (Navigeer niet weg van de pagina voordat u dit doet, anders hebt u geen toegang tot de waarde.)

Vergeet niet om op te **slaan** voordat je doorgaat naar de volgende sectie!

### <a name="test-drive-listings-optional"></a>Aanbiedingen voor teststations (optioneel)

De optie **Testdrive-vermeldingen** onder het tabblad **Teststation** geeft de talen (en markten) weer waar uw testrit beschikbaar is, momenteel is Engels (Verenigde Staten) de enige beschikbare locatie. Bovendien geeft deze pagina de status weer van de taalspecifieke vermelding en de datum/tijd die is toegevoegd. U moet de testdrive details (beschrijving, handleiding, video's, enz.) voor elke taal / markt te definiëren.

- **Beschrijving** (vereist): Beschrijf uw testrit, wat wordt aangetoond, doelstellingen voor de gebruiker om mee te experimenteren, functies om te verkennen en alle relevante informatie om de gebruiker te helpen bepalen of hij uw aanbieding wil verwerven. In dit veld kunnen maximaal 3.000 tekens tekst worden ingevoerd. 

- **Toegangsgegevens** (vereist voor Azure Resource Manager- en Logic-teststations): leg uit wat een klant moet weten om toegang te krijgen tot deze testrit en deze te gebruiken. Loop door een scenario voor het gebruik van uw aanbieding en precies wat de klant moet weten om toegang te krijgen tot functies tijdens de testrit. In dit veld kunnen maximaal 10.000 tekens tekst worden ingevoerd.

- **Handleiding** (vereist): een diepgaande doorloop van uw testritervaring. De gebruikershandleiding moet precies betrekking hebben op wat u wilt dat de klant te winnen bij het ervaren van de proefrit en dienen als een referentie voor eventuele vragen die ze kunnen hebben. Het bestand moet in PDF-formaat zijn en worden benoemd (maximaal 255 tekens) na het uploaden.

- **Video's: Video's toevoegen** (optioneel): video's kunnen worden geüpload naar YouTube of Vimeo en hier worden verwezen met een link en miniatuurafbeelding (533 x 324 pixels), zodat een klant een reeks informatie kan bekijken om de testrit beter te begrijpen, inclusief hoe ze de functies van uw aanbieding met succes kunnen gebruiken en scenario's kunnen begrijpen die hun voordelen benadrukken.
  - **Naam** (vereist)
  - **URL (alleen YouTube of Vimeo)** (vereist)
  - **Miniatuur (533 x 324px)**: Afbeeldingsbestand moet in PNG-indeling zijn.

Selecteer **Opslaan** nadat u deze velden hebt voltooid.
