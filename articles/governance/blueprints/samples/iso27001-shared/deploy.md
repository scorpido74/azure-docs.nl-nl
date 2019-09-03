---
title: Voor beeld-ISO 27001 van gedeelde services blauw druk-stappen implementeren
description: Implementeer de stappen van het voor beeld van de ISO 27001 Shared Services-blauw druk.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 04285ace1713a03a812f1c79895da64e6dac42a8
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231710"
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

1. Selecteer **alle services** , zoek naar en selecteer **beleid** in het linkerdeel venster. Selecteer **blauw drukken**op de pagina **beleid** .

1. Op de pagina **aan** de slag aan de linkerkant selecteert u de knop **maken** onder _een blauw druk maken_.

1. De **ISO 27001 zoeken: Voor beeld** van een blauw druk voor de gedeelde services onder _andere voor beelden_ en selecteert **u dit voor beeld gebruiken**.

1. Voer de _basis beginselen_ van het voor beeld van de blauw druk in:

   - **Naam blauw druk**: Geef een naam op voor uw exemplaar van het voor beeld van de ISO 27001-gedeelde services.
   - **Locatie van definitie**: Gebruik het weglatings teken en selecteer de beheer groep om uw kopie van het voor beeld op te slaan.

1. Selecteer het tabblad artefacten boven aan de pagina of **volgende:**  Artefacten aan de onderkant van de pagina.

1. Bekijk de lijst met artefacten die deel uitmaken van het voor beeld van de blauw druk. Veel van de artefacten hebben para meters die later worden gedefinieerd. Selecteer **concept opslaan** wanneer u klaar bent met het bekijken van het voor beeld van de blauw druk.

## <a name="publish-the-sample-copy"></a>De voorbeeld kopie publiceren

Uw kopie van het voor beeld van de blauw druk is nu in uw omgeving gemaakt. Deze wordt gemaakt in de **concept** modus en moet worden **gepubliceerd** voordat deze kan worden toegewezen en geïmplementeerd. De kopie van het voor beeld van de blauw druk kan worden aangepast aan uw omgeving en behoeften, maar deze wijziging kan worden verplaatst van de ISO 27001-standaard.

1. Selecteer **alle services** , zoek naar en selecteer **beleid** in het linkerdeel venster. Selecteer **blauw drukken**op de pagina **beleid** .

1. Selecteer de pagina met **definities van blauw** drukken aan de linkerkant. Gebruik de filters om uw kopie van het voor beeld van de blauw druk te vinden en selecteer deze.

1. Selecteer **blauw druk** aan de bovenkant van de pagina publiceren. Geef op de pagina Nieuw aan de rechter kant een **versie** op voor uw kopie van het voor beeld van de blauw druk. Deze eigenschap is handig als u later een wijziging aanbrengt. Geef **wijzigings notities** op zoals ' eerste versie gepubliceerd vanuit het ISO 27001-blauw druk-voor beeld. ' Selecteer vervolgens **publiceren** onder aan de pagina.

## <a name="assign-the-sample-copy"></a>De voorbeeld kopie toewijzen

Zodra de kopie van het voor beeld van de blauw druk is **gepubliceerd**, kan deze worden toegewezen aan een abonnement in de beheer groep waarop het is opgeslagen. In deze stap worden para meters opgegeven om elke implementatie van de kopie van het voor beeld van de blauw druk te maken.

1. Selecteer **alle services** , zoek naar en selecteer **beleid** in het linkerdeel venster. Selecteer **blauw drukken**op de pagina **beleid** .

1. Selecteer de pagina met **definities van blauw** drukken aan de linkerkant. Gebruik de filters om uw kopie van het voor beeld van de blauw druk te vinden en selecteer deze.

1. Selecteer de optie **blauw** drukken boven aan de pagina met de definitie van de blauw druk.

1. Geef de parameter waarden voor de toewijzing van de blauw druk op:

   - Basics

     - **Abonnementen**: Selecteer een of meer abonnementen in de beheer groep waarop u uw kopie van het voor beeld van de blauw druk hebt opgeslagen. Als u meer dan één abonnement selecteert, wordt er een toewijzing gemaakt met behulp van de opgegeven para meters.
     - **Toewijzings naam**: De naam wordt vooraf ingevuld op basis van de naam van de blauw druk.
       Wijzig indien nodig of sluit af.
     - **Locatie**: Selecteer een regio voor de beheerde identiteit die u wilt maken in. Azure Blueprint gebruikt deze beheerde identiteit om alle artefacten in de toegewezen blauwdruk te implementeren. Zie [Beheerde identiteiten voor Azure-resources](../../../../active-directory/managed-identities-azure-resources/overview.md) voor meer informatie.
     - **Definitie van blauw druk-versie**: Kies een **gepubliceerde** versie van uw kopie van het voor beeld van de blauw druk.

   - Toewijzing vergrendelen

     Selecteer de instelling voor het vergren delen van blauw drukken voor uw omgeving. Zie voor meer informatie [Vergrendeling van blauwdrukresources](../../concepts/resource-locking.md).

   - Beheerde identiteit

     Zorg ervoor dat de standaard optie voor beheerde identiteit door het _systeem wordt toegewezen_ .

   - Blauwdrukparameters

     De para meters die in deze sectie zijn gedefinieerd, worden door veel van de artefacten in de definitie van de blauw druk gebruikt om consistentie te bieden.

     - **Organisatie naam**: Geef een korte naam op voor uw organisatie. Deze eigenschap wordt voornamelijk gebruikt voor het benoemen van resources.
     - **Adres voorvoegsel voor het subnet van gedeelde services**: Geef de CIDR-notatie waarde op voor de netwerk implementatie van de geïmplementeerde resources tegelijk.
     - **Locatie van gedeelde services**: Bepaalt op welke locatie de artefacten worden geïmplementeerd. Niet alle services zijn beschikbaar op alle locaties. Artefacten die dergelijke services implementeren, bieden een parameter optie voor de locatie waarop die artefact moet worden geïmplementeerd.
     - **Toegestane locatie (beleid: Blauw druk-initiatief voor ISO**27001): Waarde die de toegestane locaties voor resource groepen en bronnen aangeeft.
     - **Log Analytics werk ruimte voor VM-agents (beleid: Blauw druk-initiatief voor ISO**27001): Hiermee geeft u de resource-ID van een werk ruimte op. Deze para meter gebruikt `concat` een functie om de resource-id te maken.

   - Artefactparameters

     De in deze sectie gedefinieerde para meters zijn van toepassing op het artefact waaronder het is gedefinieerd. Deze para meters zijn [dynamische para meters](../../concepts/parameters.md#dynamic-parameters) , omdat ze zijn gedefinieerd tijdens de toewijzing van de blauw druk. Zie [artefact parameters Table](#artifact-parameters-table)voor een volledige lijst of artefact parameters en de bijbehorende beschrijvingen.

1. Zodra alle para meters zijn ingevoerd, selecteert u aan de onderkant van de pagina **toewijzen** . De blauw druk toewijzing wordt gemaakt en de implementatie van artefacten begint. De implementatie duurt ongeveer een uur. Als u de status van de implementatie wilt controleren, opent u de blauw druk-toewijzing.

> [!WARNING]
> De Azure-blauw drukken-service en de ingebouwde blauw druk-voor beelden zijn **gratis**. Azure-resources zijn [prijs per product](https://azure.microsoft.com/pricing/). Gebruik de [prijs calculator](https://azure.microsoft.com/pricing/calculator/) om een schatting te maken van de kosten van het uitvoeren van resources die worden geïmplementeerd door deze blauw druk-voor beeld.

## <a name="artifact-parameters-table"></a>Tabel artefact parameters

De volgende tabel geeft een lijst van de para meters van de blauw druk-artefact:

|Naam van het artefact|Type artefact|Parameternaam|Description|
|-|-|-|-|
|\[Voor\]beeld: Log Analytics agent voor Linux-VM Scale Sets implementeren (VMSS)|Beleidstoewijzing|Optioneel: Lijst met VM-installatie kopieën die een ondersteund Linux-besturings systeem hebben om toe te voegen aan het bereik|Beschrijving De standaard waarde is _[' none ']_ .|
|\[Voor\]beeld: Log Analytics-agent voor Linux-Vm's implementeren|Beleidstoewijzing|Optioneel: Lijst met VM-installatie kopieën die een ondersteund Linux-besturings systeem hebben om toe te voegen aan het bereik|Beschrijving De standaard waarde is _[' none ']_ .|
|\[Voor\]beeld: Log Analytics agent voor Windows-VM Scale Sets (VMSS) implementeren|Beleidstoewijzing|Optioneel: Lijst met VM-installatie kopieën die een ondersteund Windows-besturings systeem hebben om toe te voegen aan het bereik|Beschrijving De standaard waarde is _[' none ']_ .|
|\[Voor\]beeld: Log Analytics-agent voor Windows-Vm's implementeren|Beleidstoewijzing|Optioneel: Lijst met VM-installatie kopieën die een ondersteund Windows-besturings systeem hebben om toe te voegen aan het bereik|Beschrijving De standaard waarde is _[' none ']_ .|
|Toegestane resourcetypen|Beleidstoewijzing|Toegestane resourcetypen|Lijst met resource typen die mogen worden geïmplementeerd. Deze lijst bestaat uit alle resource typen die zijn geïmplementeerd in gedeelde services.|
|Toegestane opslagaccount-SKU's|Beleidstoewijzing|Toegestane opslag-Sku's|Lijst met Diagnostische logboeken opslag account-Sku's toegestaan. De standaard waarde is _["Standard_LRS"]_ .|
|Toegestane SKU's van virtuele machines|Beleidstoewijzing|Lijst met Sku's voor virtuele machines die mogen worden geïmplementeerd. De standaard waarde is _["Standard_DS1_v2", "Standard_DS2_v2"]_ .|
|Blauwdrukinitiatief voor ISO 27001|Beleidstoewijzing|Bron typen voor het controleren van Diagnostische logboeken|Lijst met resource typen om te controleren of de instelling voor diagnostische logboek registratie niet is ingeschakeld. Acceptabele waarden zijn te vinden in [Azure monitor Diagnostische logboeken schema's](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type).|
|Log Analytics resource groep|Resource group|Name|**Vergrendeld** : voegt de **naam** van `-sharedsvsc-log-rg` de organisatie samen om de resource groep uniek te maken.|
|Log Analytics resource groep|Resource group|Location|**Vergrendeld** : maakt gebruik van de blauw druk-para meter.|
|Log Analytics-sjabloon|Resource Manager-sjabloon|Servicelaag|Hiermee stelt u de laag van de Log Analytics-werk ruimte. De standaard waarde is _PerNode_.|
|Log Analytics-sjabloon|Resource Manager-sjabloon|Behoud van logboeken in dagen|Bewaren van gegevens in dagen. De standaard waarde is _365_.|
|Log Analytics-sjabloon|Resource Manager-sjabloon|Location|De regio die wordt gebruikt voor het maken van de Log Analytics-werk ruimte. De standaard waarde is _VS-West 2_.|
|Netwerk resource groep|Resource group|Name|**Vergrendeld** : voegt de **naam** van `-sharedsvcs-net-rg` de organisatie samen om de resource groep uniek te maken.|
|Netwerk resource groep|Resource group|Location|**Vergrendeld** : maakt gebruik van de blauw druk-para meter.|
|Sjabloon voor Azure Firewall|Resource Manager-sjabloon|Privé-IP-adres voor Azure-firewall|Hiermee configureert u het privé IP-adres van de [Azure-firewall](../../../../firewall/overview.md). Deze waarde wordt ook gebruikt als standaard route tabel op het subnet met gedeelde services. Moet deel uitmaken van de CIDR-notatie die is gedefinieerd in **Azure firewall subnet adres voorvoegsel**. De standaard waarde is _10.0.4.4_.|
|Sjabloon voor Azure Firewall|Resource Manager-sjabloon|Behoud van logboeken in dagen|Bewaren van gegevens in dagen. De standaard waarde is _365_.|
|Sjabloon voor netwerkbeveiligingsgroep|Resource Manager-sjabloon|Behoud van logboeken in dagen|Bewaren van gegevens in dagen. De standaard waarde is _365_.|
|Sjabloon voor Virtual Network en route tabel|Resource Manager-sjabloon|Voorvoegsel van adres voor virtueel netwerk|De CIDR-notatie voor het virtuele netwerk. De standaard waarde is _10.0.0.0/16_.|
|Sjabloon voor Virtual Network en route tabel|Resource Manager-sjabloon|DDoS-beveiliging voor virtueel netwerk inschakelen|Hiermee configureert u DDoS-beveiliging voor het virtuele netwerk. De standaard waarde is _True_.|
|Sjabloon voor Virtual Network en route tabel|Resource Manager-sjabloon|Adres voorvoegsel voor het subnet van gedeelde services|De CIDR-notatie voor het subnet met gedeelde services. De standaard waarde is _10.0.0.0/24_.|
|Sjabloon voor Virtual Network en route tabel|Resource Manager-sjabloon|Voorvoegsel van subnetadres van DMZ|De CIDR-notatie voor het DMZ-subnet. De standaard waarde is _10.0.1.0/24_.|
|Sjabloon voor Virtual Network en route tabel|Resource Manager-sjabloon|Voorvoegsel van subnetadres van Application Gateway|De CIDR-notatie voor het toepassings gateway-subnet. De standaard waarde is _10.0.2.0/24_.|
|Sjabloon voor Virtual Network en route tabel|Resource Manager-sjabloon|Adres voorvoegsel Virtual Network gateway-subnet|De CIDR-notatie voor het subnet van de gateway van het virtuele netwerk. De standaard waarde is _10.0.3.0/24_.|
|Sjabloon voor Virtual Network en route tabel|Resource Manager-sjabloon|Voorvoegsel van subnetadres van Azure Firewall|De CIDR-notatie voor het subnet van de [Azure-firewall](../../../../firewall/overview.md) . Moet de para meter **Private IP van Azure firewall** bevatten.|
|Key Vault resource groep|Resource group|Name|**Vergrendeld** : voegt de **naam** van `-sharedsvcs-kv-rg` de organisatie samen om de resource groep uniek te maken.|
|Key Vault resource groep|Resource group|Location|**Vergrendeld** : maakt gebruik van de blauw druk-para meter.|
|Key Vault-sjabloon|Resource Manager-sjabloon|Gebruikersnaam van Jumpbox-beheerder|Gebruikers naam voor de JumpBox. Moet overeenkomen met dezelfde eigenschaps waarde in de **JumpBox-sjabloon**. De standaard waarde is _JB-admin-user_.|
|Key Vault-sjabloon|Resource Manager-sjabloon|SSH-sleutel of-wacht woord voor JumpBox-beheerder|De sleutel of het wacht woord voor het account op het JumpBox. Moet overeenkomen met dezelfde eigenschaps waarde in de **JumpBox-sjabloon**. Geen standaard waarde en mag niet leeg zijn.|
|Key Vault-sjabloon|Resource Manager-sjabloon|Gebruikersnaam van domeinbeheerder|Gebruikers naam die wordt gebruikt voor toegang tot Active Directory virtuele machine en om andere virtuele machines aan een domein toe te voegen. De waarde van de gebruikers eigenschap van de **domein beheerder** moet overeenkomen in **Active Directory Domain Services sjabloon**. De standaard waarde is _domein beheerder-gebruiker_.|
|Key Vault-sjabloon|Resource Manager-sjabloon|Wachtwoord van domeinbeheerder|Wacht woord van gebruiker van domein beheerder. Geen standaard waarde en mag niet leeg zijn.|
|Key Vault-sjabloon|Resource Manager-sjabloon|AAD-object-id|De AAD-object-id van het account dat toegang moet hebben tot het Key Vault exemplaar. Geen standaard waarde en mag niet leeg zijn. Als u deze waarde wilt vinden in de Azure Portal, zoekt en selecteert u ' gebruikers ' onder _Services_. Gebruik het vak _naam_ om te filteren op de account naam en selecteer dat account. Selecteer op de pagina _gebruikers profiel_ het pictogram ' Klik om te kopiëren ' naast de _object-id_.  |
|Key Vault-sjabloon|Resource Manager-sjabloon|Behoud van logboeken in dagen|Bewaren van gegevens in dagen. De standaard waarde is _365_.|
|Key Vault-sjabloon|Resource Manager-sjabloon|Key Vault-SKU|Hiermee geeft u de SKU op van de Key Vault die is gemaakt. De standaard waarde is _Premium_.|
|Resource groep JumpBox|Resource group|Name|**Vergrendeld** : voegt de **naam** van `-sharedsvcs-jb-rg` de organisatie samen om de resource groep uniek te maken.|
|Resource groep JumpBox|Resource group|Location|**Vergrendeld** : maakt gebruik van de blauw druk-para meter.|
|Jumpbox-sjabloon|Resource Manager-sjabloon|Gebruikersnaam van Jumpbox-beheerder|De gebruikers naam die wordt gebruikt voor toegang tot JumpBox Vm's. Moet overeenkomen met dezelfde eigenschaps waarde in **Key Vault sjabloon**. De standaard waarde is _JB-admin-user_.|
|Jumpbox-sjabloon|Resource Manager-sjabloon|JumpBox-beheerders wachtwoord (Key Vault Resource-ID)|De resource-ID van de Key Vault. Gebruik '/Subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-KV-RG/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-KV ' en vervang `{subscriptionId}` door uw abonnements-id `{orgName}` en de **organisatie** para meter naam blauw druk.|
|Jumpbox-sjabloon|Resource Manager-sjabloon|JumpBox-beheerders wachtwoord (Key Vault geheime naam)|De gebruikers naam van de JumpBox-beheerder. Moet overeenkomen met de waarde in **Key Vault sjabloon** eigenschap **JumpBox beheerder**.|
|Jumpbox-sjabloon|Resource Manager-sjabloon|Jumpbox-besturingssysteem|Bepaalt het besturings systeem van de JumpBox-VM. De standaard waarde is _Windows_.|
|Active Directory Domain Services resource groep|Resource group|Name|**Vergrendeld** : voegt de **naam** van `-sharedsvcs-adds-rg` de organisatie samen om de resource groep uniek te maken.|
|Active Directory Domain Services resource groep|Resource group|Location|**Vergrendeld** : maakt gebruik van de blauw druk-para meter.|
|Sjabloon voor Active Directory Domain Services|Resource Manager-sjabloon|Gebruikersnaam van domeinbeheerder|Gebruikers naam voor de voegt JumpBox toe. Moet overeenkomen met dezelfde eigenschaps waarde in **Key Vault sjabloon**. De standaard waarde is _toevoegen-admin-gebruiker_.|
|Sjabloon voor Active Directory Domain Services|Resource Manager-sjabloon|Domein beheerders wachtwoord (Key Vault Resource-ID)|De resource-ID van de Key Vault. Gebruik '/Subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-KV-RG/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-KV ' en vervang `{subscriptionId}` door uw abonnements-id `{orgName}` en de **organisatie** para meter naam blauw druk.|
|Sjabloon voor Active Directory Domain Services|Resource Manager-sjabloon|Domein beheerders wachtwoord (Key Vault geheime naam)|De gebruikers naam van de domein beheerder. Moet overeenkomen met de waarde in Key Vault eigenschap van de **domein beheerder**van de **sjabloon** .|
|Sjabloon voor Active Directory Domain Services|Resource Manager-sjabloon|Domeinnaam|De naam van de Active Directory gemaakt door het voor beeld. De standaard waarde is _contoso.com_.|
|Sjabloon voor Active Directory Domain Services|Resource Manager-sjabloon|Gebruiker van domein beheerder|Gebruikers naam voor het AD-beheer account en voor het toevoegen van apparaten aan het AD-domein. De waarde van de eigenschap van de **ad-beheerders naam** moet overeenkomen met de **Key Vault sjabloon**. De standaard waarde is _domein beheerder-gebruiker_.|
|Sjabloon voor Active Directory Domain Services|Resource Manager-sjabloon|Wachtwoord van domeinbeheerder|Stel de Key Vault Details in voor het opslaan van het wacht woord. Geen standaard waarde en mag niet leeg zijn.|

## <a name="next-steps"></a>Volgende stappen

Nu u de stappen voor het implementeren van het voor beeld van de ISO 27001 Shared Services-blauw drukken hebt gecontroleerd, gaat u naar de volgende artikelen voor meer informatie over de architectuur en de beheer toewijzing:

> [!div class="nextstepaction"]
> [ISO 27001-blauw druk: overzicht](./index.md)
> van[ISO 27001 gedeelde services blauw druk-besturings element toewijzing](./control-mapping.md)

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levens duur van de blauw druk](../../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../../concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](../../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../../concepts/resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).
