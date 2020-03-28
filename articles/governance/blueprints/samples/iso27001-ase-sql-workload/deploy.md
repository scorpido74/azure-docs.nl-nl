---
title: Iso 27001 ASE/SQL-workloadblauwvoorbeeld implementeren
description: Stappen implementeren van het blauwdrukvoorbeeld van de besturingselement -besturingselement voor de besturingselement ISO 27001 App Service Environment/SQL Database met details over parameters voor artefacten.
ms.date: 01/13/2020
ms.topic: sample
ms.openlocfilehash: 6b8f3b753f1dd8cfbc247a77f2004e3c4d3423bb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75922581"
---
# <a name="deploy-the-iso-27001-app-service-environmentsql-database-workload-blueprint-sample"></a>Het blauwdrukvoorbeeld van de ISO 27001 App Service Environment/SQL Database-workload implementeren

Als u het blueprintsample azure blueprints ISO 27001 App Service Environment/SQL Database-workload wilt implementeren, moeten de volgende stappen worden genomen:

> [!div class="checklist"]
> - Het blauwdrukvoorbeeld [van ISO 27001 Shared Services](../iso27001-shared/index.md) implementeren
> - Een nieuwe blauwdruk maken uit het voorbeeld
> - Uw exemplaar van het voorbeeld markeren als **Gepubliceerd**
> - Uw kopie van de blauwdruk toewijzen aan een bestaand abonnement

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free) voordat u begint.

## <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Het blauwdrukvoorbeeld van ISO 27001 Shared Services implementeren

Voordat dit blueprintvoorbeeld kan worden geïmplementeerd, moet het blauwdrukvoorbeeld [van ISO 27001 Shared Services](../iso27001-shared/index.md) worden geïmplementeerd in het doelabonnement. Zonder een succesvolle implementatie van het blauwdrukvoorbeeld van ISO 27001 Shared Services, mist dit blueprint-voorbeeld afhankelijkheden van de infrastructuur en mislukt deze tijdens de implementatie.

> [!IMPORTANT]
> Dit blauwdrukvoorbeeld moet worden toegewezen in hetzelfde abonnement als het blauwdrukvoorbeeld [van ISO 27001 Shared Services.](../iso27001-shared/index.md)

## <a name="create-blueprint-from-sample"></a>Blauwdruk maken uit voorbeeld

Implementeer eerst het blauwdrukvoorbeeld door een nieuwe blauwdruk in uw omgeving te maken met behulp van het voorbeeld als starter.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer op de pagina **Aan de** slag aan de linkerkant de knop **Maken** onder Een _blauwdruk maken_.

1. Zoek het **blueprintsample VOOR ISO 27001: ASE/SQL-workload** onder _Andere voorbeelden_ en selecteer **Dit voorbeeld gebruiken**.

1. Voer de _basisbeginselen_ van het blauwdrukvoorbeeld in:

   - **Blauwdruknaam:** geef een naam op voor uw kopie van het ASE/SQL-blauwdrukvoorbeeld van ISO 27001.
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
     - **Gebruikersnaam voor een abonnement voor gedeelde**service : abonnements-id waarbij het blauwdrukvoorbeeld van [ISO 27001 Shared Services](../iso27001-shared/index.md) is toegewezen.
     - **Standaardsubnetadresvoorvoegsel:** de CIDR-notatie voor het standaardsubnet van het virtuele netwerk.
       De standaardwaarde is _10.1.0.0/16_.
     - **Werkbelastinglocatie:** hiermee bepaalt u naar welke locatie de artefacten worden geïmplementeerd. Niet alle services zijn beschikbaar op alle locaties. Artefacten die dergelijke services implementeren, bieden een parameteroptie voor de locatie om dat artefact te implementeren.

   - Artefact-parameters

     De parameters die in deze sectie zijn gedefinieerd, zijn van toepassing op het artefact waaronder het is gedefinieerd. Deze parameters zijn [dynamische parameters](../../concepts/parameters.md#dynamic-parameters) omdat ze worden gedefinieerd tijdens de toewijzing van de blauwdruk. Zie [Artefact-parameterstabel](#artifact-parameters-table)voor een volledige lijst of artefactparameters en hun beschrijvingen.

1. Zodra alle parameters zijn ingevoerd, selecteert **u Toewijzen** onder aan de pagina. De blauwdruktoewijzing wordt gemaakt en de implementatie van artefacten begint. Implementatie duurt ongeveer een uur. Als u de status van implementatie wilt controleren, opent u de blauwdruktoewijzing.

> [!WARNING]
> De Azure Blueprints-service en de ingebouwde blauwdrukvoorbeelden zijn **gratis.** Azure-bronnen worden [geprijsd per product.](https://azure.microsoft.com/pricing/) Gebruik de [prijscalculator](https://azure.microsoft.com/pricing/calculator/) om de kosten van het uitvoeren van resources die door dit blauwdrukvoorbeeld worden geïmplementeerd, te schatten.

## <a name="artifact-parameters-table"></a>Tabel Artefactparameters

In de volgende tabel vindt u een lijst met de parameters van het blauwdrukartefact:

|Artefact-naam|Artefacttype|Parameternaam|Beschrijving|
|-|-|-|-|
|Logboekanalysebrongroep|Resourcegroep|Name|**Vergrendeld** - Hiermee wordt de naam `-workload-log-rg` van de **organisatie** gekoppeld om de resourcegroep uniek te maken.|
|Logboekanalysebrongroep|Resourcegroep|Locatie|**Vergrendeld** - Gebruikt de parameter blueprint.|
|Sjabloon Logboekanalyse|Resource Manager-sjabloon|Servicelaag|Hiermee stelt u de laag van de werkruimte Log Analytics in. Standaardwaarde is _Pernode_.|
|Sjabloon Logboekanalyse|Resource Manager-sjabloon|Logboekbehoud in dagen|Gegevensbewaring in dagen. Standaardwaarde is _365_.|
|Sjabloon Logboekanalyse|Resource Manager-sjabloon|Locatie|Regio die wordt gebruikt voor het maken van de werkruimte Log Analytics. Standaardwaarde is _West US 2_.|
|Netwerkbrongroep|Resourcegroep|Name|**Vergrendeld** - Hiermee wordt de naam `-workload-net-rg` van de **organisatie** gekoppeld om de resourcegroep uniek te maken.|
|Netwerkbrongroep|Resourcegroep|Locatie|**Vergrendeld** - Gebruikt de parameter blueprint.|
|Sjabloon netwerkbeveiligingsgroep|Resource Manager-sjabloon|Logboekbehoud in dagen|Gegevensbewaring in dagen. Standaardwaarde is _365_.|
|Sjabloon virtuele netwerk- en routetabel|Resource Manager-sjabloon|Privé-IP voor Azure-firewall|Hiermee configureert u het privé-IP van de [Azure-firewall](../../../../firewall/overview.md). Moet deel uitmaken van het CIDR-notatie dat is gedefinieerd in _ISO 27001:_ **Subnet-voorvoegsel azure firewall-artefactvoorvoegsel**van gedeelde services. De standaardwaarde is _10.0.4.4_.|
|Sjabloon virtuele netwerk- en routetabel|Resource Manager-sjabloon|Abonnements-id voor gedeelde services|Waarde die wordt gebruikt om VNET-peering tussen een workload en gedeelde services mogelijk te maken.|
|Sjabloon virtuele netwerk- en routetabel|Resource Manager-sjabloon|Voorvoegsel voor virtueel netwerkadres|De CIDR-notatie voor het virtuele netwerk. De standaardwaarde is _10.1.0.0/16_.|
|Sjabloon virtuele netwerk- en routetabel|Resource Manager-sjabloon|Standaardsubnetadresvoorvoegsel|De CIDR-notatie voor het standaardsubnet van het virtuele netwerk. De standaardwaarde is _10.1.0.0/16_.|
|Sjabloon virtuele netwerk- en routetabel|Resource Manager-sjabloon|VOEGT IP-adres toe|IP-adres van de eerste ADDS VM. Deze waarde wordt gebruikt als aangepaste VNET DNS.|
|Key Vault-brongroep|Resourcegroep|Name|**Vergrendeld** - Hiermee wordt de naam `-workload-kv-rg` van de **organisatie** gekoppeld om de resourcegroep uniek te maken.|
|Key Vault-brongroep|Resourcegroep|Locatie|**Vergrendeld** - Gebruikt de parameter blueprint.|
|Sjabloon Sleutelkluis|Resource Manager-sjabloon|AAD-object-id|De AAD-object-id van het account waarvoor toegang tot de instantie Key Vault vereist is. Geen standaardwaarde en kan niet leeg worden gelaten. Als u deze waarde wilt vinden vanuit de Azure-portal, zoekt en selecteert u 'Gebruikers' onder _Services._ Gebruik het vak _Naam_ om te filteren op de accountnaam en selecteer dat account. Selecteer op de pagina _Gebruikersprofiel_ het pictogram 'Klik om te kopiëren' naast de _object-id._|
|Sjabloon Sleutelkluis|Resource Manager-sjabloon|Logboekbehoud in dagen|Gegevensbewaring in dagen. Standaardwaarde is _365_.|
|Sjabloon Sleutelkluis|Resource Manager-sjabloon|Key Vault SKU|Hiermee geeft u de SKU op van de sleutelkluis die is gemaakt. Standaardwaarde is _Premium_.|
|Sjabloon Sleutelkluis|Resource Manager-sjabloon|Gebruikersnaam Azure SQL Server-beheerder|De gebruikersnaam die wordt gebruikt om toegang te krijgen tot Azure SQL Server. Moet overeenkomen met dezelfde eigenschapswaarde in **azure SQL Database-sjabloon**. Standaardwaarde is _sql-admin-user._|
|Azure SQL Database-brongroep|Resourcegroep|Name|**Vergrendeld** - Hiermee wordt de naam `-workload-azsql-rg` van de **organisatie** gekoppeld om de resourcegroep uniek te maken.|
|Azure SQL Database-brongroep|Resourcegroep|Locatie|**Vergrendeld** - Gebruikt de parameter blueprint.|
|Azure SQL-databasesjabloon|Resource Manager-sjabloon|Gebruikersnaam Azure SQL Server-beheerder|Gebruikersnaam voor de Azure SQL Server. Moet overeenkomen met dezelfde eigenschapswaarde in **de sjabloon Key Vault**. Standaardwaarde is _sql-admin-user._|
|Azure SQL-databasesjabloon|Resource Manager-sjabloon|Azure SQL Server-beheerderswachtwoord (Key Vault Resource ID)|De resource-id van de sleutelkluis. Gebruik "/subscription/{subscriptionId}/resourceGroups/{orgName}-workload-kv/providers/Microsoft.KeyVault/vaults/{orgName}-workload-kv" `{subscriptionId}` en vervang uw `{orgName}` abonnements-id en de parameter **Organiblauwatie.**|
|Azure SQL-databasesjabloon|Resource Manager-sjabloon|Azure SQL Server-beheerderswachtwoord (Sleutelkluis geheime naam)|Gebruikersnaam van de SQL Server-beheerder. Moet overeenkomen met de waarde in de **naamname Azure SQL Server-beheerder van** **key Vault-sjabloon.**|
|Azure SQL-databasesjabloon|Resource Manager-sjabloon|Logboekbehoud in dagen|Gegevensbewaring in dagen. Standaardwaarde is _365_.|
|Azure SQL-databasesjabloon|Resource Manager-sjabloon|AAD-beheerobject-id|AAD-object-id van de gebruiker die wordt toegewezen als Active Directory-beheerder. Geen standaardwaarde en kan niet leeg worden gelaten. Als u deze waarde wilt vinden vanuit de Azure-portal, zoekt en selecteert u 'Gebruikers' onder _Services._ Gebruik het vak _Naam_ om te filteren op de accountnaam en selecteer dat account. Selecteer op de pagina _Gebruikersprofiel_ het pictogram 'Klik om te kopiëren' naast de _object-id._|
|Azure SQL-databasesjabloon|Resource Manager-sjabloon|Aad-beheerder inloggen|Momenteel kunnen Microsoft-accounts (zoals live.com of outlook.com) niet worden ingesteld als beheerder. Alleen gebruikers en beveiligingsgroepen binnen uw organisatie kunnen als beheerder worden ingesteld. Geen standaardwaarde en kan niet leeg worden gelaten. Als u deze waarde wilt vinden vanuit de Azure-portal, zoekt en selecteert u 'Gebruikers' onder _Services._ Gebruik het vak _Naam_ om te filteren op de accountnaam en selecteer dat account. Kopieer op de pagina _Gebruikersprofiel_ de _gebruikersnaam_.|
|Resourcegroep App-serviceomgeving|Resourcegroep|Name|**Vergrendeld** - Hiermee wordt de naam `-workload-ase-rg` van de **organisatie** gekoppeld om de resourcegroep uniek te maken.|
|Resourcegroep App-serviceomgeving|Resourcegroep|Locatie|**Vergrendeld** - Gebruikt de parameter blueprint.|
|Sjabloon App-serviceomgeving|Resource Manager-sjabloon|Domeinnaam|Naam van de Active Directory die door het voorbeeld is gemaakt. De standaardwaarde is _contoso.com_.|
|Sjabloon App-serviceomgeving|Resource Manager-sjabloon|ASE-locatie|Locatie van de app-serviceomgeving. Standaardwaarde is _West US 2_.|
|Sjabloon App-serviceomgeving|Resource Manager-sjabloon|Behoud van logboeken voor toepassingen in dagen|Gegevensbewaring in dagen. Standaardwaarde is _365_.|

## <a name="next-steps"></a>Volgende stappen

Nu u de stappen hebt bekeken om het blauwdrukvoorbeeld van de ISO 27001 App Service Environment/SQL Database-workload te implementeren, gaat u naar de volgende artikelen voor meer informatie over de architectuur en beheertoewijzing:

> [!div class="nextstepaction"]
> [BLAUWDRUK VAN ISO 27001 App Service Environment/SQL Database workload - Overzicht](./index.md)
> [ISO 27001 App Service Environment/SQL Database workload blueprint - Control mapping](./control-mapping.md)

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van de blauwdruk](../../concepts/lifecycle.md).
- Begrijpen hoe [statische en dynamische parameters](../../concepts/parameters.md)te gebruiken.
- Leer de volgorde van de [blauwdrukvolgorde](../../concepts/sequencing-order.md)aan te passen.
- Ontdek hoe u gebruik maken van het vergrendelen van [blauwdrukbronnen.](../../concepts/resource-locking.md)
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).