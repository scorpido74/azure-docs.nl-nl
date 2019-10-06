---
title: Een omgeving maken op basis van een blauw druk-voor beeld
description: Gebruik een voor beeld van een blauw druk om een blauw druk definitie te maken waarmee twee resource groepen worden ingesteld en waarmee een roltoewijzing voor elk wordt geconfigureerd.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/05/2019
ms.topic: tutorial
ms.service: blueprints
ms.openlocfilehash: f3250052a7e26b5d8ef7cb1d411f9d9252089875
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/06/2019
ms.locfileid: "71980727"
---
# <a name="tutorial-create-an-environment-from-a-blueprint-sample"></a>Zelfstudie: Een omgeving maken op basis van een blauw druk-voor beeld

Voorbeeld blauw drukken biedt voor beelden van wat u kunt doen met Azure-blauw drukken. Elk is een voor beeld met een specifieke intentie of doel, maar maakt zelf geen volledige omgeving. Elk is bedoeld als uitgangs punt om te verkennen met behulp van Azure-blauw drukken met verschillende combi Naties van opgenomen artefacten, ontwerpen en para meters.

In de volgende zelf studie worden de **resource groepen met RBAC** -blauw druk-voor beeld gebruikt om verschillende aspecten van de blauw drukken-service te tonen. De volgende stappen worden behandeld:

> [!div class="checklist"]
> - Een nieuwe blauw druk-definitie maken op basis van het voor beeld
> - Uw kopie van het voor beeld markeren als **gepubliceerd**
> - Uw kopie van de blauw druk toewijzen aan een bestaand abonnement
> - Geïmplementeerde resources voor de toewijzing controleren
> - De blauw druk voor het verwijderen van de vergren delingen opheffen

## <a name="prerequisites"></a>Vereisten

Voor het volt ooien van deze zelf studie is een Azure-abonnement nodig. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="create-blueprint-definition-from-sample"></a>Definitie van blauw druk maken op basis van voor beeld

Implementeer eerst het voor beeld van de blauw druk. Bij het importeren wordt een nieuwe blauw druk in uw omgeving gemaakt op basis van het voor beeld.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Op de pagina **aan** de slag aan de linkerkant selecteert u de knop **maken** onder _een blauw druk maken_.

1. Zoek het **voor beeld van de resource groepen met RBAC** -blauw druk onder _andere voor beelden_ en selecteer **dit voor beeld gebruiken**.

1. Voer de _basis beginselen_ van het voor beeld van de blauw druk in:

   - **Naam blauw druk**: Geef een naam op voor uw kopie van het voor beeld van de blauw druk. Voor deze zelf studie gebruiken we de naam _twee-RGS-with-Role-Assignments_.
   - **Locatie van definitie**: Gebruik het weglatings teken en selecteer de beheer groep of het abonnement om uw kopie van het voor beeld op te slaan.

1. Selecteer het tabblad _artefacten_ boven aan de pagina of **Next: Artefacten @ no__t-0 aan de onderkant van de pagina.

1. Bekijk de lijst met artefacten die deel uitmaken van het voor beeld van de blauw druk. In dit voor beeld worden twee resource groepen gedefinieerd, met weergave namen van _ProdRG_ en _PreProdRG_. De uiteindelijke naam en locatie van elke resource groep worden ingesteld tijdens de toewijzing van blauw drukken. De resource groep _ProdRG_ is toegewezen aan de rol _Inzender_ en de resource groep _PreProdRG_ is toegewezen aan de rollen _eigenaar_ en _lezers_ . De rollen die zijn toegewezen in de definitie zijn statisch, maar de gebruiker, de app of de groep waaraan de rol is toegewezen, wordt ingesteld tijdens de toewijzing van de blauw druk.

1. Selecteer **concept opslaan** wanneer u klaar bent met het bekijken van het voor beeld van de blauw druk.

Met deze stap maakt u een kopie van de definitie van de voor beeld-blauw druk in de geselecteerde beheer groep of dit abonnement. De definitie van de opgeslagen blauw druk wordt beheerd zoals elke blauw druk die helemaal is gemaakt. U kunt het voor beeld zo vaak opslaan als nodig voor uw beheer groep of abonnement. Elk exemplaar moet echter een unieke naam hebben.

Wanneer de **definitie van de blauw druk** voor de portal is geslaagd, gaat u verder met de volgende stap.

## <a name="publish-the-sample-copy"></a>De voorbeeld kopie publiceren

Uw kopie van het voor beeld van de blauw druk is nu in uw omgeving gemaakt. Deze wordt gemaakt in de **concept** modus en moet worden **gepubliceerd** voordat deze kan worden toegewezen en geïmplementeerd. De kopie van het voor beeld van de blauw druk kan worden aangepast aan uw omgeving en behoeften. Voor deze zelf studie worden er geen wijzigingen aangebracht.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer de pagina met **definities van blauw** drukken aan de linkerkant. Gebruik de filters om de definitie van de blauw druk voor _twee RGS-with-Role-Assignments_ te vinden en deze te selecteren.

1. Selecteer **blauw druk** aan de bovenkant van de pagina publiceren. Geef in het nieuwe deel venster aan de rechter kant versie _1,0_ op voor uw kopie van het voor beeld van de blauw druk. Deze eigenschap is handig als u later een wijziging aanbrengt. Geef **wijzigings notities** op, zoals ' eerste versie gepubliceerd vanuit de resource groepen met een voor beeld van RBAC-blauw drukken '. Selecteer vervolgens **publiceren** onder aan de pagina.

Met deze stap kunt u de blauw druk toewijzen aan een abonnement. Nadat de wijzigingen zijn gepubliceerd, kunnen ze nog steeds worden gemaakt. Aanvullende wijzigingen moeten worden gepubliceerd met een nieuwe **versie** waarde voor het bijhouden van verschillen tussen verschillende versies van dezelfde blauw druk-definitie.

Wanneer de portal- **definitie voor publicatie blauw** drukken wordt weer gegeven, gaat u naar de volgende stap.

## <a name="assign-the-sample-copy"></a>De voorbeeld kopie toewijzen

Zodra de kopie van het voor beeld van de blauw druk is **gepubliceerd**, kan deze worden toegewezen aan een abonnement in de beheer groep waarop het is opgeslagen. In deze stap worden para meters opgegeven om elke implementatie van de kopie van het voor beeld van de blauw druk te maken.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer de pagina met **definities van blauw** drukken aan de linkerkant. Gebruik de filters om de definitie van de blauw druk voor _twee RGS-with-Role-Assignments_ te vinden en deze te selecteren.

1. Selecteer de optie **blauw** drukken boven aan de pagina met de definitie van de blauw druk.

1. Geef de parameter waarden voor de toewijzing van de blauw druk op:

   - Basics

     - **Abonnementen**: Selecteer een of meer abonnementen in de beheer groep waarop u uw kopie van het voor beeld van de blauw druk hebt opgeslagen. Als u meer dan één abonnement selecteert, wordt er een toewijzing gemaakt met behulp van de opgegeven para meters.
     - **Toewijzings naam**: De naam wordt vooraf ingevuld op basis van de naam van de definitie van de blauw druk.
     - **Locatie**: Selecteer een regio voor de beheerde identiteit die u wilt maken in. Azure Blueprint gebruikt deze beheerde identiteit om alle artefacten in de toegewezen blauwdruk te implementeren. Zie [Beheerde identiteiten voor Azure-resources](../../../active-directory/managed-identities-azure-resources/overview.md) voor meer informatie.
       Voor deze zelf studie selecteert u _VS Oost 2_.
     - **Definitie van blauw druk-versie**: Kies de **gepubliceerde** versie _1,0_ van uw kopie van de definitie van de voorbeeld blauw drukken.

   - Toewijzing vergrendelen

     Selecteer de vergrendelings modus voor _alleen-lezen_ blauw drukken. Zie voor meer informatie [Vergrendeling van blauwdrukresources](../concepts/resource-locking.md).

   - Beheerde identiteit

     Wijzig de standaard optie _systeem toegewezen_ . Zie [Managed Identities](../../../active-directory/managed-identities-azure-resources/overview.md)(Engelstalig) voor meer informatie.

   - Artefactparameters

     De in deze sectie gedefinieerde para meters zijn van toepassing op het artefact waaronder het is gedefinieerd. Deze para meters zijn [dynamische para meters](../concepts/parameters.md#dynamic-parameters) , omdat ze zijn gedefinieerd tijdens de toewijzing van de blauw druk. Stel voor elk artefact de parameter waarde in op wat in de kolom **waarde** is gedefinieerd. Selecteer uw Azure-gebruikers account voor `{Your ID}`.

     |Naam van het artefact|Type artefact|Parameternaam|Value|Description|
     |-|-|-|-|-|
     |Resource groep ProdRG|Resource group|Name|ProductionRG|Hiermee definieert u de naam van de eerste resource groep.|
     |Resource groep ProdRG|Resource group|Location|US - west 2|Hiermee stelt u de locatie van de eerste resource groep.|
     |Inzender|Roltoewijzing|Gebruiker of groep|{Uw ID}|Hiermee definieert u welke gebruiker of groep de rol van _Inzender_ toewijzing moet verlenen binnen de eerste resource groep.|
     |Resource groep PreProdRG|Resource group|Name|PreProductionRG|Hiermee definieert u de naam van de tweede resource groep.|
     |Resource groep PreProdRG|Resource group|Location|US - west|Hiermee stelt u de locatie van de tweede resource groep in.|
     |Eigenaar|Roltoewijzing|Gebruiker of groep|{Uw ID}|Hiermee definieert u welke gebruiker of groep de roltoewijzing van de _eigenaar_ binnen de tweede resource groep moet verlenen.|
     |Lezers|Roltoewijzing|Gebruiker of groep|{Uw ID}|Hiermee definieert u welke gebruiker of groep de roltoewijzing van _lezers_ moet verlenen in de tweede resource groep.|

1. Zodra alle para meters zijn ingevoerd, selecteert u aan de onderkant van de pagina **toewijzen** .

Met deze stap implementeert u de gedefinieerde resources en configureert u de geselecteerde **vergrendelings toewijzing**. Het kan tot 30 minuten duren om blauw drukken te vergren delen.

Als de portal-definitie voor het toewijzen van de **blauw druk is geslaagd** , gaat u verder met de volgende stap.

## <a name="inspect-resources-deployed-by-the-assignment"></a>Resources controleren die door de toewijzing zijn geïmplementeerd

De toewijzing blauw drukken maakt en traceert de artefacten die in de definitie van de blauw druk zijn gedefinieerd. We kunnen de status van de resources zien op de pagina blauw druk toewijzen en door de resources rechtstreeks te bekijken.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer de pagina **toegewezen blauw drukken** aan de linkerkant. Gebruik de filters om de toewijzing van de blauw druk _RGS-with-Role-Assignments_ te vinden en selecteer deze.

   Op deze pagina ziet u dat de toewijzing is geslaagd en de lijst met gemaakte resources samen met de vergrendelings status blauw drukken. Als de toewijzing is bijgewerkt, bevat de vervolg keuzelijst **toewijzings bewerking** Details over de implementatie van elke definitie versie. U kunt op elke genoteerde resource klikken en de eigenschappen pagina van de resources openen.

1. Selecteer de resource groep **ProductionRG** .

   We zien dat de naam van de resource groep **ProductionRG** is en niet de weergave naam _ProdRG_voor artefacten. Deze naam komt overeen met de waarde die is ingesteld tijdens de blauw druk-toewijzing.

1. Selecteer de pagina **toegangs beheer (IAM)** aan de linkerkant en klik vervolgens op het tabblad **roltoewijzingen** .

   Hier zien we dat uw account is toegewezen aan de rol _Inzender_ voor het bereik van _deze resource_. De toewijzing van de RGS-functie- _two-with-Role-Assignments_ is de rol _eigenaar_ , omdat deze is gebruikt voor het maken van de resource groep. Deze machtigingen worden ook gebruikt voor het beheren van resources met geconfigureerde blauw druk vergrendelingen.

1. Selecteer in de Azure Portal breadcrumb **toewijzing-twee RGS-with-Role-Assignments** om één pagina terug te gaan en selecteer vervolgens de resource groep **PreProductionRG** .

1. Selecteer de pagina **toegangs beheer (IAM)** aan de linkerkant en klik vervolgens op het tabblad **roltoewijzingen** .

   Hier zien we dat aan uw account zowel de rol van _eigenaar_ als _lezer_ zijn verleend, zowel op het bereik van _deze resource_. De toewijzing van de blauw druk heeft ook de rol van _eigenaar_ , zoals de eerste resource groep.

1. Selecteer het tabblad **toewijzingen weigeren** .

   De toewijzing van de blauw druk heeft een [deny-toewijzing](../../../role-based-access-control/deny-assignments.md) gemaakt voor de geïmplementeerde resource groep om de _alleen-lezen_ vergrendelings modus voor blauw drukken af te dwingen. Met de weiger toewijzing kan iemand met de juiste rechten op het tabblad _roltoewijzingen_ geen specifieke acties uitvoeren. De weiger toewijzing heeft gevolgen voor _alle principals_.

1. Selecteer de toewijzing weigeren en selecteer vervolgens de pagina **geweigerde machtigingen** aan de linkerkant.

   De weiger toewijzing verhindert alle bewerkingen met de configuratie van de **\*** en de **actie** , maar staat Lees toegang toe door de **\*/Lees** bewerking uit te **sluiten.**

1. Selecteer in de Azure Portal brood kruimel **PreProductionRG-Access Control (IAM)** . Selecteer vervolgens de pagina **overzicht** aan de linkerkant en klik vervolgens op de knop **resource groep verwijderen** . Voer de naam _PreProductionRG_ in om de verwijdering te bevestigen en selecteer **verwijderen** onder aan het deel venster.

   De portal melding voor het verwijderen van de **resource groep PreProductionRG is mislukt** . De fout geeft aan dat uw account is gemachtigd om de resource groep te verwijderen, wordt de toegang geweigerd door de toewijzing van de blauw druk. Houd er rekening mee dat de _alleen-lezen_ modus blauw drukken tijdens het toewijzen van blauw drukken is geselecteerd. Met de vergren deling blauw druk voor komt u dat een account met toestemming, zelfs _eigenaar_, de resource verwijdert. Zie voor meer informatie [Vergrendeling van blauwdrukresources](../concepts/resource-locking.md).

Deze stappen laten zien dat onze resources zijn gemaakt zoals gedefinieerd en dat de blauw drukken vergrendeld ongewenste verwijdering, zelfs van een account met toestemming, blokkeert.

## <a name="unassign-the-blueprint"></a>De blauw druk intrekken

De laatste stap bestaat uit het verwijderen van de toewijzing van de blauw druk en de resources die deze heeft geïmplementeerd.
Als u de toewijzing verwijdert, worden de geïmplementeerde artefacten niet verwijderd.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer de pagina **toegewezen blauw drukken** aan de linkerkant. Gebruik de filters om de toewijzing van de blauw druk _RGS-with-Role-Assignments_ te vinden en selecteer deze.

1. Selecteer de knop **blauw** drukken opheffen boven aan de pagina. Lees de waarschuwing in het bevestigings venster en selecteer vervolgens **OK**.

   Wanneer de blauw druk-toewijzing is verwijderd, worden de blauw druk-vergren delingen ook verwijderd. De gemaakte resources kunnen opnieuw worden verwijderd door een account met machtigingen.

1. Selecteer **resource groepen** in het menu van Azure en selecteer vervolgens **ProductionRG**.

1. Selecteer de pagina **toegangs beheer (IAM)** aan de linkerkant en klik vervolgens op het tabblad **roltoewijzingen** .

De beveiliging voor elke resource groepen heeft nog steeds de geïmplementeerde roltoewijzingen, maar de toewijzing van de blauw druk heeft geen _eigenaars_ toegang meer.

Als de portal melding voor het verwijderen van de **blauw druk is voltooid** , gaat u naar de volgende stap.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met deze zelf studie, moet u de volgende resources verwijderen:

- _ProductionRG_ van resource groep
- _PreProductionRG_ van resource groep
- Definitie van blauw druk _twee-RGS-with-Role-Assignments_

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [levens duur van de blauw druk](../concepts/lifecycle.md)
- Informatie over hoe u [statische en dynamische parameters](../concepts/parameters.md) gebruikt
- Ontdek hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../concepts/resource-locking.md)
- Meer informatie over hoe u de [blauwdrukvolgorde](../concepts/sequencing-order.md) aanpast
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../how-to/update-existing-assignments.md)
- Problemen oplossen tijdens de toewijzing van een blauwdruk met [algemene probleemoplossing](../troubleshoot/general.md)