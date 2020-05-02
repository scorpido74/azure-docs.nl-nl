---
title: Voor beeld van Azure Security Bench Mark-blauw punt implementeren
description: Implementeer stappen voor het voor beeld van Azure Security Bench Mark blauw druk, inclusief blauw druk artefact parameter Details.
ms.date: 05/01/2020
ms.topic: sample
ms.openlocfilehash: 0e967e2217b90e036d809ce7baf500c3bc791c6f
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82725964"
---
# <a name="deploy-the-azure-security-benchmark-blueprint-sample"></a>Het voor beeld van Azure Security Bench Mark blauw drukken

De volgende stappen moeten worden uitgevoerd om het voor beeld van Azure Blues voor Azure Security Bench Mark te implementeren:

> [!div class="checklist"]
> - Een nieuwe blauw druk maken op basis van het voor beeld
> - Uw kopie van het voor beeld markeren als **gepubliceerd**
> - Uw kopie van de blauw druk toewijzen aan een bestaand abonnement

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free) aan voordat u begint.

## <a name="create-blueprint-from-sample"></a>Blauw druk maken op basis van voor beeld

Implementeer eerst het voor beeld van de blauw druk door een nieuwe blauw druk in uw omgeving te maken met behulp van het voor beeld als een starter.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Op de pagina **aan** de slag aan de linkerkant selecteert u de knop **maken** onder _een blauw druk maken_.

1. Zoek het voor beeld van **Azure Security Bench Mark** blauw druk onder _andere voor beelden_ en selecteer op de naam om dit voor beeld te selecteren.

1. Voer de _basis beginselen_ van het voor beeld van de blauw druk in:

   - **Blauw druk-naam**: Geef een naam op voor uw kopie van het voor beeld van Azure Security Bench Mark.
   - **Locatie van definitie**: gebruik het weglatings teken en selecteer de beheer groep om uw kopie van het voor beeld op te slaan.

1. Selecteer het tabblad _artefacten_ boven aan de pagina of **volgende: artefacten** aan de onderkant van de pagina.

1. Bekijk de lijst met artefacten die deel uitmaken van het voor beeld van de blauw druk. Veel van de artefacten hebben para meters die later worden gedefinieerd. Selecteer **concept opslaan** wanneer u klaar bent met het bekijken van het voor beeld van de blauw druk.

## <a name="publish-the-sample-copy"></a>De voorbeeld kopie publiceren

Uw kopie van het voor beeld van de blauw druk is nu in uw omgeving gemaakt. Deze wordt gemaakt in de **concept** modus en moet worden **gepubliceerd** voordat deze kan worden toegewezen en geïmplementeerd. De kopie van het voor beeld van de blauw druk kan worden aangepast aan uw omgeving en behoeften, maar deze wijziging kan worden verplaatst van uitlijning met Azure Security Bench Mark-aanbevelingen.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer de pagina met **definities van blauw** drukken aan de linkerkant. Gebruik de filters om uw kopie van het voor beeld van de blauw druk te vinden en selecteer deze.

1. Selecteer **blauw druk** aan de bovenkant van de pagina publiceren. Geef op de pagina Nieuw aan de rechter kant een **versie** op voor uw kopie van het voor beeld van de blauw druk. Deze eigenschap is handig als u later een wijziging aanbrengt. Geef **wijzigings notities** op, zoals ' eerste versie gepubliceerd vanuit het voor beeld van Azure Security Bench Mark. ' Selecteer vervolgens **publiceren** onder aan de pagina.

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
     - **Locatie**: Selecteer een regio voor de beheerde identiteit die u wilt maken in. Azure Blueprint gebruikt deze beheerde identiteit om alle artefacten in de toegewezen blauwdruk te implementeren. Zie [beheerde identiteiten voor Azure-resources](../../../../active-directory/managed-identities-azure-resources/overview.md)voor meer informatie.
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

|Artefact naam|Type artefact|Parameternaam|Beschrijving|
|-|-|-|-|
|Aanbevelingen voor Azure Security Bench Mark controleren en specifieke ondersteunende VM-extensies implementeren|Beleidstoewijzing|
Lijst met gebruikers die zijn uitgesloten van de Windows VM-Beheerders groep|Een door punt komma's gescheiden lijst met leden die moeten worden uitgesloten van de lokale groep Administrators. Bijvoorbeeld: beheerder; myUser1; myUser2|
|Aanbevelingen voor Azure Security Bench Mark controleren en specifieke ondersteunende VM-extensies implementeren|Beleidstoewijzing|Lijst met gebruikers die moeten worden opgenomen in de Windows VM-Beheerders groep|Een door punt komma's gescheiden lijst met leden die moeten worden opgenomen in de lokale groep Administrators. Bijvoorbeeld: beheerder; myUser1; myUser2|
|Aanbevelingen voor Azure Security Bench Mark controleren en specifieke ondersteunende VM-extensies implementeren|Beleidstoewijzing|Lijst met gebruikers die Windows VM-Beheerders groep *alleen* mag bevatten|Een door punt komma's gescheiden lijst met alle verwachte leden van de lokale groep Administrators. Bijvoorbeeld: beheerder; myUser1; myUser2|
|Aanbevelingen voor Azure Security Bench Mark controleren en specifieke ondersteunende VM-extensies implementeren|Beleidstoewijzing|Lijst met regio's waar Network Watcher moet worden ingeschakeld|Als u een volledige lijst met regio's wilt zien, gebruikt u Get-AzLocation|
|Aanbevelingen voor Azure Security Bench Mark controleren en specifieke ondersteunende VM-extensies implementeren|Beleidstoewijzing|Virtueel netwerk waarop Vm's moeten worden aangesloten|Voor beeld:/subscriptions/YourSubscriptionId/resourceGroups/YourResourceGroupName/providers/Microsoft.Network/virtualNetworks/Name|
|Aanbevelingen voor Azure Security Bench Mark controleren en specifieke ondersteunende VM-extensies implementeren|Beleidstoewijzing|Netwerk gateway die door virtuele netwerken moet worden gebruikt|Voor beeld:/subscriptions/YourSubscriptionId/resourceGroups/YourResourceGroup/providers/Microsoft.Network/virtualNetworkGateways/Name|
|Aanbevelingen voor Azure Security Bench Mark controleren en specifieke ondersteunende VM-extensies implementeren|Beleidstoewijzing|Lijst met werk ruimte-Id's waar Log Analytics agents verbinding mee moet maken|Een door punt komma's gescheiden lijst met de werk ruimte-Id's waarmee de Log Analytics-agent moet worden verbonden|
|Aanbevelingen voor Azure Security Bench Mark controleren en specifieke ondersteunende VM-extensies implementeren|Beleidstoewijzing|Lijst met resource typen waarvoor Diagnostische logboeken moeten zijn ingeschakeld|Diagnostische instelling voor geselecteerde bron typen controleren|
|Aanbevelingen voor Azure Security Bench Mark controleren en specifieke ondersteunende VM-extensies implementeren|Beleidstoewijzing|Meest recente PHP-versie|Nieuwste ondersteunde PHP-versie voor App Services|
|Aanbevelingen voor Azure Security Bench Mark controleren en specifieke ondersteunende VM-extensies implementeren|Beleidstoewijzing|Nieuwste Java-versie|Nieuwste ondersteunde Java-versie voor App Services|
|Aanbevelingen voor Azure Security Bench Mark controleren en specifieke ondersteunende VM-extensies implementeren|Beleidstoewijzing|Meest recente Windows python-versie|Meest recente ondersteunde python-versie voor App Services|
|Aanbevelingen voor Azure Security Bench Mark controleren en specifieke ondersteunende VM-extensies implementeren|Beleidstoewijzing|Meest recente versie van Linux python|Meest recente ondersteunde python-versie voor App Services|

## <a name="next-steps"></a>Volgende stappen

Nu u de stappen voor het implementeren van het voor beeld van Azure Security Bench Mark Blue hebt bekeken, gaat u naar de volgende artikelen voor meer informatie over de blauw druk en de beheer toewijzing:

> [!div class="nextstepaction"]
> [Voor beeld van Azure Security Bench Mark-overzicht](./index.md)
> [Azure Security Bench Mark-blauw druk-besturings element toewijzing](./control-mapping.md)

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van een blauwdruk](../../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../../concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](../../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../../concepts/resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).
