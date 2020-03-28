---
title: 'Zelfstudie: Nieuwe bronnen beveiligen met vergrendelingen'
description: In deze zelfstudie gebruikt u de opties Read Only en Do Not Delete van Azure Blueprints-bronvergrendelingen om nieuw geïmplementeerde resources te beschermen.
ms.date: 11/21/2019
ms.topic: tutorial
ms.openlocfilehash: ee57ff0c08f4fb8aa710dd2fa4dcef664484973d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74327449"
---
# <a name="tutorial-protect-new-resources-with-azure-blueprints-resource-locks"></a>Zelfstudie: Nieuwe resources beveiligen met Azure Blueprints-bronvergrendelingen

Met Azure [Blueprints-bronvergrendelingen](../concepts/resource-locking.md)u voorkomen dat nieuw geïmplementeerde resources worden geknoeid, zelfs door een account met de rol _Eigenaar._ U deze bescherming toevoegen aan de blauwdrukdefinities van bronnen die zijn gemaakt door een resourcebeheersjabloonartefact.

In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
> - Een blauwdrukdefinitie maken
> - Uw blauwdrukdefinitie markeren als **Gepubliceerd**
> - Uw blauwdrukdefinitie toewijzen aan een bestaand abonnement
> - De nieuwe resourcegroep inspecteren
> - De blauwdruk ongedaan maken om de vergrendelingen te verwijderen

## <a name="prerequisites"></a>Vereisten

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free) voordat u begint.

## <a name="create-a-blueprint-definition"></a>Een blauwdrukdefinitie maken

Maak eerst de blauwdrukdefinitie.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer op de pagina **Aan de** slag aan de linkerkant de optie **Maken** onder Een **blauwdruk maken**.

1. Zoek het voorbeeld van de **blauwdruk van de lege blauwdruk** boven aan de pagina. Selecteer **Begin met een lege blauwdruk**.

1. Voer deze informatie in op het tabblad **Basisbeginselen:**

   - **Blauwdruknaam:** geef een naam op voor uw kopie van het blauwdrukvoorbeeld. Voor deze zelfstudie gebruiken we de naam **locked-storageaccount**.
   - **Blauwdrukbeschrijving**: Voeg een beschrijving toe voor de blauwdrukdefinitie. Gebruiken **Voor het testen van blauwdrukbronvergrendeling op geïmplementeerde resources**.
   - **Definitielocatie**: Selecteer de knop ellips (...) en selecteer vervolgens de beheergroep of het abonnement om uw blauwdrukdefinitie op te slaan.

1. Selecteer het tabblad **Artefacten** boven aan de pagina of selecteer **Volgende: artefacten** onder aan de pagina.

1. Een resourcegroep toevoegen op abonnementsniveau:
   1. Selecteer de rij **Artefact toevoegen** onder **Abonnement**.
   1. Selecteer **Resourcegroep** onder **artefacttype**.
   1. Stel de naam van het **artefact-weergave** in op **RGtoLock**.
   1. Laat de vakken Naam en **locatie resourcegroep** leeg, maar zorg ervoor dat het selectievakje op elke eigenschap is ingeschakeld om ze **dynamische parameters**te maken. **Resource Group Name**
   1. Selecteer **Toevoegen** om het artefact aan de blauwdruk toe te voegen.

1. Een sjabloon toevoegen onder de resourcegroep:
   1. Selecteer de rij **Artefact toevoegen** onder de **RGtoLock-vermelding.**
   1. Selecteer **Azure Resource Manager-sjabloon** onder **Artefact-type,** stel **de weergavenaam Artefact** in op **StorageAccount**en laat **Beschrijving** leeg.
   1. Plak op het tabblad **Sjabloon** de volgende sjabloon Resourcebeheer in het editorvak.
      Nadat u in de sjabloon hebt geplakt, selecteert u **Toevoegen** om het artefact aan de blauwdruk toe te voegen.

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
           }
       },
       "variables": {
           "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
       },
       "resources": [{
           "type": "Microsoft.Storage/storageAccounts",
           "name": "[variables('storageAccountName')]",
           "location": "[resourceGroup().location]",
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

1. Selecteer **Concept opslaan** onder aan de pagina.

Met deze stap wordt de blauwdrukdefinitie gemaakt in de geselecteerde beheergroep of -abonnement.

Nadat de **opgeslagen poortmelding voor de bestandsdefinitie opslaan** is weergegeven, gaat u naar de volgende stap.

## <a name="publish-the-blueprint-definition"></a>De blauwdrukdefinitie publiceren

Uw blauwdrukdefinitie is nu gemaakt in uw omgeving. Het is gemaakt in **de conceptmodus** en moet worden gepubliceerd voordat het kan worden toegewezen en geïmplementeerd.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer de pagina **Blauwdrukdefinities** aan de linkerkant. Gebruik de filters om de blauwdrukdefinitie **voor vergrendelde opslagaccount** te vinden en selecteer deze vervolgens.

1. Selecteer **Blauwdruk publiceren** boven aan de pagina. Voer in het nieuwe deelvenster aan de rechterkant **1.0** in als **versie**. Deze eigenschap is handig als u later een wijziging aanbrengt. Voer **Wijzigingsnotities in,** zoals **Eerste versie die is gepubliceerd voor het vergrendelen van door blueprint geïmplementeerde bronnen**. Selecteer vervolgens **Publiceren** onder aan de pagina.

Deze stap maakt het mogelijk om de blauwdruk toe te wijzen aan een abonnement. Nadat de blauwdrukdefinitie is gepubliceerd, u nog steeds wijzigingen aanbrengen. Als u wijzigingen aanbrengt, moet u de definitie publiceren met een nieuwe versiewaarde om verschillen tussen versies van dezelfde blauwdrukdefinitie bij te houden.

Nadat de **publicatieblauwdrukdefinitie is opgevolgd,** gaat u naar de volgende stap.

## <a name="assign-the-blueprint-definition"></a>De blauwdrukdefinitie toewijzen

Nadat de blauwdrukdefinitie is gepubliceerd, u deze toewijzen aan een abonnement binnen de beheergroep waar u het hebt opgeslagen. In deze stap geeft u parameters op om elke implementatie van de blauwdrukdefinitie uniek te maken.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer de pagina **Blauwdrukdefinities** aan de linkerkant. Gebruik de filters om de blauwdrukdefinitie **voor vergrendelde opslagaccount** te vinden en selecteer deze vervolgens.

1. Selecteer **Blauwdruk toewijzen** boven aan de pagina met blauwdrukdefinitie.

1. Geef de parameterwaarden op voor de blauwdruktoewijzing:

   - **Basics**

     - **Abonnementen:** selecteer een of meer abonnementen in de beheergroep waar u uw blauwdrukdefinitie hebt opgeslagen. Als u meer dan één abonnement selecteert, wordt voor elk abonnement een toewijzing gemaakt met behulp van de parameters die u invoert.
     - **Toewijzingsnaam**: De naam wordt vooraf ingevuld op basis van de naam van de blauwdrukdefinitie. We willen dat deze toewijzing de vergrendeling van de nieuwe resourcegroep vertegenwoordigt, dus wijzig de toewijzingsnaam in **toewijzing-locked-storageaccount-TestingBPLocks**.
     - **Locatie:** selecteer een gebied waarin u de beheerde identiteit wilt maken. Azure Blueprint gebruikt deze beheerde identiteit om alle artefacten in de toegewezen blauwdruk te implementeren. Zie [beheerde identiteiten voor Azure-resources voor](../../../active-directory/managed-identities-azure-resources/overview.md)meer informatie.
       Selecteer voor deze zelfstudie **Oost-VS 2**.
     - Versie van de **blauwdrukdefinitie**: Selecteer de gepubliceerde versie **1.0** van de blauwdrukdefinitie.

   - **Vergrendelingstoewijzing**

     Selecteer de **modus Alleen-lezen-** blauwdrukvergrendeling. Zie voor meer informatie [Vergrendeling van blauwdrukresources](../concepts/resource-locking.md).

   - **Beheerde identiteit**

     Gebruik de standaardoptie: **Systeem toegewezen**. Zie [beheerde identiteiten](../../../active-directory/managed-identities-azure-resources/overview.md)voor meer informatie .

   - **Artefact-parameters**

     De parameters die in deze sectie zijn gedefinieerd, zijn van toepassing op het artefact waaronder ze zijn gedefinieerd. Deze parameters zijn [dynamische parameters](../concepts/parameters.md#dynamic-parameters) omdat ze worden gedefinieerd tijdens de toewijzing van de blauwdruk. Stel voor elk artefact de parameterwaarde in op wat u ziet in de kolom **Waarde.**

     |Artefact-naam|Artefacttype|Parameternaam|Waarde|Beschrijving|
     |-|-|-|-|-|
     |RGtoLock-brongroep|Resourcegroep|Name|Testen BPLocks|Hiermee definieert u de naam van de nieuwe resourcegroep om blauwdrukvergrendelingen op toe te passen.|
     |RGtoLock-brongroep|Resourcegroep|Locatie|VS - west 2|Hiermee definieert u de locatie van de nieuwe resourcegroep om blauwdrukvergrendelingen op toe te passen.|
     |StorageAccount|Resource Manager-sjabloon|storageAccountType (StorageAccount)|Standard_GRS|De opslag SKU. De standaardwaarde is _Standard_LRS_.|

1. Nadat u alle parameters hebt ingevoerd, selecteert u **Toewijzen** onder aan de pagina.

Met deze stap worden de gedefinieerde resources geïmplementeerd en wordt de geselecteerde **vergrendelingstoewijzing geconfigureerd.** Het kan tot 30 minuten duren om blauwdrukvergrendelingen toe te passen.

Nadat de **ontwerpblauwdrukdefinitie is geslaagd,** gaat u naar de volgende stap.

## <a name="inspect-resources-deployed-by-the-assignment"></a>Resources inspecteren die door de toewijzing zijn geïmplementeerd

De toewijzing maakt de resourcegroep _TestingBPLocks_ en het opslagaccount dat is geïmplementeerd door het artefact resourcemanager-sjabloon. De nieuwe resourcegroep en de geselecteerde vergrendelingsstatus worden weergegeven op de pagina toewijzingsdetails.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer de pagina **Toegewezen blauwdrukken** aan de linkerkant. Gebruik de filters om de blauwdruktoewijzing voor de blauwdruktoewijzing voor de **toewijzing vergrendeld-storageaccount-TestingBPLocks** te vinden en deze vervolgens te selecteren.

   Op deze pagina kunnen we zien dat de toewijzing is geslaagd en dat de resources zijn geïmplementeerd met de nieuwe status van blauwdrukvergrendeling. Als de toewijzing wordt bijgewerkt, worden in de vervolgkeuzelijst **Toewijzingsbewerking** details weergegeven over de implementatie van elke definitieversie. U de resourcegroep selecteren om de eigenschappenpagina te openen.

1. Selecteer de brongroep **TestingBPLocks.**

1. Selecteer de pagina **IAM (Access control)** aan de linkerkant. Selecteer vervolgens het tabblad **Toewijzingen van rollen.**

   Hier zien we dat de _toewijzing-locked-storageaccount-TestingBPLocks_ blauwdruk opdracht heeft de _rol Eigenaar._ Deze rol heeft deze rol omdat deze rol is gebruikt om de resourcegroep te implementeren en te vergrendelen.

1. Selecteer het tabblad **Toewijzingen weigeren.**

   De blauwdruktoewijzing heeft een [toewijzing voor weigeren](../../../role-based-access-control/deny-assignments.md) gemaakt in de geïmplementeerde resourcegroep om de modus **Alleen-lezen-blauwdrukvergrendeling** af te dwingen. De toewijzing weigeren voorkomt dat iemand met de juiste rechten op het tabblad **Toewijzingen van rollen** specifieke acties kan uitvoeren. De toewijzing weigeren is van invloed op _alle principals_.

   Zie [blauwdrukken bronvergrendeling](../concepts/resource-locking.md#exclude-a-principal-from-a-deny-assignment)voor informatie over het uitsluiten van een principal van een toewijzing voor weigeren.

1. Selecteer de toewijzing weigeren en selecteer de pagina **Geweigerde machtigingen** aan de linkerkant.

   De weigeringstoewijzing voorkomt **\*** alle bewerkingen met de configuratie en **Actie,** maar biedt leestoegang mogelijk door het uitsluiten ** \*/lezen** via **NotActions**.

1. Selecteer in de broodkruimel van de Azure-portal de optie **TestingBPLocks - Access control (IAM)**. Selecteer vervolgens de pagina **Overzicht** aan de linkerkant en vervolgens de knop **Brongroep verwijderen.** Voer de naam **TestingBPLocks** in om de delete te bevestigen en selecteer **Delete** onder aan het deelvenster.

   De portalmelding **TestBPLocks verwijderen van resourcesgroep wordt mislukt.** De fout stelt dat hoewel uw account toestemming heeft om de brongroep te verwijderen, de toegang wordt geweigerd door de blauwdruktoewijzing. Vergeet niet dat we de **lees-alleen-blauwdrukvergrendelingsmodus** hebben geselecteerd tijdens de blauwdruktoewijzing. Het blauwdrukslot voorkomt dat een account met toestemming, zelfs _Eigenaar,_ de bron kan verwijderen. Zie voor meer informatie [Vergrendeling van blauwdrukresources](../concepts/resource-locking.md).

Deze stappen laten zien dat onze geïmplementeerde resources nu zijn beveiligd met blauwdrukvergrendelingen die ongewenste verwijdering voorkomen, zelfs van een account dat toestemming heeft om de bronnen te verwijderen.

## <a name="unassign-the-blueprint"></a>De blauwdruk ongedaan maken

De laatste stap is het verwijderen van de toewijzing van de blauwdrukdefinitie. Als u de toewijzing verwijdert, worden de bijbehorende artefacten niet verwijderd.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer de pagina **Toegewezen blauwdrukken** aan de linkerkant. Gebruik de filters om de blauwdruktoewijzing voor de blauwdruktoewijzing voor de **toewijzing vergrendeld-storageaccount-TestingBPLocks** te vinden en deze vervolgens te selecteren.

1. Selecteer **Blauwdruk ongedaan maken** boven aan de pagina. Lees de waarschuwing in het bevestigingsdialoogvenster en selecteer **OK**.

   Wanneer de blauwdruktoewijzing wordt verwijderd, worden ook de blauwdrukvergrendelingen verwijderd. De bronnen kunnen opnieuw worden verwijderd door een account met de juiste machtigingen.

1. Selecteer **Resourcegroepen** in het Azure-menu en selecteer **Vervolgens TestBPLocks**.

1. Selecteer de pagina **Toegangsbesturingselement (IAM)** aan de linkerkant en selecteer vervolgens het tabblad **Toewijzingen van rollen.**

De beveiliging voor de resourcegroep geeft aan dat de blauwdruktoewijzing geen _toegang tot de eigenaar_ meer heeft.

Nadat de **geslaagde portalmelding voor het verwijderen van** de ontwerpopdracht is weergegeven, gaat u naar de volgende stap.

## <a name="clean-up-resources"></a>Resources opschonen

Als u klaar bent met deze zelfstudie, verwijdert u deze bronnen:

- Testen _bplocks van resourcegroep_
- Blueprint definitie _locked-storageaccount_

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u nieuwe resources beschermen die zijn geïmplementeerd met Azure Blueprints. Ga voor meer informatie over Azure Blueprints verder naar het artikel over de levenscyclus van de blauwdruk.

> [!div class="nextstepaction"]
> [Meer informatie over de levenscyclus van de blauwdruk](../concepts/lifecycle.md)