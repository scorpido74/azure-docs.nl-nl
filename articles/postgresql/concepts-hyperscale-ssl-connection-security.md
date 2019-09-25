---
title: SSL configureren in Azure Database for PostgreSQL-grootschalige (Citus)
description: Instructies en informatie voor het configureren van Azure Database for PostgreSQL-grootschalige (Citus) en bijbehorende toepassingen voor een juiste gebruik van SSL-verbindingen.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: be02c9114f30a63fe710cfd2fc56347bfc68fab5
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71273722"
---
# <a name="configure-ssl-in-azure-database-for-postgresql---hyperscale-citus"></a>SSL configureren in Azure Database for PostgreSQL-grootschalige (Citus)
Client toepassings verbindingen met het grootschalige (Citus)-coördinator knooppunt vereisen Secure Sockets Layer (SSL). Het afdwingen van SSL-verbindingen tussen uw database server en uw client toepassingen biedt beveiliging tegen man-in-the-middle-aanvallen door de gegevens stroom tussen de server en uw toepassing te versleutelen.

## <a name="enforcing-ssl-connections"></a>SSL-verbindingen afdwingen
Voor alle Azure Database for PostgreSQL servers die via de Azure Portal zijn ingericht, wordt het afdwingen van SSL-verbindingen standaard ingeschakeld. 

De verbindings reeksen die vooraf zijn gedefinieerd in de instellingen voor verbindings reeksen onder uw server in de Azure Portal bevatten ook de vereiste para meters voor algemene talen om verbinding te maken met uw database server met behulp van SSL. De SSL-para meter varieert op basis van de connector, bijvoorbeeld "SSL = True" of "sslmode = vereist" of "sslmode = required" en andere variaties.

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Zorg ervoor dat uw toepassing of Framework SSL-verbindingen ondersteunt
Voor sommige toepassings raamwerken die gebruikmaken van PostgreSQL voor hun database services, wordt SSL niet standaard ingeschakeld tijdens de installatie. Als uw PostgreSQL-server SSL-verbindingen afdwingt, maar de toepassing niet is geconfigureerd voor SSL, kan de toepassing geen verbinding maken met de database server. Raadpleeg de documentatie van uw toepassing voor meer informatie over het inschakelen van SSL-verbindingen.

## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Toepassingen waarvoor certificaat verificatie is vereist voor SSL-connectiviteit
In sommige gevallen vereist toepassingen een lokaal certificaat bestand dat is gegenereerd op basis van een certificaat bestand van een vertrouwde certificerings instantie (. CER) om veilig verbinding te kunnen maken. Het certificaat om verbinding te maken met een Azure Database for PostgreSQL-grootschalige (Citus) bevindt zich op https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem. Down load het certificaat bestand en sla het op naar uw voorkeurs locatie.

### <a name="connect-using-psql"></a>Verbinding maken met psql
In het volgende voor beeld ziet u hoe u verbinding maakt met uw grootschalige (Citus)-coördinator knooppunt met behulp van het psql-opdracht regel programma. Gebruik de `sslmode=verify-full` instelling Connection String voor het afdwingen van SSL-certificaat verificatie. Geef het pad van het lokale certificaat bestand `sslrootcert` door aan de para meter.

Hieronder ziet u een voor beeld van de psql-connection string:
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> Controleer of de waarde die is `sslrootcert` door gegeven, overeenkomt met het bestandspad voor het certificaat dat u hebt opgeslagen.

## <a name="next-steps"></a>Volgende stappen
Verbeter de beveiliging verder met de [firewall regels in azure database for PostgreSQL-grootschalige (Citus)](concepts-hyperscale-firewall-rules.md).
