---
title: 'Zelf studie: geëxporteerde gegevens maken en beheren vanuit Azure Cost Management'
description: Dit artikel laat u zien hoe u geëxporteerde Azure Cost Management gegevens kunt maken en beheren, zodat u deze kunt gebruiken in externe systemen.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/14/2020
ms.topic: tutorial
ms.service: cost-management-billing
manager: jasonh
ms.custom: seodec18
ms.openlocfilehash: 76ee5aba0f1d0769e15a5969409dfef2a018e477
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75987283"
---
# <a name="tutorial-create-and-manage-exported-data"></a>Zelfstudie: Gegevensexports instellen en beheren

Als u de zelfstudie over kostenanalyse hebt doorgenomen, bent u al bekend met het handmatig downloaden van gegevens uit Cost Management. U kunt echter ook een terugkerende taak instellen om uw Cost Management-gegevens automatisch dagelijks, wekelijks of maandelijks naar uw Azure-opslag te exporteren. De gegevens worden in CSV-indeling geëxporteerd en bevatten alle informatie die door Cost Management is verzameld. U kunt de geëxporteerde gegevens in uw Azure-opslag vervolgens in externe systemen laden en combineren met uw eigen gegevens. Externe systemen waarin de geëxporteerde gegevens kunnen worden gebruikt, zijn bijvoorbeeld dashboards of andere financiële systemen.

Bekijk [hoe u exporteert naar opslag met Azure Cost Management](https://www.youtube.com/watch?v=rWa_xI1aRzo) video over het maken van een geplande export van uw Azure-kosten gegevens naar Azure Storage.

De voorbeelden in deze zelfstudie laten u stapsgewijs zien hoe u uw Cost Management-gegevens exporteert en vervolgens controleert of de export is gelukt.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een dagelijkse export uitvoeren
> * Controleren of de gegevens zijn geëxporteerd

## <a name="prerequisites"></a>Vereisten
Het exporteren van gegevens is beschikbaar voor diverse typen Azure-accounts, waaronder [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) en [micro soft](get-started-partners.md) -klanten overeenkomst. Zie [Gegevens van kostenbeheer begrijpen](understand-cost-mgt-data.md) voor de volledige lijst met ondersteunde accounttypen. De volgende Azure-machtigingen of-bereiken worden ondersteund per abonnement voor het exporteren van gegevens per gebruiker en groep. Zie voor meer informatie over bereiken [begrijpen en werken met scopes](understand-work-scopes.md).

- Eigenaar: eigenaren kunnen geplande exports voor een abonnement instellen, wijzigen en verwijderen.
- Inzender: inzenders kunnen hun eigen geplande exports instellen, wijzigen en verwijderen. Zij kunnen bovendien de naam wijzigen van geplande exports die door anderen zijn ingesteld.
- Lezer: lezers kunnen exports plannen waarvoor ze zijn gemachtigd.

Voor Azure-opslagaccounts:
- Voor het wijzigen van een geconfigureerd opslagaccount zijn schrijfmachtigingen vereist, ongeacht de machtiging voor het exporteren.
- Uw Azure-opslagaccount moet zijn geconfigureerd voor blob- of bestandsopslag.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure
Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com/).

## <a name="create-a-daily-export"></a>Een dagelijkse export uitvoeren

Als u een gegevens export wilt maken of weer geven of een export wilt plannen, opent u het gewenste bereik in de Azure Portal en selecteert u **kosten analyse** in het menu. Ga bijvoorbeeld naar **abonnementen**, selecteer een abonnement in de lijst en selecteer vervolgens **kosten analyse** in het menu. Selecteer bovenaan de pagina Kostenanalyse de optie **Exporteren** en kies vervolgens een optie voor het exporteren. Selecteer bijvoorbeeld **planning exporteren**.  

> [!NOTE]
> - U kunt exports maken voor abonnementen, maar ook voor resourcegroepen, accounts, afdelingen en inschrijvingen. Zie voor meer informatie over bereiken [begrijpen en werken met scopes](understand-work-scopes.md).
>- Wanneer u bent aangemeld als een partner op het bereik van de facturerings account of op de Tenant van een klant, kunt u gegevens exporteren naar een Azure Storage-account dat is gekoppeld aan uw partner-opslag account. U moet echter een actief abonnement hebben in uw CSP-Tenant.
>


Selecteer **toevoegen**, typ een naam voor de export en selecteer vervolgens de optie **dagelijks exporteren van de kosten voor de maand tot heden** . Selecteer **Next**.

![Voorbeeld van een nieuwe export, waarbij het type export wordt weergegeven](./media/tutorial-export-acm-data/basics_exports.png)

Geef het abonnement voor uw Azure opslagaccount op en selecteer vervolgens uw opslagaccount.  Geef de opslagcontainer en het pad naar de map op waar u het exportbestand wilt opslaan. Selecteer **Next**.

![Voorbeeld van een nieuwe export, waarbij de opslagaccountdetails worden weergegeven](./media/tutorial-export-acm-data/storage_exports.png)

Controleer de details van uw export en selecteer **Maken**.

Uw nieuwe export wordt weergegeven in de lijst met exports. Nieuwe exports zijn standaard ingeschakeld. Als u een geplande export wilt uitschakelen of verwijderen, selecteert u een item in de lijst en selecteert u vervolgens **uitschakelen** of **verwijderen**.

Het kan één tot twee uur duren voordat de export voor het eerst wordt uitgevoerd. Het kan echter maximaal vier uur duren voordat er in het exportbestand gegevens worden opgenomen.

### <a name="export-schedule"></a>Exportschema

Geplande exports worden beïnvloed door het tijdstip en de dag van de week waarop u de export in eerste instantie maakt. Wanneer u een geplande export maakt, wordt de export uitgevoerd met dezelfde frequentie voor elk volgende export exemplaar. Bijvoorbeeld, voor een exportset van maand tot heden, wordt de export dagelijks uitgevoerd. Net als bij een wekelijkse export wordt de export elke week op dezelfde dag uitgevoerd als deze wordt gepland. De exacte lever tijd van de export is niet gegarandeerd en de geëxporteerde gegevens zijn binnen vier uur na de uitvoerings tijd beschikbaar. "
Voor elke export wordt een nieuw bestand gemaakt. Oudere exports worden dus niet overschreven.

Er zijn twee typen export opties:

**Dagelijkse export van de maandkosten tot heden**: de eerste uitvoer wordt onmiddellijk uitgevoerd. Volgende exports worden de volgende dag op hetzelfde tijdstip als de eerste export uitgevoerd. De meest recente gegevens worden uit de vorige dagelijkse exports samengevoegd.

**Aangepast**: hiermee kunt u wekelijkse en maandelijkse exports plannen met week-tot-datum- en maand-tot-heden-opties. *De eerste uitvoer wordt onmiddellijk uitgevoerd.*

Als u een abonnement op basis van betalen per gebruik, een MSDN- of Visual Studio-abonnement hebt, komt de factureringsperiode van uw factuur mogelijk niet overeen met de kalendermaand. Voor deze typen abonnementen en resourcegroepen kunt u een export maken die is afgestemd op uw factuurperiode of de kalendermaanden. Als u een export wilt maken die is afgestemd op uw factuur maand, gaat u naar **aangepast**en selecteert u **facturering-periode-tot-datum**.  Als u een export wilt maken die is afgestemd op de kalender maand, selecteert u **maand tot heden**.
>
>

![Nieuwe export: het tabblad Basis toont een aangepaste wekelijkse week-tot-heden-selectie](./media/tutorial-export-acm-data/tutorial-export-schedule-weekly-week-to-date.png)

## <a name="verify-that-data-is-collected"></a>Controleren of de gegevens zijn geëxporteerd

U kunt eenvoudig controleren of uw Cost Management-gegevens daadwerkelijk zijn geëxporteerd door het CSV-exportbestand in Azure Storage Explorer te bekijken.

Selecteer in de lijst met exports de naam van het opslagaccount. Selecteer op de pagina opslag account de optie openen in Verkenner. Als u een bevestigings venster ziet, selecteert u **Ja** om het bestand te openen in azure Storage Explorer.

![Opslagaccountpagina, waarbij voorbeeldinformatie en een koppeling naar Openen in Explorer worden weergegeven](./media/tutorial-export-acm-data/storage-account-page.png)

Ga in Storage Explorer naar de container die u wilt openen en selecteer de map voor de huidige maand. U ziet nu een lijst met CSV-bestanden. Selecteer er een en selecteer **openen**.

![Voorbeeldinformatie weergegeven in Storage Explorer](./media/tutorial-export-acm-data/storage-explorer.png)

Het bestand wordt geopend met het programma of de toepassing waarmee bestanden met de extensie CSV standaard worden geopend. Dit is een voorbeeld met Excel.

![Voorbeeld van geëxporteerde CSV-gegevens, weergegeven in Excel](./media/tutorial-export-acm-data/example-export-data.png)


## <a name="access-exported-data-from-other-systems"></a>Geëxporteerde gegevens gebruiken in externe systemen

Het exporteren van Cost Management-gegevens heeft vaak als doel om deze gegevens in een extern systeem te gebruiken. Denk bijvoorbeeld aan een dashboard of een ander financieel systeem. Omdat al deze systemen sterk van elkaar verschillen, heeft het weinig zin om hier een voorbeeld van te laten zien.  In [Introduction to Azure Storage](../../storage/common/storage-introduction.md) (Inleiding tot Azure Storage) kunt u echter aan de slag met het openen van uw gegevens in de door u gebruikte toepassingen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een dagelijkse export uitvoeren
> * Controleren of de gegevens zijn geëxporteerd

Ga naar de volgende zelfstudie, waarin u leert om de efficiëntie te optimaliseren en te verbeteren door inactieve en onderbenutte resources te identificeren.

> [!div class="nextstepaction"]
> [Aanbevelingen bekijken en hier actie op ondernemen](tutorial-acm-opt-recommendations.md)
