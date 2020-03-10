---
title: 'Zelfstudie: Gegevensexports uit Azure Cost Management instellen en beheren'
description: In dit artikel leest u hoe u gegevensexports uit Azure Cost Management instelt en beheert, zodat u deze kunt gebruiken in externe systemen.
author: bandersmsft
ms.author: banders
ms.date: 02/12/2020
ms.topic: tutorial
ms.service: cost-management-billing
ms.reviewer: adwise
ms.custom: seodec18
ms.openlocfilehash: 04228a5f3f7e0b10e3cd881043af7861bb9fb347
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78251141"
---
# <a name="tutorial-create-and-manage-exported-data"></a>Zelfstudie: Geëxporteerde gegevens maken en beheren

Als u de zelfstudie over kostenanalyse hebt doorgenomen, bent u al bekend met het handmatig downloaden van gegevens uit Cost Management. U kunt echter ook een terugkerende taak instellen om uw Cost Management-gegevens automatisch dagelijks, wekelijks of maandelijks naar uw Azure-opslag te exporteren. De gegevens worden in CSV-indeling geëxporteerd en bevatten alle informatie die door Cost Management is verzameld. U kunt de geëxporteerde gegevens in uw Azure-opslag vervolgens in externe systemen laden en combineren met uw eigen gegevens. Externe systemen waarin de geëxporteerde gegevens kunnen worden gebruikt, zijn bijvoorbeeld dashboards of andere financiële systemen.

Bekijk de video [How to schedule exports to storage with Azure Cost Management](https://www.youtube.com/watch?v=rWa_xI1aRzo) (Exports naar opslag plannen met Azure Cost Management) over het maken van een geplande export van uw Azure-kostengegevens naar Azure Storage.

De voorbeelden in deze zelfstudie laten u stapsgewijs zien hoe u uw Cost Management-gegevens exporteert en vervolgens controleert of de export is gelukt.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een dagelijkse export uitvoeren
> * Controleren of de gegevens zijn geëxporteerd

## <a name="prerequisites"></a>Vereisten
Exporteren van gegevens is beschikbaar voor verschillende typen Azure-accounts, waaronder klanten met een [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) en een [Microsoft-klantovereenkomst](get-started-partners.md). Zie voor de volledige lijst met ondersteunde accounttypen [Gegevens van Azure Cost Management begrijpen](understand-cost-mgt-data.md). In abonnementen worden de volgende Azure-machtigingen, of bereiken, ondersteund voor het exporteren van gegevens door gebruikers en groepen. Zie [Bereiken begrijpen en gebruiken](understand-work-scopes.md) voor meer informatie over bereiken

- Eigenaar: eigenaren kunnen geplande exports voor een abonnement instellen, wijzigen en verwijderen.
- Inzender: inzenders kunnen hun eigen geplande exports instellen, wijzigen en verwijderen. Zij kunnen bovendien de naam wijzigen van geplande exports die door anderen zijn ingesteld.
- Lezer: lezers kunnen exports plannen waarvoor ze zijn gemachtigd.

Voor Azure-opslagaccounts:
- Voor het wijzigen van een geconfigureerd opslagaccount zijn schrijfmachtigingen vereist, ongeacht de machtiging voor het exporteren.
- Uw Azure-opslagaccount moet zijn geconfigureerd voor blob- of bestandsopslag.

Als u een nieuw abonnement hebt, kunt u niet direct de Cost Management-functies gebruiken. Het kan tot 48 uur duren voordat u alle Cost Management-functies kunt gebruiken.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure
Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com/).

## <a name="create-a-daily-export"></a>Een dagelijkse export uitvoeren

Als u een gegevensexport wilt maken of weergeven, of een export wilt plannen, opent u het gewenste bereik in de Azure-portal en selecteert u **Kostenanalyse** in het menu. Ga bijvoorbeeld naar **Abonnementen**, selecteer een abonnement in de lijst en selecteer vervolgens **Kostenanalyse** in het menu. Selecteer bovenaan de pagina Kostenanalyse **Exporteren** en kies vervolgens een exportoptie. Selecteer bijvoorbeeld **Export plannen**.  

> [!NOTE]
> - Naast abonnementen kunt u exports maken voor resourcegroepen, accounts, afdelingen en registraties. Zie [Bereiken begrijpen en gebruiken](understand-work-scopes.md) voor meer informatie over bereiken
>- Wanneer u als partner bent aangemeld bij het bereik van het factureringsaccount of bij de tenant van een klant, kunt u gegevens exporteren naar een Azure Storage-account dat is gekoppeld aan uw partneropslagaccount. U moet hiervoor wel een actief abonnement hebben in uw CSP-tenant.
>


Selecteer **Toevoegen**, typ een naam voor de export en selecteer de optie **Dagelijkse export van kosten (maand tot heden)** . Selecteer **Next**.

![Voorbeeld van een nieuwe export, waarbij het type export wordt weergegeven](./media/tutorial-export-acm-data/basics_exports.png)

Geef het abonnement voor uw Azure opslagaccount op en selecteer vervolgens uw opslagaccount.  Geef de opslagcontainer en het pad naar de map op waar u het exportbestand wilt opslaan. Selecteer **Next**.

![Voorbeeld van een nieuwe export, waarbij de opslagaccountdetails worden weergegeven](./media/tutorial-export-acm-data/storage_exports.png)

Controleer de gegevens van de export en selecteer **Maken**.

Uw nieuwe export wordt weergegeven in de lijst met exports. Nieuwe exports zijn standaard ingeschakeld. Als u een geplande export wilt uitschakelen of verwijderen, selecteert u een item in de lijst en selecteert u vervolgens **Uitschakelen** of **Verwijderen**.

Het kan één tot twee uur duren voordat de export voor het eerst wordt uitgevoerd. Het kan echter maximaal vier uur duren voordat er in het exportbestand gegevens worden opgenomen.

### <a name="export-schedule"></a>Exportschema

Geplande exports worden beïnvloed door het tijdstip en de dag van de week waarop u de export in eerste instantie maakt. Wanneer u een geplande export maakt, wordt de export voor elk volgend exemplaar van de export met dezelfde frequentie uitgevoerd. Als voor een exportset van maand tot heden bijvoorbeeld een dagelijkse frequentie is ingesteld, wordt de export dagelijks uitgevoerd. Bij een wekelijkse export wordt de export elke week op dezelfde dag uitgevoerd zoals dit is gepland. De exacte levertijd van de export kan niet worden gegarandeerd en de geëxporteerde gegevens zijn binnen vier uur na de uitvoering beschikbaar.
Voor elke export wordt een nieuw bestand gemaakt. Oudere exports worden dus niet overschreven.

Er zijn twee soorten exportopties:

**Dagelijkse export van de maandkosten tot heden**: de eerste uitvoer wordt onmiddellijk uitgevoerd. Volgende exports worden de volgende dag op hetzelfde tijdstip als de eerste export uitgevoerd. De meest recente gegevens worden uit de vorige dagelijkse exports samengevoegd.

**Aangepast**: hiermee kunt u wekelijkse en maandelijkse exports plannen met week-tot-datum- en maand-tot-heden-opties. *De eerste uitvoer wordt onmiddellijk uitgevoerd.*

Als u een Betalen per gebruik-, MSDN- of Visual Studio-abonnement hebt, komt de factureringsperiode van uw factuur mogelijk niet overeen met de kalendermaand. Voor deze typen abonnementen en resourcegroepen kunt u een export maken die is afgestemd op uw factuurperiode of op kalendermaanden. Als u een export wilt maken die is afgestemd op uw factuurmaand, gaat u naar **Aangepast** en selecteert u **Factureringsperiode tot heden**.  Als u een export wilt maken die is afgestemd op de kalendermaand, selecteert u **Maand tot heden**.
>
>

![Nieuwe export: het tabblad Basis toont een aangepaste wekelijkse week-tot-heden-selectie](./media/tutorial-export-acm-data/tutorial-export-schedule-weekly-week-to-date.png)

## <a name="verify-that-data-is-collected"></a>Controleren of de gegevens zijn geëxporteerd

U kunt eenvoudig controleren of uw Cost Management-gegevens daadwerkelijk zijn geëxporteerd door het CSV-exportbestand in Azure Storage Explorer te bekijken.

Selecteer de naam van het opslagaccount in de lijst met exports. Selecteer Openen in Explorer op de pagina van het opslagaccount. Als u een bevestigingsvenster ziet, selecteert u **Ja** om het bestand te openen in Azure Storage Explorer.

![Opslagaccountpagina, waarbij voorbeeldinformatie en een koppeling naar Openen in Explorer worden weergegeven](./media/tutorial-export-acm-data/storage-account-page.png)

Ga in Storage Explorer naar de container die u wilt openen en selecteer de map voor de huidige maand. U ziet nu een lijst met CSV-bestanden. Selecteer het gewenste bestand en selecteer **Openen**.

![Voorbeeldinformatie weergegeven in Storage Explorer](./media/tutorial-export-acm-data/storage-explorer.png)

Het bestand wordt geopend met het programma of de toepassing waarmee bestanden met de extensie CSV standaard worden geopend. Dit is een voorbeeld met Excel.

![Voorbeeld van geëxporteerde CSV-gegevens, weergegeven in Excel](./media/tutorial-export-acm-data/example-export-data.png)


## <a name="access-exported-data-from-other-systems"></a>Geëxporteerde gegevens gebruiken in externe systemen

Het exporteren van Cost Management-gegevens heeft vaak als doel om deze gegevens in een extern systeem te gebruiken. Denk bijvoorbeeld aan een dashboard of een ander financieel systeem. Omdat al deze systemen sterk van elkaar verschillen, heeft het weinig zin om hier een voorbeeld van te laten zien.  In [Introduction to Azure Storage](../../storage/common/storage-introduction.md) (Inleiding tot Azure Storage) kunt u echter aan de slag met het openen van uw gegevens in de door u gebruikte toepassingen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Een dagelijkse export uitvoeren
> * Controleren of de gegevens zijn geëxporteerd

Ga naar de volgende zelfstudie, waarin u leert om de efficiëntie te optimaliseren en te verbeteren door inactieve en onderbenutte resources te identificeren.

> [!div class="nextstepaction"]
> [Aanbevelingen bekijken en hier actie op ondernemen](tutorial-acm-opt-recommendations.md)
