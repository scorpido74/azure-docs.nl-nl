---
title: Tijdzones voor beheerde instantie
description: Meer informatie over de tijdzonespecificaties van Azure SQL Database Managed Instance
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: ''
ms.date: 09/03/2019
ms.openlocfilehash: 0dbed3db8e106b9bfe1b48ff2b9bc52840fc4c3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256092"
---
# <a name="time-zones-in-azure-sql-database-managed-instance"></a>Tijdzones in Azure SQL Database Managed Instance

Coordinated Universal Time (UTC) is de aanbevolen tijdzone voor de gegevenslaag van cloudoplossingen. Azure SQL Database Managed Instance biedt ook een keuze aan tijdzones om te voldoen aan de behoeften van bestaande toepassingen die datum- en tijdwaarden opslaan en aanroepdatum- en tijdfuncties met een impliciete context van een specifieke tijdzone.

T-SQL-functies zoals [GETDATE()](https://docs.microsoft.com/sql/t-sql/functions/getdate-transact-sql) of CLR-code observeren de tijdzone die is ingesteld op instantieniveau. SQL Server Agent-taken volgen ook schema's volgens de tijdzone van de instantie.

  >[!NOTE]
  > Beheerde instantie is de enige implementatieoptie van Azure SQL Database die tijdzone-instelling ondersteunt. Andere implementatieopties volgen UTC altijd.
Gebruik [AT TIME ZONE](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql) in enkele en gepoolde SQL-databases als u datum- en tijdinformatie moet interpreteren in een niet-UTC-tijdzone.

## <a name="supported-time-zones"></a>Ondersteunde tijdzones

Een set ondersteunde tijdzones wordt overgenomen van het onderliggende besturingssysteem van de beheerde instantie. Het wordt regelmatig bijgewerkt om nieuwe tijdzonedefinities te krijgen en wijzigingen in de bestaande te weerspiegelen.

[Zomertijd/tijdzonewijzigingen het beleid](https://aka.ms/time) garandeert historische nauwkeurigheid vanaf 2010.

Een lijst met namen van de ondersteunde tijdzones wordt weergegeven via de [systeemweergave sys.time_zone_info.](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql)

## <a name="set-a-time-zone"></a>Een tijdzone instellen

Een tijdzone van een beheerde instantie kan alleen worden ingesteld tijdens het maken van instanties. De standaardtijdzone is UTC.

  >[!NOTE]
  > De tijdzone van een bestaand beheerde instantie kan niet worden gewijzigd.

### <a name="set-the-time-zone-through-the-azure-portal"></a>De tijdzone instellen via de Azure-portal

Wanneer u parameters voor een nieuw exemplaar invoert, selecteert u een tijdzone in de lijst met ondersteunde tijdzones.
  
![Een tijdzone instellen tijdens het maken van instanties](media/sql-database-managed-instance-timezone/01-setting_timezone-during-instance-creation.png)

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-sjabloon

Geef de eigenschap timezoneId op in de [sjabloon Resourcemanager](https://aka.ms/sql-mi-create-arm-posh) om de tijdzone in te stellen tijdens het maken van een instantie.

```json
"properties": {
                "administratorLogin": "[parameters('user')]",
                "administratorLoginPassword": "[parameters('pwd')]",
                "subnetId": "[parameters('subnetId')]",
                "storageSizeInGB": 256,
                "vCores": 8,
                "licenseType": "LicenseIncluded",
                "hardwareFamily": "Gen5",
                "collation": "Serbian_Cyrillic_100_CS_AS",
                "timezoneId": "Central European Standard Time"
            },

```

Een lijst met ondersteunde waarden voor de eigenschap timezoneId bevindt zich aan het einde van dit artikel.

Als dit niet is opgegeven, wordt de tijdzone ingesteld op UTC.

## <a name="check-the-time-zone-of-an-instance"></a>De tijdzone van een instantie controleren

De functie [CURRENT_TIMEZONE](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql) retourneert een weergavenaam van de tijdzone van de instantie.

## <a name="cross-feature-considerations"></a>Overwegingen voor functies

### <a name="restore-and-import"></a>Herstellen en importeren

U een back-upbestand herstellen of gegevens importeren naar een beheerde instantie vanuit een instantie of een server met verschillende tijdzone-instellingen. Zorg ervoor dat u dit met de nodige voorzichtigheid doet. Analyseer het toepassingsgedrag en de resultaten van de query's en rapporten, net als wanneer u gegevens overdraagt tussen twee SQL Server-exemplaren met verschillende tijdzone-instellingen.

### <a name="point-in-time-restore"></a>Terugzetten naar eerder tijdstip

Wanneer u een point-in-time-herstel uitvoert, wordt de tijd om te herstellen geïnterpreteerd als UTC-tijd. Op deze manier worden eventuele onduidelijkheden als gevolg van zomertijd en de mogelijke veranderingen ervan vermeden.

### <a name="auto-failover-groups"></a>Automatische failover-groepen

Het gebruik van dezelfde tijdzone in een primaire en secundaire instantie in een failovergroep wordt niet afgedwongen, maar we raden het ten zeerste aan.

  >[!WARNING]
  > We raden u ten zeerste aan dezelfde tijdzone te gebruiken voor de primaire en secundaire instantie in een failovergroep. Vanwege bepaalde zeldzame gebruiksgevallen wordt dezelfde tijdzone voor primaire en secundaire instanties niet afgedwongen. Het is belangrijk om te begrijpen dat in het geval van handmatige of automatische failover, de secundaire instantie zijn oorspronkelijke tijdzone behoudt.

## <a name="limitations"></a>Beperkingen

- De tijdzone van de bestaande beheerde instantie kan niet worden gewijzigd.
- Externe processen die zijn gestart vanuit de SQL Server Agent-taken, houden geen rekening met de tijdzone van de instantie.

## <a name="list-of-supported-time-zones"></a>Lijst van ondersteunde tijdzones

| **Tijdzone-id** | **Weergavenaam tijdzone** |
| --- | --- |
| Dateline-standaardtijd | (UTC-12:00) Internationale Datumlijn West |
| UTC-11 | (UTC-11:00) Gecoördineerde Universele Tijd-11 |
| Aleutian Standaard Tijd | (UTC-10:00) Aleutische eilanden |
| Hawaiiaanse standaardtijd | (UTC-10:00) Hawaii |
| Marquesas standaardtijd | (UTC-09:30) Marquesas eilanden |
| Alaskastandaardtijd | (UTC-09:00) Alaska |
| UTC-09 | (UTC-09:00) Gecoördineerde Universele Tijd-09 |
| Pacific Standard Time (Mexico) | (UTC-08:00) Baja Californië |
| UTC-08 | (UTC-08:00) Gecoördineerde Universele Tijd-08 |
| Pacific Standaardtijd | (UTC-08:00) Pacific Time (VS & Canada) |
| Amerikaanse berg standaard tijd | (UTC-07:00) Arizona |
| Mountain Standard Time (Mexico) | (UTC-07:00) Chihuahua, La Paz, Mazatlan |
| Berg standaard tijd | (UTC-07:00) Mountain Time (VS & Canada) |
| Standaardtijd in Midden-Amerika | (UTC-06:00) Midden-Amerika |
| Centrale standaardtijd | (UTC-06:00) Central Time (VS & Canada) |
| Paaseiland Standaard tijd | (UTC-06:00) Paaseiland |
| Centrale standaardtijd (Mexico) | (UTC-06:00) Guadalajara, Mexico-Stad, Monterrey |
| Canada Central Standard Time | (UTC-06:00) Saskatchewan |
| SA Pacific Standaardtijd | (UTC-05:00) Bogota, Lima, Quito, Rio Branco |
| Oostelijke standaardtijd (Mexico) | (UTC-05:00) Chetumal |
| Oostelijke standaardtijd | (UTC-05:00) Eastern Time (VS & Canada) |
| Haïti Standaard Tijd | (UTC-05:00) Haïti |
| Cuba Standaard tijd | (UTC-05:00) Havana |
| Amerikaanse Eastern Standard Time | (UTC-05:00) Indiana (Oost) |
| Turken en Caicos Standaard Tijd | (UTC-05:00) Turken en Caicos |
| Paraguay Standaard Tijd | (UTC-04:00) Asuncion |
| Atlantische standaardtijd | (UTC-04:00) Atlantic Time (Canada) |
| Venezuela Standaardtijd | (UTC-04:00) Caracas |
| Centrale Braziliaanse standaardtijd | (UTC-04:00) Cuiaba |
| SA Westerse standaardtijd | (UTC-04:00) Georgetown, La Paz, Manaus, San Juan |
| Pacific SA standaardtijd | (UTC-04:00) Santiago |
| Newfoundland Standaard Tijd | (UTC-03:30) Newfoundland |
| Tocantins Standaardtijd | (UTC-03:00) Araguaina (Araguaina) |
| E. Zuid-Amerika Standaard Tijd | (UTC-03:00) Brasilia |
| SA Oost-standaardtijd | (UTC-03:00) Cayenne, Fortaleza |
| Argentinië Standaard tijd | (UTC-03:00) Stad Buenos Aires |
| Groenland standaard tijd | (UTC-03:00) Groenland |
| Montevideo Standaardtijd | (UTC-03:00) Montevideo |
| Magallanes Standaard Tijd | (UTC-03:00) Punta Arenas |
| Saint Pierre Standaard Tijd | (UTC-03:00) Saint Pierre en Miquelon |
| Bahia Standaard Tijd | (UTC-03:00) Salvador |
| UTC-02 | (UTC-02:00) Gecoördineerde Universele Tijd-02 |
| Mid-Atlantische standaardtijd | (UTC-02:00) Mid-Atlantische Oceaan - Oud |
| Azoren Standaardtijd | (UTC-01:00) Azoren |
| Kaapverdië standaard tijd | (UTC-01:00) Cabo Verde Is. |
| UTC | (UTC) Coordinated Universal Time |
| GMT-standaardtijd | (UTC+00:00) Dublin, Edinburgh, Lissabon, Londen |
| Greenwich Standaard Tijd | (UTC+00:00) Monrovia |
| W. Europa Standaardtijd | (UTC+01:00) Amsterdam, Berlijn, Bern, Rome, Stockholm, Wenen |
| Standaardtijd in Midden-Europa | (UTC+01:00) Belgrado, Bratislava, Boedapest, Ljubljana, Praag |
| Romance Standaard Tijd | (UTC+01:00) Brussel, Kopenhagen, Madrid, Parijs |
| Marokko Standaardtijd | (UTC+01:00) Casablanca |
| Sao Tome Standaardtijd | (UTC+01:00) Sao Tome |
| Centraal-Europese standaardtijd | (UTC+01:00) Sarajevo, Skopje, Warschau, Zagreb |
| W. Standaardtijd in Centraal-Afrika | (UTC+01:00) West Centraal-Afrika |
| Jordan Standaard tijd | (UTC+02:00) Amman |
| GTB-standaardtijd | (UTC+02:00) Athene, Boekarest |
| Standaardtijd in het Midden-Oosten | (UTC+02:00) Beirut |
| Egypte Standaard tijd | (UTC+02:00) Cairo |
| E. Europa Standaardtijd | (UTC+02:00) Chisinau |
| Syrië Standaard Tijd | (UTC+02:00) Damascus |
| Standaardtijd op de Westelijke Jordaanoever | (UTC+02:00) Gaza, Hebron |
| Zuid-Afrika Standaard Tijd | (UTC+02:00) Harare, Pretoria |
| FLE-standaardtijd | (UTC+02:00) Helsinki, Kiev, Riga, Sofia, Tallinn, Vilnius |
| Israël Standaard tijd | (UTC+02:00) Jeruzalem |
| Kaliningrad Standaardtijd | (UTC+02:00) Kaliningrad |
| Soedan Standaard Tijd | (UTC+02:00) Khartoum |
| Standaardtijd libië | (UTC+02:00) Tripoli |
| Namibië Standaardtijd | (UTC+02:00) Windhoek |
| Arabische standaardtijd | (UTC+03:00) Bagdad |
| Turkije Standaard tijd | (UTC+03:00) Istanbul |
| Arabische standaardtijd | (UTC+03:00) Koeweit, Riyad |
| Wit-Rusland Standaard Tijd | (UTC+03:00) Minsk |
| Russische standaardtijd | (UTC+03:00) Moskou, St. Petersburg |
| E. Afrika Standaard Tijd | (UTC+03:00) Nairobi |
| Iran Standaardtijd | (UTC+03:30) Teheran |
| Arabische standaardtijd | (UTC+04:00) Abu Dhabi, Muscat |
| Astrakhan Standaard Tijd | (UTC+04:00) Astrakhan |
| Azerbeidzjan Standaardtijd | (UTC+04:00) Bakoe |
| Rusland Tijdzone 3 | (UTC+04:00) Izhevsk, Samara |
| Mauritius Standaard tijd | (UTC+04:00) Haven Louis |
| Saratov standaardtijd | (UTC+04:00) Saratov |
| Georgische standaardtijd | (UTC+04:00) Tbilisi |
| Volgograd Standaardtijd | (UTC+04:00) Volgograd |
| Kaukasus Standaard Tijd | (UTC+04:00) Yerevan |
| Afghanistan Standaard tijd | (UTC+04:30) Kabul |
| West-Aziatische standaardtijd | (UTC+05:00) Ashgabat |
| Ekaterinburg Standaard Tijd | (UTC+05:00) Ekaterinburg |
| Standaardtijd pakistan | (UTC+05:00) Islamabad, Karachi |
| India Standaard Tijd | (UTC+05:30) Chennai, Kolkata, Mumbai, New Delhi |
| Sri Lanka Standaard tijd | (UTC+05:30) Sri Jayawardenepura |
| Nepal Standaardtijd | (UTC+05:45) Kathmandu |
| Standaardtijd in Centraal-Azië | (UTC+06:00) Astana |
| Bangladesh Standaardtijd | (UTC+06:00) Dhaka |
| Omsk Standaardtijd | (UTC+06:00) Omsk |
| Myanmar Standaard tijd | (UTC+06:30) Yangon (Rangoon) |
| SE Asia Standaard Tijd | (UTC+07:00) Bangkok, Hanoi, Jakarta |
| Altai Standaard tijd | (UTC+07:00) Barnaul, Gorno-Altaysk |
| W. Standaardtijd van Mongolië | (UTC+07:00) Hovd Hovd |
| Noord-Azië Standaard tijd | (UTC+07:00) Krasnoyarsk |
| N. Standaardtijd in Centraal-Azië | (UTC+07:00) Novosibirsk |
| Tomsk Standaardtijd | (UTC+07:00) Tomsk |
| China Standaardtijd | (UTC+08:00) Peking, Chongqing, Hong Kong, Urumqi |
| Noord-Azië-Oost Standaardtijd | (UTC+08:00) Irkoetsk |
| Singapore Standaard Tijd | (UTC+08:00) Kuala Lumpur |
| W. Australië Standaardtijd | (UTC+08:00) Perth |
| Taipei Standaard tijd | (UTC+08:00) Taipei |
| Ulaanbaatar Standaardtijd | (UTC+08:00) Ulaanbaatar |
| Aus Central W. Standaardtijd | (UTC+08:45) Eucla Eucla |
| Transbaikal StandaardTijd | (UTC+09:00) Tsjita |
| Tokyo Standaard tijd | (UTC+09:00) Osaka, Sapporo, Tokio |
| Noord-Korea Standaard tijd | (UTC+09:00) Pyongyang |
| Korea Standaard tijd | (UTC+09:00) Seoul |
| Yakutsk Standaard tijd | (UTC+09:00) Yakutsk Yakutsk |
| Cen. Australië Standaardtijd | (UTC+09:30) Adelaide |
| AUS Central Standard Time | (UTC+09:30) Darwin |
| E. Australië Standaardtijd | (UTC+10:00) Brisbane |
| AUS Eastern Standaard Tijd | (UTC+10:00) Canberra, Melbourne, Sydney |
| West-Pacific Standaard Tijd | (UTC+10:00) Guam, Port Moresby |
| Tasmanië Standaard Tijd | (UTC+10:00) Hobart |
| Vladivostok Standaard Tijd | (UTC+10:00) Vladivostok |
| Lord Howe Standaard Tijd | (UTC+10:30) Lord Howe Eiland |
| Bougainville Standaard Tijd | (UTC+11:00) Bougainville Eiland |
| Rusland Tijdzone 10 | (UTC+11:00) Chokurdakh Chokurdakh |
| Magadan Standaard Tijd | (UTC+11:00) Magadan |
| Norfolk Standaardtijd | (UTC+11:00) Norfolk |
| Sakhalin Standaard Tijd | (UTC+11:00) Sachalin |
| Central Pacific Standaardtijd | (UTC+11:00) Salomo Is., Nieuw-Caledonië |
| Rusland Tijdzone 11 | (UTC+12:00) Anadyr, Petropavlovsk-Kamchatsky |
| Nieuw-Zeelandse standaardtijd | (UTC+12:00) Auckland, Wellington |
| UTC+12 | (UTC+12:00) Gecoördineerde universele tijd+12 |
| Fiji Standaardtijd | (UTC+12:00) Fiji |
| Kamchatka standaard tijd | (UTC+12:00) Petropavlovsk-Kamchatsky - Oud |
| Chatham Eilanden Standaard Tijd | (UTC+12:45) Chatham eilanden |
| UTC+13 | (UTC+13:00) Gecoördineerde universele tijd+13 |
| Tonga Standaard Tijd | (UTC+13:00) Nuku'alofa |
| Samoa Standaard Tijd | (UTC+13:00) Samoa |
| Line Islands Standaard Tijd | (UTC+14:00) Kiritimati Eiland |

## <a name="see-also"></a>Zie ook 

- [CURRENT_TIMEZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql)
- [AT TIME ZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql)
- [sys.time_zone_info (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql)
