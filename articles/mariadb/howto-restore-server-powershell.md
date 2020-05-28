---
title: Backup en Restore-Azure PowerShell-Azure Database for MariaDB
description: Meer informatie over het maken van een back-up en het herstellen van een server in Azure Database for MariaDB met behulp van Azure PowerShell.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurepowershell
ms.topic: conceptual
ms.date: 05/26/2020
ms.openlocfilehash: c16c18a7056b2d23b64caeca4a88905b3dd2557c
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84041943"
---
# <a name="how-to-back-up-and-restore-an-azure-database-for-mariadb-server-using-powershell"></a>Een back-up maken van een Azure Database for MariaDB-server en deze herstellen met Power shell

Er wordt regel matig een back-up van Azure Database for MariaDB servers gemaakt om herstel functies in te scha kelen. Met deze functie kunt u de server en alle bijbehorende data bases naar een eerder tijdstip herstellen op een nieuwe server.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze hand leiding te volt ooien:

- De [AZ Power shell-module](https://docs.microsoft.com/powershell/azure/install-az-ps) die lokaal is geïnstalleerd of [Azure Cloud shell](https://shell.azure.com/) in de browser
- Een [Azure database for MariaDB server](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Hoewel de Power shell-module AZ. MariaDb in preview is, moet u deze afzonderlijk van de AZ Power shell-module installeren met behulp van de volgende opdracht: `Install-Module -Name Az.MariaDb -AllowPrerelease` .
> Zodra de Power shell-module AZ. MariaDb algemeen beschikbaar is, wordt het onderdeel van toekomstige AZ Power shell-module releases en is deze systeem eigen beschikbaar vanuit Azure Cloud Shell.

Als u Power shell lokaal wilt gebruiken, maakt u verbinding met uw Azure-account met behulp van de cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-backup-configuration"></a>Back-upconfiguratie instellen

Bij het maken van de server maakt u de keuze tussen het configureren van uw server voor lokaal redundante of geografisch redundante back-ups.

> [!NOTE]
> Nadat een server is gemaakt, is het soort redundantie die het heeft, geografisch redundant versus lokaal redundant en kan niet worden gewijzigd.

Tijdens het maken van een server via de `New-AzMariaDbServer` opdracht, bepaalt de para meter **GeoRedundantBackup** de optie voor de redundantie van back-ups. Als deze functie is **ingeschakeld**, worden er geo redundante back-ups gemaakt. Als deze functie is **uitgeschakeld**, worden er lokaal redundante back-ups gemaakt.

De Bewaar periode voor back-ups is ingesteld met de para meter **BackupRetentionDay** .

Zie [een Azure database for MariaDB-server maken met Power shell](quickstart-create-mariadb-server-database-using-azure-powershell.md)voor meer informatie over het instellen van deze waarden tijdens het maken van een server.

De Bewaar periode van een back-up van een server kan als volgt worden gewijzigd:

```azurepowershell-interactive
Update-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -BackupRetentionDay 10
```

In het vorige voor beeld wordt de Bewaar periode voor back-ups van mydemoserver gewijzigd in 10 dagen.

De Bewaar periode voor back-ups bepaalt hoe ver terug een herstel naar een bepaald tijdstip kan worden opgehaald, omdat het is gebaseerd op beschik bare back-ups. Herstel naar een bepaald tijdstip wordt verderop in de volgende sectie beschreven.

## <a name="server-point-in-time-restore"></a>Server herstel naar een bepaald tijdstip

U kunt de server naar een eerder tijdstip herstellen. De herstelde gegevens worden gekopieerd naar een nieuwe server en de bestaande server blijft ongewijzigd. Als een tabel bijvoorbeeld per ongeluk wordt verwijderd, kunt u de herstel bewerking op het tijdstip herstellen. Vervolgens kunt u de ontbrekende tabel en gegevens ophalen van de herstelde kopie van de server.

Als u de server wilt herstellen, gebruikt u de `Restore-AzMariaDbServer` Power shell-cmdlet.

### <a name="run-the-restore-command"></a>Voer de opdracht herstellen uit

Voer het volgende voor beeld uit in Power shell om de server te herstellen.

```azurepowershell-interactive
$restorePointInTime = (Get-Date).AddMinutes(-10)
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMariaDbServer -Name mydemoserver-restored -ResourceGroupName myresourcegroup -RestorePointInTime $restorePointInTime -UsePointInTimeRestore
```

De para meters set **PointInTimeRestore** van de `Restore-AzMariaDbServer` cmdlet vereist de volgende para meters:

| Instelling | Voorgestelde waarde | Beschrijving  |
| --- | --- | --- |
| ResourceGroupName |  myResourceGroup |  De resource groep waar de bron server zich bevindt.  |
| Naam | mydemoserver-restored | De naam van de nieuwe server die door de opdracht restore is gemaakt. |
| RestorePointInTime | 2020-03-13T13:59:00Z | Selecteer een punt in de tijd om te herstellen. Deze datum en tijd moet binnen de back-upretentieperiode van de bronserver vallen. Gebruik de ISO8601 datum-en tijd notatie. U kunt bijvoorbeeld uw eigen lokale tijd zone gebruiken, zoals **2020-03-13T05:59:00-08:00**. U kunt ook de UTC Zulu-indeling gebruiken, bijvoorbeeld **2018-03-13T13:59:00Z**. |
| UsePointInTimeRestore | `<SwitchParameter>` | Gebruik de punt-in-time-modus om te herstellen. |

Wanneer u een server naar een eerder tijdstip herstelt, wordt er een nieuwe server gemaakt. De oorspronkelijke server en de bijbehorende data bases van het opgegeven tijdstip worden naar de nieuwe server gekopieerd.

De waarden voor de locatie en de prijs categorie voor de herstelde server blijven hetzelfde als de oorspronkelijke server.

Nadat het herstel proces is voltooid, zoekt u de nieuwe server en controleert u of de gegevens correct zijn hersteld. De nieuwe server heeft dezelfde aanmeldings naam en hetzelfde wacht woord voor de server beheerder op het moment dat de herstel bewerking werd gestart. Het wacht woord kan worden gewijzigd op de pagina **overzicht** van de nieuwe server.

De nieuwe server die tijdens het herstellen is gemaakt, heeft geen VNet-service-eind punten die bestonden op de oorspronkelijke server. Deze regels moeten afzonderlijk worden ingesteld voor de nieuwe server. Firewall regels van de oorspronkelijke server worden hersteld.

## <a name="geo-restore"></a>Geo-herstel

Als u uw server voor geografisch redundante back-ups hebt geconfigureerd, kan een nieuwe server worden gemaakt op basis van de back-up van de bestaande server. Deze nieuwe server kan worden gemaakt in elke regio die Azure Database for MariaDB beschikbaar is.

Als u een server wilt maken met behulp van een geo-redundante back-up, gebruikt u de `Restore-AzMariaDbServer` opdracht met de para meter **UseGeoRestore** .

> [!NOTE]
> Wanneer een server voor het eerst wordt gemaakt, is deze mogelijk niet onmiddellijk beschikbaar voor geo Restore. Het kan enkele uren duren voordat de benodigde meta gegevens zijn gevuld.

Als u geo-server wilt herstellen, voert u het volgende voor beeld uit in Power shell:

```azurepowershell-interactive
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMariaDbServer -Name mydemoserver-georestored -ResourceGroupName myresourcegroup -Location eastus -Sku GP_Gen5_8 -UseGeoRestore
```

In dit voor beeld wordt een nieuwe-server met de naam **mydemoserver-restored** gemaakt in de regio VS-Oost die hoort bij **myresourcegroup**. Het is een Algemeen, Gen 5-server met 8 vCores. De-server wordt gemaakt op basis van de geo-redundante back-up van **mydemoserver**, ook in de resource groep **myresourcegroup**.

Als u de nieuwe server in een andere resource groep wilt maken op basis van de bestaande server, geeft u de naam van de nieuwe resource groep op met behulp van de para meter **ResourceGroupName** , zoals weer gegeven in het volgende voor beeld:

```azurepowershell-interactive
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMariaDbServer -Name mydemoserver-georestored -ResourceGroupName newresourcegroup -Location eastus -Sku GP_Gen5_8 -UseGeoRestore
```

Voor de para meters set **Restore** van de `Restore-AzMariaDbServer` cmdlet zijn de volgende para meters vereist:

| Instelling | Voorgestelde waarde | Beschrijving  |
| --- | --- | --- |
|ResourceGroupName | myResourceGroup | De naam van de resource groep waartoe de nieuwe server behoort.|
|Naam | mydemoserver-geoterugzet bewerking | De naam van de nieuwe server. |
|Locatie | eastus | De locatie van de nieuwe server. |
|UseGeoRestore | `<SwitchParameter>` | Gebruik de geo-modus om te herstellen. |

Wanneer u een nieuwe server maakt met behulp van geo Restore, neemt deze dezelfde opslag grootte en prijs categorie als de bron server op, tenzij de **SKU** -para meter is opgegeven.

Nadat het herstel proces is voltooid, zoekt u de nieuwe server en controleert u of de gegevens correct zijn hersteld. De nieuwe server heeft dezelfde aanmeldings naam en hetzelfde wacht woord voor de server beheerder op het moment dat de herstel bewerking werd gestart. Het wacht woord kan worden gewijzigd op de pagina **overzicht** van de nieuwe server.

De nieuwe server die tijdens het herstellen is gemaakt, heeft geen VNet-service-eind punten die bestonden op de oorspronkelijke server. Deze regels moeten afzonderlijk worden ingesteld voor deze nieuwe server. Firewall regels van de oorspronkelijke server worden hersteld.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Azure Database for MariaDB server parameters aanpassen met behulp van Power shell](howto-configure-server-parameters-using-powershell.md)
