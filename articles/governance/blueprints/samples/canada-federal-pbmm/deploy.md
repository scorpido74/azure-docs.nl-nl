---
title: Voor beeld van Canada Federal PBMM Blue Print-stappen implementeren
description: Implementeer stappen voor het voor beeld van de Canada Federal PBMM blauw druk, inclusief blauw druk artefact parameter Details.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/05/2019
ms.topic: conceptual
ms.service: blueprints
ms.openlocfilehash: 22806f497283c1885446eadd5f444dacf3769c12
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73163319"
---
# <a name="deploy-the-canada-federal-pbmm-blueprint-samples"></a>De voor beelden van de Canada Federal PBMM-blauw druk implementeren

Als u de voor beelden van Canada Federal PBMM blauw drukken wilt implementeren, moet u de volgende stappen uitvoeren:

> [!div class="checklist"]
> - Een nieuwe blauw druk maken op basis van het voor beeld
> - Uw kopie van het voor beeld markeren als **gepubliceerd**
> - Uw kopie van de blauw druk toewijzen aan een bestaand abonnement

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free) aan voordat u begint.

## <a name="create-blueprint-from-sample"></a>Blauw druk maken op basis van voor beeld

Implementeer eerst het voor beeld van de blauw druk door een nieuwe blauw druk in uw omgeving te maken met behulp van het voor beeld als een starter.

1. Selecteer **alle services** , zoek naar en selecteer **beleid** in het linkerdeel venster. Selecteer **blauw drukken**op de pagina **beleid** .

1. Op de pagina **aan** de slag aan de linkerkant selecteert u de knop **maken** onder _een blauw druk maken_.

1. Zoek het voor beeld van **Canada Federal PBMM** blauw druk onder _andere voor beelden_ en selecteer **dit voor beeld gebruiken**.

1. Voer de _basis beginselen_ van het voor beeld van de blauw druk in:

   - **Blauw druk-naam**: Geef een naam op voor uw kopie van het voor beeld van de blauw druk.
   - **Locatie van definitie**: gebruik het weglatings teken en selecteer de beheer groep om uw kopie van het voor beeld op te slaan.

1. Selecteer het tabblad _artefacten_ boven aan de pagina of **volgende: artefacten** aan de onderkant van de pagina.

1. Bekijk de lijst met artefacten die deel uitmaken van het voor beeld van de blauw druk. Veel van de artefacten hebben para meters die later worden gedefinieerd. Selecteer **concept opslaan** wanneer u klaar bent met het bekijken van het voor beeld van de blauw druk.

## <a name="publish-the-sample-copy"></a>De voorbeeld kopie publiceren

Uw kopie van het voor beeld van de blauw druk is nu in uw omgeving gemaakt. Deze wordt gemaakt in de **concept** modus en moet worden **gepubliceerd** voordat deze kan worden toegewezen en geïmplementeerd. De kopie van het voor beeld van de blauw druk kan worden aangepast aan uw omgeving en behoeften, maar deze wijziging kan worden verplaatst van de standaard waarde.

1. Selecteer **alle services** , zoek naar en selecteer **beleid** in het linkerdeel venster. Selecteer **blauw drukken**op de pagina **beleid** .

1. Selecteer de pagina met **definities van blauw** drukken aan de linkerkant. Gebruik de filters om uw kopie van het voor beeld van de blauw druk te vinden en selecteer deze.

1. Selecteer **blauw druk** aan de bovenkant van de pagina publiceren. Geef op de pagina Nieuw aan de rechter kant een **versie** op voor uw kopie van het voor beeld van de blauw druk. Deze eigenschap is handig als u later een wijziging aanbrengt. Geef **wijzigings notities** op, zoals ' eerste versie gepubliceerd vanuit het voor beeld van Canada Federal PBMM Blue. ' Selecteer vervolgens **publiceren** onder aan de pagina.

## <a name="assign-the-sample-copy"></a>De voorbeeld kopie toewijzen

Zodra de kopie van het voor beeld van de blauw druk is **gepubliceerd**, kan deze worden toegewezen aan een abonnement in de beheer groep waarop het is opgeslagen. In deze stap worden para meters opgegeven om elke implementatie van de kopie van het voor beeld van de blauw druk te maken.

1. Selecteer **alle services** , zoek naar en selecteer **beleid** in het linkerdeel venster. Selecteer **blauw drukken**op de pagina **beleid** .

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

   - Artefact parameters

     De in deze sectie gedefinieerde para meters zijn van toepassing op het artefact waaronder het is gedefinieerd. Deze para meters zijn [dynamische para meters](../../concepts/parameters.md#dynamic-parameters) , omdat ze zijn gedefinieerd tijdens de toewijzing van de blauw druk. Zie [artefact parameters Table](#artifact-parameters-table)voor een volledige lijst of artefact parameters en de bijbehorende beschrijvingen.

1. Zodra alle para meters zijn ingevoerd, selecteert u aan de onderkant van de pagina **toewijzen** . De blauw druk toewijzing wordt gemaakt en de implementatie van artefacten begint. De implementatie duurt ongeveer een uur. Als u de status van de implementatie wilt controleren, opent u de blauw druk-toewijzing.

> [!WARNING]
> De Azure-blauw drukken-service en de ingebouwde blauw druk-voor beelden zijn **gratis**. Azure-resources zijn [prijs per product](https://azure.microsoft.com/pricing/). Gebruik de [prijs calculator](https://azure.microsoft.com/pricing/calculator/) om een schatting te maken van de kosten van het uitvoeren van resources die worden geïmplementeerd door deze blauw druk-voor beeld.

## <a name="artifact-parameters-table"></a>Tabel artefact parameters

De volgende tabel geeft een lijst van de para meters van de blauw druk-artefact:

Artefact naam|Type artefact|Parameternaam|Beschrijving|
|-|-|-|-|
|\[preview\]: Log Analytics agent voor virtuele Linux-machines implementeren |Beleidstoewijzing |Log Analytics-werk ruimte voor Linux Vm's |Zie [een log Analytics-werk ruimte maken in de Azure Portal](../../../../azure-monitor/learn/quick-create-workspace.md)voor meer informatie. |
|\[preview\]: Log Analytics agent voor virtuele Linux-machines implementeren |Beleidstoewijzing |Optioneel: lijst met VM-installatie kopieën met ondersteund Linux-besturings systeem om toe te voegen aan het bereik |Een lege matrix kan worden gebruikt om geen optionele para meters aan te duiden: `[]` |
|\[preview\]: Log Analytics-agent implementeren voor Windows-Vm's |Beleidstoewijzing |Optioneel: lijst met VM-installatie kopieën die een ondersteund Windows-besturings systeem hebben om toe te voegen aan het bereik |Een lege matrix kan worden gebruikt om geen optionele para meters aan te duiden: `[]` |
|\[preview\]: Log Analytics-agent implementeren voor Windows-Vm's |Beleidstoewijzing |Log Analytics-werk ruimte voor Windows-Vm's |Zie [een log Analytics-werk ruimte maken in de Azure Portal](../../../../azure-monitor/learn/quick-create-workspace.md)voor meer informatie. |
|\[preview\]: controle van de Federal PBMM Controls en implementeer specifieke VM-extensies ter ondersteuning van de controle vereisten |Beleidstoewijzing |Werk ruimte-ID Log Analytics waarvoor Vm's moeten worden geconfigureerd |Dit is de ID (GUID) van de Log Analytics-werk ruimte waarvoor de Vm's moeten worden geconfigureerd. |
|\[preview\]: controle van de Federal PBMM Controls en implementeer specifieke VM-extensies ter ondersteuning van de controle vereisten |Beleidstoewijzing |Lijst met resource typen waarvoor Diagnostische logboeken moeten zijn ingeschakeld |Lijst met resource typen om te controleren of de diagnostische logboek instelling niet is ingeschakeld. Acceptabele waarden zijn te vinden in [Azure monitor Diagnostische logboeken schema's](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type). |
|\[preview\]: controle van de Federal PBMM Controls en implementeer specifieke VM-extensies ter ondersteuning van de controle vereisten |Beleidstoewijzing |Groep Administrators |Gegroepeerd. Voorbeeld: `Administrator; myUser1; myUser2` |
|\[preview\]: controle van de Federal PBMM Controls en implementeer specifieke VM-extensies ter ondersteuning van de controle vereisten |Beleidstoewijzing |Lijst met gebruikers die moeten worden opgenomen in de Windows VM-Beheerders groep |Een door punt komma's gescheiden lijst met leden die moeten worden opgenomen in de lokale groep Administrators. Voorbeeld: `Administrator; myUser1; myUser2` |
|Geavanceerde beveiliging tegen bedreigingen implementeren voor opslag accounts |Beleidstoewijzing |Effect |Informatie over beleids effecten vindt u in [inzicht Azure Policy effecten](../../../policy/concepts/effects.md). |
|Controle op SQL-servers implementeren |Beleidstoewijzing |De waarde in dagen van de Bewaar periode (0 geeft een onbeperkte retentie aan) |Bewaar dagen (optioneel, _180_ dagen als niet opgegeven) |
|Controle op SQL-servers implementeren |Beleidstoewijzing |Naam van de resource groep voor het opslag account voor SQL Server-controle |Met controle worden database gebeurtenissen geschreven naar een audit logboek in uw Azure Storage-account (er wordt een opslag account gemaakt in elke regio waarin een SQL Server wordt gemaakt dat wordt gedeeld door alle servers in die regio). Belang rijk: voor een juiste werking van de controle worden de resource groep of de opslag accounts niet verwijderd of de naam ervan niet gewijzigd. |
|Diagnostische instellingen voor netwerk beveiligings groepen implementeren |Beleidstoewijzing |Voor voegsel van het opslag account voor diagnostische gegevens van netwerk beveiligings groepen |Dit voor voegsel wordt gecombineerd met de locatie van de netwerk beveiligings groep om de naam van het gemaakte opslag account op te maken. |
|Diagnostische instellingen voor netwerk beveiligings groepen implementeren |Beleidstoewijzing |Naam van de resource groep voor het opslag account voor diagnostische gegevens van de netwerk beveiligings groep (moet bestaan) |De resource groep waarin het opslag account is gemaakt. Deze resource groep moet al bestaan. |

## <a name="next-steps"></a>Volgende stappen

Nu u de stappen voor het implementeren van het voor beeld van Canada Federal PBMM hebt bekeken, gaat u naar de volgende artikelen voor meer informatie over het overzicht en de toewijzing van het besturings element:

> [!div class="nextstepaction"]
> [Canada Federal PBMM blauw drukken-overzicht](./index.md)
> [Canada Federal PBMM blauw drukken-controle toewijzing](./control-mapping.md)

Aanvullende artikelen over blauw drukken en hoe u deze kunt gebruiken:

- Meer informatie over de [levenscyclus van een blauwdruk](../../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../../concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](../../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../../concepts/resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).