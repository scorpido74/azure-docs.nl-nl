---
title: Nieuwe resources beveiligen met de vergren delingen van blauw drukken
description: In deze zelf studie leert u hoe u de Azure blauw drukken-resource vergrendelings opties alleen-lezen kunt gebruiken en niet verwijderen om nieuwe geïmplementeerde resources te beveiligen.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/28/2019
ms.topic: tutorial
ms.service: blueprints
ms.openlocfilehash: 2f66677df7cd1c6fbde9c0467b4d7f2094509ee8
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72297008"
---
# <a name="tutorial-protect-new-resources-with-azure-blueprints-resource-locks"></a>Zelf studie: nieuwe resources beveiligen met Azure-blauw drukken resource vergrendelingen

Met Azure-blauw drukken [resource vergrendeling](../concepts/resource-locking.md)kunt u nieuwe geïmplementeerde resources beveiligen tegen onrecht matig gemanipuleerd, zelfs door een account met de rol van _eigenaar_ . U kunt deze beveiliging toevoegen in de blauw drukken definities van resources die zijn gemaakt met een resource manager-sjabloon artefact.

In deze zelf studie voert u de volgende stappen uit:

> [!div class="checklist"]
> - Een definitie van een blauw druk maken
> - De definitie van uw blauw druk markeren als **gepubliceerd**
> - De definitie van de blauw druk toewijzen aan een bestaand abonnement
> - De nieuwe resource groep controleren
> - De blauw druk voor het verwijderen van de vergren delingen opheffen

## <a name="prerequisites"></a>Vereisten

U hebt een Azure-abonnement nodig om deze zelfstudie te voltooien. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="create-a-blueprint-definition"></a>Een definitie van een blauw druk maken

Maak eerst de definitie van de blauw druk.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer op de pagina **aan** de slag aan de linkerkant **maken** onder **een blauw druk maken**.

1. Zoek het voor beeld van de **lege blauw** druk boven aan de pagina. Selecteer **beginnen met een lege blauw druk**.

1. Voer deze informatie in op het tabblad **basis beginselen** :

   - **Blauw druk-naam**: Geef een naam op voor uw kopie van het voor beeld van de blauw druk. Voor deze zelf studie gebruiken we de naam **Locked-Storage account**.
   - **Beschrijving blauw druk**: een beschrijving voor de definitie van de blauw druk toevoegen. Gebruiken **voor het testen van de resource vergrendeling op een blauw druk op geïmplementeerde resources**.
   - **Definitie locatie**: Selecteer de knop met het weglatings teken (...) en selecteer vervolgens de beheer groep of het abonnement waarvoor u de definitie van de blauw druk wilt opslaan.

1. Selecteer het tabblad **artefacten** boven aan de pagina of selecteer **volgende: artefacten** aan de onderkant van de pagina.

1. Een resource groep toevoegen op het abonnements niveau:
   1. Selecteer de rij **artefact toevoegen** onder **abonnement**.
   1. Selecteer **resource groep** onder **Type artefact**.
   1. Stel de **weergave naam artefact** in op **RGtoLock**.
   1. Laat de vakken naam en **locatie** van **resource groep** leeg, maar zorg ervoor dat het selectie vakje is ingeschakeld op elke eigenschap om deze **dynamische para meters**te maken.
   1. Selecteer **toevoegen** om het artefact toe te voegen aan de blauw druk.

1. Voeg een sjabloon toe onder de resource groep:
   1. Selecteer de rij **artefact toevoegen** onder de vermelding **RGtoLock** . 
   1. Selecteer **Azure Resource Manager sjabloon** onder **Type artefact**, stel **weergave naam voor artefact** in op **Storage account**en laat de **Beschrijving** leeg. 
   1. Plak op het tabblad **sjabloon** de volgende Resource Manager-sjabloon in het vak editor. Nadat u de sjabloon hebt geplakt, selecteert u **toevoegen** om het artefact toe te voegen aan de blauw druk.

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

1. Selecteer **concept opslaan** onder aan de pagina.

Met deze stap maakt u de blauw druk-definitie in de geselecteerde beheer groep of dit abonnement.

Nadat de waarschuwing voor het opslaan van de **blauw druk is geslaagd** , gaat u naar de volgende stap.

## <a name="publish-the-blueprint-definition"></a>De definitie van de blauw druk publiceren

De definitie van uw blauw druk is nu gemaakt in uw omgeving. Deze wordt gemaakt in de **concept** modus en moet worden gepubliceerd voordat deze kan worden toegewezen en geïmplementeerd.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer de pagina met **definities van blauw** drukken aan de linkerkant. Gebruik de filters om de definitie van de **afgesloten Storage account** blauw druk te vinden en selecteer deze.

1. Selecteer **blauw druk** aan de bovenkant van de pagina publiceren. Voer in het deel venster Nieuw rechts **1,0** in als de **versie**. Deze eigenschap is handig als u later een wijziging aanbrengt. Voer **wijzigings notities**in, zoals de **eerste versie die is gepubliceerd voor het vergren delen van op blauw drukken geïmplementeerde resources**. Selecteer vervolgens **publiceren** onder aan de pagina.

Met deze stap kunt u de blauw druk toewijzen aan een abonnement. Wanneer de definitie van de blauw druk is gepubliceerd, kunt u nog steeds wijzigingen aanbrengen. Als u wijzigingen aanbrengt, moet u de definitie publiceren met een nieuwe versie waarde om de verschillen tussen versies van dezelfde blauw druk-definitie bij te houden.

Ga naar de volgende stap nadat de portal-melding voor het publiceren van de **blauw druk is voltooid** weer gegeven.

## <a name="assign-the-blueprint-definition"></a>De definitie van de blauw druk toewijzen

Wanneer de definitie van de blauw druk is gepubliceerd, kunt u deze toewijzen aan een abonnement binnen de beheer groep waar u het hebt opgeslagen. In deze stap geeft u para meters op om elke implementatie van de definitie van de blauw druk uniek te maken.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer de pagina met **definities van blauw** drukken aan de linkerkant. Gebruik de filters om de definitie van de **afgesloten Storage account** blauw druk te vinden en selecteer deze.

1. Selecteer de optie **blauw** drukken boven aan de pagina met de definitie van de blauw druk.

1. Geef de parameter waarden voor de toewijzing van de blauw druk op:

   - **Basisinstellingen**

     - **Abonnementen**: Selecteer een of meer abonnementen in de beheer groep waar u de definitie van de blauw druk hebt opgeslagen. Als u meer dan één abonnement selecteert, wordt er voor elk abonnement een toewijzing gemaakt met behulp van de para meters die u invoert.
     - **Toewijzings naam**: de naam wordt vooraf ingevuld op basis van de naam van de definitie van de blauw druk. We willen dat deze toewijzing de vergren deling van de nieuwe resource groep aangeeft. Wijzig daarom de naam van de toewijzing in **Assignment-Locked-Storage account-TestingBPLocks**.
     - **Locatie**: Selecteer een regio waarin u de beheerde identiteit wilt maken. Azure Blueprint gebruikt deze beheerde identiteit om alle artefacten in de toegewezen blauwdruk te implementeren. Zie [Beheerde identiteiten voor Azure-resources](../../../active-directory/managed-identities-azure-resources/overview.md) voor meer informatie.
       Voor deze zelf studie selecteert u **VS Oost 2**.
     - **Definitie van blauw druk-definition**: Selecteer de gepubliceerde versie **1,0** van de blauw druk-definitie.

   - **Toewijzing vergren delen**

     Selecteer de vergrendelings modus voor **alleen-lezen** blauw drukken. Zie voor meer informatie [Vergrendeling van blauwdrukresources](../concepts/resource-locking.md).

   - **Beheerde identiteit**

     Gebruik de standaard optie: **toegewezen systeem**. Zie [Managed Identities](../../../active-directory/managed-identities-azure-resources/overview.md)(Engelstalig) voor meer informatie.

   - **Artefact parameters**

     De in deze sectie gedefinieerde para meters zijn van toepassing op het artefact waaronder ze zijn gedefinieerd. Deze para meters zijn [dynamische para meters](../concepts/parameters.md#dynamic-parameters) , omdat ze zijn gedefinieerd tijdens de toewijzing van de blauw druk. Stel voor elk artefact de parameter waarde in op wat u ziet in de kolom **waarde** .

     |Artefact naam|Type artefact|Parameternaam|Waarde|Beschrijving|
     |-|-|-|-|-|
     |Resource groep RGtoLock|Resourcegroep|Naam|TestingBPLocks|Hiermee definieert u de naam van de nieuwe resource groep waarop blauw drukken moet worden toegepast.|
     |Resource groep RGtoLock|Resourcegroep|Locatie|US - west 2|Hiermee definieert u de locatie van de nieuwe resource groep waarop blauw drukken moet worden toegepast.|
     |Storage account|Resource Manager-sjabloon|storageAccountType (Storage account)|Standard_GRS|De opslag-SKU. De standaard waarde is _Standard_LRS_.|

1. Nadat u alle para meters hebt ingevoerd, selecteert u aan de onderkant van de pagina **toewijzen** .

Met deze stap implementeert u de gedefinieerde resources en configureert u de geselecteerde **vergrendelings toewijzing**. Het kan tot 30 minuten duren om blauw druk vergrendelingen toe te passen.

Nadat de portal-definitie voor het toewijzen van de **blauw druk is geslaagd** , gaat u naar de volgende stap.

## <a name="inspect-resources-deployed-by-the-assignment"></a>Resources controleren die door de toewijzing zijn geïmplementeerd

De toewijzing maakt de resource groep _TestingBPLocks_ en het opslag account dat door het Resource Manager-sjabloon artefact wordt geïmplementeerd. De nieuwe resource groep en de geselecteerde vergrendelings status worden weer gegeven op de pagina toewijzings Details.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer de pagina **toegewezen blauw drukken** aan de linkerkant. Gebruik de filters om de Storage account-blauw druk toewijzing met **toewijzings vergrendeling-TestingBPLocks** te vinden en selecteer deze.

   Op deze pagina kunt u zien dat de toewijzing is geslaagd en dat de resources zijn geïmplementeerd met de nieuwe status blauw drukken. Als de toewijzing is bijgewerkt, bevat de vervolg keuzelijst **toewijzings bewerking** Details over de implementatie van elke definitie versie. U kunt de resource groep selecteren om de eigenschappen pagina te openen.

1. Selecteer de resource groep **TestingBPLocks** .

1. Selecteer de pagina **toegangs beheer (IAM)** aan de linkerkant. Selecteer vervolgens het tabblad **roltoewijzingen** .

   Hier zien we dat de toewijzing van de _Storage account-TestingBPLocks_ blauw drukken de rol _eigenaar_ heeft. Deze functie heeft deze rol omdat deze rol is gebruikt om de resource groep te implementeren en te vergren delen.

1. Selecteer het tabblad **toewijzingen weigeren** .

   De toewijzing van de blauw druk heeft een [deny-toewijzing](../../../role-based-access-control/deny-assignments.md) gemaakt voor de geïmplementeerde resource groep om de **alleen-lezen** vergrendelings modus voor blauw drukken af te dwingen. Met de weiger toewijzing kan iemand met de juiste rechten op het tabblad **roltoewijzingen** geen specifieke acties uitvoeren. De weiger toewijzing heeft gevolgen voor _alle principals_.

   Zie [blauw drukken voor resource vergrendeling](../concepts/resource-locking.md#exclude-a-principal-from-a-deny-assignment)voor meer informatie over het uitsluiten van een principal van een weigerings toewijzing.

1. Selecteer de toewijzing weigeren en selecteer vervolgens de pagina **geweigerde machtigingen** aan de linkerkant.

   De weiger toewijzing voor komt dat alle bewerkingen met de configuratie van de **\*** en de **actie** worden uitgevoerd, maar de lees toegang is toegestaan door de **\*/Lees** bewerking uit te **sluiten.**

1. Selecteer **TestingBPLocks-Access Control (IAM)** in de breadcrumb Azure Portal. Selecteer vervolgens de pagina **overzicht** aan de linkerkant en klik vervolgens op de knop **resource groep verwijderen** . Voer de naam **TestingBPLocks** in om het verwijderen te bevestigen en selecteer vervolgens **verwijderen** onder aan het deel venster.

   De portal melding voor het verwijderen van de **resource groep TestingBPLocks is mislukt** . De fout geeft aan dat hoewel uw account gemachtigd is om de resource groep te verwijderen, de toegang wordt geweigerd door de toewijzing van de blauw druk. Houd er rekening mee dat de **alleen-lezen** modus blauw drukken tijdens het toewijzen van blauw drukken is geselecteerd. Met de vergren deling blauw druk voor komt u dat een account met toestemming, zelfs _eigenaar_, de resource verwijdert. Zie voor meer informatie [Vergrendeling van blauwdrukresources](../concepts/resource-locking.md).

Deze stappen laten zien dat onze geïmplementeerde resources nu worden beveiligd met blauw drukken die ongewenste verwijdering voor komen, zelfs van een account dat is gemachtigd om de resources te verwijderen.

## <a name="unassign-the-blueprint"></a>De blauw druk intrekken

De laatste stap bestaat uit het verwijderen van de toewijzing van de definitie van de blauw druk. Als u de toewijzing verwijdert, worden de gekoppelde artefacten niet verwijderd.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer de pagina **toegewezen blauw drukken** aan de linkerkant. Gebruik de filters om de Storage account-blauw druk toewijzing met **toewijzings vergrendeling-TestingBPLocks** te vinden en selecteer deze.

1. Selecteer aan de bovenkant van de pagina de optie **blauw** pagina opheffen. Lees de waarschuwing in het bevestigings venster en selecteer **OK**.

   Wanneer de blauw druk-toewijzing wordt verwijderd, worden de blauw druk vergrendelingen ook verwijderd. De resources kunnen opnieuw worden verwijderd door een account met de juiste machtigingen.

1. Selecteer **resource groepen** in het menu van Azure en selecteer vervolgens **TestingBPLocks**.

1. Selecteer de pagina **toegangs beheer (IAM)** aan de linkerkant en selecteer vervolgens het tabblad **roltoewijzingen** .

De beveiliging voor de resource groep toont aan dat de toewijzing van de blauw druk geen _eigenaars_ toegang meer heeft.

Ga naar de volgende stap nadat u de portal melding voor het verwijderen van de **blauw druk-toewijzing hebt verwijderd** weer gegeven.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met deze zelf studie, verwijdert u deze resources:

- _TestingBPLocks_ van resource groep
- Definitie van blauw druk _vergrendeld-Storage account_

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [levenscyclus van een blauwdruk](../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../concepts/parameters.md) gebruikt.
- Meer informatie over het gebruik van de [resource vergrendeling blauw](../concepts/resource-locking.md)drukken.
- Meer informatie over hoe u de [blauwdrukvolgorde](../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../how-to/update-existing-assignments.md).
- Problemen [oplossen](../troubleshoot/general.md) tijdens de toewijzing van een blauw druk.
