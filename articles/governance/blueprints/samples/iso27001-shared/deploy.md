---
title: ISO 27001-voor beeld van gedeelde services gebruiken blauw drukken
description: Implementeer stappen voor het voor beeld van de ISO 27001 Shared Services-blauw druk, inclusief para meters voor de artefact parameter.
ms.date: 01/13/2020
ms.topic: sample
ms.openlocfilehash: a9dabc99469321445006e449757a10fbc51aba87
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/13/2020
ms.locfileid: "75920682"
---
# <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Het voor beeld van de ISO 27001-gedeelde services gebruiken

De volgende stappen moeten worden uitgevoerd om het blauw druk-voor beeld van het gebruik van de Azure-blauw drukken ISO 27001-gezamenlijke services te implementeren:

> [!div class="checklist"]
> - Een nieuwe blauw druk maken op basis van het voor beeld
> - Uw kopie van het voor beeld markeren als **gepubliceerd**
> - Uw kopie van de blauw druk toewijzen aan een bestaand abonnement

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free) aan voordat u begint.

## <a name="create-blueprint-from-sample"></a>Blauw druk maken op basis van voor beeld

Implementeer eerst het voor beeld van de blauw druk door een nieuwe blauw druk in uw omgeving te maken met behulp van het voor beeld als een starter.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Op de pagina **aan** de slag aan de linkerkant selecteert u de knop **maken** onder _een blauw druk maken_.

1. Zoek de **ISO 27001:** voor beeld van een blauw druk van gedeelde services onder _andere voor beelden_ en selecteer **dit voor beeld gebruiken**.

1. Voer de _basis beginselen_ van het voor beeld van de blauw druk in:

   - **Blauw druk-naam**: Geef een naam op voor uw exemplaar van het voor beeld van de ISO 27001-gedeelde services.
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
     - **Adres voorvoegsel voor het subnet van gedeelde services**: Geef de CIDR-notatie waarde op voor de netwerk implementatie van de geïmplementeerde resources tegelijk.
     - **Locatie van gedeelde services**: bepaalt op welke locatie de artefacten worden geïmplementeerd. Niet alle services zijn beschikbaar op alle locaties. Artefacten die dergelijke services implementeren, bieden een parameter optie voor de locatie waarop die artefact moet worden geïmplementeerd.
     - **Toegestane locatie (beleid: blauw druk-initiatief voor ISO 27001)** : waarde die de toegestane locaties voor resource groepen en bronnen aangeeft.
     - **Log Analytics werk ruimte voor VM-agents (beleid: blauw druk-initiatief voor ISO 27001)** : Hiermee geeft u de resource-id van een werk ruimte op. Deze para meter gebruikt een `concat` functie om de resource-ID te maken.

   - Artefact parameters

     De in deze sectie gedefinieerde para meters zijn van toepassing op het artefact waaronder het is gedefinieerd. Deze para meters zijn [dynamische para meters](../../concepts/parameters.md#dynamic-parameters) , omdat ze zijn gedefinieerd tijdens de toewijzing van de blauw druk. Zie [artefact parameters Table](#artifact-parameters-table)voor een volledige lijst of artefact parameters en de bijbehorende beschrijvingen.

1. Zodra alle para meters zijn ingevoerd, selecteert u aan de onderkant van de pagina **toewijzen** . De blauw druk toewijzing wordt gemaakt en de implementatie van artefacten begint. De implementatie duurt ongeveer een uur. Als u de status van de implementatie wilt controleren, opent u de blauw druk-toewijzing.

> [!WARNING]
> De Azure-blauw drukken-service en de ingebouwde blauw druk-voor beelden zijn **gratis**. Azure-resources zijn [prijs per product](https://azure.microsoft.com/pricing/). Gebruik de [prijs calculator](https://azure.microsoft.com/pricing/calculator/) om een schatting te maken van de kosten van het uitvoeren van resources die worden geïmplementeerd door deze blauw druk-voor beeld.

## <a name="artifact-parameters-table"></a>Tabel artefact parameters

De volgende tabel geeft een lijst van de para meters van de blauw druk-artefact:

|Artefact naam|Type artefact|Parameternaam|Beschrijving|
|-|-|-|-|
|\[preview\]: Log Analytics agent voor Linux VM Scale Sets implementeren (VMSS)|Beleidstoewijzing|Optioneel: lijst met VM-installatie kopieën met ondersteund Linux-besturings systeem om toe te voegen aan het bereik|Beschrijving De standaard waarde is _[' none ']_ .|
|\[Preview-versie\]: Log Analytics-Agent voor Linux-VM's implementeren|Beleidstoewijzing|Optioneel: lijst met VM-installatie kopieën met ondersteund Linux-besturings systeem om toe te voegen aan het bereik|Beschrijving De standaard waarde is _[' none ']_ .|
|\[preview\]: Log Analytics agent voor Windows VM Scale Sets implementeren (VMSS)|Beleidstoewijzing|Optioneel: lijst met VM-installatie kopieën die een ondersteund Windows-besturings systeem hebben om toe te voegen aan het bereik|Beschrijving De standaard waarde is _[' none ']_ .|
|\[Preview-versie\]: Log Analytics-Agent voor Windows-VM's implementeren|Beleidstoewijzing|Optioneel: lijst met VM-installatie kopieën die een ondersteund Windows-besturings systeem hebben om toe te voegen aan het bereik|Beschrijving De standaard waarde is _[' none ']_ .|
|Toegestane brontypen|Beleidstoewijzing|Toegestane brontypen|Lijst met resource typen die mogen worden geïmplementeerd. Deze lijst bestaat uit alle resource typen die zijn geïmplementeerd in gedeelde services.|
|Toegestane opslagaccount-SKU's|Beleidstoewijzing|Toegestane opslag-Sku's|Lijst met Diagnostische logboeken opslag account-Sku's toegestaan. De standaard waarde is _["Standard_LRS"]_ .|
|Toegestane SKU's van virtuele machines|Beleidstoewijzing|Lijst met Sku's voor virtuele machines die mogen worden geïmplementeerd. De standaard waarde is _["Standard_DS1_v2", "Standard_DS2_v2"]_ .|
|Blauw druk-initiatief voor ISO 27001|Beleidstoewijzing|Bron typen voor het controleren van Diagnostische logboeken|Lijst met resource typen om te controleren of de instelling voor diagnostische logboek registratie niet is ingeschakeld. Acceptabele waarden zijn te vinden in [Azure monitor Diagnostische logboeken schema's](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type).|
|Log Analytics resource groep|Resourcegroep|Name|**Vergrendeld** : voegt de naam van de **organisatie** samen met `-sharedsvsc-log-rg` om de resource groep uniek te maken.|
|Log Analytics resource groep|Resourcegroep|Locatie|**Vergrendeld** : maakt gebruik van de blauw druk-para meter.|
|Log Analytics sjabloon|Resource Manager-sjabloon|Serviceniveau|Hiermee stelt u de laag van de Log Analytics-werk ruimte. De standaard waarde is _PerNode_.|
|Log Analytics sjabloon|Resource Manager-sjabloon|Bewaar periode logboek in dagen|Bewaren van gegevens in dagen. De standaard waarde is _365_.|
|Log Analytics sjabloon|Resource Manager-sjabloon|Locatie|De regio die wordt gebruikt voor het maken van de Log Analytics-werk ruimte. De standaard waarde is _VS-West 2_.|
|Netwerk resource groep|Resourcegroep|Name|**Vergrendeld** : voegt de naam van de **organisatie** samen met `-sharedsvcs-net-rg` om de resource groep uniek te maken.|
|Netwerk resource groep|Resourcegroep|Locatie|**Vergrendeld** : maakt gebruik van de blauw druk-para meter.|
|Azure Firewall sjabloon|Resource Manager-sjabloon|Privé-IP van Azure-firewall|Hiermee configureert u het privé IP-adres van de [Azure-firewall](../../../../firewall/overview.md). Deze waarde wordt ook gebruikt als standaard route tabel op het subnet met gedeelde services. Moet deel uitmaken van de CIDR-notatie die is gedefinieerd in **Azure firewall subnet adres voorvoegsel**. De standaard waarde is _10.0.4.4_.|
|Azure Firewall sjabloon|Resource Manager-sjabloon|Bewaar periode logboek in dagen|Bewaren van gegevens in dagen. De standaard waarde is _365_.|
|Sjabloon netwerk beveiligings groep|Resource Manager-sjabloon|Bewaar periode logboek in dagen|Bewaren van gegevens in dagen. De standaard waarde is _365_.|
|Sjabloon voor Virtual Network en route tabel|Resource Manager-sjabloon|Adres voorvoegsel Virtual Network|De CIDR-notatie voor het virtuele netwerk. De standaard waarde is _10.0.0.0/16_.|
|Sjabloon voor Virtual Network en route tabel|Resource Manager-sjabloon|Virtual Network DDoS-beveiliging inschakelen|Hiermee configureert u DDoS-beveiliging voor het virtuele netwerk. De standaard waarde is _True_.|
|Sjabloon voor Virtual Network en route tabel|Resource Manager-sjabloon|Adres voorvoegsel voor het subnet van gedeelde services|De CIDR-notatie voor het subnet met gedeelde services. De standaard waarde is _10.0.0.0/24_.|
|Sjabloon voor Virtual Network en route tabel|Resource Manager-sjabloon|Adres voorvoegsel van DMZ-subnet|De CIDR-notatie voor het DMZ-subnet. De standaard waarde is _10.0.1.0/24_.|
|Sjabloon voor Virtual Network en route tabel|Resource Manager-sjabloon|Adres voorvoegsel van Application Gateway subnet|De CIDR-notatie voor het toepassings gateway-subnet. De standaard waarde is _10.0.2.0/24_.|
|Sjabloon voor Virtual Network en route tabel|Resource Manager-sjabloon|Adres voorvoegsel Virtual Network gateway-subnet|De CIDR-notatie voor het subnet van de gateway van het virtuele netwerk. De standaard waarde is _10.0.3.0/24_.|
|Sjabloon voor Virtual Network en route tabel|Resource Manager-sjabloon|Adres voorvoegsel van Azure Firewall subnet|De CIDR-notatie voor het subnet van de [Azure-firewall](../../../../firewall/overview.md) . Moet de para meter **Private IP van Azure firewall** bevatten.|
|Key Vault resource groep|Resourcegroep|Name|**Vergrendeld** : voegt de naam van de **organisatie** samen met `-sharedsvcs-kv-rg` om de resource groep uniek te maken.|
|Key Vault resource groep|Resourcegroep|Locatie|**Vergrendeld** : maakt gebruik van de blauw druk-para meter.|
|Key Vault sjabloon|Resource Manager-sjabloon|Gebruikers naam JumpBox beheerder|Gebruikers naam voor de JumpBox. Moet overeenkomen met dezelfde eigenschaps waarde in de **JumpBox-sjabloon**. De standaard waarde is _JB-admin-user_.|
|Key Vault sjabloon|Resource Manager-sjabloon|SSH-sleutel of-wacht woord voor JumpBox-beheerder|De sleutel of het wacht woord voor het account op het JumpBox. Moet overeenkomen met dezelfde eigenschaps waarde in de **JumpBox-sjabloon**. Geen standaard waarde en mag niet leeg zijn.|
|Key Vault sjabloon|Resource Manager-sjabloon|Gebruikers naam domein beheerder|Gebruikers naam die wordt gebruikt voor toegang tot Active Directory virtuele machine en om andere virtuele machines aan een domein toe te voegen. De waarde van de gebruikers eigenschap van de **domein beheerder** moet overeenkomen in **Active Directory Domain Services sjabloon**. De standaard waarde is _domein beheerder-gebruiker_.|
|Key Vault sjabloon|Resource Manager-sjabloon|Domein beheerders wachtwoord|Wacht woord van gebruiker van domein beheerder. Geen standaard waarde en mag niet leeg zijn.|
|Key Vault sjabloon|Resource Manager-sjabloon|AAD-object-ID|De AAD-object-id van het account dat toegang moet hebben tot het Key Vault exemplaar. Geen standaard waarde en mag niet leeg zijn. Als u deze waarde wilt vinden in de Azure Portal, zoekt en selecteert u ' gebruikers ' onder _Services_. Gebruik het vak _naam_ om te filteren op de account naam en selecteer dat account. Selecteer op de pagina _gebruikers profiel_ het pictogram ' Klik om te kopiëren ' naast de _object-id_.  |
|Key Vault sjabloon|Resource Manager-sjabloon|Bewaar periode logboek in dagen|Bewaren van gegevens in dagen. De standaard waarde is _365_.|
|Key Vault sjabloon|Resource Manager-sjabloon|Key Vault SKU|Hiermee geeft u de SKU op van de Key Vault die is gemaakt. De standaard waarde is _Premium_.|
|Resource groep JumpBox|Resourcegroep|Name|**Vergrendeld** : voegt de naam van de **organisatie** samen met `-sharedsvcs-jb-rg` om de resource groep uniek te maken.|
|Resource groep JumpBox|Resourcegroep|Locatie|**Vergrendeld** : maakt gebruik van de blauw druk-para meter.|
|JumpBox-sjabloon|Resource Manager-sjabloon|Gebruikers naam JumpBox beheerder|De gebruikers naam die wordt gebruikt voor toegang tot JumpBox Vm's. Moet overeenkomen met dezelfde eigenschaps waarde in **Key Vault sjabloon**. De standaard waarde is _JB-admin-user_.|
|JumpBox-sjabloon|Resource Manager-sjabloon|JumpBox-beheerders wachtwoord (Key Vault Resource-ID)|De resource-ID van de Key Vault. Gebruik '/subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv ' en vervang `{subscriptionId}` door uw abonnements-ID en `{orgName}` met de para meter voor de naam blauw druk van de **organisatie** .|
|JumpBox-sjabloon|Resource Manager-sjabloon|JumpBox-beheerders wachtwoord (Key Vault geheime naam)|De gebruikers naam van de JumpBox-beheerder. Moet overeenkomen met de waarde in **Key Vault sjabloon** eigenschap **JumpBox beheerder**.|
|JumpBox-sjabloon|Resource Manager-sjabloon|JumpBox-besturings systeem|Bepaalt het besturings systeem van de JumpBox-VM. De standaard waarde is _Windows_.|
|Active Directory Domain Services resource groep|Resourcegroep|Name|**Vergrendeld** : voegt de naam van de **organisatie** samen met `-sharedsvcs-adds-rg` om de resource groep uniek te maken.|
|Active Directory Domain Services resource groep|Resourcegroep|Locatie|**Vergrendeld** : maakt gebruik van de blauw druk-para meter.|
|Active Directory Domain Services sjabloon|Resource Manager-sjabloon|Gebruikers naam domein beheerder|Gebruikers naam voor de voegt JumpBox toe. Moet overeenkomen met dezelfde eigenschaps waarde in **Key Vault sjabloon**. De standaard waarde is _toevoegen-admin-gebruiker_.|
|Active Directory Domain Services sjabloon|Resource Manager-sjabloon|Domein beheerders wachtwoord (Key Vault Resource-ID)|De resource-ID van de Key Vault. Gebruik '/subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv ' en vervang `{subscriptionId}` door uw abonnements-ID en `{orgName}` met de para meter voor de naam blauw druk van de **organisatie** .|
|Active Directory Domain Services sjabloon|Resource Manager-sjabloon|Domein beheerders wachtwoord (Key Vault geheime naam)|De gebruikers naam van de domein beheerder. Moet overeenkomen met de waarde in Key Vault eigenschap van de **domein beheerder**van de **sjabloon** .|
|Active Directory Domain Services sjabloon|Resource Manager-sjabloon|Domeinnaam|De naam van de Active Directory gemaakt door het voor beeld. De standaard waarde is _contoso.com_.|
|Active Directory Domain Services sjabloon|Resource Manager-sjabloon|Gebruiker van domein beheerder|Gebruikers naam voor het AD-beheer account en voor het toevoegen van apparaten aan het AD-domein. De waarde van de eigenschap van de **ad-beheerders naam** moet overeenkomen met de **Key Vault sjabloon**. De standaard waarde is _domein beheerder-gebruiker_.|
|Active Directory Domain Services sjabloon|Resource Manager-sjabloon|Domein beheerders wachtwoord|Stel de Key Vault Details in voor het opslaan van het wacht woord. Geen standaard waarde en mag niet leeg zijn.|

## <a name="next-steps"></a>Volgende stappen

Nu u de stappen voor het implementeren van het voor beeld van de ISO 27001 Shared Services-blauw drukken hebt gecontroleerd, gaat u naar de volgende artikelen voor meer informatie over de architectuur en de beheer toewijzing:

> [!div class="nextstepaction"]
> [ISO 27001-blauw druk: overzicht](./index.md)
> [ISO 27001 gedeelde services blauw druk-beheer toewijzing](./control-mapping.md)

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van een blauwdruk](../../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../../concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](../../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../../concepts/resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).
