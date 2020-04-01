---
title: Veelvoorkomende implementatiefouten oplossen
description: Beschrijft hoe u veelvoorkomende fouten oplossen wanneer u resources implementeert naar Azure met Azure Resource Manager.
tags: top-support-issue
ms.topic: troubleshooting
ms.date: 10/04/2019
ms.openlocfilehash: bc1568c53cdb5518f694d77a2f28f3cf77296ee2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460378"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Veelvoorkomende fouten met Azure-implementatie oplossen met Azure Resource Manager

In dit artikel worden enkele veelvoorkomende Azure-implementatiefouten beschreven en worden informatie verstrekt om de fouten op te lossen. Zie [Foutcode zoeken](#find-error-code)als u de foutcode voor uw implementatiefout niet vinden.

Als u op zoek bent naar informatie over een foutcode en die informatie wordt niet verstrekt in dit artikel, laat het ons weten. Onderaan deze pagina u feedback geven. De feedback wordt bijgehouden met GitHub Issues.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="error-codes"></a>Foutcodes

| Foutcode | Oplossing | Meer informatie |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | Volg naamgevingsbeperkingen voor opslagaccounts. | [Naam van het opslagaccount oplossen](error-storage-account-name.md) |
| AccountPropertyCannotBeSet | Controleer de beschikbare opslagaccounteigenschappen. | [storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| Toewijzing mislukt | Het cluster of de regio heeft geen resources beschikbaar of kan de gevraagde VM-grootte niet ondersteunen. Probeer de aanvraag op een later tijdstip opnieuw of vraag een andere VM-grootte aan. | [Problemen met inrichten en toewijzen voor Linux,](../../virtual-machines/linux/troubleshoot-deployment-new-vm.md) [Provisioning en toewijzing voor Windows](../../virtual-machines/windows/troubleshoot-deployment-new-vm.md) en problemen met [toewijzingoplossen](../../virtual-machines/troubleshooting/allocation-failure.md)|
| AnotherOperationInProgress | Wacht tot de gelijktijdige bewerking is voltooid. | |
| Autorisatie mislukt | Uw account- of serviceprincipal heeft onvoldoende toegang om de implementatie te voltooien. Controleer de rol waartoe uw account behoort en de toegang voor het implementatiebereik.<br><br>Mogelijk ontvangt u deze fout wanneer een vereiste resourceprovider niet is geregistreerd. | [Azure-toegangsbeheer op basis van azure-rollen](../../role-based-access-control/role-assignments-portal.md)<br><br>[Registratie oplossen](error-register-resource-provider.md) |
| BadRequest | U hebt implementatiewaarden verzonden die niet overeenkomen met wat resourcebeheer verwacht. Controleer het bericht met de innerlijke status voor hulp bij het oplossen van problemen. | [Sjabloonverwijzing](/azure/templates/) en [ondersteunde locaties](resource-location.md) |
| Conflict | U vraagt een bewerking aan die niet is toegestaan in de huidige status van de resource. Het aanpassen van de schijf is bijvoorbeeld alleen toegestaan bij het maken van een vm of wanneer de vm wordt toegewezen. | |
| DeploymentActiveAndUneditable | Wacht tot de gelijktijdige implementatie naar deze resourcegroep is voltooid. | |
| ImplementatiefailedCleanUp | Wanneer u in de volledige modus implementeert, worden alle resources die niet in de sjabloon staan, verwijderd. U krijgt deze fout als u niet over voldoende machtigingen beschikt om alle bronnen die niet in de sjabloon staan te verwijderen. Als u de fout wilt voorkomen, wijzigt u de implementatiemodus in incrementeel. | [Implementatiemodi azure resource manager](deployment-modes.md) |
| DeploymentNameInvalidCharacters | De implementatienaam kan alleen letter, cijfer, '-', '.' of '_' bevatten. | |
| DeploymentNameLengthLimitExceeded | De implementatienamen zijn beperkt tot 64 tekens.  | |
| Implementatie is mislukt | De fout DeploymentFailed is een algemene fout die niet de gegevens bevat die u nodig hebt om de fout op te lossen. Kijk in de foutgegevens naar een foutcode die meer informatie biedt. | [Foutcode zoeken](#find-error-code) |
| DeploymentQuotaExceeded | Als u de limiet van 800 implementaties per resourcegroep bereikt, verwijdert u implementaties uit de geschiedenis die niet langer nodig zijn. | [Fout oplossen wanneer het aantal implementaties hoger is dan 800](deployment-quota-exceeded.md) |
| DnsRecordinuse | De DNS-recordnaam moet uniek zijn. Voer een andere naam in. | |
| ImageNotFound | Controleer de instellingen van VM-afbeelding. |  |
| InUseSubnetCannotBeDeleted | Deze fout kan worden opgetreden wanneer u een resource probeert bij te werken en de aanvraag wordt verwerkt door de bron te verwijderen en te maken. Zorg ervoor dat u alle ongewijzigde waarden opgeeft. | [Bron bijwerken](/azure/architecture/building-blocks/extending-templates/update-resource) |
| Ongeldig verificatieTokenTenant | Krijg toegangstoken voor de juiste tenant. U het token alleen krijgen van de tenant waartoe uw account behoort. | |
| Ongeldig ContentLink | U hebt waarschijnlijk geprobeerd een koppeling te maken naar een geneste sjabloon die niet beschikbaar is. Controleer de URI die u hebt opgegeven voor de geneste sjabloon. Als de sjabloon bestaat in een opslagaccount, controleert u of de URI toegankelijk is. Mogelijk moet u een SAS-token doorgeven. Momenteel u geen koppeling maken naar een sjabloon die zich in een opslagaccount achter een [Azure Storage-firewall bevindt.](../../storage/common/storage-network-security.md) Overweeg om uw sjabloon naar een andere opslagplaats te verplaatsen, zoals GitHub. | [Gekoppelde sjablonen](linked-templates.md) |
| Ongeldige locatie voor implementatie | Wanneer u op abonnementsniveau implementeert, hebt u een andere locatie opgegeven voor een eerder gebruikte implementatienaam. | [Implementaties op abonnementsniveau](deploy-to-subscription.md) |
| OngeldigParameter | Een van de waarden die u voor een resource hebt opgegeven, komt niet overeen met de verwachte waarde. Deze fout kan het gevolg zijn van veel verschillende omstandigheden. Een wachtwoord kan bijvoorbeeld onvoldoende zijn of een blobnaam kan onjuist zijn. Het foutbericht moet aangeven welke waarde moet worden gecorrigeerd. | |
| Ongeldigeinhoud | De implementatiewaarden bevatten waarden die niet worden herkend of vereiste waarden ontbreken. Bevestig de waarden voor uw resourcetype. | [Sjabloonverwijzing](/azure/templates/) |
| Ongeldige opmaak van verzoek | Schakel foutopsporingslogboekregistratie in bij het uitvoeren van de implementatie en controleer de inhoud van de aanvraag. | [Foutopsporingslogboeken](#enable-debug-logging) |
| Ongeldige ResourceNameruimte | Controleer de resourcenaamruimte die u hebt opgegeven in de **eigenschap type.** | [Sjabloonverwijzing](/azure/templates/) |
| Ongeldige ResourceReference | De bron bestaat nog niet of wordt onjuist verwezen. Controleer of u een afhankelijkheid moet toevoegen. Controleer of uw gebruik van de **referentiefunctie** de vereiste parameters voor uw scenario bevat. | [Afhankelijkheden oplossen](error-not-found.md) |
| Ongeldig ResourceType | Controleer het resourcetype dat u hebt opgegeven in de **eigenschap type.** | [Sjabloonverwijzing](/azure/templates/) |
| Ongeldige inschrijvingstaat | Registreer uw abonnement bij de resourceprovider. | [Registratie oplossen](error-register-resource-provider.md) |
| InvalidTemplate | Controleer de syntaxis van de sjabloon op fouten. | [Ongeldige sjabloon oplossen](error-invalid-template.md) |
| Ongeldige TemplateCircularDependency | Verwijder onnodige afhankelijkheden. | [Kringafhankelijkheden oplossen](error-invalid-template.md#circular-dependency) |
| LinkedAuthorization mislukt | Controleer of uw account tot dezelfde tenant behoort als de resourcegroep waarnaar u implementeert. | |
| LinkedInvalidPropertyId | De resource-id voor een resource wordt niet correct opgelost. Controleer of u alle vereiste waarden opgeeft voor de resource-id, waaronder abonnements-id, naam van de resourcegroep, resourcetype, naam van de bovenliggende resource (indien nodig) en resourcenaam. | |
| Locatievereist | Geef een locatie op voor de resource. | [Locatie instellen](resource-location.md) |
| MismatchingResourceSegmenten | Zorg ervoor dat geneste resource het juiste aantal segmenten in naam en type heeft. | [Resourcesegmenten oplossen](error-invalid-template.md#incorrect-segment-lengths)
| Ontbrekende registratievoorlocatie | Controleer de registratiestatus van resourceprovider en ondersteunde locaties. | [Registratie oplossen](error-register-resource-provider.md) |
| Ontbrekende inschrijvingRegistratie | Registreer uw abonnement bij de resourceprovider. | [Registratie oplossen](error-register-resource-provider.md) |
| NoRegisteredProviderFound | Controleer de registratiestatus van resourceprovider. | [Registratie oplossen](error-register-resource-provider.md) |
| NotFound | Mogelijk probeert u een afhankelijke resource parallel met een bovenliggende resource te implementeren. Controleer of u een afhankelijkheid moet toevoegen. | [Afhankelijkheden oplossen](error-not-found.md) |
| OperationNotA toegestaan | De implementatie probeert een bewerking uit die het quotum voor het abonnement, de resourcegroep of de regio overschrijdt. Indien mogelijk uw implementatie herzien om binnen de quota te blijven. Anders u overwegen een wijziging van uw quota aan te vragen. | [Quota oplossen](error-resource-quota.md) |
| ParentResourceNotFound | Zorg ervoor dat er een bovenliggende bron bestaat voordat u de onderliggende bronnen maakt. | [Bovenliggende bron oplossen](error-parent-resource.md) |
| WachtwoordTeLong | Mogelijk hebt u een wachtwoord met te veel tekens geselecteerd of uw wachtwoordwaarde geconverteerd naar een beveiligde tekenreeks voordat u het als parameter doorgeeft. Als de sjabloon een **beveiligde tekenreeksparameter** bevat, hoeft u de waarde niet om te zetten in een beveiligde tekenreeks. Geef de wachtwoordwaarde op als tekst. |  |
| PrivateIP-InReservedRange | Het opgegeven IP-adres bevat een adresbereik dat vereist is voor Azure. Wijzig het IP-adres om een gereserveerd bereik te vermijden. | [IP-adressen](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PrivateIPAddressNotInSubnet | Het opgegeven IP-adres bevindt zich buiten het subnetbereik. Wijzig IP-adres om binnen het subnetbereik te vallen. | [IP-adressen](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PropertyChangeNotA toegestaan | Sommige eigenschappen kunnen niet worden gewijzigd op een geïmplementeerde resource. Wanneer u een resource bijwerkt, beperkt u uw wijzigingen tot toegestane eigenschappen. | [Bron bijwerken](/azure/architecture/building-blocks/extending-templates/update-resource) |
| RequestDisallowedByPolicy | Uw abonnement bevat een resourcebeleid dat voorkomt dat een actie wordt uitgevoerd tijdens de implementatie. Zoek het beleid dat de actie blokkeert. Wijzig indien mogelijk uw implementatie om te voldoen aan de beperkingen van het beleid. | [Beleid oplossen](error-policy-requestdisallowedbypolicy.md) |
| ReservedResourceName | Geef een resourcenaam op die geen gereserveerde naam bevat. | [Gereserveerde resourcenamen](error-reserved-resource-name.md) |
| ResourceGroupWordtverwijderd | Wacht tot de verwijdering is voltooid. | |
| ResourceGroupNotFound | Controleer de naam van de doelgroep voor de implementatie. De doelgroep moet al in uw abonnement aanwezig zijn. Controleer de context van uw abonnement. | [Azure CLI](/cli/azure/account?#az-account-set) [PowerShell](/powershell/module/Az.Accounts/Set-AzContext) |
| ResourceNotFound | Uw implementatie verwijst naar een resource die niet kan worden opgelost. Controleer of uw gebruik van de **referentiefunctie** de parameters bevat die nodig zijn voor uw scenario. | [Verwijzingen oplossen](error-not-found.md) |
| ResourceQuotaExceeded | De implementatie probeert resources te maken die het quotum voor het abonnement, de resourcegroep of de regio overschrijden. Indien mogelijk, herzie uw infrastructuur om binnen de quota te blijven. Anders u overwegen een wijziging van uw quota aan te vragen. | [Quota oplossen](error-resource-quota.md) |
| SkuNotAvailable | Selecteer SKU (zoals VM-grootte) die beschikbaar is voor de locatie die u hebt geselecteerd. | [SKU oplossen](error-sku-not-available.md) |
| StorageAccountBestaat al | Geef een unieke naam op voor het opslagaccount. | [Naam van het opslagaccount oplossen](error-storage-account-name.md)  |
| StorageAccountAlTaken | Geef een unieke naam op voor het opslagaccount. | [Naam van het opslagaccount oplossen](error-storage-account-name.md) |
| StorageAccountNotFound | Controleer het abonnement, de brongroep en de naam van het opslagaccount dat u probeert te gebruiken. | |
| SubnettenNotInSameVnet | Een virtuele machine kan slechts één virtueel netwerk hebben. Controleer bij het implementeren van meerdere NIC's of ze tot hetzelfde virtuele netwerk behoren. | [Meerdere NIC's](../../virtual-machines/windows/multiple-nics.md) |
| Abonnement niet geregistreerd | Bij het implementeren van netwerkbronnen wordt de Microsoft.Network-bronprovider automatisch geregistreerd in het abonnement. Soms is de automatische registratie niet op tijd voltooid. Als u deze intermitterende fout wilt voorkomen, registreert u de Microsoft.Network-bronprovider vóór de implementatie. | [Registratie oplossen](error-register-resource-provider.md) |
| TemplateResourceCircularDependency | Verwijder onnodige afhankelijkheden. | [Kringafhankelijkheden oplossen](error-invalid-template.md#circular-dependency) |
| TooManyTargetResourceGroepen | Verminder het aantal resourcegroepen voor één implementatie. | [Implementatie in meerdere resourcegroepen](cross-resource-group-deployment.md) |

## <a name="find-error-code"></a>Foutcode zoeken

U kunt twee typen foutmeldingen krijgen:

* validatiefouten
* implementatiefouten

Validatiefouten ontstaan door scenario's en kunnen vóór de implementatie worden vastgesteld. Ze bevatten syntaxisfouten in de sjabloon of proberen resources te implementeren waarmee uw abonnementquota worden overschreden. Implementatiefouten ontstaan als gevolg van voorwaarden die tijdens het implementatieproces optreden. Bijvoorbeeld wanneer deze toegang proberen te krijgen tot een resource die parallel wordt geïmplementeerd.

Beide typen fouten retourneren een foutcode die u gebruikt om de problemen met de implementatie op te lossen. Beide typen fouten worden weergegeven in het [activiteitenlogboek](../management/view-activity-logs.md). Validatiefouten worden echter niet weergegeven in de implementatiegeschiedenis omdat de implementatie niet is gestart.

### <a name="validation-errors"></a>Validatiefouten

Wanneer u een implementatie uitvoert via de portal, ziet u een validatiefout na het indienen van uw waarden.

![portalvalidatiefout weergeven](./media/common-deployment-errors/validation-error.png)

Selecteer het bericht voor meer informatie. In de volgende afbeelding ziet u een **fout ongeldige templatedeployment** en een bericht dat een beleidsgeblokkeerde implementatie aangeeft.

![validatiegegevens weergeven](./media/common-deployment-errors/validation-details.png)

### <a name="deployment-errors"></a>Implementatiefouten

Als de bewerking is gevalideerd, maar mislukt tijdens de implementatie, treedt er een implementatiefout op.

Om foutcodes en meldingen van implementatiefouten weer te geven in PowerShell, gebruikt u:

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -DeploymentName exampledeployment -ResourceGroupName examplegroup).Properties.statusMessage
```

Om foutcodes en meldingen van implementatiefouten weer te geven in de Azure-CLI, gebruikt u:

```azurecli-interactive
az deployment group operation list --name exampledeployment -g examplegroup --query "[*].properties.statusMessage"
```

In de portal selecteert u de melding.

![meldingsfout](./media/common-deployment-errors/notification.png)

U ziet meer details over de implementatie. Selecteer de optie voor meer informatie over de fout.

![implementatie is mislukt](./media/common-deployment-errors/deployment-failed.png)

Het foutbericht en de foutcodes worden dan weergegeven. U ziet twee foutcodes. De eerste foutcode (**DeploymentFailed**) is een algemene fout die geen informatie biedt die u nodig hebt om het probleem op te lossen. De tweede foutcode (**StorageAccountNotFound**) bevat de details die u nodig hebt.

![foutgegevens](./media/common-deployment-errors/error-details.png)

## <a name="enable-debug-logging"></a>Foutopsporingslogboekregistratie inschakelen

Soms hebt u meer informatie nodig over het verzoek en het antwoord om te weten te komen wat er is misgegaan. Tijdens de implementatie u vragen dat aanvullende informatie wordt geregistreerd tijdens een implementatie.

### <a name="powershell"></a>PowerShell

Stel in PowerShell de parameter **DeploymentDebugLogLevel** in op Alles, ResponseContent of RequestContent.

```powershell
New-AzResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName examplegroup `
  -TemplateFile c:\Azure\Templates\storage.json `
  -DeploymentDebugLogLevel All
```

Bestudeer de inhoud van het verzoek met de volgende cmdlet:

```powershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.request `
| ConvertTo-Json
```

Of de antwoordinhoud met:

```powershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.response `
| ConvertTo-Json
```

Met deze informatie u bepalen of een waarde in de sjabloon onjuist wordt ingesteld.

### <a name="azure-cli"></a>Azure CLI

Op dit moment biedt Azure CLI geen ondersteuning voor het inschakelen van foutopsporingslogboekregistratie, maar u defoutfoutinglogboeken ophalen.

Bestudeer de implementatiebewerkingen met de volgende opdracht:

```azurecli
az deployment group operation list \
  --resource-group examplegroup \
  --name exampledeployment
```

Controleer de inhoud van het verzoek met de volgende opdracht:

```azurecli
az deployment group operation list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.request
```

Bestudeer de antwoordinhoud met de volgende opdracht:

```azurecli
az deployment group operation list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.response
```

### <a name="nested-template"></a>Geneste sjabloon

Als u foutopsporingsgegevens voor een geneste sjabloon wilt registreren, gebruikt u het **foutopsporingselement.**

```json
{
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2016-09-01",
  "name": "nestedTemplate",
  "properties": {
    "mode": "Incremental",
    "templateLink": {
      "uri": "{template-uri}",
      "contentVersion": "1.0.0.0"
    },
    "debugSetting": {
       "detailLevel": "requestContent, responseContent"
    }
  }
}
```

## <a name="create-a-troubleshooting-template"></a>Een sjabloon voor probleemoplossing maken

In sommige gevallen is de eenvoudigste manier om problemen met uw sjabloon op te lossen het testen van delen ervan. U een vereenvoudigde sjabloon maken waarmee u zich concentreren op het onderdeel waarvan u denkt dat deze de fout veroorzaakt. Stel dat u een fout ontvangt wanneer u naar een resource verwijst. Maak in plaats van een hele sjabloon te maken, een sjabloon die het onderdeel retourneert dat het probleem kan veroorzaken. Het kan u helpen bepalen of u de juiste parameters doorgeeft, sjabloonfuncties correct gebruikt en de bron krijgt die u verwacht.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  "storageName": {
    "type": "string"
  },
  "storageResourceGroup": {
    "type": "string"
  }
  },
  "variables": {},
  "resources": [],
  "outputs": {
  "exampleOutput": {
    "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageName')), '2016-05-01')]",
    "type" : "object"
  }
  }
}
```

Of stel dat u implementatiefouten ophaalt waarvan u denkt dat ze gerelateerd zijn aan verkeerd ingestelde afhankelijkheden. Test uw sjabloon door deze op te splitsen in vereenvoudigde sjablonen. Maak eerst een sjabloon die slechts één resource implementeert (zoals een SQL Server). Als u zeker weet dat de bron correct is gedefinieerd, voegt u een bron toe die hiervan afhankelijk is (zoals een SQL-database). Als u deze twee resources correct hebt gedefinieerd, voegt u andere afhankelijke resources toe (zoals controlebeleid). Verwijder tussen elke testimplementatie de brongroep om ervoor te zorgen dat u de afhankelijkheden adequaat test.

## <a name="next-steps"></a>Volgende stappen

* Zie [Zelfstudie: Resourcemanager-sjabloonimplementaties](template-tutorial-troubleshoot.md) oplossen
* Zie [Controlebewerkingen met Resource Manager](../management/view-activity-logs.md)voor meer informatie over controleacties.
* Zie [Implementatiebewerkingen weergeven](deployment-history.md)voor meer informatie over acties om de fouten tijdens de implementatie te bepalen.
