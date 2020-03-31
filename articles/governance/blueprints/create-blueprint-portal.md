---
title: 'Snelstart: een blauwdruk maken in de portal'
description: In deze quickstart gebruikt u Azure Blueprints om artefacten te maken, te definiëren en te implementeren via de Azure-portal.
ms.date: 03/25/2020
ms.topic: quickstart
ms.openlocfilehash: 457f4f9c53f45077129b291c904bc1580b6d965e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282049"
---
# <a name="quickstart-define-and-assign-a-blueprint-in-the-portal"></a>Snelstart: een blauwdruk definiëren en toewijzen in de portal

Wanneer u leert hoe u blauwdrukken maken en toewijzen, u algemene patronen definiëren om herbruikbare en snel implementeerbare configuraties te ontwikkelen op basis van Azure Resource Manager-sjablonen, beleid, beveiliging en meer. In deze zelfstudie leert u Azure Blueprints te gebruiken om enkele veelvoorkomende taken uit te voeren die verband houden met het maken, publiceren en toewijzen van een blauwdruk binnen uw organisatie. Deze taken omvatten:

## <a name="prerequisites"></a>Vereisten

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free) voordat u begint.

## <a name="create-a-blueprint"></a>Een blauwdruk maken

De eerste stap bij het definiëren van een standaardpatroon voor naleving bestaat uit het samenstellen van een blauwdruk uit de beschikbare resources. Maak in dit voorbeeld een nieuwe blauwdruk met de naam **MyBlueprint** om rol- en beleidstoewijzingen voor het abonnement te configureren. Voeg vervolgens een nieuwe resourcegroep toe en maak een resourcemanagersjabloon en -roltoewijzing voor de nieuwe resourcegroep.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer **Blauwdrukdefinities** op de pagina aan de linkerkant en selecteer de knop **+ Blueprint maken** boven aan de pagina.

   Of selecteer **Maken** op de pagina **Aan de slag** om direct naar het maken van een blauwdruk te gaan.

   ![Een blauwdruk maken op de pagina Blueprint-definities](./media/create-blueprint-portal/create-blueprint-button.png)

1. Selecteer **Begin met een lege blauwdruk** op de kaart boven aan de lijst met ingebouwde blauwdrukken.

1. Geef een **blauwdruknaam** op, zoals **MyBlueprint**. (Gebruik maximaal 48 letters en cijfers, maar geen spaties of speciale tekens). Laat **de beschrijving van de blauwdruk** voorlopig leeg.

1. Selecteer **in** het vak Locatie definitie de ellips aan de rechterkant, selecteer de [beheergroep](../management-groups/overview.md) of het abonnement waar u de blauwdruk wilt opslaan en kies **Selecteren**.

1. Controleer of de informatie juist is. De locatievelden **Blauwdruknaam** en **Definitie** kunnen later niet worden gewijzigd. Selecteer **vervolgens Volgende: Artefacten** onder aan de pagina of het tabblad **Artefacten** boven aan de pagina.

1. Een roltoewijzing toevoegen op abonnementsniveau:

   1. Selecteer de **rij + Artefact toevoegen** onder **Abonnement**. Het **venster Artefact toevoegen** wordt aan de rechterkant van de browser geopend.

   1. Selecteer **Roltoewijzing** voor **artefacttype**.

   1. Selecteer **Onder Rol**selecteer **Bijdrager .** Laat het **vakGebruiker, app of groep toevoegen** met het selectievakje dat een dynamische parameter aangeeft.

   1. Selecteer **Toevoegen** om dit artefact aan de blauwdruk toe te voegen.

   ![Roltoewijzing voor een blauwdrukartefact](./media/create-blueprint-portal/add-role-assignment.png)

   > [!NOTE]
   > De meeste artefacten ondersteunen parameters. Een parameter die een waarde heeft toegewezen tijdens het maken van blauwdrukken, is een *statische parameter.* Als de parameter is toegewezen tijdens de blauwdruktoewijzing, is dit een *dynamische parameter.* Zie [Blauwdrukparameters](./concepts/parameters.md)voor meer informatie .

1. Een beleidstoewijzing toevoegen op abonnementsniveau:

   1. Selecteer de **rij + Artefact toevoegen** onder het artefact van de roltoewijzing.

   1. Selecteer **Beleidstoewijzing** voor **artefacttype**.

   1. **Type wijzigen** **in ingebouwd**. Voer in **Zoeken** **de tag**in .

   1. Klik buiten het veld **Zoeken** om de filtering weer te geven. Selecteer **Tag toevoegen en de standaardwaarde ervan voor resourcegroepen**.

   1. Selecteer **Toevoegen** om dit artefact aan de blauwdruk toe te voegen.

1. Selecteer de rij van de label Toevoegen aan de beleidstoewijzing **en de standaardwaarde ervan aan resourcegroepen**.

1. Het venster waarin u parameters voor het artefact kunt opgeven als onderdeel van de blauwdrukdefinitie wordt geopend. Hier kunt u de parameters voor alle toewijzingen instellen (statische parameters) op basis van deze blauwdruk in plaats van tijdens de toewijzing (dynamische parameters). In dit voorbeeld worden dynamische parameters gebruikt tijdens de blauwdruktoewijzing, dus laat de standaardinstellingen achter en selecteer **Annuleren**.

1. Een resourcegroep toevoegen op abonnementsniveau:

   1. Selecteer de **rij + Artefact toevoegen** onder **Abonnement**.

   1. Selecteer **Resourcegroep** voor **artefacttype**.

   1. Laat de **weergavenaam Artefact,** **Resourcegroepnaam**en **Locatie** leeg, maar zorg ervoor dat het selectievakje is ingeschakeld voor elke parametereigenschap om ze dynamische parameters te maken.

   1. Selecteer **Toevoegen** om dit artefact aan de blauwdruk toe te voegen.

1. Een sjabloon toevoegen onder de resourcegroep:

   1. Selecteer de **rij + Artefact toevoegen** onder de vermelding **Resourcegroep.**

   1. Selecteer **Azure Resource Manager-sjabloon** voor **artefacttype,** stel **de weergavenaam Artefact** in op **StorageAccount**en laat **Beschrijving** leeg.

   1. Op het tabblad **Sjabloon** van het editorvak plakt u de volgende Resource Manager-sjabloon.
      Nadat u de sjabloon hebt geplakt, selecteert u het tabblad **Parameters** en houdt u er rekening mee dat de **opslagvan de sjabloonparametersAccountType** en **locatie** zijn gedetecteerd. Elke parameter is automatisch gedetecteerd en gevuld, maar geconfigureerd als een dynamische parameter.

      > [!IMPORTANT]
      > Als u de sjabloon importeert, moet u ervoor zorgen dat het bestand alleen JSON is en geen HTML bevat. Wanneer u naar een URL op GitHub wijst, moet u ervoor zorgen dat u **RAW** hebt geselecteerd om het pure JSON-bestand te krijgen en niet het bestand dat is verpakt met HTML voor weergave op GitHub. Er treedt een fout op als de geïmporteerde sjabloon geen zuivere JSON is.

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

   1. Schakel het selectievakje **storageAccountType** uit en houd er rekening mee dat de vervolgkeuzelijst alleen waarden bevat die zijn opgenomen in de sjabloon Resourcebeheer onder **toegestane waarden**. Selecteer het vak om het weer in te stellen op een dynamische parameter.

   1. Selecteer **Toevoegen** om dit artefact aan de blauwdruk toe te voegen.

   ![Resourcemanager-sjabloon voor het blauwdrukartefact](./media/create-blueprint-portal/add-resource-manager-template.png)

1. De voltooide blauwdruk moet er nu ongeveer als volgt uitzien. Elk artefact heeft ** _x_ van _y-parameters_ die zijn ingevuld** in de kolom **Parameters.** De dynamische parameters worden ingesteld tijdens elke toewijzing van de blauwdruk.

   ![Voltooide blauwdrukdefinitie](./media/create-blueprint-portal/completed-blueprint.png)

1. Nu alle geplande artefacten zijn toegevoegd, selecteert u **Concept opslaan** onder aan de pagina.

## <a name="edit-a-blueprint"></a>Een blauwdruk bewerken

In [Een blauwdruk maken](#create-a-blueprint)hebt u geen beschrijving gegeven of de roltoewijzing toegevoegd aan de nieuwe resourcegroep. U beide oplossen door de volgende stappen te volgen:

1. Selecteer **Blauwdrukdefinities** op de pagina aan de linkerkant.

1. Klik in de lijst met blauwdrukken met de rechtermuisknop op de lijst die u eerder hebt gemaakt en selecteer **Blauwdruk bewerken**.

1. Geef in **Beschrijving van blauwdruk** informatie op over de blauwdruk en de artefacten waaruit deze bestaat. Voer in dit geval iets in als: **Met deze blauwdruk wordt het tagbeleid en de roltoewijzing op het abonnement ingesteld, wordt een ResourceGroep gemaakt en wordt een resourcesjabloon en roltoewijzing geïmplementeerd in die ResourceGroep.**

1. Selecteer **Volgende: Artefacten** onder aan de pagina of het tabblad **Artefacten** boven aan de pagina.

1. Voeg een roltoewijzing toe onder de resourcegroep:

   1. Selecteer de **rij + Artefact toevoegen** direct onder het item **ResourceGroep.**

   1. Selecteer **Roltoewijzing** voor **artefacttype**.

   1. Schakel **onder Rol**De optie **Eigenaar**in en schakel het selectievakje uit onder het vak **Gebruiker, app of groep toevoegen.**

   1. Zoek naar en selecteer een gebruiker, app of groep die u wilt toevoegen. Dit artefact maakt gebruik van een statische parameter die in elke toewijzing van deze blauwdruk is ingesteld.

   e. Selecteer **Toevoegen** om dit artefact aan de blauwdruk toe te voegen.

   ![Tweede roltoewijzing voor het blauwdrukartefact](./media/create-blueprint-portal/add-role-assignment-2.png)

1. De voltooide blauwdruk moet er nu ongeveer als volgt uitzien. De nieuw toegevoegde roltoewijzing toont **1 van de 1 parameters die zijn ingevuld.** Dat betekent dat het een statische parameter is.

   ![Tweede definitie voor de voltooide blauwdruk](./media/create-blueprint-portal/completed-blueprint-2.png)

1. Selecteer **Concept opslaan** nu het is bijgewerkt.

## <a name="publish-a-blueprint"></a>Een blauwdruk publiceren

Nu alle geplande artefacten aan de blauwdruk zijn toegevoegd, kunt u deze gaan publiceren.
Met publiceren is de blauwdruk beschikbaar om aan een abonnement te worden toegewezen.

1. Selecteer **Blauwdrukdefinities** op de pagina aan de linkerkant.

1. Klik in de lijst met blauwdrukken met de rechtermuisknop op de lijst die u eerder hebt gemaakt en selecteer **Blauwdruk publiceren**.

1. Geef in het deelvenster dat wordt geopend een **versie** op (letters, cijfers en koppeltekens met een maximale lengte van 20 tekens), zoals **v1**. Voer eventueel tekst in **Notities wijzigen**, zoals **Eerste publiceren**.

1. Selecteer **Publiceren** onder aan de pagina.

## <a name="assign-a-blueprint"></a>Een blauwdruk toewijzen

Nadat een blauwdruk is gepubliceerd, kan deze worden toegewezen aan een abonnement. Wijs de blauwdruk die u hebt gemaakt toe aan een van de abonnementen onder de hiërarchie van uw beheergroep. Als de blauwdruk is opgeslagen in een abonnement, kan deze alleen aan dat abonnement worden toegewezen.

1. Selecteer **Blauwdrukdefinities** op de pagina aan de linkerkant.

1. Klik in de lijst met blauwdrukken met de rechtermuisknop op de lijst die u eerder hebt gemaakt (of selecteer de ellips) en selecteer **Blauwdruk toewijzen**.

1. Selecteer **op** de vervolgpagina Blauwdruk toewijzen in de vervolgkeuzelijst **Abonnement** de abonnementen waarop u deze blauwdruk wilt implementeren.

   Als er ondersteunde Enterprise-aanbiedingen beschikbaar zijn via [Azure Billing](../../billing/index.md), wordt de koppeling **Nieuw** onder het vak **Abonnement** geactiveerd. Volg deze stappen:

   1. Klik op de koppeling **Nieuw** om een nieuw abonnement te maken in plaats van een bestaand abonnement te selecteren.

   1. Geef een **weergavenaam** voor het nieuwe abonnement op.

   1. Selecteer de beschikbare **aanbieding** in de vervolgkeuzelijst.

   1. Gebruik de ellips om de [beheergroep](../management-groups/overview.md) te selecteren waarvan het abonnement een kind zal zijn.

   1. Selecteer **Maken** onderaan de pagina.

   ![Een abonnement maken voor een abonnement op een blauwdruktoewijzing](./media/create-blueprint-portal/assignment-create-subscription.png)

   > [!IMPORTANT]
   > Het nieuwe abonnement wordt direct gemaakt nadat u Maken hebt **geselecteerd.**

   > [!NOTE]
   > Er wordt een toewijzing gemaakt voor elk abonnement dat u selecteert. U op een later tijdstip wijzigingen aanbrengen in één abonnementstoewijzing zonder wijzigingen op de rest van de geselecteerde abonnementen te forceren.

1. Geef **voor toewijzingsnaam**een unieke naam voor deze opdracht op.

1. Selecteer **in Locatie**een gebied voor het beheerde identiteits- en abonnementsimplementatieobject waarin moet worden gemaakt. Azure Blueprint gebruikt deze beheerde identiteit om alle artefacten in de toegewezen blauwdruk te implementeren. Zie [Beheerde identiteiten voor Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md) voor meer informatie.

1. Laat de vervolgkeuzelijst **Blueprint-definitieversie** van **gepubliceerde** versies op het **v1-item.** (De standaardinstelling is de meest recent gepubliceerde versie.)

1. Laat **Toewijzing vergrendelen** staan op de standaardwaarde **Niet vergrendelen**. Zie [Blauwdrukken bronvergrendeling](./concepts/resource-locking.md)voor meer informatie.

   ![Identiteiten vergrendelen en beheren voor de opdracht](./media/create-blueprint-portal/assignment-locking-mi.png)

1. Laat **onder Beheerde identiteit**de standaardinstelling van systeem toegewezen **.**

1. Voor de roltoewijzing op abonnementsniveau **[gebruikersgroep of toepassingsnaam]: Inzender** selecteert u een gebruiker, app of groep.

1. Stel **tagnaam** in op **CostCenter** en de **tagwaarde** **voor**de beleidstoewijzing op abonnementsniveau .

1. Geef **voor ResourceGroup**een **naam** van **StorageAccount** en een **locatie** van **Oost-US 2** op in de vervolgkeuzelijst.

   > [!NOTE]
   > Voor elk artefact dat u tijdens de blauwdrukdefinitie onder de resourcegroep hebt toegevoegd, wordt dat artefact ingesprongen om af te stemmen op de resourcegroep of het object waarmee u het wilt implementeren.
   > Artefacten die geen parameters bevatten of die geen parameters hebben die bij toewijzing moeten worden gedefinieerd, worden alleen vermeld voor contextuele informatie.

1. Selecteer **Standard_GRS** voor de parameter **StorageAccount** van de Azure Resource Manager-sjabloon **Storage.**

1. Lees het informatievak onder aan de pagina en selecteer **Toewijzen**.

## <a name="track-deployment-of-a-blueprint"></a>Implementatie van een blauwdruk bijhouden

Wanneer een blauwdruk is toegewezen aan een of meer abonnementen, gebeuren er twee dingen:

- De blauwdruk wordt toegevoegd aan de pagina **Toegewezen blauwdrukken** voor elk abonnement.
- Het proces van het implementeren van alle artefacten gedefinieerd door de blauwdruk begint.

Controleer nu de blauwdruk is toegewezen aan een abonnement, of de voortgang van de implementatie:

1. Selecteer **Toegewezen blauwdrukken** op de pagina aan de linkerkant.

1. Klik in de lijst met blauwdrukken met de rechtermuisknop op de lijst die u eerder hebt toegewezen en selecteer **Toewijzingsdetails weergeven.**

   ![Toewijzingsdetails weergeven op de pagina Toegewezen blauwdrukken](./media/create-blueprint-portal/view-assignment-details.png)

1. Valideer op de pagina **Blueprint-toewijzing** of alle artefacten zijn geïmplementeerd en dat er geen fouten zijn gemaakt tijdens de implementatie. Zie [Blauwdrukken oplossen](./troubleshoot/general.md) voor stappen om te bepalen wat er fout is gegaan als er fouten zijn gemaakt.

## <a name="clean-up-resources"></a>Resources opschonen

### <a name="unassign-a-blueprint"></a>De toewijzing van een blauwdruk ongedaan maken

Als u geen blauwdruktoewijzing meer nodig hebt, verwijdert u deze uit een abonnement. De blauwdruk is mogelijk vervangen door een nieuwere blauwdruk met bijgewerkte patronen, beleidsregels en ontwerpen. Wanneer een blauwdruk wordt verwijderd, blijven de artefacten die als onderdeel van die blauwdruk zijn toegewezen, achter. Voer de volgende stappen uit als u een blauwdruktoewijzing wilt verwijderen:

1. Selecteer **Toegewezen blauwdrukken** op de pagina aan de linkerkant.

1. Selecteer in de lijst met blauwdrukken de blauwdruk die u niet wilt toewijzen. Selecteer vervolgens de **knop Blauwdruk ongedaan maken** boven aan de pagina.

1. Lees het bevestigingsbericht en selecteer **OK**.

### <a name="delete-a-blueprint"></a>Een blauwdruk verwijderen

1. Selecteer **Blauwdrukdefinities** op de pagina aan de linkerkant.

1. Klik met de rechtermuisknop op de blauwdruk die u wilt verwijderen en selecteer **Blauwdruk verwijderen**. Selecteer vervolgens **Ja** in het bevestigingsdialoogvenster.

> [!NOTE]
> Als u een blauwdruk in deze methode verwijdert, worden ook alle gepubliceerde versies van de geselecteerde blauwdruk verwijderd.
> Als u één versie wilt verwijderen, opent u de blauwdruk, selecteert u het tabblad **Gepubliceerde versies,** selecteert u de versie die u wilt verwijderen en selecteert u **Deze versie verwijderen**. Je ook geen blauwdruk verwijderen totdat je alle blauwdruktoewijzing van die blauwdrukdefinitie hebt verwijderd.

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u een blauwdruk met Azure-portal gemaakt, toegewezen en verwijderd. Ga voor meer informatie over Azure Blueprints verder naar het artikel over de levenscyclus van de blauwdruk.

> [!div class="nextstepaction"]
> [Meer informatie over de levenscyclus van de blauwdruk](./concepts/lifecycle.md)