---
title: 'Zelf studie: geëxporteerde gegevens maken en beheren vanuit Azure Cost Management'
description: Dit artikel laat u zien hoe u geëxporteerde Azure Cost Management gegevens kunt maken en beheren, zodat u deze kunt gebruiken in externe systemen.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/12/2019
ms.topic: tutorial
ms.service: cost-management-billing
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: a462b3d165a596673049abbbb8b5b8d346f5fc9d
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229832"
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
Exporteren van gegevens is beschikbaar voor verschillende typen Azure-accounts, met inbegrip van [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)-klanten. Zie voor de volledige lijst met ondersteunde accounttypen [Gegevens van Azure Cost Management begrijpen](understand-cost-mgt-data.md). De volgende Azure-machtigingen of-bereiken worden ondersteund per abonnement voor het exporteren van gegevens per gebruiker en groep. Zie voor meer informatie over bereiken [begrijpen en werken met scopes](understand-work-scopes.md).

- Eigenaar: eigenaren kunnen geplande exports voor een abonnement instellen, wijzigen en verwijderen.
- Inzender: inzenders kunnen hun eigen geplande exports instellen, wijzigen en verwijderen. Zij kunnen bovendien de naam wijzigen van geplande exports die door anderen zijn ingesteld.
- Lezer: lezers kunnen exports plannen waarvoor ze zijn gemachtigd.

Voor Azure-opslagaccounts:
- Voor het wijzigen van een geconfigureerd opslagaccount zijn schrijfmachtigingen vereist, ongeacht de machtiging voor het exporteren.
- Uw Azure-opslagaccount moet zijn geconfigureerd voor blob- of bestandsopslag.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure
Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com/).

## <a name="create-a-daily-export"></a>Een dagelijkse export uitvoeren

Als u een gegevens export wilt maken of weer geven of een export wilt plannen, opent u het gewenste bereik in de Azure Portal en selecteert u **kosten analyse** in het menu. Ga bijvoorbeeld naar **abonnementen**, selecteer een abonnement in de lijst en selecteer vervolgens **kosten analyse** in het menu. Klik boven aan de pagina kosten analyse op **exporteren** en kies vervolgens een export optie. Klik bijvoorbeeld op **planning exporteren**.  

> [!NOTE]
> - Naast abonnementen kunt u exports maken voor resource groepen, accounts, afdelingen en registraties. Zie voor meer informatie over bereiken [begrijpen en werken met scopes](understand-work-scopes.md).
>- Wanneer u bent aangemeld als een partner op het bereik van de facturerings account of op de Tenant van een klant, kunt u gegevens exporteren naar een Azure Storage-account dat is gekoppeld aan uw partner-opslag account. U moet echter een actief abonnement hebben in uw CSP-Tenant.
>


Klik op **toevoegen**, typ een naam voor de export en selecteer vervolgens de optie **dagelijks exporteren van de kosten voor de maand tot heden** . Klik op **Volgende**.

![Voorbeeld van een nieuwe export, waarbij het type export wordt weergegeven](./media/tutorial-export-acm-data/basics_exports.png)

Geef het abonnement voor uw Azure opslagaccount op en selecteer vervolgens uw opslagaccount.  Geef de opslagcontainer en het pad naar de map op waar u het exportbestand wilt opslaan.  Klik op **Volgende**.

![Voorbeeld van een nieuwe export, waarbij de opslagaccountdetails worden weergegeven](./media/tutorial-export-acm-data/storage_exports.png)

Controleer de details van uw export en klik op **Maken**.

Uw nieuwe export wordt weergegeven in de lijst met exports. Nieuwe exports zijn standaard ingeschakeld. Als u een geplande export wilt uitschakelen of verwijderen, klikt u op het betreffende item in de lijst en vervolgens op **Uitschakelen** of **Verwijderen**.

Het kan één tot twee uur duren voordat de export voor het eerst wordt uitgevoerd. Het kan echter maximaal vier uur duren voordat er in het exportbestand gegevens worden opgenomen.

### <a name="export-schedule"></a>Exportschema

Geplande exports worden beïnvloed door het tijdstip en de dag van de week waarop u de export in eerste instantie maakt. Wanneer u een geplande export maakt, wordt de export uitgevoerd op hetzelfde tijdstip van de dag voor elk volgend exemplaar van de export. Stel, u maakt een dagelijkse export om 13:00 uur. De volgende uitvoer wordt de volgende dag om 13:00 uur uitgevoerd. De huidige tijd is op dezelfde manier van invloed op alle andere exporttypen; ze worden altijd uitgevoerd op hetzelfde moment van de dag als waarop de export in eerste instantie is gemaakt. In een ander voorbeeld maakt u een wekelijkse export op maandag om 16:00 uur. Het volgende rapport wordt de maandag erna om 16:00 uur uitgevoerd. *Geëxporteerde gegevens zijn binnen vier uur na de uitvoeringstijd beschikbaar.*

Voor elke export wordt een nieuw bestand gemaakt. Oudere exports worden dus niet overschreven.

Er zijn drie soorten exportopties:

**Dagelijkse export van de maandkosten tot heden**: de eerste uitvoer wordt onmiddellijk uitgevoerd. Volgende exports worden de volgende dag op hetzelfde tijdstip als de eerste export uitgevoerd. De meest recente gegevens worden uit de vorige dagelijkse exports samengevoegd.

**Wekelijkse export van kosten voor de afgelopen zeven dagen**: de eerste uitvoer wordt onmiddellijk uitgevoerd. Volgende exports worden op dezelfde weekdag en hetzelfde tijdstip als de eerste export uitgevoerd. De kosten zijn voor de afgelopen zeven dagen.

**Aangepast**: hiermee kunt u wekelijkse en maandelijkse exports plannen met week-tot-datum- en maand-tot-heden-opties. *De eerste uitvoer wordt onmiddellijk uitgevoerd.*

Als u een abonnement op basis van betalen per gebruik, MSDN of Visual Studio hebt, kan de facturerings periode van uw factuur niet worden uitgelijnd op de kalender maand. Voor deze typen abonnementen en resource groepen kunt u een export maken die is afgestemd op uw factuur periode of voor kalender maanden. Als u een export wilt maken die is afgestemd op uw factuur maand, gaat u naar **aangepast**en selecteert u **facturering-periode-tot-datum**.  Als u een export wilt maken die is afgestemd op de kalender maand, selecteert u **maand tot heden**.
>
>

![Nieuwe export: het tabblad Basis toont een aangepaste wekelijkse week-tot-heden-selectie](./media/tutorial-export-acm-data/tutorial-export-schedule-weekly-week-to-date.png)

## <a name="verify-that-data-is-collected"></a>Controleren of de gegevens zijn geëxporteerd

U kunt eenvoudig controleren of uw Cost Management-gegevens daadwerkelijk zijn geëxporteerd door het CSV-exportbestand in Azure Storage Explorer te bekijken.

Klik in de lijst met exports op de naam van het opslagaccount. Klik op de pagina van het opslagaccount op Openen in Explorer. Er wordt nu een bevestigingsvenster weergegeven. Klik op **Ja** om het bestand te openen in Azure Storage Explorer.

![Opslagaccountpagina, waarbij voorbeeldinformatie en een koppeling naar Openen in Explorer worden weergegeven](./media/tutorial-export-acm-data/storage-account-page.png)

Ga in Storage Explorer naar de container die u wilt openen en selecteer de map voor de huidige maand. U ziet nu een lijst met CSV-bestanden. Selecteer het gewenste bestand en klik op **Openen**.

![Voorbeeldinformatie weergegeven in Storage Explorer](./media/tutorial-export-acm-data/storage-explorer.png)

Het bestand wordt geopend met het programma of de toepassing waarmee bestanden met de extensie CSV standaard worden geopend. Dit is een voorbeeld met Excel.

![Voorbeeld van geëxporteerde CSV-gegevens, weergegeven in Excel](./media/tutorial-export-acm-data/example-export-data.png)


## <a name="access-exported-data-from-other-systems"></a>Geëxporteerde gegevens gebruiken in externe systemen

Het exporteren van Cost Management-gegevens heeft vaak als doel om deze gegevens in een extern systeem te gebruiken. Denk bijvoorbeeld aan een dashboard of een ander financieel systeem. Omdat al deze systemen sterk van elkaar verschillen, heeft het weinig zin om hier een voorbeeld van te laten zien.  In [Introduction to Azure Storage](../storage/common/storage-introduction.md) (Inleiding tot Azure Storage) kunt u echter aan de slag met het openen van uw gegevens in de door u gebruikte toepassingen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Een dagelijkse export uitvoeren
> * Controleren of de gegevens zijn geëxporteerd

Ga naar de volgende zelfstudie, waarin u leert om de efficiëntie te optimaliseren en te verbeteren door inactieve en onderbenutte resources te identificeren.

> [!div class="nextstepaction"]
> [Aanbevelingen bekijken en hier actie op ondernemen](tutorial-acm-opt-recommendations.md)
