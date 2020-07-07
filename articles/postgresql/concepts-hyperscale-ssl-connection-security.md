---
title: TLS-grootschalige (Citus)-Azure Database for PostgreSQL
description: Instructies en informatie voor het configureren van Azure Database for PostgreSQL-grootschalige (Citus) en bijbehorende toepassingen voor het goed gebruiken van TLS-verbindingen.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 791eed9419375c7245488b8ec61a1c5481be382e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82580561"
---
# <a name="configure-tls-in-azure-database-for-postgresql---hyperscale-citus"></a>TLS configureren in Azure Database for PostgreSQL-grootschalige (Citus)
Client toepassings verbindingen met het grootschalige (Citus)-coördinator knooppunt vereisen Transport Layer Security (TLS), voorheen bekend als Secure Sockets Layer (SSL). Het afdwingen van TLS-verbindingen tussen uw database server en uw client toepassingen helpt bij het beveiligen van ' man-in-the-middle '-aanvallen door de gegevens stroom tussen de server en uw toepassing te versleutelen.

## <a name="enforcing-tls-connections"></a>TLS-verbindingen afdwingen
Voor alle Azure Database for PostgreSQL servers die via de Azure Portal worden ingericht, wordt het afdwingen van TLS-verbindingen standaard ingeschakeld. 

De verbindings reeksen die vooraf zijn gedefinieerd in de instellingen voor verbindings reeksen onder uw server in de Azure Portal bevatten ook de vereiste para meters voor algemene talen om verbinding te maken met uw database server met behulp van TLS. De TLS-para meter varieert op basis van de connector, bijvoorbeeld "SSL = True" of "sslmode = vereist" of "sslmode = required" en andere variaties.

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Zorg ervoor dat uw toepassing of Framework TLS-verbindingen ondersteunt
Voor sommige toepassings raamwerken die gebruikmaken van PostgreSQL voor hun database services, wordt TLS niet standaard ingeschakeld tijdens de installatie. Als uw PostgreSQL-server TLS-verbindingen afdwingt, maar de toepassing niet is geconfigureerd voor TLS, kan de toepassing geen verbinding maken met de database server. Raadpleeg de documentatie van uw toepassing voor informatie over het inschakelen van TLS-verbindingen.

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>Toepassingen waarvoor certificaat verificatie voor TLS-connectiviteit is vereist
In sommige gevallen vereist toepassingen een lokaal certificaat bestand dat is gegenereerd op basis van een certificaat bestand van een vertrouwde certificerings instantie (. CER) om veilig verbinding te kunnen maken. Het certificaat om verbinding te maken met een Azure Database for PostgreSQL-grootschalige (Citus) bevindt zich op https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem . Down load het certificaat bestand en sla het op naar uw voorkeurs locatie.

### <a name="connect-using-psql"></a>Verbinding maken met psql
In het volgende voor beeld ziet u hoe u verbinding maakt met uw grootschalige (Citus)-coördinator knooppunt met behulp van het psql-opdracht regel programma. Gebruik de `sslmode=verify-full` instelling Connection String om TLS-certificaat verificatie af te dwingen. Geef het pad van het lokale certificaat bestand door aan de `sslrootcert` para meter.

Hieronder ziet u een voor beeld van de psql-connection string:
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> Controleer of de waarde die is door gegeven, `sslrootcert` overeenkomt met het bestandspad voor het certificaat dat u hebt opgeslagen.

## <a name="next-steps"></a>Volgende stappen
Verbeter de beveiliging verder met de [firewall regels in azure database for PostgreSQL-grootschalige (Citus)](concepts-hyperscale-firewall-rules.md).
