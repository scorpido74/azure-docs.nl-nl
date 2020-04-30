---
title: 'Quick Start: een blauw druk maken in de portal'
description: In deze Quick Start gebruikt u Azure-blauw drukken om artefacten te maken, te definiëren en te implementeren via de Azure Portal.
ms.date: 03/25/2020
ms.topic: quickstart
ms.openlocfilehash: dd50b1833f16d364a4494483fcccfee017bb982b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81381896"
---
# <a name="quickstart-define-and-assign-a-blueprint-in-the-portal"></a>Snelstartgids: een blauw druk definiëren en toewijzen in de portal

Als u informatie wilt over het maken en toewijzen van blauw drukken, kunt u algemene patronen definiëren voor het ontwikkelen van herbruikbare en snel te implementeren configuraties op basis van Azure Resource Manager sjablonen, beleid, beveiliging en meer. In deze zelf studie leert u hoe u Azure-blauw drukken kunt gebruiken om een aantal algemene taken uit te voeren die betrekking hebben op het maken, publiceren en toewijzen van een blauw druk binnen uw organisatie. Deze taken omvatten:

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free) aan voordat u begint.

## <a name="create-a-blueprint"></a>Een blauwdruk maken

De eerste stap bij het definiëren van een standaardpatroon voor naleving bestaat uit het samenstellen van een blauwdruk uit de beschikbare resources. In dit voor beeld maakt u een nieuwe blauw druk met de naam **MyBlueprint** om rollen en beleids toewijzingen voor het abonnement te configureren. Vervolgens voegt u een nieuwe resource groep toe en maakt u een resource manager-sjabloon en roltoewijzing voor de nieuwe resource groep.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer op de pagina links **blauw** drukken en selecteer de knop **+ blauw drukken maken** boven aan de pagina.

   Of selecteer **maken** op **de pagina aan de slag om** direct een blauw druk te maken.

   :::image type="content" source="./media/create-blueprint-portal/create-blueprint-button.png" alt-text="Een blauw druk maken op de pagina met definities van blauw drukken" border="false":::

1. Selecteer **beginnen met een lege blauw druk** op de kaart boven aan de lijst met ingebouwde blauw drukken.

1. Geef een **blauw druk naam** op, bijvoorbeeld **MyBlueprint**. (Gebruik Maxi maal 48 letters en cijfers, maar geen spaties of speciale tekens). Geef de **Beschrijving van blauw druk** voor nu leeg.

1. Selecteer in het vak **definitie locatie** het beletsel teken aan de rechter kant, selecteer de [beheer groep](../management-groups/overview.md) of het abonnement waar u de blauw druk wilt opslaan en kies **selecteren**.

1. Controleer of de gegevens correct zijn. De velden **blauw druk** en **definitie locatie** kunnen niet later worden gewijzigd. Selecteer vervolgens **volgende: artefacten** onder aan de pagina of het tabblad **artefacten** boven aan de pagina.

1. Een roltoewijzing toevoegen op het abonnements niveau:

   1. Selecteer de rij voor **artefacten toevoegen** onder **abonnement**. Het venster **artefact toevoegen** wordt weer gegeven aan de rechter kant van de browser.

   1. Selecteer de **roltoewijzing** voor het **Type artefact**.

   1. Selecteer onder **rol** **Inzender**. Sluit het vak **gebruiker, app of groep toevoegen** aan met het selectie vakje dat een dynamische para meter aangeeft.

   1. Selecteer **toevoegen** om dit artefact toe te voegen aan de blauw druk.

   :::image type="content" source="./media/create-blueprint-portal/add-role-assignment.png" alt-text="Roltoewijzing voor een blauw druk-artefact" border="false":::

   > [!NOTE]
   > De meeste artefacten ondersteunen parameters. Een para meter die een waarde krijgt toegewezen tijdens het maken van blauw drukken is een _statische para meter_. Als de para meter wordt toegewezen tijdens het toewijzen van blauw druk, is het een _dynamische para meter_. Zie [blauw drukken para meters](./concepts/parameters.md)voor meer informatie.

1. Een beleids toewijzing toevoegen op het abonnements niveau:

   1. Selecteer de rij voor **artefacten toevoegen** onder het onderdeel voor roltoewijzing.

   1. Selecteer **beleids toewijzing** voor **Type artefact**.

   1. Wijzig **Type** het type **in ingebouwd**. Voer in **zoeken** **tag**in.

   1. Klik buiten het veld **Zoeken** om de filtering weer te geven. Selecteer **tag en de standaard waarde toevoegen aan resource groepen**.

   1. Selecteer **toevoegen** om dit artefact toe te voegen aan de blauw druk.

1. Selecteer de rij van de code voor het toevoegen van beleids toewijzingen **en de standaard waarde voor resource groepen**.

1. Het venster waarin u parameters voor het artefact kunt opgeven als onderdeel van de blauwdrukdefinitie wordt geopend. Hier kunt u de parameters voor alle toewijzingen instellen (statische parameters) op basis van deze blauwdruk in plaats van tijdens de toewijzing (dynamische parameters). In dit voor beeld wordt gebruikgemaakt van dynamische para meters tijdens het toewijzen van blauw drukken, behoud de standaard waarden en selecteer **Annuleren**.

1. Een resource groep toevoegen op het abonnements niveau:

   1. Selecteer de rij voor **artefacten toevoegen** onder **abonnement**.

   1. Selecteer een **resource groep** voor het **Type artefact**.

   1. Laat de vakken **weergave naam van artefact**, **naam van resource groep**en **locatie** leeg, maar zorg ervoor dat het selectie vakje is ingeschakeld voor elke parameter eigenschap om deze dynamische para meters te maken.

   1. Selecteer **toevoegen** om dit artefact toe te voegen aan de blauw druk.

1. Voeg een sjabloon toe onder de resource groep:

   1. Selecteer de rij voor het **toevoegen van artefacten** onder de vermelding **ResourceGroup** .

   1. Selecteer **Azure Resource Manager sjabloon** voor **artefact type**, stel **weergave naam voor artefact** in op **Storage account**en laat de **Beschrijving** leeg.

   1. Op het tabblad **Sjabloon** van het editorvak plakt u de volgende Resource Manager-sjabloon.
      Nadat u de sjabloon hebt geplakt, selecteert u het tabblad **para meters** en ziet u dat de **storageAccountType** en **locatie** van de sjabloon parameters zijn gedetecteerd. Elke parameter is automatisch gedetecteerd en gevuld, maar geconfigureerd als een dynamische parameter.

      > [!IMPORTANT]
      > Als u de sjabloon wilt importeren, moet u ervoor zorgen dat het bestand alleen JSON is en geen HTML-code bevat. Wanneer u verwijst naar een URL op GitHub, moet u ervoor zorgen dat u **RAW** hebt geselecteerd om het zuivere JSON-bestand op te halen en niet de tekst die wordt verpakt met HTML voor weer gave op github. Er treedt een fout op als de geïmporteerde sjabloon geen zuivere JSON is.

      ```json
      {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
              "storageAccountType": {
                  "type": "string",
                  "defaultValue": "Standard_LRS",
                  "allowedValues": [
                      "Standard_LRS",
                      "Standard_GRS",
                      "Standard_ZRS",
                      "Premium_LRS"
                  ],
                  "metadata": {
                      "description": "Storage Account type"
                  }
              },
              "location": {
                  "type": "string",
                  "defaultValue": "[resourceGroup().location]",
                  "metadata": {
                      "description": "Location for all resources."
                  }
              }
          },
          "variables": {
              "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
          },
          "resources": [{
              "type": "Microsoft.Storage/storageAccounts",
              "name": "[variables('storageAccountName')]",
              "location": "[parameters('location')]",
              "apiVersion": "2018-07-01",
              "sku": {
                  "name": "[parameters('storageAccountType')]"
              },
              "kind": "StorageV2",
              "properties": {}
          }],
          "outputs": {
              "storageAccountName": {
                  "type": "string",
                  "value": "[variables('storageAccountName')]"
              }
          }
      }
      ```

   1. Schakel het selectie vakje **storageAccountType** uit en houd er rekening mee dat de vervolg keuzelijst alleen waarden bevat die zijn opgenomen in de Resource Manager-sjabloon onder **allowedValues**. Schakel het selectie vakje in om het weer in te stellen op een dynamische para meter.

   1. Selecteer **toevoegen** om dit artefact toe te voegen aan de blauw druk.

   :::image type="content" source="./media/create-blueprint-portal/add-resource-manager-template.png" alt-text="Resource Manager-sjabloon voor het blauw druk-artefact" border="false":::

1. De voltooide blauwdruk moet er nu ongeveer als volgt uitzien. U ziet dat voor elk artefact ** _x_ uit _y_ -para meters is ingevuld** in de kolom **para meters** . De dynamische parameters worden ingesteld tijdens elke toewijzing van de blauwdruk.

   :::image type="content" source="./media/create-blueprint-portal/completed-blueprint.png" alt-text="Definitie van voltooide blauw druk" border="false":::

1. Nu alle geplande artefacten zijn toegevoegd, selecteert u **concept opslaan** onder aan de pagina.

## <a name="edit-a-blueprint"></a>Een blauwdruk bewerken

Als u [een blauw druk maakt](#create-a-blueprint), hebt u geen beschrijving verstrekt of voegt u de roltoewijzing toe aan de nieuwe resource groep. U kunt dit probleem oplossen door de volgende stappen uit te voeren:

1. Selecteer **Blauwdrukdefinities** op de pagina aan de linkerkant.

1. Klik in de lijst met blauw drukken met de rechter muisknop op het item dat u eerder hebt gemaakt en selecteer **blauw druk bewerken**.

1. Geef in **Beschrijving van blauwdruk** informatie op over de blauwdruk en de artefacten waaruit deze bestaat. In dit geval voert u een van de volgende handelingen uit: met **deze blauw druk stelt u het code beleid en de roltoewijzing in voor het abonnement, maakt u een ResourceGroup en implementeert u een resource sjabloon en roltoewijzing voor die ResourceGroup.**

1. Selecteer **volgende: artefacten** aan de onderkant van de pagina of het tabblad **artefacten** boven aan de pagina.

1. Een roltoewijzing toevoegen onder de resource groep:

   1. Selecteer de rij **+ artefact toevoegen** direct onder de vermelding **ResourceGroup** .

   1. Selecteer de **roltoewijzing** voor het **Type artefact**.

   1. Selecteer onder **rol**de optie **eigenaar**en schakel het selectie vakje in het vak **gebruiker, app of groep toevoegen** uit.

   1. Zoek en selecteer een gebruiker, app of groep om toe te voegen. Dit artefact maakt gebruik van een statische parameter die in elke toewijzing van deze blauwdruk is ingesteld.

   1. Selecteer **toevoegen** om dit artefact toe te voegen aan de blauw druk.

   :::image type="content" source="./media/create-blueprint-portal/add-role-assignment-2.png" alt-text="Tweede roltoewijzing voor het artefacten van blauw drukken" border="false":::

1. De voltooide blauwdruk moet er nu ongeveer als volgt uitzien. U ziet dat in de zojuist toegevoegde roltoewijzing **1 van de 1 para meters zijn ingevuld**. Dit betekent dat het een statische para meter is.

   :::image type="content" source="./media/create-blueprint-portal/completed-blueprint-2.png" alt-text="Tweede definitie voor de voltooide blauw druk" border="false":::

1. Selecteer **concept opslaan** nu dat is bijgewerkt.

## <a name="publish-a-blueprint"></a>Een blauwdruk publiceren

Nu alle geplande artefacten aan de blauwdruk zijn toegevoegd, kunt u deze gaan publiceren.
Bij het publiceren wordt de blauw druk beschikbaar om te worden toegewezen aan een abonnement.

1. Selecteer **Blauwdrukdefinities** op de pagina aan de linkerkant.

1. Klik in de lijst met blauw drukken met de rechter muisknop op het item dat u eerder hebt gemaakt en selecteer **blauw druk publiceren**.

1. In het deel venster dat wordt geopend, geeft u een **versie** (letters, cijfers en afbreek streepjes met een maximale lengte van 20 tekens), zoals **v1**. Voer eventueel tekst in bij **wijzigings notities**, zoals **eerst publiceren**.

1. Selecteer onder aan de pagina **publiceren** .

## <a name="assign-a-blueprint"></a>Een blauwdruk toewijzen

Nadat een blauw druk is gepubliceerd, kan deze worden toegewezen aan een abonnement. Wijs de blauw druk toe die u hebt gemaakt voor een van de abonnementen in de hiërarchie van uw beheer groep. Als de blauwdruk is opgeslagen in een abonnement, kan deze alleen aan dat abonnement worden toegewezen.

1. Selecteer **Blauwdrukdefinities** op de pagina aan de linkerkant.

1. Klik in de lijst met blauw drukken met de rechter muisknop op het item dat u eerder hebt gemaakt (of selecteer het beletsel teken) en selecteer **blauw druk toewijzen**.

1. Selecteer op de pagina **blauw** drukken de abonnementen waarvoor u deze blauw druk wilt implementeren in de vervolg keuzelijst **abonnement** .

   Als er ondersteunde Enterprise-aanbiedingen beschikbaar zijn via [Azure Billing](../../billing/index.md), wordt de koppeling **Nieuw** onder het vak **Abonnement** geactiveerd. Volg deze stappen:

   1. Klik op de koppeling **Nieuw** om een nieuw abonnement te maken in plaats van een bestaand abonnement te selecteren.

   1. Geef een **weergavenaam** voor het nieuwe abonnement op.

   1. Selecteer de beschik bare **aanbieding** in de vervolg keuzelijst.

   1. Gebruik het beletsel teken om de [beheer groep](../management-groups/overview.md) te selecteren waarvan het abonnement een onderliggend element is.

   1. Selecteer **Maken** onderaan de pagina.

      :::image type="content" source="./media/create-blueprint-portal/assignment-create-subscription.png" alt-text="Een abonnement maken voor een blauw druk toewijzings abonnement" border="false":::

      > [!IMPORTANT]
      > Het nieuwe abonnement wordt direct na het selecteren van **maken**gemaakt.

   > [!NOTE]
   > Er wordt een toewijzing gemaakt voor elk abonnement dat u selecteert. U kunt wijzigingen aanbrengen in één abonnements toewijzing op een later tijdstip zonder dat er wijzigingen in de rest van de geselecteerde abonnementen worden afgedwongen.

1. Geef voor de **toewijzings naam**een unieke naam op voor deze toewijzing.

1. Selecteer bij **locatie**een regio voor het implementatie object voor beheerde identiteiten en abonnementen dat u wilt maken in. Azure Blueprint gebruikt deze beheerde identiteit om alle artefacten in de toegewezen blauwdruk te implementeren. Zie [Beheerde identiteiten voor Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md) voor meer informatie.

1. Laat de selectie van **gepubliceerde** versies op de **v1** -vermelding in de vervolg keuzelijst de **definitie versie** van de blauw druk staan. (De standaard waarde is de meest recent gepubliceerde versie.)

1. Laat **Toewijzing vergrendelen** staan op de standaardwaarde **Niet vergrendelen**. Zie [blauw drukken voor resource vergrendeling](./concepts/resource-locking.md)voor meer informatie.

   :::image type="content" source="./media/create-blueprint-portal/assignment-locking-mi.png" alt-text="Het vergren delen en beheren van identiteiten voor de toewijzing" border="false":::

1. Accepteer onder **beheerde identiteit**de standaard waarde van het **systeem toegewezen**.

1. Voor de roltoewijzing op abonnementsniveau **[gebruikersgroep of toepassingsnaam]: Inzender** selecteert u een gebruiker, app of groep.

1. Stel voor de toewijzing van het beleid op abonnements niveau de **label naam** in op **CostCenter** en de **waarde van tag** naar **ContosoIT**.

1. Geef voor **ResourceGroup**de **naam** **Storage account** en een **locatie** van VS- **Oost 2** op in de vervolg keuzelijst.

   > [!NOTE]
   > Voor elk artefact dat u hebt toegevoegd onder de resource groep tijdens de blauw druk-definitie, wordt dat artefact Inge sprongen voor uitlijning met de resource groep of het object dat u wilt implementeren met.
   > Artefacten waarvoor geen para meters worden opgegeven of waarvoor geen para meters moeten worden gedefinieerd tijdens de toewijzing, worden alleen weer gegeven voor contextuele informatie.

1. Selecteer op de Azure Resource Manager sjabloon **storage account** **Standard_GRS** voor de para meter **storageAccountType** .

1. Lees het vak informatie aan de onderkant van de pagina en selecteer vervolgens **toewijzen**.

## <a name="track-deployment-of-a-blueprint"></a>Implementatie van een blauwdruk bijhouden

Wanneer een blauwdruk is toegewezen aan een of meer abonnementen, gebeuren er twee dingen:

- De blauw druk wordt toegevoegd aan de pagina **toegewezen blauw drukken** voor elk abonnement.
- Het proces voor het implementeren van alle artefacten die zijn gedefinieerd door de blauw druk wordt gestart.

Nu de blauw druk is toegewezen aan een abonnement, controleert u de voortgang van de implementatie:

1. Selecteer **toegewezen blauw drukken** op de pagina aan de linkerkant.

1. Klik in de lijst met blauw drukken met de rechter muisknop op het item dat u eerder hebt toegewezen en selecteer **toewijzings details weer geven**.

   :::image type="content" source="./media/create-blueprint-portal/view-assignment-details.png" alt-text="Toewijzings details weer geven op de pagina toegewezen blauw drukken" border="false":::

1. Controleer op de pagina **blauw druk** of alle artefacten zijn geïmplementeerd en of er geen fouten zijn opgetreden tijdens de implementatie. Als er fouten zijn opgetreden, raadpleegt u [probleemoplossings schema's](./troubleshoot/general.md) voor stappen om te bepalen wat er mis ging.

## <a name="clean-up-resources"></a>Resources opschonen

### <a name="unassign-a-blueprint"></a>De toewijzing van een blauwdruk ongedaan maken

Als u een blauw druk-toewijzing niet meer nodig hebt, verwijdert u deze uit een abonnement. De blauw druk is mogelijk vervangen door een nieuwe blauw druk met bijgewerkte patronen, beleids regels en ontwerpen. Wanneer een blauwdruk wordt verwijderd, blijven de artefacten die als onderdeel van die blauwdruk zijn toegewezen, achter. Voer de volgende stappen uit als u een blauwdruktoewijzing wilt verwijderen:

1. Selecteer **toegewezen blauw drukken** op de pagina aan de linkerkant.

1. Selecteer in de lijst met blauw drukken de blauw druk die u wilt verwijderen. Selecteer vervolgens de knop voor het **opheffen van de blauw druk** boven aan de pagina.

1. Lees het bevestigings bericht en selecteer **OK**.

### <a name="delete-a-blueprint"></a>Een blauwdruk verwijderen

1. Selecteer **Blauwdrukdefinities** op de pagina aan de linkerkant.

1. Klik met de rechter muisknop op de blauw druk die u wilt verwijderen en selecteer **blauw druk verwijderen**. Selecteer vervolgens **Ja** in het bevestigings venster.

> [!NOTE]
> Als u een blauw druk in deze methode verwijdert, worden ook alle gepubliceerde versies van de geselecteerde blauw druk verwijderd.
> Als u één versie wilt verwijderen, opent u de blauw druk, selecteert u het tabblad **gepubliceerde versies** , selecteert u de versie die u wilt verwijderen en selecteert u vervolgens **deze versie verwijderen**. U kunt een blauw druk ook pas verwijderen als u alle blauw drukken-toewijzing van die definitie van de blauw druk hebt verwijderd.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een blauw druk gemaakt, toegewezen en verwijderd met Azure Portal. Voor meer informatie over Azure-blauw drukken gaat u verder met het artikel van de blauw druk-levens duur.

> [!div class="nextstepaction"]
> [Meer informatie over de levens duur van de blauw druk](./concepts/lifecycle.md)