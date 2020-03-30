---
title: Azure HDInsight-clusters met schijfversleuteling verliezen key vault-toegang
description: Problemen oplossen met stappen en mogelijke oplossingen voor problemen bij interactie met Azure HDInsight-clusters.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.openlocfilehash: 2ae389be25cd8633a53a49cf000796c1510733a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76965162"
---
# <a name="scenario-azure-hdinsight-clusters-with-disk-encryption-lose-key-vault-access"></a>Scenario: Azure HDInsight-clusters met schijfversleuteling verliezen key vault-toegang

In dit artikel worden stappen voor het oplossen van problemen en mogelijke oplossingen voor problemen beschreven bij interactie met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Het Resource Health Center (RHC) alert, `The HDInsight cluster is unable to access the key for BYOK encryption at rest`wordt weergegeven voor Bring Your Own Key (BYOK) clusters waar de clusterknooppunten hebben geen toegang tot klanten Key Vault (KV). Soortgelijke waarschuwingen kunnen ook worden gezien op Apache Ambari UI.

## <a name="cause"></a>Oorzaak

De waarschuwing zorgt ervoor dat KV toegankelijk is vanaf de clusterknooppunten, waardoor de netwerkverbinding, de KV-status en het toegangsbeleid voor de gebruiker die Managed Identity is toegewezen, worden gewaarborgd. Deze waarschuwing is slechts een waarschuwing van dreigende broker shutdown op latere node reboots, het cluster blijft functioneren totdat knooppunten opnieuw opstarten.

Navigeer naar de Apache Ambari-gebruikersinterface voor meer informatie over de waarschuwing van de **status van de kluisstatus van de sleutelvan schijfversleuteling**. Deze waarschuwing bevat details over de reden voor verificatiefouten.

## <a name="resolution"></a>Oplossing

### <a name="kvaad-outage"></a>KV/AAD-storing

Bekijk [de beschikbaarheid en redundantie](../../key-vault/key-vault-disaster-recovery-guidance.md) pagina azure key vault en de azure-statuspagina voor meer informatiehttps://status.azure.com/

### <a name="kv-accidental-deletion"></a>KV per ongeluk verwijderen

* Verwijderde sleutel herstellen op KV om automatisch te herstellen. Zie [Verwijderde sleutel herstellen voor](https://docs.microsoft.com/rest/api/keyvault/recoverdeletedkey)meer informatie .
* Neem contact op met kv-team om te herstellen van onbedoelde verwijderingen.

### <a name="kv-access-policy-changed"></a>KV-toegangsbeleid gewijzigd

Het toegangsbeleid voor de gebruiker die managed identity heeft toegewezen en die is toegewezen aan het HDI-cluster herstellen voor toegang tot de KV.

### <a name="key-permitted-operations"></a>Belangrijke toegestane bewerkingen

Voor elke sleutel in KV u de set toegestane bewerkingen kiezen. Zorg ervoor dat u wrap- en uitpakbewerkingen hebt ingeschakeld voor de BYOK-toets

### <a name="expired-key"></a>Verlopen sleutel

Als het verlopen is verstreken en de sleutel niet is gedraaid, herstelt u de sleutel van back-up HSM of neemt u contact op met kv-team om de vervaldatum te wissen.

### <a name="kv-firewall-blocking-access"></a>KV-firewall blokkeert toegang

Fix de KV firewall instellingen om BYOK cluster knooppunten toegang te geven tot de KV.

### <a name="nsg-rules-on-virtual-network-blocking-access"></a>NSG-regels voor het blokkeren van het virtuele netwerk

Controleer de NSG-regels die zijn gekoppeld aan het virtuele netwerk dat aan het cluster is gekoppeld.

## <a name="mitigation-and-prevention-steps"></a>Mitigatie- en preventiemaatregelen

### <a name="kv-accidental-deletion"></a>KV per ongeluk verwijderen

* Sleutelkluis configureren met [resourcevergrendelingset](../../azure-resource-manager/management/lock-resources.md).
* Back-ups van sleutels van hun Hardware Security Module.

### <a name="key-deletion"></a>Sleutelverwijdering

Het cluster moet worden verwijderd voordat de sleutel wordt verwijderd.

### <a name="kv-access-policy-changed"></a>KV-toegangsbeleid gewijzigd

Regelmatig controle- en testtoegangsbeleid.

### <a name="expired-key"></a>Verlopen sleutel

* Maak een back-up van de sleutels van uw HSM.
* Gebruik een sleutel zonder vervaldatum ingesteld.
* Als het verlopen moet worden ingesteld, draait u de toetsen vóór de vervaldatum.

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure Community Support.](https://azure.microsoft.com/support/community/)

* Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met - het officiële Microsoft Azure-account voor het verbeteren van de klantervaring. De Azure-community verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, u een ondersteuningsaanvraag indienen via de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecteer **Ondersteuning** op de menubalk of open de **Help + ondersteuningshub.** Voor meer gedetailleerde informatie, bekijk [Hoe maak je een Azure-ondersteuningsaanvraag](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Toegang tot abonnementsbeheer en factureringsondersteuning is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geboden via een van de [Azure Support-abonnementen](https://azure.microsoft.com/support/plans/).
