---
title: Azure SQL Edge configureren (preview-versie)
description: Meer informatie over het configureren van Azure SQL Edge (preview)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 5dcdd1604674ca56fb7a646d4c571d63bd2c0e3e
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596958"
---
# <a name="configure-azure-sql-edge-preview"></a>Azure SQL Edge configureren (preview-versie)

Azure SQL Edge ondersteunt configuratie via een van de volgende twee opties:

- Het gebruik van omgevings variabelen.
- Gebruik het bestand MSSQL. conf dat in de map/var/opt/MSSQL is geplaatst.

> [!NOTE]
> Als omgevings variabelen worden ingesteld, worden de instellingen overschreven die zijn opgegeven in het bestand MSSQL. conf.

## <a name="configure-using-environment-variables"></a>Configureren met omgevings variabelen

In Azure SQL Edge worden verschillende omgevings variabelen weer gegeven die kunnen worden gebruikt voor het configureren van de SQL Edge-container. Deze omgevings variabelen zijn een subset van de omgevings variabelen die beschikbaar zijn voor SQL Server on Linux. Zie [omgevings variabelen](/sql/linux/sql-server-linux-configure-environment-variables/)voor meer informatie over SQL Server on Linux omgevings variabelen.

De volgende SQL Server on Linux omgevings variabelen worden niet ondersteund voor Azure SQL Edge. Indien gedefinieerd, worden deze omgevings variabelen genegeerd tijdens het initialiseren van de container.

| Omgevingsvariabele | Beschrijving |
|-----|-----|
| **MSSQL_ENABLE_HADR** | Beschikbaarheids groep inschakelen. Bijvoorbeeld: ' 1 ' is ingeschakeld en ' 0 ' is uitgeschakeld |

> [!IMPORTANT]
> De omgevings variabele *MSSQL_PID* voor SQL Edge accepteert alleen **Premium** en **Developer** als geldige waarden. Azure SQL Edge biedt geen ondersteuning voor initialisatie met behulp van een product code.

> [!NOTE]
> Voor het downloaden van de gebruiksrecht overeenkomst voor de Azure SQL Edge-gebruiksrecht overeenkomst, verwijzen wij u naar de [licentieovereenkomst voor eind gebruikers](https://go.microsoft.com/fwlink/?linkid=2128283).

### <a name="specifying-the-environment-variables"></a>De omgevings variabelen opgeven

U kunt omgevings variabelen voor SQL Edge opgeven wanneer u Azure SQL Edge implementeert via de [Azure Portal](deploy-portal.md). Dit kan worden toegevoegd in de sectie omgevings variabelen van de module-implementatie of als onderdeel van de optie voor het maken van de container, zoals hieronder wordt beschreven.

*Opties instellen met omgevings variabelen*

![instellen met omgevings variabelen lijst](media/configure/set-environment-variables.png)

*Instellen met behulp van opties voor het maken van containers*

![instellen met behulp van opties voor het maken van containers](media/configure/set-environment-variables-using-create-options.png)

## <a name="configure-using-mssqlconf-file"></a>Configureren met het bestand MSSQL. conf

Azure SQL Edge bevat niet het [configuratie hulpprogramma MSSQL-conf](/sql/linux/sql-server-linux-configure-mssql-conf/) , zoals SQL Server on Linux wel, omdat het bestand MSSQL. conf hand matig moet worden geconfigureerd en geplaatst in de permanente opslag schijf die is toegewezen aan de map/var/opt/MSSQL/in de SQL-Edge-module. Bij het implementeren van SQL Edge vanuit Azure Marketplace, wordt deze toewijzing opgegeven als de optie voor het koppelen * * in de optie voor het maken van een container

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
</br></br>
|Optie|Beschrijving|
|:---|:---|
|**Feedback van klanten** | Kies of SQL Server feedback naar micro soft verzendt. |
|**Database Mail profiel** | Stel het standaard-e-mail profiel voor de data base in voor SQL Server on Linux. |
|**Hoge beschikbaarheid** | Beschikbaarheids groepen inschakelen. |
|**Micro soft Distributed Transaction Coordinator** | MSDTC op Linux configureren en problemen oplossen. Aanvullende configuratie opties voor gedistribueerde trans acties worden ook niet ondersteund voor SQL-rand. Voor meer informatie over deze aanvullende configuratie opties raadpleegt [u MSDTC configureren](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#msdtc) |
|**MLServices-gebruiksrecht overeenkomsten** | Accepteer R-en python-gebruiksrecht overeenkomsten voor Machine Learning Services-pakketten. Is alleen van toepassing op SQL Server 2019.|
|**outboundnetworkaccess** |Schakel uitgaande netwerk toegang in voor [Machine Learning Services](/sql/linux/sql-server-linux-setup-machine-learning/) R-, python-en Java-extensies.|

Hieronder vindt u een voor beeld van een MSSQL. conf-bestand, dat geschikt is voor SQL Edge. Zie [MSSQL. conf-indeling](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#mssql-conf-format)voor meer informatie over de indeling van het bestand MSSQL. conf.

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

## <a name="next-step"></a>Volgende stap

- [Verbinding maken met Azure SQL Edge](connect.md)
- [Een end-to-end IoT-oplossing bouwen met SQL Edge](tutorial-deploy-azure-resources.md)
