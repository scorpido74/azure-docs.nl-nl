---
title: 'Zelfstudie: Voorbeeld van blauwdruk naar nieuwe omgeving'
description: In deze zelfstudie gebruikt u een blauwdrukvoorbeeld om een blauwdrukdefinitie te maken die twee resourcegroepen instelt en een roltoewijzing voor elk groep configureert.
ms.date: 03/25/2020
ms.topic: tutorial
ms.openlocfilehash: 79928822b41dfe0583afbeaf2f5f1a4a87dd3202
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80677092"
---
# <a name="tutorial-create-an-environment-from-a-blueprint-sample"></a>Zelfstudie: Een omgeving maken op basis van een blauwdrukvoorbeeld

Voorbeeldblauwdrukken geven voorbeelden van wat er met Azure Blueprints kan worden gedaan. Elk is een voorbeeld met een specifieke intentie of doel, maar creëert niet zelf een volledige omgeving. Elk is bedoeld als startplaats om te verkennen met behulp van Azure Blueprints met verschillende combinaties van opgenomen artefacten, ontwerpen en parameters.

De volgende zelfstudie gebruikt het voorbeeld **resourcegroepen met een RBAC-blauwdrukom** verschillende aspecten van de Azure Blueprints-service te presenteren. De volgende stappen worden behandeld:

> [!div class="checklist"]
> - Een nieuwe blauwdrukdefinitie maken in het voorbeeld
> - Uw exemplaar van het voorbeeld markeren als **Gepubliceerd**
> - Uw kopie van de blauwdruk toewijzen aan een bestaand abonnement
> - Geïmplementeerde resources voor de toewijzing inspecteren
> - De blauwdruk ongedaan maken om de vergrendelingen te verwijderen

## <a name="prerequisites"></a>Vereisten

Om deze zelfstudie te voltooien, is een Azure-abonnement nodig. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="create-blueprint-definition-from-sample"></a>Blauwdrukdefinitie maken uit voorbeeld

Implementeer eerst het blauwdrukvoorbeeld. Als u importeert, wordt een nieuwe blauwdruk in uw omgeving gemaakt op basis van het voorbeeld.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer op de pagina **Aan de** slag aan de linkerkant de knop **Maken** onder Een _blauwdruk maken_.

1. Zoek de **resourcegroepen met rbac-blauwdrukvoorbeeld** onder _Andere voorbeelden_ en selecteer deze.

1. Voer de _basisbeginselen_ van het blauwdrukvoorbeeld in:

   - **Blauwdruknaam:** geef een naam op voor uw kopie van het blauwdrukvoorbeeld. Voor deze zelfstudie gebruiken we de naam _twee-rgs-met-rol-opdrachten._
   - **Definitielocatie**: Gebruik de ellips en selecteer de beheergroep of het abonnement om uw exemplaar van het voorbeeld op te slaan.

1. Selecteer het tabblad _Artefacten_ boven aan de pagina of **Volgende: Artefacten** onder aan de pagina.

1. Bekijk de lijst met artefacten die deel uitmaken van het blauwdrukvoorbeeld. In dit voorbeeld worden twee resourcegroepen gedefinieerd, met weergavenamen van _ProdRG_ en _PreProdRG._ De uiteindelijke naam en locatie van elke resourcegroep worden ingesteld tijdens de blauwdruktoewijzing. Aan de resourcegroep _ProdRG_ wordt de rol _Medewerker_ toegewezen en krijgt de brongroep _PreProdRG_ de rollen _Eigenaar_ en _Lezers_ toegewezen. De rollen die in de definitie zijn toegewezen, zijn statisch, maar de gebruiker, app of groep die de rol toegewezen krijgt, wordt ingesteld tijdens de blauwdruktoewijzing.

1. Selecteer **Concept opslaan** wanneer u klaar bent met het bekijken van het blauwdrukvoorbeeld.

Met deze stap wordt een kopie gemaakt van de blauwdrukdefinitie van het voorbeeld in de geselecteerde beheergroep of -abonnement. De opgeslagen blauwdrukdefinitie wordt beheerd zoals elke blauwdruk die vanuit het niets is gemaakt. U het voorbeeld zo vaak als nodig opslaan in uw beheergroep of abonnement. Elk exemplaar moet echter een unieke naam krijgen.

Zodra de **opgeslagen poortmelding voor het opslaan van de** portal wordt weergegeven, gaat u naar de volgende stap.

## <a name="publish-the-sample-copy"></a>De voorbeeldkopie publiceren

Uw kopie van het blauwdrukvoorbeeld is nu gemaakt in uw omgeving. Het is gemaakt in **de conceptmodus** en moet worden **gepubliceerd** voordat het kan worden toegewezen en geïmplementeerd. De kopie van het blauwdrukvoorbeeld kan worden aangepast aan uw omgeving en behoeften. Voor deze zelfstudie zullen we geen wijzigingen aanbrengen.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer de pagina **Blauwdrukdefinities** aan de linkerkant. Gebruik de filters om de blauwdrukdefinitie voor _twee rgs-met-roltoewijzingen_ te vinden en deze vervolgens te selecteren.

1. Selecteer **Blauwdruk publiceren** boven aan de pagina. Geef in het nieuwe deelvenster aan de rechterkant **versie** als _1.0_ op voor uw exemplaar van het blauwdrukvoorbeeld. Deze eigenschap is handig voor als u later een wijziging aanbrengt. **Geef wijzigingsnotities** op, zoals 'Eerste versie gepubliceerd uit de resourcegroepen met RBAC-blauwdrukvoorbeeld'. Selecteer vervolgens **Publiceren** onder aan de pagina.

Deze stap maakt het mogelijk om de blauwdruk toe te wijzen aan een abonnement. Na publicatie kunnen er nog wijzigingen worden aangebracht. Aanvullende wijzigingen vereisen publicatie met een nieuwe **versiewaarde** om verschillen tussen verschillende versies van dezelfde blauwdrukdefinitie bij te houden.

Zodra de **publicatieblauwdrukdefinitie is geslaagd,** gaat u naar de volgende stap.

## <a name="assign-the-sample-copy"></a>De voorbeeldkopie toewijzen

Zodra de kopie van het blauwdrukvoorbeeld is **gepubliceerd,** kan deze worden toegewezen aan een abonnement binnen de beheergroep waaraan het is opgeslagen. Deze stap is waar parameters worden verstrekt om elke implementatie van de kopie van het blauwdrukvoorbeeld uniek te maken.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer de pagina **Blauwdrukdefinities** aan de linkerkant. Gebruik de filters om de blauwdrukdefinitie voor _twee rgs-met-roltoewijzingen_ te vinden en deze vervolgens te selecteren.

1. Selecteer **Blauwdruk toewijzen** boven aan de pagina met blauwdrukdefinitie.

1. Geef de parameterwaarden op voor de blauwdruktoewijzing:

   - Basisbeginselen

     - **Abonnementen:** selecteer een of meer abonnementen in de beheergroep waarop u uw exemplaar van het blauwdrukvoorbeeld hebt opgeslagen. Als u meer dan één abonnement selecteert, wordt voor elk van de parameters een toewijzing gemaakt met behulp van de ingevoerde parameters.
     - **Toewijzingsnaam:** de naam is vooraf ingevuld op basis van de naam van de blauwdrukdefinitie.
     - **Locatie:** selecteer een gebied waarin de beheerde identiteit moet worden gemaakt. Azure Blueprint gebruikt deze beheerde identiteit om alle artefacten in de toegewezen blauwdruk te implementeren. Zie [beheerde identiteiten voor Azure-resources voor](../../../active-directory/managed-identities-azure-resources/overview.md)meer informatie.
       Selecteer voor deze zelfstudie _Oost-VS 2_.
     - **Versie van de blauwdrukdefinitie**: Kies de **gepubliceerde** versie _1.0_ van uw exemplaar van de voorbeeldblauwdrukdefinitie.

   - Vergrendelingstoewijzing

     Selecteer de _modus Alleen-lezen-_ blauwdrukvergrendeling. Zie voor meer informatie [Vergrendeling van blauwdrukresources](../concepts/resource-locking.md).

   - Beheerde identiteit

     Laat de _standaardsysteemtoegewezen optie._ Zie [beheerde identiteiten](../../../active-directory/managed-identities-azure-resources/overview.md)voor meer informatie .

   - Artefact-parameters

     De parameters die in deze sectie zijn gedefinieerd, zijn van toepassing op het artefact waaronder het is gedefinieerd. Deze parameters zijn [dynamische parameters](../concepts/parameters.md#dynamic-parameters) omdat ze worden gedefinieerd tijdens de toewijzing van de blauwdruk. Stel voor elk artefact de parameterwaarde in op wat is gedefinieerd in de kolom **Waarde.** Selecteer `{Your ID}`hiervoor uw Azure-gebruikersaccount.

     |Artefact-naam|Artefacttype|Parameternaam|Waarde|Beschrijving|
     |-|-|-|-|-|
     |ProdRG-resourcegroep|Resourcegroep|Name|ProductieRG|Hiermee definieert u de naam van de eerste resourcegroep.|
     |ProdRG-resourcegroep|Resourcegroep|Locatie|VS - west 2|Hiermee stelt u de locatie in van de eerste resourcegroep.|
     |Inzender|Nieuwe roltoewijzing|Gebruiker of groep|{Uw ID}|Hiermee bepaalt u welke gebruiker of groep de toewijzing van de _bijdrager_ moet verlenen binnen de eerste resourcegroep.|
     |PreProdRG-resourcegroep|Resourcegroep|Name|PreProductionRG|Hiermee definieert u de naam van de tweede resourcegroep.|
     |PreProdRG-resourcegroep|Resourcegroep|Locatie|VS - west|Hiermee stelt u de locatie in van de tweede resourcegroep.|
     |Eigenaar|Nieuwe roltoewijzing|Gebruiker of groep|{Uw ID}|Hiermee bepaalt u welke gebruiker of groep de _roltoewijzing van eigenaar_ binnen de tweede resourcegroep wilt verlenen.|
     |Lezers|Nieuwe roltoewijzing|Gebruiker of groep|{Uw ID}|Hiermee bepaalt u welke gebruiker of groep de roltoewijzing _Lezers_ binnen de tweede resourcegroep wilt verlenen.|

1. Zodra alle parameters zijn ingevoerd, selecteert **u Toewijzen** onder aan de pagina.

Met deze stap worden de gedefinieerde resources geïmplementeerd en wordt de geselecteerde **vergrendelingstoewijzing geconfigureerd.** Het kan tot 30 minuten duren voordat blueprint-sloten zijn toegepast.

Zodra de **ontwerpblauwdrukdefinitie is geslaagd,** gaat u naar de volgende stap.

## <a name="inspect-resources-deployed-by-the-assignment"></a>Resources inspecteren die door de toewijzing zijn geïmplementeerd

De blauwdruktoewijzing maakt en volgt de artefacten die zijn gedefinieerd in de blauwdrukdefinitie. We kunnen de status van de resources zien op de pagina met blauwdruktoewijzing en door rechtstreeks naar de bronnen te kijken.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer de pagina **Toegewezen blauwdrukken** aan de linkerkant. Gebruik de filters om de _blauwdruktoewijzing Toewijzing-twee-rgs-met-roltoewijzingen_ te vinden en deze vervolgens te selecteren.

   Op deze pagina kunnen we zien dat de toewijzing is geslaagd en de lijst met gemaakte resources samen met de status van hun blauwdrukvergrendeling. Als de toewijzing wordt bijgewerkt, worden in de vervolgkeuzelijst **Toewijzingsbewerking** details weergegeven over de implementatie van elke definitieversie. Op elke vermelde resource die is gemaakt, kan worden geklikt en wordt de eigenschappagina resources geopend.

1. Selecteer de **productieRG-brongroep.**

   We zien dat de naam van de resourcegroep **ProductionRG** is en niet de artefact-weergavenaam _ProdRG._ Deze naam komt overeen met de waardedie is ingesteld tijdens de blauwdruktoewijzing.

1. Selecteer de pagina **Toegangsbesturingselement (IAM)** aan de linkerkant en vervolgens het tabblad **Toewijzingen van rollen.**

   Hier zien we dat uw account de rol _van bijdrager_ heeft gekregen voor het bereik van _deze bron._ De blauwdruktoewijzing _Toewijzing-twee-rgs-met-roltoewijzingen_ heeft de rol _Eigenaar_ zoals deze is gebruikt om de resourcegroep te maken. Deze machtigingen worden ook gebruikt om resources met geconfigureerde blauwdrukvergrendelingen te beheren.

1. Selecteer in de breadcrumb van de **Azure-portal Toewijzing-twee-rgs-met-roltoewijzingen** om één pagina terug te gaan en selecteer vervolgens de brongroep **PreProductionRG.**

1. Selecteer de pagina **Toegangsbesturingselement (IAM)** aan de linkerkant en vervolgens het tabblad **Toewijzingen van rollen.**

   Hier zien we dat uw account zowel de _rol van eigenaar_ als van _reader_ heeft gekregen, zowel op het bereik van _deze bron_. De blauwdruktoewijzing heeft ook de rol _Eigenaar,_ zoals de eerste resourcegroep.

1. Selecteer het tabblad **Toewijzingen weigeren.**

   De blauwdruktoewijzing heeft een [toewijzing voor weigeren](../../../role-based-access-control/deny-assignments.md) gemaakt in de geïmplementeerde resourcegroep om de modus _Alleen-lezen-blauwdrukvergrendeling_ af te dwingen. De toewijzing weigeren voorkomt dat iemand met de juiste rechten op het tabblad _Toewijzingen van rollen_ specifieke acties kan uitvoeren. De toewijzing weigeren is van invloed op _alle principals_.

1. Selecteer de toewijzing weigeren en selecteer de pagina **Geweigerde machtigingen** aan de linkerkant.

   De weigeringstoewijzing voorkomt **\*** alle bewerkingen met de configuratie en **Actie,** maar biedt leestoegang door het uitsluiten ** \*/lezen** via **NotActions**uit te sluiten.

1. Selecteer **PreProductionRG - Toegangscontrole (IAM)** in de broodkruimel van de Azure-portal. Selecteer vervolgens de pagina **Overzicht** aan de linkerkant en vervolgens de knop **Brongroep verwijderen.** Voer de naam _PreProductionRG_ in om de delete te bevestigen en selecteer **Delete** onder in het deelvenster.

   De portalmelding **Verwijder brongroep PreProductionRG is** mislukt. De fout stelt dat terwijl uw account toestemming heeft om de brongroep te verwijderen, de toegang wordt geweigerd door de blauwdruktoewijzing. Vergeet niet dat we de _lees-alleen-blauwdrukvergrendelingsmodus_ hebben geselecteerd tijdens de blauwdruktoewijzing. Het blauwdrukslot voorkomt dat een account met toestemming, zelfs _Eigenaar,_ de bron kan verwijderen. Zie voor meer informatie [Vergrendeling van blauwdrukresources](../concepts/resource-locking.md).

Deze stappen laten zien dat onze resources zijn gemaakt zoals gedefinieerd en dat de blauwdrukvergrendelingen ongewenste verwijdering hebben voorkomen, zelfs van een account met toestemming.

## <a name="unassign-the-blueprint"></a>De blauwdruk ongedaan maken

De laatste stap is het verwijderen van de toewijzing van de blauwdruk en de resources die deze heeft geïmplementeerd.
Als u de toewijzing verwijdert, worden de geïmplementeerde artefacten niet verwijderd.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer de pagina **Toegewezen blauwdrukken** aan de linkerkant. Gebruik de filters om de _blauwdruktoewijzing Toewijzing-twee-rgs-met-roltoewijzingen_ te vinden en deze vervolgens te selecteren.

1. Selecteer de **knop Blauwdruk ongedaan maken** boven aan de pagina. Lees de waarschuwing in het bevestigingsdialoogvenster en selecteer **OK**.

   Nu de blauwdruktoewijzing is verwijderd, worden ook de blauwdrukvergrendelingen verwijderd. De gemaakte bronnen kunnen opnieuw worden verwijderd door een account met machtigingen.

1. Selecteer **Resourcegroepen** in het Azure-menu en selecteer **ProductionRG**.

1. Selecteer de pagina **Toegangsbesturingselement (IAM)** aan de linkerkant en vervolgens het tabblad **Toewijzingen van rollen.**

De beveiliging voor elke resourcegroep heeft nog steeds de geïmplementeerde roltoewijzingen, maar de blauwdruktoewijzing heeft geen toegang tot _de eigenaar_ meer.

Zodra de **portalmelding voor het verwijderen van de ontwerpopdracht is** gelukt, gaat u naar de volgende stap.

## <a name="clean-up-resources"></a>Resources opschonen

Als u klaar bent met deze zelfstudie, verwijdert u de volgende bronnen:

- Grondstoffengroep _ProductionRG_
- Brongroep _PreProductionRG_
- Blauwdrukdefinitie _twee-rgs-met-rol-opdrachten_

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een nieuwe blauwdruk maken op basis van een voorbeelddefinitie. Ga voor meer informatie over Azure Blueprints verder naar het artikel over de levenscyclus van de blauwdruk.

> [!div class="nextstepaction"]
> [Meer informatie over de levenscyclus van de blauwdruk](../concepts/lifecycle.md)