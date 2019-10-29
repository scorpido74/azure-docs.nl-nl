---
title: Problemen met System Center-Data Protection Manager-Azure Backup oplossen
description: Problemen oplossen in System Center Data Protection Manager.
ms.reviewer: kasinh
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: dacurwin
ms.openlocfilehash: a348a11d35a8803bb0c01e1736e644847e2a07a1
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/27/2019
ms.locfileid: "72968418"
---
# <a name="troubleshoot-system-center-data-protection-manager"></a>Problemen oplossen met System Center Data Protection Manager

In dit artikel worden oplossingen beschreven voor problemen die kunnen optreden tijdens het gebruik van Data Protection Manager.

Raadpleeg de [documentatie van System](https://docs.microsoft.com/system-center/dpm/dpm-release-notes?view=sc-dpm-2016)Center voor de nieuwste opmerkingen bij de release voor system Center Data Protection Manager. In [deze matrix](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2016)vindt u meer informatie over ondersteuning voor Data Protection Manager.

## <a name="error-replica-is-inconsistent"></a>Fout: replica is inconsistent

Een replica kan om de volgende redenen inconsistent zijn:

- De taak voor het maken van de replica is mislukt.
- Er zijn problemen met het wijzigings logboek.
- De bitmap voor filter op volume niveau bevat fouten.
- De bron machine wordt onverwacht afgesloten.
- Het synchronisatie logboek overschrijdt.
- De replica is echt inconsistent.

U kunt dit probleem oplossen door de volgende acties uit te voeren:

- Als u de inconsistente status wilt verwijderen, voert u de consistentie controle hand matig uit of plant u een dagelijkse consistentie controle.
- Zorg ervoor dat u de nieuwste versie van Microsoft Azure Backup Server en Data Protection Manager gebruikt.
- Zorg ervoor dat de instelling **automatische consistentie** is ingeschakeld.
- Probeer de services opnieuw te starten vanaf de opdracht prompt. Gebruik de opdracht `net stop dpmra` gevolgd door `net start dpmra`.
- Zorg ervoor dat u voldoet aan de vereisten voor de netwerk verbinding en de band breedte.
- Controleer of de bron machine onverwacht is afgesloten.
- Zorg ervoor dat de schijf in orde is en dat er voldoende ruimte is voor de replica.
- Zorg ervoor dat er geen dubbele back-uptaken zijn die gelijktijdig worden uitgevoerd.

## <a name="error-online-recovery-point-creation-failed"></a>Fout: kan het online herstel punt niet maken

U kunt dit probleem oplossen door de volgende acties uit te voeren:

- Zorg ervoor dat u de nieuwste versie van de Azure Backup-agent gebruikt.
- Probeer hand matig een herstel punt in het taak gebied beveiliging te maken.
- Zorg ervoor dat u een consistentie controle op de gegevens bron uitvoert.
- Zorg ervoor dat u voldoet aan de vereisten voor de netwerk verbinding en de band breedte.
- Wanneer de replica gegevens een inconsistente status hebben, maakt u een schijf herstel punt voor deze gegevens bron.
- Zorg ervoor dat de replica aanwezig is en niet ontbreekt.
- Zorg ervoor dat de replica voldoende ruimte heeft om het USN-logboek (Update Sequence Number) te maken.

## <a name="error-unable-to-configure-protection"></a>Fout: kan de beveiliging niet configureren

Deze fout treedt op wanneer de Data Protection Manager server geen verbinding kan maken met de beveiligde server.

U kunt dit probleem oplossen door de volgende acties uit te voeren:

- Zorg ervoor dat u de nieuwste versie van de Azure Backup-agent gebruikt.
- Zorg ervoor dat er verbinding is (netwerk/firewall/proxy) tussen uw Data Protection Manager-server en de beveiligde server.
- Als u een SQL server beveiligt, moet u ervoor zorgen dat de **aanmeldings eigenschappen** > **NT AUTHORITY\SYSTEM** -eigenschap de instelling **sysadmin** weer geven ingeschakeld.

## <a name="error-server-not-registered-as-specified-in-vault-credential-file"></a>Fout: de server is niet geregistreerd zoals opgegeven in het kluis referentie bestand

Deze fout treedt op tijdens het herstel proces voor Data Protection Manager-Azure Backup-Server gegevens. Het kluis referentie bestand dat wordt gebruikt in het herstel proces hoort niet bij de Recovery Services kluis voor de Data Protection Manager/Azure Backup Server.

Voer de volgende stappen uit om dit probleem op te lossen:

1. Down load het kluis referentie bestand van de Recovery Services kluis waarop de Data Protection Manager/Azure Backup Server is geregistreerd.
2. Probeer de server bij de kluis te registreren met het laatst gedownloade kluis referentie bestand.

## <a name="error-no-recoverable-data-or-selected-server-not-a-data-protection-manager-server"></a>Fout: herstel bare gegevens of de geselecteerde server is geen Data Protection Manager server

Deze fout treedt op om de volgende redenen:

- Er zijn geen andere Data Protection Manager-of Azure Backup-servers geregistreerd bij de Recovery Services kluis.
- De-servers hebben de meta gegevens nog niet geüpload.
- De geselecteerde server is geen Data Protection Manager/Azure Backup-Server.

Voer de volgende stappen uit om het probleem op te lossen wanneer er andere Data Protection Manager-of Azure Backup-servers zijn geregistreerd bij de Recovery Services kluis:

1. Zorg ervoor dat de nieuwste Azure Backup-Agent is geïnstalleerd.
2. Nadat u hebt gezorgd dat de nieuwste agent is geïnstalleerd, wacht u één dag voordat u het herstel proces start. De taak 's nachts backup uploadt de meta gegevens voor alle beveiligde back-ups naar de Cloud. De back-upgegevens zijn vervolgens beschikbaar voor herstel.

## <a name="error-provided-encryption-passphrase-doesnt-match-passphrase-for-server"></a>Fout: de opgegeven wachtwoordzin voor versleuteling komt niet overeen met de wachtwoordzin voor de server

Deze fout treedt op tijdens het versleutelen van Data Protection Manager/Azure Backup Server-gegevens. De wachtwoordzin voor versleuteling die wordt gebruikt in het herstel proces komt niet overeen met de wachtwoordzin voor versleuteling van de server. Als gevolg hiervan kan de agent de gegevens niet ontsleutelen en mislukt de herstel bewerking.

> [!IMPORTANT]
> Als u de wachtwoordzin voor versleuteling vergeet of kwijtraakt, zijn er geen andere methoden voor het herstellen van de gegevens. De enige optie is het opnieuw genereren van de wachtwoordzin. Gebruik de nieuwe wachtwoordzin voor het versleutelen van toekomstige back-upgegevens.
>
> Wanneer u gegevens herstelt, moet u altijd dezelfde versleutelings wachtwoord opgeven die is gekoppeld aan de Data Protection Manager-Azure Backup Server.
>
