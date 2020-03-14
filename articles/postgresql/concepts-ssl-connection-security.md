---
title: SSL-Azure Database for PostgreSQL-één server
description: Instructies en informatie over het configureren van SSL-connectiviteit voor Azure Database for PostgreSQL-één server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: c235562834ae78a12b690fcd1b96d6a3640e0c66
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371661"
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql---single-server"></a>SSL-connectiviteit configureren in Azure Database for PostgreSQL-één server

Azure Database for PostgreSQL geven de voor keur aan uw client toepassingen te koppelen aan de PostgreSQL-service met behulp van Secure Sockets Layer (SSL). Het afdwingen van SSL-verbindingen tussen uw database server en uw client toepassingen biedt beveiliging tegen man-in-the-middle-aanvallen door de gegevens stroom tussen de server en uw toepassing te versleutelen.

De PostgreSQL-database service is standaard zo geconfigureerd dat SSL-verbinding vereist is. U kunt ervoor kiezen om SSL vereist uit te scha kelen als uw client toepassing geen ondersteuning biedt voor SSL-connectiviteit.

## <a name="enforcing-ssl-connections"></a>SSL-verbindingen afdwingen

Voor alle Azure Database for PostgreSQL servers die via de Azure Portal en CLI zijn ingericht, wordt het afdwingen van SSL-verbindingen standaard ingeschakeld. 

De verbindings reeksen die vooraf zijn gedefinieerd in de instellingen voor verbindings reeksen onder uw server in de Azure Portal bevatten ook de vereiste para meters voor algemene talen om verbinding te maken met uw database server met behulp van SSL. De SSL-para meter varieert op basis van de connector, bijvoorbeeld "SSL = True" of "sslmode = vereist" of "sslmode = required" en andere variaties.

## <a name="configure-enforcement-of-ssl"></a>Afdwinging van SSL configureren

U kunt eventueel het afdwingen van SSL-connectiviteit uitschakelen. Microsoft Azure wordt aanbevolen SSL- **verbindings instelling afdwingen** altijd in te scha kelen voor verbeterde beveiliging.

### <a name="using-the-azure-portal"></a>Azure Portal gebruiken

Ga naar uw Azure Database for PostgreSQL-server en klik op **verbindings beveiliging**. Gebruik de wissel knop om de instelling SSL- **verbinding afdwingen** in of uit te scha kelen. Klik vervolgens op **Opslaan**.

![Verbindings beveiliging-SSL afdwingen uitschakelen](./media/concepts-ssl-connection-security/1-disable-ssl.png)

U kunt de instelling bevestigen door de pagina **overzicht** te bekijken om de **SSL-afdwingings status** indicator te bekijken.

### <a name="using-azure-cli"></a>Azure CLI gebruiken

U kunt de **SSL-afdwinging-** para meter in-of uitschakelen met behulp van `Enabled` of `Disabled` waarden respectievelijk in azure cli.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Zorg ervoor dat uw toepassing of Framework SSL-verbindingen ondersteunt

Voor sommige toepassings raamwerken die gebruikmaken van PostgreSQL voor hun database services, wordt SSL niet standaard ingeschakeld tijdens de installatie. Als uw PostgreSQL-server SSL-verbindingen afdwingt, maar de toepassing niet is geconfigureerd voor SSL, kan de toepassing geen verbinding maken met de database server. Raadpleeg de documentatie van uw toepassing voor meer informatie over het inschakelen van SSL-verbindingen.

## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Toepassingen waarvoor certificaat verificatie is vereist voor SSL-connectiviteit

In sommige gevallen vereist toepassingen een lokaal certificaat bestand dat is gegenereerd op basis van een certificaat bestand van een vertrouwde certificerings instantie (. CER) om veilig verbinding te kunnen maken. Het certificaat om verbinding te maken met een Azure Database for PostgreSQL server bevindt zich op https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem. Down load het certificaat bestand en sla het op naar uw voorkeurs locatie.

### <a name="connect-using-psql"></a>Verbinding maken met psql

In het volgende voor beeld ziet u hoe u verbinding maakt met uw PostgreSQL-server met behulp van het opdracht regel programma psql. Gebruik de instelling `sslmode=verify-full` connection string om de verificatie van het SSL-certificaat af te dwingen. Geef het pad van het lokale certificaat bestand door aan de para meter `sslrootcert`.

De volgende opdracht is een voor beeld van de psql-connection string:

```shell
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> Controleer of de waarde die is door gegeven aan `sslrootcert` overeenkomt met het bestandspad voor het certificaat dat u hebt opgeslagen.

## <a name="tls-connectivity-in-azure-database-for-postgresql-single-server"></a>TLS-connectiviteit in Azure Database for PostgreSQL één server

Azure Database for PostgreSQL-één server ondersteunt versleuteling voor clients die verbinding maken met uw database server met behulp van Transport Layer Security (TLS). TLS is een industrie standaard protocol dat beveiligde netwerk verbindingen tussen uw database server-en client toepassingen waarborgt, zodat u kunt voldoen aan de nalevings vereisten.

### <a name="tls-settings"></a>TLS-instellingen

Klanten kunnen nu de TLS-versie afdwingen voor de client die verbinding maakt met hun Azure Database for PostgreSQL één server. Als u de optie TLS wilt gebruiken, gebruikt u de instelling **minimale TLS-versie** . De volgende waarden zijn toegestaan voor deze optie-instelling:

|  Minimale TLS-instelling             | TLS-versie wordt ondersteund                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled (standaard) | Geen TLS vereist                      |
| TLS1_0                           | TLS 1,0, TLS 1,1, TLS 1,2 en hoger |
| TLS1_1                           | TLS 1,1, TLS 1,2 en hoger          |
| TLS1_2                           | TLS-versie 1,2 en hoger           |


Als u bijvoorbeeld deze versie van een TLS-instelling instelt op TLS 1,0, betekent dit dat uw server verbindingen met clients toestaat die gebruikmaken van TLS 1,0, 1,1 en 1.2 +. U kunt dit ook instellen op 1,2 zodat u alleen verbindingen van clients toestaat die gebruikmaken van TLS 1,2 en alle verbindingen met TLS 1,0 en TLS 1,1 worden geweigerd.

> [!Note] 
> Azure Database for PostgreSQL van één server wordt standaard TLS uitgeschakeld voor alle nieuwe servers.
>
> Momenteel worden de TLS-versies ondersteund byAzure-Data Base voor PostgreSQL TLS 1,0, 1,1 en 1,2.

Zie [TLS-instelling configureren](howto-tls-configurations.md)voor meer informatie over het instellen van de TLS-instelling voor uw Azure database for PostgreSQL één server.

## <a name="next-steps"></a>Volgende stappen

Bekijk verschillende opties voor toepassings connectiviteit in [verbindings bibliotheken voor Azure database for PostgreSQL](concepts-connection-libraries.md).
