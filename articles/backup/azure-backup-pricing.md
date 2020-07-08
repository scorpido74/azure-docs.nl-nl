---
title: Prijzen van Azure Backup
description: Meer informatie over het schatten van de kosten voor budget tering Azure Backup prijzen.
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: 274a61ff5a98fa1291f9d8917af9ab1d1b3da2fd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85391108"
---
# <a name="azure-backup-pricing"></a>Prijzen van Azure Backup

Ga naar de [pagina met Azure backup prijzen](https://azure.microsoft.com/pricing/details/backup/)voor meer informatie over Azure backup prijzen.

## <a name="download-detailed-estimates-for-azure-backup-pricing"></a>Gedetailleerde schattingen voor de Azure Backup prijzen downloaden

Als u een schatting wilt maken van de kosten voor budget tering of het vergelijken van kosten, kunt u de gedetailleerde [Azure backup prijzen Estimator](https://aka.ms/AzureBackupCostEstimates).  

### <a name="what-does-the-estimator-contain"></a>Wat bevat de estimator?

Het Estimator-venster voor Azure Backup kosten bevat een optie waarmee u kunt schatten van alle mogelijke werk belastingen waarvan u een back-up wilt maken met behulp van Azure Backup. Deze werkbelastingen zijn onder andere:

- Azure-VM's
- On-premises servers
- SQL in azure Vm's
- SAP HANA in azure Vm's
- Azure-bestands shares

## <a name="estimate-costs-for-backing-up-azure-vms-or-on-premises-servers"></a>Kosten schatten voor het maken van back-ups van virtuele Azure-machines of on-premises servers

Voor een schatting van de kosten voor het maken van een back-up van virtuele Azure-machines of on-premises servers met Azure Backup hebt u de volgende para meters nodig:

- Grootte van de Vm's of on-premises servers waarvan u een back-up wilt maken
  - Geef de ' gebruikte grootte ' op van schijven of servers die vereist zijn om een back-up te maken

- Aantal servers met deze grootte

- Wat is de verwachte hoeveelheid gegevens verloop op deze servers?<br>
  Verloop verwijst naar de hoeveelheid wijziging in gegevens. Als u bijvoorbeeld een virtuele machine met 200 GB aan gegevens hebt waarvan een back-up moet worden gemaakt en als 10 GB aan het bestand elke dag wordt gewijzigd, is het dagelijkse verloop 5%.

  - Een hoger verloop betekent dat u een back-up maakt van meer gegevens

  - Kies **laag** of **gemiddeld** voor bestands servers en **hoog** als u data bases uitvoert

  - Als u het **verloop percentage**weet, kunt u de optie **uw eigen% opgeven** gebruiken

- Het back-upbeleid kiezen

  - Hoe lang wilt u ' dagelijks ' back-ups behouden? (in dagen)

  - Hoe lang wilt u ' wekelijkse ' back-ups behouden? (in weken)

  - Hoe lang wilt u ' maandelijks ' back-ups behouden? (in maanden)

  - Hoe lang wilt u ' jaarlijks ' back-ups behouden? (in jaren)

  - Hoe lang verwacht u "moment opnamen direct terugzetten"? (1-5 dagen)

    - Met deze optie kunt u vanaf Maxi maal zeven dagen op een snelle manier herstellen met behulp van moment opnamen die op schijven zijn opgeslagen

- **Optioneel** : selectieve schijf back-up

  - Als u de optie **selectieve schijf back-up** gebruikt tijdens het maken van een back-up van virtuele Azure-machines, kiest u de optie **schijf uitsluiten** en voert u het percentage schijven in dat is uitgesloten van back-up in termen van grootte. Als u bijvoorbeeld een virtuele machine hebt die is verbonden met drie schijven met 200 GB die op elke schijf worden gebruikt en als u twee wilt uitsluiten van een back-up, voert u 66,7% in.

- **Optioneel** : redundantie van back-upopslag

  - Dit duidt op de redundantie van het opslag account waarnaar de back-upgegevens worden verzonden. We raden u aan **GRS** te gebruiken voor de hoogste Beschik baarheid. Omdat het ervoor zorgt dat een kopie van uw back-upgegevens in een andere regio wordt bewaard, helpt u bij het voldoen aan meerdere nalevings standaarden. Wijzig de redundantie in **LRS** als u een back-up maakt van ontwikkel-of test omgevingen waarvoor geen back-ups op bedrijfs niveau nodig zijn. Selecteer de optie **RAGRS** in het blad als u wilt weten wat de kosten zijn wanneer het niet meer nodig is om een back-up te maken van [meerdere regio's](backup-azure-arm-restore-vms.md#cross-region-restore) .

- **Optioneel** : regionale prijzen aanpassen of kortings tarieven Toep assen

  - Als u de schattingen voor een andere regio of kortings tarieven wilt controleren, selecteert u **Ja** voor de **schatting pogingen voor een andere regio?** en voert u de tarieven in waarmee u de schattingen wilt uitvoeren.

## <a name="estimate-costs-for-backing-up-sql-servers-in-azure-vms"></a>Kosten schatten voor het maken van back-ups van SQL-servers in azure-Vm's

Voor een schatting van de kosten voor het maken van een back-up van SQL-servers die worden uitgevoerd in azure Vm's met Azure Backup, hebt u de volgende para meters nodig:

- Grootte van de SQL-servers waarvan u een back-up wilt maken

- Aantal SQL-servers met de bovenstaande grootte

- Wat is de verwachte compressie van de back-upgegevens van uw SQL-servers?

  - De meeste Azure Backup-klanten zien dat de back-upgegevens 80% compressie hebben ten opzichte van de SQL Server-grootte wanneer de SQL-compressie is **ingeschakeld**.

  - Als u verwacht een andere compressie te zien, voert u het nummer in dit veld in

- Wat is de verwachte grootte van logboek back-ups?

  - De% geeft de dagelijkse logboek grootte aan als een% van de SQL-Server grootte

- Wat is het verwachte aantal dagelijkse gegevens verloop op deze servers?

  - Data bases hebben doorgaans ' hoog ' verloop

  - Als u het **verloop percentage**weet, kunt u de optie **uw eigen% opgeven** gebruiken

- Het back-upbeleid kiezen

  - Type back-up

    - Het meest effectief beleid dat u kunt kiezen, is een **dagelijkse onderscheiding** van wekelijkse/maandelijkse/jaarlijkse volledige back-ups. Azure Backup kunt met behulp van een enkele keer klikken herstellen uit verschillen.

    - U kunt er ook voor kiezen om een beleid te hebben met dagelijkse/wekelijkse/maandelijkse/jaarlijkse volledige back-ups. Met deze optie wordt iets meer opslag verbruikt dan de eerste optie.

  - Hoe lang verwacht u de back-ups van het logboek te bewaren? (in dagen) [7-35]

  - Hoe lang wilt u ' dagelijks ' back-ups behouden? (in dagen)

  - Hoe lang wilt u ' wekelijkse ' back-ups behouden? (in weken)

  - Hoe lang wilt u ' maandelijks ' back-ups behouden? (in maanden)

  - Hoe lang wilt u ' jaarlijks ' back-ups behouden? (in jaren)

- **Optioneel** : redundantie van back-upopslag

  - Dit duidt op de redundantie van het opslag account waarnaar de back-upgegevens worden verzonden. We raden u aan **GRS** te gebruiken voor de hoogste Beschik baarheid. Omdat het ervoor zorgt dat een kopie van uw back-upgegevens in een andere regio wordt bewaard, helpt u bij het voldoen aan meerdere nalevings standaarden. Wijzig de redundantie in **LRS** als u een back-up maakt van ontwikkel-of test omgevingen waarvoor geen back-ups op bedrijfs niveau nodig zijn.

- **Optioneel** : regionale prijzen aanpassen of kortings tarieven Toep assen

  - Als u de schattingen voor een andere regio of kortings tarieven wilt controleren, selecteert u **Ja** voor de **schatting pogingen voor een andere regio?** en voert u de tarieven in waarmee u de schattingen wilt uitvoeren.

## <a name="estimate-costs-for-backing-up-sap-hana-servers-in-azure-vms"></a>Kosten schatten voor het maken van back-ups van SAP HANA-servers in azure Vm's

Voor een schatting van de kosten voor het maken van een back-up van SAP HANA servers die worden uitgevoerd in azure Vm's met Azure Backup, hebt u de volgende para meters nodig:

- De totale grootte van de SAP HANA data bases waarvan u een back-up wilt maken. Dit moet de som zijn van de volledige back-upgrootte van elk van de data bases, zoals gerapporteerd door SAP HANA.
- Aantal SAP HANA servers met de bovenstaande grootte
- Wat is de verwachte grootte van logboek back-ups?
  - De% geeft de gemiddelde dagelijkse logboek grootte aan als een percentage van de totale grootte van SAP HANA data bases waarvan u een back-up maakt op de SAP HANA server
- Wat is het verwachte aantal dagelijkse gegevens verloop op deze servers?
  - De% geeft de gemiddelde dagelijkse verloop grootte aan als een percentage van de totale grootte van SAP HANA data bases waarvan u een back-up maakt op de SAP HANA server
  - Data bases hebben doorgaans ' hoog ' verloop
  - Als u het **verloop percentage**weet, kunt u de optie **uw eigen% opgeven** gebruiken
- Het back-upbeleid kiezen
  - Type back-up
    - Het meest effectief beleid dat u kunt kiezen, is een **dagelijkse onderscheiding** van **wekelijkse/maandelijkse/jaarlijkse** volledige back-ups. Azure Backup kunt met behulp van een enkele keer klikken herstellen uit verschillen.
    - U kunt er ook voor kiezen om een beleid te hebben met **dagelijkse/wekelijkse/maandelijkse/jaarlijkse** volledige back-ups. Met deze optie wordt iets meer opslag verbruikt dan de eerste optie.
  - Hoe lang verwacht u de back-ups van het logboek te bewaren? (in dagen) [7-35]
  - Hoe lang wilt u ' dagelijks ' back-ups behouden? (in dagen)
  - Hoe lang wilt u ' wekelijkse ' back-ups behouden? (in weken)
  - Hoe lang wilt u ' maandelijks ' back-ups behouden? (in maanden)
  - Hoe lang wilt u ' jaarlijks ' back-ups behouden? (in jaren)
- **Optioneel** : redundantie van back-upopslag
  - Dit duidt op de redundantie van het opslag account waarnaar de back-upgegevens worden verzonden. We raden u aan **GRS** te gebruiken voor de hoogste Beschik baarheid. Omdat het ervoor zorgt dat een kopie van uw back-upgegevens in een andere regio wordt bewaard, helpt u bij het voldoen aan meerdere nalevings standaarden. Wijzig de redundantie in **LRS** als u een back-up maakt van ontwikkel-of test omgevingen waarvoor geen back-ups op bedrijfs niveau nodig zijn.
- **Optioneel** : regionale prijzen aanpassen of kortings tarieven Toep assen
  - Als u de schattingen voor een andere regio of kortings tarieven wilt controleren, selecteert u **Ja** voor de **schatting pogingen voor een andere regio?** en voert u de tarieven in waarmee u de schattingen wilt uitvoeren.

## <a name="next-steps"></a>Volgende stappen

[Wat is de Azure Backup-service?](backup-overview.md)
