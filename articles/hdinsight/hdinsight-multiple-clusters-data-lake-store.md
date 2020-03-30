---
title: Meerdere HDInsight-clusters & één Azure Data Lake Storage-account
description: Meer informatie over het gebruik van meer dan één HDInsight-cluster met één Data Lake Storage-account
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/18/2019
ms.openlocfilehash: cc67acca11e7e0f24dc0597dcd19672a38a7bf28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75495756"
---
# <a name="use-multiple-hdinsight-clusters-with-an-azure-data-lake-storage-account"></a>Meerdere HDInsight-clusters gebruiken met een Azure Data Lake Storage-account

Vanaf HDInsight-versie 3.5 u HDInsight-clusters maken met Azure Data Lake Storage-accounts als standaardbestandssysteem.
Data Lake Storage ondersteunt onbeperkte opslag, wat het niet alleen ideaal maakt voor het hosten van grote hoeveelheden gegevens; maar ook voor het hosten van meerdere HDInsight-clusters die één Data Lake Storage-account delen. Zie [Quickstart: Clusters instellen in HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)voor instructies over het maken van een HDInsight-cluster met Data Lake Storage als opslag.

In dit artikel worden aanbevelingen gedaan aan de beheerder van Data Lake Storage voor het instellen van één gedeeld Data Lake-opslagaccount dat kan worden gebruikt in meerdere **actieve** HDInsight-clusters. Deze aanbevelingen zijn van toepassing op het hosten van meerdere beveiligde en niet-beveiligde Apache Hadoop-clusters op een gedeeld Data Lake Storage-account.

## <a name="data-lake-storage-file-and-folder-level-acls"></a>ACL's voor gegevensbestanden en mapniveau

De rest van dit artikel gaat ervan uit dat u een goede kennis hebt van acl.n. bestands- en mapniveau in Azure Data Lake Storage, die in detail wordt beschreven bij [Access-beheer in Azure Data Lake Storage.](../data-lake-store/data-lake-store-access-control.md)

## <a name="data-lake-storage-setup-for-multiple-hdinsight-clusters"></a>Data Lake Storage setup voor meerdere HDInsight clusters

Laten we een maphiërarchie op twee niveaus nemen om de aanbevelingen voor het gebruik van meerdere HDInsight-clusters met een Data Lake Storage-account uit te leggen. Overweeg dat u een Data Lake Storage-account hebt met de mapstructuur **/clusters/financiën.** Met deze structuur kunnen alle clusters die de financiële organisatie vereist/ clusters/finance als opslaglocatie gebruiken. In de toekomst, als een andere organisatie, zeg Marketing, HDInsight-clusters wil maken met behulp van hetzelfde Data Lake Storage-account, kunnen ze /clusters/marketing maken. Voor nu, laten we gewoon gebruik maken **van / clusters / financiën**.

Om ervoor te zorgen dat deze mapstructuur effectief wordt gebruikt door HDInsight-clusters, moet de beheerder van Data Lake Storage de juiste machtigingen toewijzen, zoals beschreven in de tabel. De machtigingen in de tabel komen overeen met Access-ACL's en niet met Standaard-ACL's.

|Map  |Machtigingen  |Gebruiker die eigenaar is  |Groep die eigenaar is  | Benoemde gebruiker | Benoemde gebruikersmachtigingen | Benoemde groep | Benoemde groepsmachtigingen |
|---------|---------|---------|---------|---------|---------|---------|---------|
|/ | rwxr-x  |beheerder |beheerder  |Service-principal |--x  |FINGRP FINGRP   |r-x         |
|/clusters | rwxr-x |beheerder |beheerder |Service-principal |--x  |FINGRP FINGRP |r-x         |
|/clusters/financiën | rwxr-x-t |beheerder |FINGRP FINGRP  |Service-principal |Rwx  |-  |-     |

In de tabel,

- **beheerder** is de maker en beheerder van het Data Lake Storage-account.
- **Serviceprincipal** is de Azure Active Directory (AAD) serviceprincipal die aan het account is gekoppeld.
- **FINGRP** is een gebruikersgroep die is gemaakt in AAD en die gebruikers van de financiële organisatie bevat.

Zie [Een AAD-toepassing maken](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)voor instructies voor het maken van een AAD-toepassing (die ook een serviceprincipal maakt. Zie [Groepen beheren in Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)voor instructies voor het maken van een gebruikersgroep in AAD.

Enkele belangrijke punten om te overwegen.

- De mapstructuur met twee niveaus (**/clusters/financiën/**) moet worden gemaakt en ingericht met de juiste machtigingen door de data lake-opslagbeheerder **voordat** u het opslagaccount voor clusters gebruikt. Deze structuur wordt niet automatisch gemaakt tijdens het maken van clusters.
- In het bovenstaande voorbeeld wordt aanbevolen de groep **/clusters/finance** in te stellen als **FINGRP** en **rx-toegang** tot FINGRP toe te staan tot de hele maphiërarchie vanaf de hoofdmap. Dit zorgt ervoor dat de leden van FINGRP vanaf de hoofdmap door de mapstructuur kunnen navigeren.
- In het geval dat verschillende AAD Service Principals clusters kunnen maken onder **/clusters/finance,** zorgt de sticky-bit (wanneer deze is ingesteld op de **financiële** map) ervoor dat mappen die door de ene serviceprincipal zijn gemaakt, niet door de andere kunnen worden verwijderd.
- Zodra de mapstructuur en machtigingen zijn ingevoerd, maakt het HDInsight-clustercreatieproces een clusterspecifieke opslaglocatie onder **/clusters/financiën/**. De opslag voor een cluster met de naam fincluster01 kan bijvoorbeeld **/clusters/finance/fincluster01**zijn. Het eigendom en de machtigingen voor de mappen die zijn gemaakt door het HDInsight-cluster worden hier weergegeven in de tabel.

    |Map  |Machtigingen  |Gebruiker die eigenaar is  |Groep die eigenaar is  | Benoemde gebruiker | Benoemde gebruikersmachtigingen | Benoemde groep | Benoemde groepsmachtigingen |
    |---------|---------|---------|---------|---------|---------|---------|---------|
    |/clusters/finanace/ fincluster01 | rwxr-x---  |Service-principal |FINGRP FINGRP  |- |-  |-   |-  |

## <a name="recommendations-for-job-input-and-output-data"></a>Aanbevelingen voor taakinvoer- en uitvoergegevens

We raden u aan gegevens in te voeren naar een taak en de uitvoer van een taak op te slaan in een map buiten **/clusters.** Dit zorgt ervoor dat zelfs als de clusterspecifieke map wordt verwijderd om wat opslagruimte terug te winnen, de taakinvoer en -uitgangen nog steeds beschikbaar zijn voor toekomstig gebruik. Zorg er in een dergelijk geval voor dat de maphiërarchie voor het opslaan van de taakinvoer en -uitvoer het juiste toegangsniveau voor de serviceprincipal mogelijk maakt.

## <a name="limit-on-clusters-sharing-a-single-storage-account"></a>Limiet voor clusters die één opslagaccount delen

De limiet voor het aantal clusters dat één Data Lake Storage-account kan delen, is afhankelijk van de werkbelasting die op deze clusters wordt uitgevoerd. Als u te veel clusters of zeer zware workloads op de clusters hebt die een opslagaccount delen, kan het binnendringen/binnendringen van opslag/uitgang van het opslagaccount worden beperkt.

## <a name="support-for-default-acls"></a>Ondersteuning voor standaard-ACL's

Bij het maken van een Service Principal met toegang tot de benoemde gebruiker (zoals in de bovenstaande tabel) raden we aan om de benoemde gebruiker **niet** toe te voegen met een standaard-ACL. Het inrichten van toegang met de naam gebruiker met behulp van standaard-ACL's resulteert in de toewijzing van 770 machtigingen voor het bezitten van de gebruiker, eigenaar-groep, en anderen. Hoewel deze standaardwaarde van 770 geen machtigingen wegneemt van de eigenaar (7) of de eigenaarvan de groep (7), neemt deze alle machtigingen voor anderen weg (0). Dit resulteert in een bekend probleem met een bepaalde use-case die in detail wordt besproken in de sectie [Bekende problemen en oplossingen.](#known-issues-and-workarounds)

## <a name="known-issues-and-workarounds"></a>Bekende problemen en oplossingen

In deze sectie worden de bekende problemen voor het gebruik van HDInsight met Data Lake Storage en hun tijdelijke oplossingen weergegeven.

### <a name="publicly-visible-localized-apache-hadoop-yarn-resources"></a>Openbaar zichtbare gelokaliseerde Apache Hadoop YARN-bronnen

Wanneer een nieuw Azure Data Lake Storage-account wordt gemaakt, wordt de hoofdmap automatisch ingericht met Access-ACL-machtigingsbits ingesteld op 770. De eigenaar van de hoofdmap is ingesteld op de gebruiker die het account heeft gemaakt (de gegevensopslagbeheerder) en de eigenaargroep is ingesteld op de primaire groep van de gebruiker die het account heeft gemaakt. Er is geen toegang voor "anderen".

Van deze instellingen is bekend dat ze van invloed zijn op een specifieke HDInsight-use-case die is vastgelegd in [YARN 247.](https://hwxmonarch.atlassian.net/browse/YARN-247) Vacatures kunnen mislukken met een foutbericht als volgt:

    Resource XXXX is not publicly accessible and as such cannot be part of the public cache.

Zoals vermeld in het GAREN JIRA gekoppeld eerder, terwijl het lokaliseren van openbare middelen, de localizer valideert dat alle gevraagde middelen zijn inderdaad openbaar door het controleren van hun machtigingen op de externe bestandssysteem. Elke LocalResource die niet aan die voorwaarde voldoet, wordt afgewezen voor lokalisatie. De controle op machtigingen, inclusief leestoegang tot het bestand voor "anderen". Dit scenario werkt niet out-of-the-box bij het hosten van HDInsight-clusters op Azure Data Lake, omdat Azure Data Lake alle toegang tot 'anderen' op hoofdmapniveau ontzegt.

#### <a name="workaround"></a>Tijdelijke oplossing

Stel lees-uitvoermachtigingen in voor **anderen** via de **/** hiërarchie, bijvoorbeeld bij , **/clusters** en **/clusters/finance,** zoals weergegeven in de bovenstaande tabel.

## <a name="see-also"></a>Zie ook

- [Snelstartgids: Clusters instellen in HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
- [Azure Data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters](hdinsight-hadoop-use-data-lake-storage-gen2.md)
