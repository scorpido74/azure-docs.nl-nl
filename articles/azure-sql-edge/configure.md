---
title: Azure SQL Edge configureren (preview-versie)
description: Meer informatie over het configureren van Azure SQL Edge (preview).
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: c38bb6100665cc9456b66608660bdca520b934c6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84636237"
---
# <a name="configure-azure-sql-edge-preview"></a>Azure SQL Edge configureren (preview-versie)

Azure SQL Edge ondersteunt configuratie via een van de volgende twee opties:

- Omgevingsvariabelen
- Een MSSQL. conf-bestand dat in de map/var/opt/MSSQL is geplaatst

> [!NOTE]
> Als omgevings variabelen worden ingesteld, worden de instellingen overschreven die zijn opgegeven in het bestand MSSQL. conf.

## <a name="configure-by-using-environment-variables"></a>Configureren met behulp van omgevings variabelen

In Azure SQL Edge worden verschillende omgevings variabelen weer gegeven die kunnen worden gebruikt voor het configureren van de SQL Edge-container. Deze omgevings variabelen zijn een subset van de beschik bare voor SQL Server on Linux. Zie [omgevings variabelen](/sql/linux/sql-server-linux-configure-environment-variables/)voor meer informatie over SQL Server on Linux omgevings variabelen.

De volgende SQL Server on Linux omgevings variabele wordt niet ondersteund voor Azure SQL Edge. Indien gedefinieerd, wordt deze omgevings variabele genegeerd tijdens het initialiseren van de container.

| Omgevingsvariabele | Description |
|-----|-----|
| **MSSQL_ENABLE_HADR** | Beschikbaarheids groep inschakelen. Bijvoorbeeld: **1** is ingeschakeld en **0** is uitgeschakeld. |

> [!IMPORTANT]
> De omgevings variabele **MSSQL_PID** voor SQL Edge accepteert alleen **Premium** en **Developer** als geldige waarden. Azure SQL Edge biedt geen ondersteuning voor initialisatie met behulp van een product code.

> [!NOTE]
> Down load de [licentie voorwaarden voor micro soft-software](https://go.microsoft.com/fwlink/?linkid=2128283) voor Azure SQL Edge.

### <a name="specify-the-environment-variables"></a>De omgevings variabelen opgeven

Geef omgevings variabelen voor SQL Edge op wanneer u de service implementeert via de [Azure Portal](deploy-portal.md). U kunt deze toevoegen in het gedeelte **omgevings variabelen** van de module-implementatie of als onderdeel van de opties voor het maken van de **container**.

Waarden toevoegen in **omgevings variabelen**.

![Instellen met behulp van omgevings variabelen lijst](media/configure/set-environment-variables.png)

Waarden toevoegen in de opties voor het **maken van containers**.

![Instellen met behulp van de optie voor het maken van containers](media/configure/set-environment-variables-using-create-options.png)

## <a name="configure-by-using-an-mssqlconf-file"></a>Configureren met behulp van een MSSQL. conf-bestand

Azure SQL Edge bevat niet het [configuratie hulpprogramma MSSQL-conf,](/sql/linux/sql-server-linux-configure-mssql-conf/) zoals SQL Server on Linux wel. U moet het bestand MSSQL. conf hand matig configureren en het in het permanente opslag station plaatsen dat is gekoppeld aan de map/var/opt/MSSQL/in de SQL-Edge-module. Wanneer u de SQL-rand vanuit Azure Marketplace implementeert, wordt deze toewijzing opgegeven als de optie **koppelen** in de opties voor het maken van de **container**.

```json
    {
        "Mounts": [
          {
            "Type": "volume",
            "Source": "sqlvolume",
            "Target": "/var/opt/mssql"
          }
        ]
      }
    }
```

De volgende MSSQL. conf-opties zijn niet van toepassing op SQL Edge:

|Optie|Description|
|:---|:---|
|**Feedback van klanten** | Kies of SQL Server feedback naar micro soft verzendt. |
|**Data base mail-profiel** | Stel het standaard-e-mail profiel voor de data base in voor SQL Server on Linux. |
|**Hoge beschikbaarheid** | Beschikbaarheids groepen inschakelen. |
|**Micro soft Distributed Transaction Coordinator** | MSDTC op Linux configureren en problemen oplossen. Aanvullende configuratie opties voor gedistribueerde trans acties worden niet ondersteund voor SQL-rand. Zie [Configure MSDTC](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#msdtc)(Engelstalig) voor meer informatie over deze aanvullende configuratie opties. |
|**MLServices-gebruiksrecht overeenkomsten** | Accepteer R-en python-gebruiksrecht overeenkomsten voor Azure Machine Learning-pakketten. Is alleen van toepassing op SQL Server 2019.|
|**outboundnetworkaccess** |Schakel uitgaande netwerk toegang in voor [Machine Learning Services](/sql/linux/sql-server-linux-setup-machine-learning/) R-, python-en Java-extensies.|

Het volgende voor beeld van het bestand MSSQL. conf werkt voor SQL Edge. Zie [MSSQL. conf-indeling](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#mssql-conf-format)voor meer informatie over de indeling van een MSSQL. conf-bestand.

```ini
[EULA]
accepteula = Y

[coredump]
captureminiandfull = true
coredumptype = full

[filelocation]
defaultbackupdir = /var/opt/mssql/backup/
defaultdatadir = /var/opt/mssql/data/
defaultdumpdir = /var/opt/mssql/data/
defaultlogdir = /var/opt/mssql/log/

[language]
lcid = 1033

[memory]
memorylimitmb = 6144

[sqlagent]
errorlogfile = /var/opt/mssql/log/sqlagentlog.log
errorlogginglevel = 7

[traceflag]
traceflag0 = 3604
traceflag1 = 3605
traceflag2 = 1204
```

## <a name="next-steps"></a>Volgende stappen

- [Verbinding maken met Azure SQL Edge](connect.md)
- [Een end-to-end IoT-oplossing bouwen met SQL Edge](tutorial-deploy-azure-resources.md)
