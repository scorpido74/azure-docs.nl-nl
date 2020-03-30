---
title: Problemen oplossen met System Center Data Protection Manager
description: Ontdek in dit artikel oplossingen voor problemen die u tegenkomen tijdens het gebruik van System Center Data Protection Manager.
ms.topic: troubleshooting
ms.date: 01/30/2019
ms.openlocfilehash: bcb30fa7eb3e05099761fc751b09a9fb16134e34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75664753"
---
# <a name="troubleshoot-system-center-data-protection-manager"></a>Problemen oplossen met System Center Data Protection Manager

In dit artikel worden oplossingen beschreven voor problemen die u tegenkomen tijdens het gebruik van Gegevensbeschermingsbeheer.

Zie de documentatie van het [Systeemcentrum](https://docs.microsoft.com/system-center/dpm/dpm-release-notes?view=sc-dpm-2016)voor de laatste releasenotes voor System Center Data Protection Manager. In [deze matrix](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2016)vindt u meer informatie over ondersteuning voor Gegevensbeschermingsbeheer.

## <a name="error-replica-is-inconsistent"></a>Fout: Replica is inconsistent

Een replica kan om de volgende redenen inconsistent zijn:

- De taak voor het maken van replica's mislukt.
- Er zijn problemen met het veranderdagboek.
- De bitmap van het volumeniveaufilter bevat fouten.
- De bronmachine wordt onverwacht uitgeschakeld.
- De synchronisatielog loopt over.
- De replica is echt inconsistent.

Voer de volgende acties uit om dit probleem op te lossen:

- Als u de inconsistente status wilt verwijderen, voert u de consistentiecontrole handmatig uit of plant u een dagelijkse consistentiecontrole.
- Controleer of u de nieuwste versie van Microsoft Azure Backup Server en Data Protection Manager gebruikt.
- Controleer of de instelling **Automatische consistentie** is ingeschakeld.
- Probeer de services opnieuw te starten via de opdrachtprompt. Gebruik `net stop dpmra` de opdracht `net start dpmra`gevolgd door .
- Zorg ervoor dat u voldoet aan de vereisten voor netwerkconnectiviteit en bandbreedte.
- Controleer of de bronmachine onverwacht is uitgeschakeld.
- Zorg ervoor dat de schijf gezond is en dat er voldoende ruimte is voor de replica.
- Zorg ervoor dat er geen dubbele back-uptaken zijn die gelijktijdig worden uitgevoerd.

## <a name="error-online-recovery-point-creation-failed"></a>Fout: Het maken van online herstelpunt is mislukt

Voer de volgende acties uit om dit probleem op te lossen:

- Controleer of u de nieuwste versie van de Azure Backup-agent gebruikt.
- Probeer handmatig een herstelpunt te maken in het gebied van beveiligingstaken.
- Zorg ervoor dat u de gegevensbron consistent controleert.
- Zorg ervoor dat u voldoet aan de vereisten voor netwerkconnectiviteit en bandbreedte.
- Wanneer de replicagegevens in een inconsistente status zijn, maakt u een schijfherstelpunt van deze gegevensbron.
- Zorg ervoor dat de replica aanwezig is en niet ontbreekt.
- Controleer of de replica voldoende ruimte heeft om het updatenummer (USN) te maken.

## <a name="error-unable-to-configure-protection"></a>Fout: kan beveiliging niet configureren

Deze fout treedt op wanneer de server Gegevensbeschermingsbeheer geen contact kan opnemen met de beveiligde server.

Voer de volgende acties uit om dit probleem op te lossen:

- Controleer of u de nieuwste versie van de Azure Backup-agent gebruikt.
- Zorg ervoor dat er verbinding is (netwerk/firewall/proxy) tussen uw Data Protection Manager-server en de beveiligde server.
- Als u een SQL-server beschermt, moet u ervoor zorgen dat de eigenschap **Login Properties** > **NT AUTHORITY\SYSTEM** de **sysadmin-instelling** weergeeft die is ingeschakeld.

## <a name="error-server-not-registered-as-specified-in-vault-credential-file"></a>Fout: Server niet geregistreerd zoals opgegeven in vault credential file

Deze fout treedt op tijdens het herstelproces voor gegevensbeschermingsbeheer/Azure Backup-servergegevens. Het kluisreferentiebestand dat wordt gebruikt in het herstelproces, behoort niet tot de kluis Recovery Services voor de gegevensbeschermingsbeheer/Azure Backup-server.

Voer de volgende stappen uit om dit probleem op te lossen:

1. Download het kluisreferentiebestand uit de kluis Recovery Services waarop de Gegevensbeschermingsbeheer/Azure Backup-server is geregistreerd.
2. Probeer de server te registreren bij de kluis met behulp van de meest recent gedownloade vault credential file.

## <a name="error-no-recoverable-data-or-selected-server-not-a-data-protection-manager-server"></a>Fout: Geen herstelbare gegevens of geselecteerde server, geen gegevensbeschermingsbeheerserver

Deze fout treedt op om de volgende redenen:

- Er zijn geen andere Gegevensbeschermingsbeheer-/Azure Backup-servers geregistreerd in de kluis Recovery Services.
- De servers hebben de metadata nog niet geüpload.
- De geselecteerde server is geen Gegevensbeschermingsbeheer/Azure Backup-server.

Wanneer andere Gegevensbeschermingsbeheer/Azure Backup-servers zijn geregistreerd bij de kluis Recovery Services, voert u de volgende stappen uit om het probleem op te lossen:

1. Controleer of de nieuwste Azure Backup-agent is geïnstalleerd.
2. Nadat u ervoor hebt gezorgd dat de nieuwste agent is geïnstalleerd, wacht u een dag voordat u het herstelproces start. De nachtelijke back-uptaak uploadt de metagegevens voor alle beveiligde back-ups naar de cloud. De back-upgegevens zijn dan beschikbaar voor herstel.

## <a name="error-provided-encryption-passphrase-doesnt-match-passphrase-for-server"></a>Fout: Opgegeven wachtwoordzin voor versleuteling komt niet overeen met wachtwoordzin voor server

Deze fout treedt op tijdens het versleutelingsproces bij het herstellen van gegevensbeschermingsbeheer/Azure Backup-servergegevens. De versleutelingswachtwoordzin die wordt gebruikt in het herstelproces komt niet overeen met de versleutelingswachtwoordzin van de server. Als gevolg hiervan kan de agent de gegevens niet decoderen en mislukt het herstel.

> [!IMPORTANT]
> Als u de wachtwoordzin voor versleuteling vergeet of verliest, zijn er geen andere methoden voor het herstellen van de gegevens. De enige optie is om de wachtwoordzin te regenereren. Gebruik de nieuwe wachtwoordzin om toekomstige back-upgegevens te versleutelen.
>
> Wanneer u gegevens herstelt, moet u altijd dezelfde coderingswachtwoordzin verstrekken die is gekoppeld aan de gegevensbeschermingsbeheer/Azure Backup-server.
>
