---
title: SSL - Hyperscale (Citus) - Azure Database voor PostgreSQL
description: Instructies en informatie om Azure Database te configureren voor PostgreSQL - Hyperscale (Citus) en bijbehorende toepassingen om SSL-verbindingen correct te gebruiken.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 3e4ef5d2d6db3a3d4f8923f47079f2484639a751
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74973982"
---
# <a name="configure-ssl-in-azure-database-for-postgresql---hyperscale-citus"></a>SSL configureren in Azure Database voor PostgreSQL - Hyperscale (Citus)
Clienttoepassingsverbindingen met het knooppunt Hyperscale (Citus) vereisen Secure Sockets Layer (SSL). Het afdwingen van SSL-verbindingen tussen uw databaseserver en uw clienttoepassingen helpt te beschermen tegen "man-in-the-middle"-aanvallen door de gegevensstroom tussen de server en uw toepassing te versleutelen.

## <a name="enforcing-ssl-connections"></a>SSL-verbindingen afdwingen
Voor alle Azure Database voor PostgreSQL-servers die via de Azure-portal zijn ingericht, is handhaving van SSL-verbindingen standaard ingeschakeld. 

Ook verbindingstekenreeksen die vooraf zijn gedefinieerd in de instellingen voor verbindingstekenreeksen onder uw server in de Azure-portal, bevatten de vereiste parameters voor algemene talen om verbinding te maken met uw databaseserver via SSL. De SSL-parameter varieert op basis van de connector, bijvoorbeeld "ssl=true" of "sslmode=require" of "sslmode=required" en andere varianten.

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Ervoor zorgen dat uw toepassing of framework SSL-verbindingen ondersteunt
Sommige toepassingsframeworks die PostgreSQL gebruiken voor hun databaseservices, maken SSL tijdens de installatie niet standaard mogelijk. Als uw PostgreSQL-server SSL-verbindingen afdwingt, maar de toepassing niet is geconfigureerd voor SSL, kan het zijn dat de toepassing geen verbinding maakt met uw databaseserver. Raadpleeg de documentatie van uw toepassing voor meer informatie over het inschakelen van SSL-verbindingen.

## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Toepassingen waarvoor certificaatverificatie voor SSL-connectiviteit vereist is
In sommige gevallen vereisen toepassingen een lokaal certificaatbestand dat is gegenereerd uit een bestand van een vertrouwde Certificaatautoriteit (CA) om veilig verbinding te maken. Het certificaat om verbinding te maken met een Azure Database voor PostgreSQL - Hyperscale (Citus) bevindt zich op https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem. Download het certificaatbestand en sla het op op de gewenste locatie.

### <a name="connect-using-psql"></a>Verbinding maken met psql
In het volgende voorbeeld ziet u hoe u verbinding maken met uw knooppunt voor Hyperscale-coördinator (Citus) met behulp van het psql-opdrachtregelhulpprogramma. Gebruik `sslmode=verify-full` de instelling voor verbindingstekenreeksen om ssl-certificaatverificatie af te dwingen. Geef het lokale certificaatbestandspad door aan de `sslrootcert` parameter.

Hieronder vindt u een voorbeeld van de psql-verbindingstekenreeks:
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> Controleer of de `sslrootcert` waarde die is doorgegeven aan het bestandspad overeenkomt met het certificaat dat u hebt opgeslagen.

## <a name="next-steps"></a>Volgende stappen
Verhoog de beveiliging verder met [Firewall-regels in Azure Database voor PostgreSQL - Hyperscale (Citus).](concepts-hyperscale-firewall-rules.md)
