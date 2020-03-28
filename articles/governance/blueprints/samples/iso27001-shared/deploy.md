---
title: Blueprint sample voor iso 27001 Shared Services implementeren
description: Stappen implementeren voor het blauwdrukvoorbeeld van ISO 27001 Shared Services, inclusief details van de parameterparameters voor blauwdrukken.
ms.date: 01/13/2020
ms.topic: sample
ms.openlocfilehash: a9dabc99469321445006e449757a10fbc51aba87
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75920682"
---
# <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Het blauwdrukvoorbeeld van ISO 27001 Shared Services implementeren

Als u het blueprintsample azure blueprints ISO 27001 Shared Services wilt implementeren, moeten de volgende stappen worden genomen:

> [!div class="checklist"]
> - Een nieuwe blauwdruk maken uit het voorbeeld
> - Uw exemplaar van het voorbeeld markeren als **Gepubliceerd**
> - Uw kopie van de blauwdruk toewijzen aan een bestaand abonnement

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free) voordat u begint.

## <a name="create-blueprint-from-sample"></a>Blauwdruk maken uit voorbeeld

Implementeer eerst het blauwdrukvoorbeeld door een nieuwe blauwdruk in uw omgeving te maken met behulp van het voorbeeld als starter.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer op de pagina **Aan de** slag aan de linkerkant de knop **Maken** onder Een _blauwdruk maken_.

1. Zoek het blauwdrukvoorbeeld **van ISO 27001: Shared Services** onder Andere _voorbeelden_ en selecteer Dit **voorbeeld gebruiken**.

1. Voer de _basisbeginselen_ van het blauwdrukvoorbeeld in:

   - **Blauwdruknaam:** geef een naam op voor uw exemplaar van het blauwdrukvoorbeeld van ISO 27001 Shared Services.
   - **Definitielocatie:** gebruik de ellips en selecteer de beheergroep om uw exemplaar van het voorbeeld op te slaan.

1. Selecteer het tabblad _Artefacten_ boven aan de pagina of **Volgende: Artefacten** onder aan de pagina.

1. Bekijk de lijst met artefacten die deel uitmaken van het blauwdrukvoorbeeld. Veel artefacten hebben parameters die we later zullen definiëren. Selecteer **Concept opslaan** wanneer u klaar bent met het bekijken van het blauwdrukvoorbeeld.

## <a name="publish-the-sample-copy"></a>De voorbeeldkopie publiceren

Uw kopie van het blauwdrukvoorbeeld is nu gemaakt in uw omgeving. Het is gemaakt in **de conceptmodus** en moet worden **gepubliceerd** voordat het kan worden toegewezen en geïmplementeerd. De kopie van het blauwdrukvoorbeeld kan worden aangepast aan uw omgeving en behoeften, maar die wijziging kan het verplaatsen van de ISO 27001-standaard.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer de pagina **Blauwdrukdefinities** aan de linkerkant. Gebruik de filters om uw exemplaar van het blauwdrukvoorbeeld te vinden en selecteer deze vervolgens.

1. Selecteer **Blauwdruk publiceren** boven aan de pagina. Geef op de nieuwe pagina aan de rechterkant een **versie** voor uw exemplaar van het blauwdrukvoorbeeld. Deze eigenschap is handig voor als u later een wijziging aanbrengt. **Geef wijzigingsnotities** op, zoals 'Eerste versie gepubliceerd uit het blauwdrukvoorbeeld van ISO 27001'. Selecteer vervolgens **Publiceren** onder aan de pagina.

## <a name="assign-the-sample-copy"></a>De voorbeeldkopie toewijzen

Zodra de kopie van het blauwdrukvoorbeeld is **gepubliceerd,** kan deze worden toegewezen aan een abonnement binnen de beheergroep waaraan het is opgeslagen. Deze stap is waar parameters worden verstrekt om elke implementatie van de kopie van het blauwdrukvoorbeeld uniek te maken.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer de pagina **Blauwdrukdefinities** aan de linkerkant. Gebruik de filters om uw exemplaar van het blauwdrukvoorbeeld te vinden en selecteer deze vervolgens.

1. Selecteer **Blauwdruk toewijzen** boven aan de pagina met blauwdrukdefinitie.

1. Geef de parameterwaarden op voor de blauwdruktoewijzing:

   - Basisbeginselen

     - **Abonnementen:** selecteer een of meer abonnementen in de beheergroep waarop u uw exemplaar van het blauwdrukvoorbeeld hebt opgeslagen. Als u meer dan één abonnement selecteert, wordt voor elk van de parameters een toewijzing gemaakt met behulp van de ingevoerde parameters.
     - **Toewijzingsnaam:** de naam is vooraf ingevuld op basis van de naam van de blauwdruk.
       Verander indien nodig of laat zoals het is.
     - **Locatie:** selecteer een gebied waarin de beheerde identiteit moet worden gemaakt. Azure Blueprint gebruikt deze beheerde identiteit om alle artefacten in de toegewezen blauwdruk te implementeren. Zie [beheerde identiteiten voor Azure-resources voor](../../../../active-directory/managed-identities-azure-resources/overview.md)meer informatie.
     - **Versie van de blauwdrukdefinitie**: Kies een **gepubliceerde** versie van uw exemplaar van het blauwdrukvoorbeeld.

   - Vergrendelingstoewijzing

     Selecteer de instelling voor blauwdrukvergrendeling voor uw omgeving. Zie voor meer informatie [Vergrendeling van blauwdrukresources](../../concepts/resource-locking.md).

   - Beheerde identiteit

     Laat de _standaardsysteemtoegewezen beheerde_ identiteitoptie.

   - Blauwdrukparameters

     De parameters die in deze sectie worden gedefinieerd, worden door veel van de artefacten in de blauwdrukdefinitie gebruikt om consistentie te bieden.

     - **Organisatienaam:** Voer een korte naam in voor uw organisatie. Deze eigenschap wordt voornamelijk gebruikt voor het benoemen van resources.
     - **Subnetadresvoorvoegsel voor gedeelde services**: geef de CIDR-notatiewaarde op voor het samen netwerken van de geïmplementeerde resources.
     - **Locatie gedeelde services:** bepaalt naar welke locatie de artefacten worden geïmplementeerd. Niet alle services zijn beschikbaar op alle locaties. Artefacten die dergelijke services implementeren, bieden een parameteroptie voor de locatie om dat artefact te implementeren.
     - **Toegestane locatie (Beleid: Blauwdruk-initiatief voor ISO 27001):** Waarde die de toegestane locaties voor resourcegroepen en resources aangeeft.
     - **Log Analytics-werkruimte voor VM-agents (Beleid: Blueprint-initiatief voor ISO 27001)**: hiermee geeft u de resource-id van een werkruimte op. Deze parameter `concat` gebruikt een functie om de resource-id te construeren.

   - Artefact-parameters

     De parameters die in deze sectie zijn gedefinieerd, zijn van toepassing op het artefact waaronder het is gedefinieerd. Deze parameters zijn [dynamische parameters](../../concepts/parameters.md#dynamic-parameters) omdat ze worden gedefinieerd tijdens de toewijzing van de blauwdruk. Zie [Artefact-parameterstabel](#artifact-parameters-table)voor een volledige lijst of artefactparameters en hun beschrijvingen.

1. Zodra alle parameters zijn ingevoerd, selecteert **u Toewijzen** onder aan de pagina. De blauwdruktoewijzing wordt gemaakt en de implementatie van artefacten begint. Implementatie duurt ongeveer een uur. Als u de status van implementatie wilt controleren, opent u de blauwdruktoewijzing.

> [!WARNING]
> De Azure Blueprints-service en de ingebouwde blauwdrukvoorbeelden zijn **gratis.** Azure-bronnen worden [geprijsd per product.](https://azure.microsoft.com/pricing/) Gebruik de [prijscalculator](https://azure.microsoft.com/pricing/calculator/) om de kosten van het uitvoeren van resources die door dit blauwdrukvoorbeeld worden geïmplementeerd, te schatten.

## <a name="artifact-parameters-table"></a>Tabel Artefactparameters

In de volgende tabel vindt u een lijst met de parameters van het blauwdrukartefact:

|Artefact-naam|Artefacttype|Parameternaam|Beschrijving|
|-|-|-|-|
|\[Preview\]: Log Analytics Agent voor Linux VM-schaalsets (VMSS) implementeren|Beleidstoewijzing|Optioneel: lijst met VM-afbeeldingen die Linux OS hebben ondersteund om aan bereik toe te voegen|(Optioneel) Standaardwaarde is _['geen']_.|
|\[Preview:\]Log Analytics Agent implementeren voor Linux VM's|Beleidstoewijzing|Optioneel: lijst met VM-afbeeldingen die Linux OS hebben ondersteund om aan bereik toe te voegen|(Optioneel) Standaardwaarde is _['geen']_.|
|\[Voorbeeld:\]Logboekanalyseagent voor Windows VM-schaalsets (VMSS) implementeren|Beleidstoewijzing|Optioneel: lijst met VM-afbeeldingen die Windows OS hebben ondersteund om aan bereik toe te voegen|(Optioneel) Standaardwaarde is _['geen']_.|
|\[Voorbeeld:\]Logboekanalyseagent implementeren voor Windows VM's|Beleidstoewijzing|Optioneel: lijst met VM-afbeeldingen die Windows OS hebben ondersteund om aan bereik toe te voegen|(Optioneel) Standaardwaarde is _['geen']_.|
|Toegestane brontypen|Beleidstoewijzing|Toegestane brontypen|Lijst met resourcetypen die mogen worden geïmplementeerd. Deze lijst bestaat uit alle resourcetypen die zijn geïmplementeerd in Gedeelde services.|
|Toegestane opslagaccount-SKU's|Beleidstoewijzing|Toegestane opslag SKU's|Lijst met diagnostische logboeken opslag account SKU's toegestaan. Standaardwaarde is _[Standard_LRS"]_.|
|Toegestane virtuele machine SKU's|Beleidstoewijzing|Lijst met virtuele machine SKU's mogen worden geïmplementeerd. Standaardwaarde is _[Standard_DS1_v2", "Standard_DS2_v2"]_.|
|Blauwdrukinitiatief voor ISO 27001|Beleidstoewijzing|Resourcetypen voor het controleren van diagnostische logboeken|Lijst met resourcetypen om te controleren of diagnostische logboekinstelling niet is ingeschakeld. Acceptabele waarden zijn te vinden op [azure monitor diagnostische logboekenschema's.](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type)|
|Logboekanalysebrongroep|Resourcegroep|Name|**Vergrendeld** - Hiermee wordt de naam `-sharedsvsc-log-rg` van de **organisatie** gekoppeld om de resourcegroep uniek te maken.|
|Logboekanalysebrongroep|Resourcegroep|Locatie|**Vergrendeld** - Gebruikt de parameter blueprint.|
|Sjabloon Logboekanalyse|Resource Manager-sjabloon|Servicelaag|Hiermee stelt u de laag van de werkruimte Log Analytics in. Standaardwaarde is _Pernode_.|
|Sjabloon Logboekanalyse|Resource Manager-sjabloon|Logboekbehoud in dagen|Gegevensbewaring in dagen. Standaardwaarde is _365_.|
|Sjabloon Logboekanalyse|Resource Manager-sjabloon|Locatie|Regio die wordt gebruikt voor het maken van de werkruimte Log Analytics. Standaardwaarde is _West US 2_.|
|Netwerkbrongroep|Resourcegroep|Name|**Vergrendeld** - Hiermee wordt de naam `-sharedsvcs-net-rg` van de **organisatie** gekoppeld om de resourcegroep uniek te maken.|
|Netwerkbrongroep|Resourcegroep|Locatie|**Vergrendeld** - Gebruikt de parameter blueprint.|
|Azure Firewall-sjabloon|Resource Manager-sjabloon|Privé-IP voor Azure-firewall|Hiermee configureert u het privé-IP van de [Azure-firewall](../../../../firewall/overview.md). Deze waarde wordt ook gebruikt als standaardroutetabel op subnet gedeelde services. Moet deel uitmaken van de CIDR-notatie die is gedefinieerd in **het subnet-adresvoorvoegsel azure firewall**. De standaardwaarde is _10.0.4.4_.|
|Azure Firewall-sjabloon|Resource Manager-sjabloon|Logboekbehoud in dagen|Gegevensbewaring in dagen. Standaardwaarde is _365_.|
|Sjabloon netwerkbeveiligingsgroep|Resource Manager-sjabloon|Logboekbehoud in dagen|Gegevensbewaring in dagen. Standaardwaarde is _365_.|
|Sjabloon virtuele netwerk- en routetabel|Resource Manager-sjabloon|Voorvoegsel voor virtueel netwerkadres|De CIDR-notatie voor het virtuele netwerk. De standaardwaarde is _10.0.0.0/16_.|
|Sjabloon virtuele netwerk- en routetabel|Resource Manager-sjabloon|DDoS-beveiliging voor virtuele netwerken inschakelen|Configureert DDoS-beveiliging voor het virtuele netwerk. Standaardwaarde is _waar_.|
|Sjabloon virtuele netwerk- en routetabel|Resource Manager-sjabloon|Subnetadresvoorvoegsing van gedeelde services|De CIDR-notatie voor het subnet Gedeelde Services. De standaardwaarde is _10.0.0.0/24_.|
|Sjabloon virtuele netwerk- en routetabel|Resource Manager-sjabloon|DMZ subnetadresvoorvoegs|De CIDR-notatie voor het DMZ-subnet. De standaardwaarde is _10.0.1.0/24_.|
|Sjabloon virtuele netwerk- en routetabel|Resource Manager-sjabloon|Subnet-voorvoegsel van toepassingsgateway|De CIDR-notatie voor het subnet van de toepassingsgateway. De standaardwaarde is _10.0.2.0/24_.|
|Sjabloon virtuele netwerk- en routetabel|Resource Manager-sjabloon|Subnetadresvoorvoegsvan virtual network gateway|De CIDR-notatie voor het subnet van de virtuele netwerkgateway. De standaardwaarde is _10.0.3.0/24_.|
|Sjabloon virtuele netwerk- en routetabel|Resource Manager-sjabloon|Voorvoegsel van Azure Firewall-subnetadres|De CIDR-notatie voor het [subnet Azure-firewall.](../../../../firewall/overview.md) Moet de **private IP-parameter voor azure-firewall** bevatten.|
|Key Vault-brongroep|Resourcegroep|Name|**Vergrendeld** - Hiermee wordt de naam `-sharedsvcs-kv-rg` van de **organisatie** gekoppeld om de resourcegroep uniek te maken.|
|Key Vault-brongroep|Resourcegroep|Locatie|**Vergrendeld** - Gebruikt de parameter blueprint.|
|Sjabloon Sleutelkluis|Resource Manager-sjabloon|Gebruikersnaam Jumpbox-beheerder|Gebruikersnaam voor de jumpbox. Moet overeenkomen met dezelfde eigenschapswaarde in **de sjabloon Jumpbox**. Standaardwaarde is _jb-admin-user_.|
|Sjabloon Sleutelkluis|Resource Manager-sjabloon|Jumpbox admin ssh sleutel of wachtwoord|Sleutel of wachtwoord voor het account op de jumpbox. Moet overeenkomen met dezelfde eigenschapswaarde in **de sjabloon Jumpbox**. Geen standaardwaarde en kan niet leeg worden gelaten.|
|Sjabloon Sleutelkluis|Resource Manager-sjabloon|Gebruikersnaam domeinbeheerder|Gebruikersnaam wordt gebruikt om toegang te krijgen tot Active Directory VM en om andere VM's aan een domein te koppelen. Moet overeenkomen met de eigenschappenwaarde van **de domeinbeheerder** in **de sjabloon Active Directory Domain Services**. Standaardwaarde is _domein-admin-gebruiker_.|
|Sjabloon Sleutelkluis|Resource Manager-sjabloon|Wachtwoord voor domeinbeheerder|Het wachtwoord van de domeinbeheerder. Geen standaardwaarde en kan niet leeg worden gelaten.|
|Sjabloon Sleutelkluis|Resource Manager-sjabloon|AAD-object-id|De AAD-object-id van het account waarvoor toegang tot de instantie Key Vault vereist is. Geen standaardwaarde en kan niet leeg worden gelaten. Als u deze waarde wilt vinden vanuit de Azure-portal, zoekt en selecteert u 'Gebruikers' onder _Services._ Gebruik het vak _Naam_ om te filteren op de accountnaam en selecteer dat account. Selecteer op de pagina _Gebruikersprofiel_ het pictogram 'Klik om te kopiëren' naast de _object-id._  |
|Sjabloon Sleutelkluis|Resource Manager-sjabloon|Logboekbehoud in dagen|Gegevensbewaring in dagen. Standaardwaarde is _365_.|
|Sjabloon Sleutelkluis|Resource Manager-sjabloon|Key Vault SKU|Hiermee geeft u de SKU op van de sleutelkluis die is gemaakt. Standaardwaarde is _Premium_.|
|Jumpbox-resourcegroep|Resourcegroep|Name|**Vergrendeld** - Hiermee wordt de naam `-sharedsvcs-jb-rg` van de **organisatie** gekoppeld om de resourcegroep uniek te maken.|
|Jumpbox-resourcegroep|Resourcegroep|Locatie|**Vergrendeld** - Gebruikt de parameter blueprint.|
|Sjabloon Jumpbox|Resource Manager-sjabloon|Gebruikersnaam Jumpbox-beheerder|De gebruikersnaam die wordt gebruikt om toegang te krijgen tot jumpbox VM's. Moet overeenkomen met dezelfde eigenschapswaarde in **de sjabloon Key Vault**. Standaardwaarde is _jb-admin-user_.|
|Sjabloon Jumpbox|Resource Manager-sjabloon|Jumpbox-beheerderswachtwoord (Key Vault Resource ID)|De resource-id van de sleutelkluis. Gebruik "/abonnementen/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv" `{subscriptionId}` en vervang uw `{orgName}` abonnements-id en de blauwdruk van de **parameter Organisatie.**|
|Sjabloon Jumpbox|Resource Manager-sjabloon|Jumpbox admin wachtwoord (Key Vault Secret Name)|Gebruikersnaam van de jumpbox admin. Moet overeenkomen met de waarde in **Key Vault template** eigenschap **Jumpbox admin gebruikersnaam**.|
|Sjabloon Jumpbox|Resource Manager-sjabloon|Jumpbox Besturingssysteem|Hiermee bepaalt u het besturingssysteem van de jumpbox-vm. Standaardwaarde is _Windows_.|
|Active Directory Domain Services-brongroep|Resourcegroep|Name|**Vergrendeld** - Hiermee wordt de naam `-sharedsvcs-adds-rg` van de **organisatie** gekoppeld om de resourcegroep uniek te maken.|
|Active Directory Domain Services-brongroep|Resourcegroep|Locatie|**Vergrendeld** - Gebruikt de parameter blueprint.|
|Sjabloon Active Directory Domain Services|Resource Manager-sjabloon|Gebruikersnaam domeinbeheerder|Gebruikersnaam voor de ADDS jumpbox. Moet overeenkomen met dezelfde eigenschapswaarde in **de sjabloon Key Vault**. Standaardwaarde is _adds-admin-user_.|
|Sjabloon Active Directory Domain Services|Resource Manager-sjabloon|Wachtwoord voor domeinbeheerder (Key Vault Resource ID)|De resource-id van de sleutelkluis. Gebruik "/abonnementen/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv" `{subscriptionId}` en vervang uw `{orgName}` abonnements-id en de blauwdruk van de **parameter Organisatie.**|
|Sjabloon Active Directory Domain Services|Resource Manager-sjabloon|Wachtwoord voor domeinbeheerder (sleutelkluisgeheime naam)|Gebruikersnaam van de domeinbeheerder. Moet overeenkomen met de waarde in **key vault template** eigenschap Domain admin **gebruikersnaam**.|
|Sjabloon Active Directory Domain Services|Resource Manager-sjabloon|Domeinnaam|Naam van de Active Directory die door het voorbeeld is gemaakt. De standaardwaarde is _contoso.com_.|
|Sjabloon Active Directory Domain Services|Resource Manager-sjabloon|Domeinbeheerdergebruiker|Gebruikersnaam voor het AD-account van de beheerder en voor het samenvoegen van apparaten in het AD-domein. Moet overeenkomen met de waarde van **de gebruikersnaam van ad-beheerders** in **de sjabloon Key Vault**. Standaardwaarde is _domein-admin-gebruiker_.|
|Sjabloon Active Directory Domain Services|Resource Manager-sjabloon|Wachtwoord voor domeinbeheerder|Stel de Key Vault-gegevens in voor het opslaan van het wachtwoord. Geen standaardwaarde en kan niet leeg worden gelaten.|

## <a name="next-steps"></a>Volgende stappen

Nu u de stappen hebt bekeken om het blauwdrukvoorbeeld van ISO 27001 Shared Services te implementeren, gaat u naar de volgende artikelen voor meer informatie over de architectuur- en besturingstoewijzing:

> [!div class="nextstepaction"]
> [BLAUWDRUK VAN ISO 27001 Shared Services - Blauwdruk overzicht](./index.md)
> [ISO 27001 Shared Services - Beheertoewijzing](./control-mapping.md)

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van de blauwdruk](../../concepts/lifecycle.md).
- Begrijpen hoe [statische en dynamische parameters](../../concepts/parameters.md)te gebruiken.
- Leer de volgorde van de [blauwdrukvolgorde](../../concepts/sequencing-order.md)aan te passen.
- Ontdek hoe u gebruik maken van het vergrendelen van [blauwdrukbronnen.](../../concepts/resource-locking.md)
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).
