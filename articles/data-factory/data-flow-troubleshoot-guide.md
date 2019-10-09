---
title: Problemen met Azure Data Factory gegevens stromen oplossen | Microsoft Docs
description: Meer informatie over het oplossen van problemen met gegevens stromen in Azure Data Factory.
services: data-factory
author: kromerm
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 10/08/2019
ms.author: makromer
ms.openlocfilehash: 5eafa0cc6284df5c969f63e2ab3fac4113fab417
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72178616"
---
# <a name="troubleshoot-azure-data-factory-data-flows"></a>Problemen met Azure Data Factory gegevens stromen oplossen

In dit artikel worden algemene probleemoplossings methoden voor gegevens stromen in Azure Data Factory besproken.

## <a name="error-message-df-sys-01-shadeddatabricksorgapachehadoopfsazureazureexception-commicrosoftazurestoragestorageexception-the-specified-container-does-not-exist"></a>Foutbericht: DF-SYS-01: gearceerd. databricks. org. apache. Hadoop. FS. Azure. AzureException: com. micro soft. Azure. storage. StorageException: De opgegeven container bestaat niet.

- **Symptomen**: Het uitvoeren van de gegevens voorbeeld, fout opsporing en pipeline-gegevens stroom is mislukt omdat de container niet bestaat

- **Oorzaak**: Wanneer dataset een container bevat die niet voor komt in de opslag

- **Resolutie**: Zorg ervoor dat de container waarnaar u verwijst in uw gegevensset bestaat

## <a name="error-message-df-sys-01-javalangassertionerror-assertion-failed-conflicting-directory-structures-detected-suspicious-paths"></a>Foutbericht: DF-SYS-01: Java. lang. AssertionError: bevestiging mislukt: Er zijn conflicterende Directory structuren gedetecteerd. Verdachte paden

- **Symptomen**: Joker tekens gebruiken in bron transformatie met Parquet-bestanden

- **Oorzaak**: Onjuiste of ongeldige Joker teken syntaxis

- **Resolutie**: Controleer de syntaxis van de joker tekens die u gebruikt in de opties voor de bron transformatie

## <a name="error-message-df-src-002-container-container-name-is-required"></a>Foutbericht: VG-SRC-002: ' container ' (container naam) is vereist

- **Symptomen**: Het uitvoeren van de gegevens voorbeeld, fout opsporing en pipeline-gegevens stroom is mislukt omdat de container niet bestaat

- **Oorzaak**: Wanneer dataset een container bevat die niet voor komt in de opslag

- **Resolutie**: Zorg ervoor dat de container waarnaar u verwijst in uw gegevensset bestaat

## <a name="error-message-df-uni-001-primarykeyvalue-has-incompatible-types-integertype-and-stringtype"></a>Foutbericht: DF-UNI-001: PrimaryKeyValue heeft incompatibele typen IntegerType en StringType

- **Symptomen**: Het uitvoeren van de gegevens voorbeeld, fout opsporing en pipeline-gegevens stroom is mislukt omdat de container niet bestaat

- **Oorzaak**: Treedt op bij het invoegen van een onjuist primair sleutel type in data base-sinks

- **Resolutie**: Gebruik een afgeleide kolom om de kolom te casten die u gebruikt voor de primaire sleutel in uw gegevens stroom, zodat deze overeenkomt met het gegevens type van de doel database

## <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-the-tcpip-connection-to-the-host-xxxxxdatabasewindowsnet-port-1433-has-failed-error-xxxxdatabasewindowsnet-verify-the-connection-properties-make-sure-that-an-instance-of-sql-server-is-running-on-the-host-and-accepting-tcpip-connections-at-the-port-make-sure-that-tcp-connections-to-the-port-are-not-blocked-by-a-firewall"></a>Foutbericht: DF-SYS-01: com. micro soft. sqlserver. JDBC. SQLServerException: De TCP/IP-verbinding met de host xxxxx.database.windows.net poort 1433 is mislukt. Fout: xxxx.database.windows.net. Controleer de verbindings eigenschappen. Zorg ervoor dat een exemplaar van SQL Server wordt uitgevoerd op de host en TCP/IP-verbindingen accepteert op de poort. Zorg ervoor dat TCP-verbindingen met de poort niet worden geblokkeerd door een firewall. "

- **Symptomen**: Kan geen voor beeld van gegevens weer geven of een pijp lijn uitvoeren met de database bron of sink

- **Oorzaak**: De data base wordt beveiligd door een firewall

- **Resolutie**: Open de firewall toegang tot de data base

## <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-there-is-already-an-object-named-xxxxxx-in-the-database"></a>Foutbericht: DF-SYS-01: com. micro soft. sqlserver. JDBC. SQLServerException: Er bevindt zich al een object met de naam ' xxxxxx ' in de data base.

- **Symptomen**: Sink kan de tabel niet maken

- **Oorzaak**: Er bevindt zich al een bestaande tabel naam in de doel database met dezelfde naam die is gedefinieerd in uw bron of in de gegevensset

- **Resolutie**: Wijzig de naam van de tabel die u wilt maken



## <a name="next-steps"></a>Volgende stappen

Probeer deze bronnen voor meer informatie over probleem oplossing:

*  [Data Factory Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory functie aanvragen](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-Video's](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN-forum](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack Overflow forum voor Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-informatie over Data Factory](https://twitter.com/hashtag/DataFactory)
