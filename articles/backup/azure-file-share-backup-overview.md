---
title: Back-up van Azure-bestandsshare
description: Meer informatie over het maken van back-ups van Azure-bestandsshares in de kluis Van Recovery Services
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 0e17b05a3febaa673fb29d45c2bcef25e2996df8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78386718"
---
# <a name="about-azure-file-share-backup"></a>Back-up van Azure-bestandsshare

Azure file share backup is een native, cloud gebaseerde back-upoplossing die uw gegevens in de cloud beschermt en extra onderhoudsoverheadkosten elimineert die betrokken zijn bij on-premises back-upoplossingen. De Azure Backup-service integreert probleemloos met Azure-bestandssynchronisatie en stelt u in staat om uw gegevens voor het delen van bestanden en uw back-ups te centraliseren. Met deze eenvoudige, betrouwbare en veilige oplossing u de beveiliging voor uw bedrijfsbestandsaandelen in enkele eenvoudige stappen configureren met de zekerheid dat u uw gegevens herstellen in geval van een rampscenario.

## <a name="key-benefits-of-azure-file-share-backup"></a>Belangrijkste voordelen van back-ups van Azure-bestandsshare

* Geen infrastructuur: er is geen implementatie nodig om de beveiliging voor uw bestandsshares te configureren.
* Ingebouwde beheermogelijkheden: u back-ups plannen en de gewenste bewaarperiode opgeven zonder de extra overhead van het snoeien van gegevens.
* Direct herstellen: Azure file share backup maakt gebruik van snapshots voor bestandsshare, zodat u alleen de bestanden selecteren die u direct wilt herstellen.
* Waarschuwingen en rapportage: u waarschuwingen configureren voor back-ups en herstelfouten en de rapportageoplossing van Azure Backup gebruiken om inzicht te krijgen in back-ups van uw bestanden.

## <a name="architecture"></a>Architectuur

![Back-uparchitectuur voor Azure-bestanden](./media/azure-file-share-backup-overview/azure-file-shares-backup-architecture.png)

## <a name="how-the-backup-process-works"></a>Hoe het back-upproces werkt

1. De eerste stap in het configureren van back-ups voor Azure File-shares is het maken van een kluis met herstelservices. De kluis geeft u een geconsolideerde weergave van de back-ups die zijn geconfigureerd voor verschillende workloads.

2. Zodra u een kluis hebt gemaakt, detecteert de Azure Backup-service de opslagaccounts die bij de kluis kunnen worden geregistreerd. U het opslagaccount selecteren dat de bestandsshares host die u wilt beveiligen.

3. Nadat u het opslagaccount hebt geselecteerd, geeft de Azure Backup-service de set bestandsshares weer die aanwezig zijn in het opslagaccount en worden hun namen opgeslagen in de catalogus van de beheerlaag.

4. Vervolgens configureert u het back-upbeleid (planning en retentie) op basis van uw vereisten en selecteert u de bestandsshares om een back-up te maken. De Azure Backup-service registreert de schema's in het controlevlak om geplande back-ups uit te maken.

5. Op basis van het opgegeven beleid activeert de Azure Backup scheduler back-ups op het geplande tijdstip. Als onderdeel van die taak wordt de momentopname voor bestandsshare gemaakt met behulp van de API voor bestandsshare. Alleen de url van de momentopname wordt opgeslagen in het metagegevensarchief.

    >[!NOTE]
    >De gegevens voor bestandsshare worden niet overgedragen naar de back-upservice, omdat de back-upservice momentopnamen maakt en beheert die deel uitmaken van uw opslagaccount.

6. U de inhoud van azure-bestandendelen (afzonderlijke bestanden of het volledige aandeel) herstellen van momentopnamen die beschikbaar zijn in de bronbestandsshare. Zodra de bewerking is geactiveerd, wordt de momentopname-URL opgehaald uit het metagegevensarchief en worden de gegevens weergegeven en overgedragen van de bronmomentopname naar het doelbestandaandeel van uw keuze.

7. De gegevens voor het back-upen en herstellen van taakbewaking worden naar de Azure Backup Monitoring-service gepusht. Hiermee u cloudback-ups voor uw bestandsshares in één dashboard controleren. Bovendien u ook waarschuwingen of e-mailmeldingen configureren wanneer de back-upstatus wordt beïnvloed. E-mails worden verzonden via de Azure-e-mailservice.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [maken van back-ups van Azure-bestandsshares](backup-afs.md)
* Antwoorden vinden [op vragen over het maken van back-ups van Azure-bestanden](backup-azure-files-faq.md)
