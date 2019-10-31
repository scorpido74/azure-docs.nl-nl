---
title: Voor beeld van ISO 27001 ASE/SQL workload blauw drukken-stappen implementeren
description: Implementeer stappen van het voor beeld van de ISO 27001 App Service Environment/SQL Database werk belasting blauw druk, inclusief de para meters voor de artefact parameter.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: sample
ms.service: blueprints
ms.openlocfilehash: 4836287f5308ffb11bf145b715dfd6f73672c038
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73163456"
---
# <a name="deploy-the-iso-27001-app-service-environmentsql-database-workload-blueprint-sample"></a>Het voor beeld van de ISO 27001 App Service Environment/SQL Database werk belasting blauw druk implementeren

De volgende stappen moeten worden uitgevoerd om de Azure-blauw drukken ISO 27001 App Service Environment/SQL Database werk belasting blauw druk-voor beeld te implementeren:

> [!div class="checklist"]
> - Het voor beeld van de [ISO 27001-gedeelde services](../iso27001-shared/index.md) gebruiken
> - Een nieuwe blauw druk maken op basis van het voor beeld
> - Uw kopie van het voor beeld markeren als **gepubliceerd**
> - Uw kopie van de blauw druk toewijzen aan een bestaand abonnement

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free) aan voordat u begint.

## <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Het voor beeld van de ISO 27001-gedeelde services gebruiken

Voordat dit blauw druk-voor beeld kan worden geïmplementeerd, moet het voor beeld van de [ISO 27001-service](../iso27001-shared/index.md) voor het maken van een blauw druk worden geïmplementeerd op het doel abonnement. Zonder een succes volle implementatie van het voor beeld van de ISO 27001 Shared Services-blauw druk, worden in dit voor beeld van de infra structuur ontbrekende infrastructuur afhankelijkheden en mislukken tijdens de implementatie.

> [!IMPORTANT]
> Dit blauw druk-voor beeld moet worden toegewezen in hetzelfde abonnement als het voor beeld van de [ISO 27001 Shared Services](../iso27001-shared/index.md) -blauw druk.

## <a name="create-blueprint-from-sample"></a>Blauw druk maken op basis van voor beeld

Implementeer eerst het voor beeld van de blauw druk door een nieuwe blauw druk in uw omgeving te maken met behulp van het voor beeld als een starter.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Op de pagina **aan** de slag aan de linkerkant selecteert u de knop **maken** onder _een blauw druk maken_.

1. Zoek de **ISO 27001: ASE/SQL-werk belasting** blauw druk onder _andere voor beelden_ en selecteer **dit voor beeld gebruiken**.

1. Voer de _basis beginselen_ van het voor beeld van de blauw druk in:

   - **Blauw druk-naam**: Geef een naam op voor uw kopie van het voor beeld van de ISO 27001 ASE/SQL-workload blauw druk.
   - **Locatie van definitie**: gebruik het weglatings teken en selecteer de beheer groep om uw kopie van het voor beeld op te slaan.

1. Selecteer het tabblad _artefacten_ boven aan de pagina of **volgende: artefacten** aan de onderkant van de pagina.

1. Bekijk de lijst met artefacten die deel uitmaken van het voor beeld van de blauw druk. Veel van de artefacten hebben para meters die later worden gedefinieerd. Selecteer **concept opslaan** wanneer u klaar bent met het bekijken van het voor beeld van de blauw druk.

## <a name="publish-the-sample-copy"></a>De voorbeeld kopie publiceren

Uw kopie van het voor beeld van de blauw druk is nu in uw omgeving gemaakt. Deze wordt gemaakt in de **concept** modus en moet worden **gepubliceerd** voordat deze kan worden toegewezen en geïmplementeerd. De kopie van het voor beeld van de blauw druk kan worden aangepast aan uw omgeving en behoeften, maar deze wijziging kan worden verplaatst van de ISO 27001-standaard.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer de pagina met **definities van blauw** drukken aan de linkerkant. Gebruik de filters om uw kopie van het voor beeld van de blauw druk te vinden en selecteer deze.

1. Selecteer **blauw druk** aan de bovenkant van de pagina publiceren. Geef op de pagina Nieuw aan de rechter kant een **versie** op voor uw kopie van het voor beeld van de blauw druk. Deze eigenschap is handig als u later een wijziging aanbrengt. Geef **wijzigings notities** op zoals ' eerste versie gepubliceerd vanuit het ISO 27001-blauw druk-voor beeld. ' Selecteer vervolgens **publiceren** onder aan de pagina.

## <a name="assign-the-sample-copy"></a>De voorbeeld kopie toewijzen

Zodra de kopie van het voor beeld van de blauw druk is **gepubliceerd**, kan deze worden toegewezen aan een abonnement in de beheer groep waarop het is opgeslagen. In deze stap worden para meters opgegeven om elke implementatie van de kopie van het voor beeld van de blauw druk te maken.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer de pagina met **definities van blauw** drukken aan de linkerkant. Gebruik de filters om uw kopie van het voor beeld van de blauw druk te vinden en selecteer deze.

1. Selecteer de optie **blauw** drukken boven aan de pagina met de definitie van de blauw druk.

1. Geef de parameter waarden voor de toewijzing van de blauw druk op:

   - Basisbeginselen

     - **Abonnementen**: Selecteer een of meer abonnementen in de beheer groep waarop u uw kopie van het voor beeld van de blauw druk hebt opgeslagen. Als u meer dan één abonnement selecteert, wordt er een toewijzing gemaakt met behulp van de opgegeven para meters.
     - **Toewijzings naam**: de naam wordt vooraf ingevuld op basis van de naam van de blauw druk.
       Wijzig indien nodig of sluit af.
     - **Locatie**: Selecteer een regio voor de beheerde identiteit die u wilt maken in. Azure Blueprint gebruikt deze beheerde identiteit om alle artefacten in de toegewezen blauwdruk te implementeren. Zie [Beheerde identiteiten voor Azure-resources](../../../../active-directory/managed-identities-azure-resources/overview.md) voor meer informatie.
     - **Definitie van blauw druk-versie**: Kies een **gepubliceerde** versie van uw kopie van het voor beeld van de blauw druk.

   - Toewijzing vergren delen

     Selecteer de instelling voor het vergren delen van blauw drukken voor uw omgeving. Zie voor meer informatie [Vergrendeling van blauwdrukresources](../../concepts/resource-locking.md).

   - Beheerde identiteit

     Zorg ervoor dat de standaard optie voor beheerde identiteit door het _systeem wordt toegewezen_ .

   - Blauwdrukparameters

     De para meters die in deze sectie zijn gedefinieerd, worden door veel van de artefacten in de definitie van de blauw druk gebruikt om consistentie te bieden.

     - **Organisatie naam**: Voer een korte naam in voor uw organisatie. Deze eigenschap wordt voornamelijk gebruikt voor het benoemen van resources.
     - **Id van het gedeelde service abonnement**: abonnements-id waar de [ISO 27001](../iso27001-shared/index.md) -voor beeld van een blauw druk is toegewezen.
     - **Standaard voorvoegsel**van het subnet: de CIDR-notatie voor het standaard subnet van het virtuele netwerk.
       De standaard waarde is _10.1.0.0/16_.
     - **Werkbelasting locatie**: bepaalt op welke locatie de artefacten worden geïmplementeerd. Niet alle services zijn beschikbaar op alle locaties. Artefacten die dergelijke services implementeren, bieden een parameter optie voor de locatie waarop die artefact moet worden geïmplementeerd.

   - Artefact parameters

     De in deze sectie gedefinieerde para meters zijn van toepassing op het artefact waaronder het is gedefinieerd. Deze para meters zijn [dynamische para meters](../../concepts/parameters.md#dynamic-parameters) , omdat ze zijn gedefinieerd tijdens de toewijzing van de blauw druk. Zie [artefact parameters Table](#artifact-parameters-table)voor een volledige lijst of artefact parameters en de bijbehorende beschrijvingen.

1. Zodra alle para meters zijn ingevoerd, selecteert u aan de onderkant van de pagina **toewijzen** . De blauw druk toewijzing wordt gemaakt en de implementatie van artefacten begint. De implementatie duurt ongeveer een uur. Als u de status van de implementatie wilt controleren, opent u de blauw druk-toewijzing.

> [!WARNING]
> De Azure-blauw drukken-service en de ingebouwde blauw druk-voor beelden zijn **gratis**. Azure-resources zijn [prijs per product](https://azure.microsoft.com/pricing/). Gebruik de [prijs calculator](https://azure.microsoft.com/pricing/calculator/) om een schatting te maken van de kosten van het uitvoeren van resources die worden geïmplementeerd door deze blauw druk-voor beeld.

## <a name="artifact-parameters-table"></a>Tabel artefact parameters

De volgende tabel geeft een lijst van de para meters van de blauw druk-artefact:

|Artefact naam|Type artefact|Parameternaam|Beschrijving|
|-|-|-|-|
|Log Analytics resource groep|Resourcegroep|Naam|**Vergrendeld** : voegt de naam van de **organisatie** samen met `-workload-log-rg` om de resource groep uniek te maken.|
|Log Analytics resource groep|Resourcegroep|Locatie|**Vergrendeld** : maakt gebruik van de blauw druk-para meter.|
|Log Analytics sjabloon|Resource Manager-sjabloon|Serviceniveau|Hiermee stelt u de laag van de Log Analytics-werk ruimte. De standaard waarde is _PerNode_.|
|Log Analytics sjabloon|Resource Manager-sjabloon|Bewaar periode logboek in dagen|Bewaren van gegevens in dagen. De standaard waarde is _365_.|
|Log Analytics sjabloon|Resource Manager-sjabloon|Locatie|De regio die wordt gebruikt voor het maken van de Log Analytics-werk ruimte. De standaard waarde is _VS-West 2_.|
|Netwerk resource groep|Resourcegroep|Naam|**Vergrendeld** : voegt de naam van de **organisatie** samen met `-workload-net-rg` om de resource groep uniek te maken.|
|Netwerk resource groep|Resourcegroep|Locatie|**Vergrendeld** : maakt gebruik van de blauw druk-para meter.|
|Sjabloon netwerk beveiligings groep|Resource Manager-sjabloon|Bewaar periode logboek in dagen|Bewaren van gegevens in dagen. De standaard waarde is _365_.|
|Sjabloon voor Virtual Network en route tabel|Resource Manager-sjabloon|Privé-IP van Azure-firewall|Hiermee configureert u het privé IP-adres van de [Azure-firewall](../../../../firewall/overview.md). Moet deel uitmaken van de CIDR-notatie die is gedefinieerd in _ISO 27001: Shared Services_ artefact parameter **Azure firewall subnet adres voorvoegsel**. De standaard waarde is _10.0.4.4_.|
|Sjabloon voor Virtual Network en route tabel|Resource Manager-sjabloon|ID van Shared Services-abonnement|De waarde die wordt gebruikt om VNET-peering in te scha kelen tussen een werk belasting en gedeelde services.|
|Sjabloon voor Virtual Network en route tabel|Resource Manager-sjabloon|Adres voorvoegsel Virtual Network|De CIDR-notatie voor het virtuele netwerk. De standaard waarde is _10.1.0.0/16_.|
|Sjabloon voor Virtual Network en route tabel|Resource Manager-sjabloon|Adres voorvoegsel van het standaard subnet|De CIDR-notatie voor het standaard subnet van het virtuele netwerk. De standaard waarde is _10.1.0.0/16_.|
|Sjabloon voor Virtual Network en route tabel|Resource Manager-sjabloon|IP-adres toevoegen|IP-adres van de eerste virtuele machine toevoegen. Deze waarde wordt gebruikt als aangepaste VNET DNS.|
|Key Vault resource groep|Resourcegroep|Naam|**Vergrendeld** : voegt de naam van de **organisatie** samen met `-workload-kv-rg` om de resource groep uniek te maken.|
|Key Vault resource groep|Resourcegroep|Locatie|**Vergrendeld** : maakt gebruik van de blauw druk-para meter.|
|Key Vault sjabloon|Resource Manager-sjabloon|AAD-object-ID|De AAD-object-id van het account dat toegang moet hebben tot het Key Vault exemplaar. Geen standaard waarde en mag niet leeg zijn. Als u deze waarde wilt vinden in de Azure Portal, zoekt en selecteert u ' gebruikers ' onder _Services_. Gebruik het vak _naam_ om te filteren op de account naam en selecteer dat account. Selecteer op de pagina _gebruikers profiel_ het pictogram ' Klik om te kopiëren ' naast de _object-id_.|
|Key Vault sjabloon|Resource Manager-sjabloon|Bewaar periode logboek in dagen|Bewaren van gegevens in dagen. De standaard waarde is _365_.|
|Key Vault sjabloon|Resource Manager-sjabloon|Key Vault SKU|Hiermee geeft u de SKU op van de Key Vault die is gemaakt. De standaard waarde is _Premium_.|
|Key Vault sjabloon|Resource Manager-sjabloon|Gebruikers naam van Azure SQL Server beheerder|De gebruikers naam die wordt gebruikt voor toegang tot Azure SQL Server. Moet overeenkomen met dezelfde eigenschaps waarde in **Azure SQL database sjabloon**. De standaard waarde is _SQL-admin-user_.|
|Azure SQL Database resource groep|Resourcegroep|Naam|**Vergrendeld** : voegt de naam van de **organisatie** samen met `-workload-azsql-rg` om de resource groep uniek te maken.|
|Azure SQL Database resource groep|Resourcegroep|Locatie|**Vergrendeld** : maakt gebruik van de blauw druk-para meter.|
|Azure SQL Database sjabloon|Resource Manager-sjabloon|Gebruikers naam van Azure SQL Server beheerder|Gebruikers naam voor de Azure-SQL Server. Moet overeenkomen met dezelfde eigenschaps waarde in **Key Vault sjabloon**. De standaard waarde is _SQL-admin-user_.|
|Azure SQL Database sjabloon|Resource Manager-sjabloon|Wacht woord voor Azure SQL Server-beheerder (Key Vault Resource-ID)|De resource-ID van de Key Vault. Gebruik '/subscription/{subscriptionId}/resourceGroups/{orgName}-workload-kv/providers/Microsoft.KeyVault/vaults/{orgName}-workload-kv ' en vervang `{subscriptionId}` door uw abonnements-ID en `{orgName}` door de naam blauw druk van de **organisatie** bepaalde.|
|Azure SQL Database sjabloon|Resource Manager-sjabloon|Beheerders wachtwoord voor Azure SQL Server (Key Vault geheime naam)|De gebruikers naam van de SQL Server beheerder. Moet overeenkomen met de waarde in **Key Vault sjabloon** eigenschap **Azure SQL Server gebruikers naam**van de beheerder.|
|Azure SQL Database sjabloon|Resource Manager-sjabloon|Bewaar periode logboek in dagen|Bewaren van gegevens in dagen. De standaard waarde is _365_.|
|Azure SQL Database sjabloon|Resource Manager-sjabloon|AAD-beheer object-ID|AAD-object-ID van de gebruiker die wordt toegewezen als een Active Directory beheerder. Geen standaard waarde en mag niet leeg zijn. Als u deze waarde wilt vinden in de Azure Portal, zoekt en selecteert u ' gebruikers ' onder _Services_. Gebruik het vak _naam_ om te filteren op de account naam en selecteer dat account. Selecteer op de pagina _gebruikers profiel_ het pictogram ' Klik om te kopiëren ' naast de _object-id_.|
|Azure SQL Database sjabloon|Resource Manager-sjabloon|Aanmelding AAD-beheerder|Op dit moment kunnen micro soft-accounts (zoals live.com of outlook.com) niet als beheerder worden ingesteld. Alleen gebruikers en beveiligings groepen in uw organisatie kunnen als beheerder worden ingesteld. Geen standaard waarde en mag niet leeg zijn. Als u deze waarde wilt vinden in de Azure Portal, zoekt en selecteert u ' gebruikers ' onder _Services_. Gebruik het vak _naam_ om te filteren op de account naam en selecteer dat account. Kopieer de _gebruikers naam_op de pagina _gebruikers profiel_ .|
|App Service Environment resource groep|Resourcegroep|Naam|**Vergrendeld** : voegt de naam van de **organisatie** samen met `-workload-ase-rg` om de resource groep uniek te maken.|
|App Service Environment resource groep|Resourcegroep|Locatie|**Vergrendeld** : maakt gebruik van de blauw druk-para meter.|
|App Service Environment sjabloon|Resource Manager-sjabloon|Domeinnaam|De naam van de Active Directory gemaakt door het voor beeld. De standaard waarde is _contoso.com_.|
|App Service Environment sjabloon|Resource Manager-sjabloon|Locatie van ASE|App Service Environment locatie. De standaard waarde is _VS-West 2_.|
|App Service Environment sjabloon|Resource Manager-sjabloon|Bewaar periode Application Gateway logboek in dagen|Bewaren van gegevens in dagen. De standaard waarde is _365_.|

## <a name="next-steps"></a>Volgende stappen

Nu u de stappen voor het implementeren van het voor beeld van de werk belasting van ISO 27001 App Service Environment/SQL Database workload hebt bekeken, gaat u naar de volgende artikelen voor meer informatie over de architectuur en de beheer toewijzing:

> [!div class="nextstepaction"]
> [Blauw druk voor ISO 27001 app service Environment/SQL database werk belasting-overzicht](./index.md)
> [ISO 27001 app service Environment/SQL database werk belasting blauw druk-besturings element toewijzing](./control-mapping.md)

Aanvullende artikelen over blauw drukken en hoe u deze kunt gebruiken:

- Meer informatie over de [levenscyclus van een blauwdruk](../../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../../concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](../../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../../concepts/resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).