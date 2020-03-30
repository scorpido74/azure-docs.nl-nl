---
title: SSL - Azure Database voor PostgreSQL - Single Server
description: Instructies en informatie over het configureren van SSL-connectiviteit voor Azure Database voor PostgreSQL - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 303da4dcb68a79e69254f6610afc0003bf0aa22c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476997"
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql---single-server"></a>SSL-connectiviteit configureren in Azure Database voor PostgreSQL - Single Server

Azure Database voor PostgreSQL verbindt uw clienttoepassingen het liefst met de PostgreSQL-service via Secure Sockets Layer (SSL). Het afdwingen van SSL-verbindingen tussen uw databaseserver en uw clienttoepassingen helpt te beschermen tegen "man-in-the-middle"-aanvallen door de gegevensstroom tussen de server en uw toepassing te versleutelen.

Standaard is de PostgreSQL-databaseservice geconfigureerd om ssl-verbinding te vereisen. U ervoor kiezen om het vereisen van SSL uit te schakelen als uw clienttoepassing geen SSL-connectiviteit ondersteunt.

## <a name="enforcing-ssl-connections"></a>SSL-verbindingen afdwingen

Voor alle Azure Database voor PostgreSQL-servers die zijn ingericht via de Azure-portal en CLI, is handhaving van SSL-verbindingen standaard ingeschakeld. 

Ook verbindingstekenreeksen die vooraf zijn gedefinieerd in de instellingen voor verbindingstekenreeksen onder uw server in de Azure-portal, bevatten de vereiste parameters voor algemene talen om verbinding te maken met uw databaseserver via SSL. De SSL-parameter varieert op basis van de connector, bijvoorbeeld "ssl=true" of "sslmode=require" of "sslmode=required" en andere varianten.

## <a name="configure-enforcement-of-ssl"></a>Handhaving van SSL configureren

U het afdwingen van SSL-connectiviteit optioneel uitschakelen. Microsoft Azure raadt aan om altijd **SSL-verbindingsinstelling afdwingen** in te schakelen voor verbeterde beveiliging.

### <a name="using-the-azure-portal"></a>Azure Portal gebruiken

Ga naar uw Azure Database voor PostgreSQL-server en klik op **Verbindingsbeveiliging**. Gebruik de schakelknop om de instelling **SSL-verbinding afdwingen** in of uit te schakelen. Klik vervolgens op **Opslaan.**

![Verbindingsbeveiliging - SSL afdwingen uitschakelen](./media/concepts-ssl-connection-security/1-disable-ssl.png)

U de instelling bevestigen door de pagina **Overzicht** te bekijken om de **statusindicator voor SSL af te dwingen.**

### <a name="using-azure-cli"></a>Azure CLI gebruiken

U de **parameter ssl-handhaving** in- of uitschakelen met `Enabled` respectievelijk de parameter ssl-handhaving of `Disabled` waarden in Azure CLI.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Ervoor zorgen dat uw toepassing of framework SSL-verbindingen ondersteunt

Sommige toepassingsframeworks die PostgreSQL gebruiken voor hun databaseservices, maken SSL tijdens de installatie niet standaard mogelijk. Als uw PostgreSQL-server SSL-verbindingen afdwingt, maar de toepassing niet is geconfigureerd voor SSL, kan het zijn dat de toepassing geen verbinding maakt met uw databaseserver. Raadpleeg de documentatie van uw toepassing voor meer informatie over het inschakelen van SSL-verbindingen.

## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Toepassingen waarvoor certificaatverificatie voor SSL-connectiviteit vereist is

In sommige gevallen vereisen toepassingen een lokaal certificaatbestand dat is gegenereerd uit een bestand van een vertrouwde Certificaatautoriteit (CA) om veilig verbinding te maken. Het certificaat om verbinding te maken met een https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pemAzure Database voor PostgreSQL-server bevindt zich op . Download het certificaatbestand en sla het op op de gewenste locatie.

### <a name="connect-using-psql"></a>Verbinding maken met psql

In het volgende voorbeeld ziet u hoe u verbinding maakt met uw PostgreSQL-server met behulp van het psql-command-line-hulpprogramma. Gebruik `sslmode=verify-full` de instelling voor verbindingstekenreeksen om ssl-certificaatverificatie af te dwingen. Geef het lokale certificaatbestandspad door aan de `sslrootcert` parameter.

De volgende opdracht is een voorbeeld van de psql-verbindingstekenreeks:

```shell
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> Controleer of de `sslrootcert` waarde die is doorgegeven aan het bestandspad overeenkomt met het certificaat dat u hebt opgeslagen.

## <a name="next-steps"></a>Volgende stappen

Bekijk verschillende toepassingsverbindingsopties in [verbindingsbibliotheken voor Azure Database voor PostgreSQL](concepts-connection-libraries.md).
