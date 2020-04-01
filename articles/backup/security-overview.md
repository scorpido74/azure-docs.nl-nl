---
title: Overzicht van beveiligingsfuncties
description: Meer informatie over beveiligingsmogelijkheden in Azure Backup waarmee u uw back-upgegevens beschermen en voldoen aan de beveiligingsbehoeften van uw bedrijf.
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 91a0f29862b0c9c35e562c143e28ebbc6c39cf94
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80423182"
---
# <a name="overview-of-security-features-in-azure-backup"></a>Overzicht van beveiligingsfuncties in Azure Backup

Een van de belangrijkste stappen die u nemen om uw gegevens te beschermen, is het hebben van een betrouwbare back-upinfrastructuur. Maar het is net zo belangrijk om ervoor te zorgen dat uw gegevens op een veilige manier worden geback-upt en dat uw back-ups te allen tijde worden beschermd. Azure Backup biedt beveiliging voor uw back-upomgeving, zowel wanneer uw gegevens onderweg zijn als in rust. In dit artikel worden beveiligingsmogelijkheden in Azure Backup weergegeven waarmee u uw back-upgegevens beschermen en voldoen aan de beveiligingsbehoeften van uw bedrijf.

## <a name="management-and-control-of-identity-and-user-access"></a>Beheer en controle van identiteit en gebruikerstoegang

Azure Backup stelt u in staat om fijnmazige toegang te beheren met Behulp van [Azure Role-Based Access Control (RBAC).](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) Met RBAC u taken binnen uw team scheiden en alleen de hoeveelheid toegang verlenen aan gebruikers die nodig zijn om hun werk te doen.

Azure Backup biedt drie ingebouwde rollen om back-upbeheerbewerkingen te beheren:

* Back-upbijdrager - om back-ups te maken en te beheren, behalve het verwijderen van de kluis van Recovery Services en het geven van toegang tot anderen
* Back-upoperator - alles wat een bijdrager doet, behalve het verwijderen van back-upbeleid en het beheren van back-upbeleid
* Back-uplezer - machtigingen om alle back-upbeheerbewerkingen te bekijken

Meer informatie over [toegangsbeheer op basis van rollen om Azure Backup te beheren.](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault)

Azure Backup heeft verschillende beveiligingsbesturingselementen ingebouwd in de service om beveiligingsproblemen te voorkomen, detecteren en erop te reageren. Meer informatie over [beveiligingsbesturingselementen voor Azure Backup](https://docs.microsoft.com/azure/backup/backup-security-controls).

## <a name="separation-between-guest-and-azure-storage"></a>Scheiding tussen gast- en Azure-opslag

Met Azure Backup, dat virtuele machineback-up en SQL en SAP HANA in VM-back-up omvat, worden de back-upgegevens opgeslagen in Azure-opslag en heeft de gast geen directe toegang tot back-upopslag of de inhoud ervan.  Met een back-up van virtuele machines wordt het maken en opslaan van back-ups gedaan door Azure-fabric, waarbij de gast geen andere betrokkenheid heeft dan het verwerken van de werkbelasting voor consistente back-ups van toepassingen.  Met SQL en SAP HANA krijgt de back-upextensie tijdelijke toegang om naar specifieke blobs te schrijven.  Op deze manier kunnen bestaande back-ups zelfs in een gecompromitteerde omgeving niet worden geknoeid of verwijderd door de gast.

## <a name="internet-connectivity-not-required-for-azure-vm-backup"></a>Internetverbinding niet vereist voor Azure VM-back-up

Back-ups van Azure VM's vereisen het verplaatsen van gegevens van de schijf van uw virtuele machine naar de kluis Recovery Services. Alle vereiste communicatie- en gegevensoverdracht gebeurt echter alleen op het Azure-backbonenetwerk zonder toegang te hoeven krijgen tot uw virtuele netwerk. Daarom hoeft u geen back-up van Azure VM's die in beveiligde netwerken zijn geplaatst, toegang te verlenen tot IP's of FQDN's.

## <a name="private-endpoints-for-azure-backup"></a>Privéeindpunten voor Azure-back-up

U nu [privéeindpunten](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) gebruiken om veilig een back-up van uw gegevens te maken, van servers in een virtueel netwerk naar uw Vault recovery services. Het privéeindpunt maakt gebruik van een IP van de VNET-adresruimte voor uw kluis, zodat u uw virtuele netwerken niet hoeft bloot te stellen aan openbare IP's. Private Endpoints kunnen worden gebruikt voor het maken van back-ups en het herstellen van uw SQL- en SAP HANA-databases die in uw Azure VM's worden uitgevoerd. Het kan ook worden gebruikt voor uw on-premises servers met behulp van de MARS-agent.

>[!NOTE]
> Deze functie is momenteel in beperkte beschikbaarheid. Vul [deze enquête](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u) in en [stuur ons een e-mail](mailto:azbackupnetsec@microsoft.com) als u geïnteresseerd bent in het gebruik van privéeindpunten voor Azure Backup. De mogelijkheid om deze functie te gebruiken is onder voorbehoud van goedkeuring van de Azure Backup-service.

## <a name="encryption-of-data-in-transit-and-at-rest"></a>Versleuteling van gegevens tijdens doorvoer en in rust

Versleuteling beschermt uw gegevens en helpt u om te voldoen aan uw organisatorische beveiligings- en nalevingsverplichtingen. Binnen Azure worden gegevens die onderweg zijn tussen Azure-opslag en de kluis beschermd door HTTPS. Deze gegevens blijven op het Azure-backbone-netwerk.

* Back-upgegevens worden automatisch versleuteld met door Microsoft beheerde sleutels. U uw back-up-beheerde schijfVM's ook versleutelen in de Vault voor Herstelservices met behulp van [door de klant beheerde sleutels](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#encryption-of-backup-data-using-customer-managed-keys) die zijn opgeslagen in de Azure Key Vault. U hoeft geen expliciete actie te ondernemen om deze versleuteling in te schakelen. Dit geldt voor alle workloads die worden geback-upt naar uw Vault Recovery Services.

* Azure Backup ondersteunt back-ups en herstel van Azure VM's waarbij hun BE/data-schijven zijn versleuteld met Azure Disk Encryption (ADE). [Meer informatie over versleutelde Azure VM's en Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).

## <a name="protection-of-backup-data-from-unintentional-deletes"></a>Bescherming van back-upgegevens tegen onbedoelde verwijderingen

Azure Backup biedt beveiligingsfuncties om back-upgegevens te beschermen, zelfs na verwijdering. Als de gebruiker de back-up van een vm verwijdert, worden de back-upgegevens gedurende 14 extra dagen bewaard, zodat het herstel van dat back-upitem zonder gegevensverlies kan worden hersteld. De extra 14 dagen bewaren van back-upgegevens in de status 'soft delete' brengt geen kosten met zich mee voor de klant. [Meer informatie over soft delete](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#soft-delete).

## <a name="monitoring-and-alerts-of-suspicious-activity"></a>Controle en waarschuwingen van verdachte activiteiten

Azure Backup biedt [ingebouwde bewakings- en waarschuwingsmogelijkheden](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) om acties te bekijken en te configureren voor gebeurtenissen met betrekking tot Azure Backup. [Back-uprapporten](https://docs.microsoft.com/azure/backup/configure-reports) dienen als een one-stop-bestemming voor het bijhouden van het gebruik, het controleren van back-ups en herstel, en het identificeren van belangrijke trends op verschillende niveaus van granulariteit. Met behulp van de monitoring- en rapportagetools van Azure Backup u worden gewaarschuwd voor ongeautoriseerde, verdachte of schadelijke activiteiten zodra deze zich voordoen.

## <a name="security-features-to-help-protect-hybrid-backups"></a>Beveiligingsfuncties om hybride back-ups te beschermen

Azure Backup-service gebruikt de MARS-agent (Microsoft Azure Recovery Services) om een back-up te maken en bestanden, mappen en de volume- of systeemstatus te herstellen van een on-premises computer naar Azure. MARS biedt nu beveiligingsfuncties om hybride back-ups te beschermen. Deze functies omvatten onder andere:

* Er wordt een extra verificatielaag toegevoegd wanneer een kritieke bewerking zoals het wijzigen van een wachtwoordzin wordt uitgevoerd. Deze validatie is om ervoor te zorgen dat dergelijke bewerkingen alleen kunnen worden uitgevoerd door gebruikers die over geldige Azure-referenties beschikken. [Meer informatie over de functies die aanvallen voorkomen.](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#prevent-attacks)

* Verwijderde back-upgegevens worden bewaard gedurende nog eens 14 dagen vanaf de datum van verwijdering. Dit zorgt voor herstelbaarheid van de gegevens binnen een bepaalde periode, dus er is geen gegevensverlies, zelfs als er een aanval plaatsvindt. Ook wordt een groter aantal minimale herstelpunten gehandhaafd om te beschermen tegen corrupte gegevens. [Meer informatie over het herstellen van verwijderde back-upgegevens](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#recover-deleted-backup-data).

## <a name="compliance-with-standardized-security-requirements"></a>Naleving van gestandaardiseerde beveiligingsvereisten

Microsoft Azure & Azure Backup bieden een uitgebreide set certificeringen en verklaringen om organisaties te helpen voldoen aan nationale, regionale en branchespecifieke vereisten voor het verzamelen en gebruiken van gegevens van personen. [Bekijk de lijst met nalevingscertificeringen](compliance-offerings.md)

## <a name="next-steps"></a>Volgende stappen

* [Beveiligingsfuncties om cloudworkloads te beschermen die Azure Backup gebruiken](backup-azure-security-feature-cloud.md)
* [Beveiligingsfuncties om hybride back-ups te beschermen die Azure Backup gebruiken](backup-azure-security-feature.md)
