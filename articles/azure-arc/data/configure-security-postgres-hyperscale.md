---
title: Beveiliging configureren voor uw PostgreSQL grootschalige-Server groep voor Azure Arc ingeschakeld
description: Beveiliging configureren voor uw PostgreSQL grootschalige-Server groep voor Azure Arc ingeschakeld
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: b166348031e9f72e8005e866a198855db9c01a9c
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936103"
---
# <a name="configure-security-for-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Beveiliging configureren voor uw PostgreSQL grootschalige-Server groep voor Azure Arc ingeschakeld

In dit document worden verschillende aspecten van de beveiliging van uw server groep beschreven:
- Versleuteling 'at rest'
- Gebruikersbeheer
   - Algemene perspectieven
   - Het wacht woord van de gebruiker met beheerders rechten voor _post gres_ wijzigen

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="encryption-at-rest"></a>Versleuteling 'at rest'
U kunt versleuteling op rest implementeren door de schijven te versleutelen waarop u uw data bases opslaat en/of door database functies te gebruiken voor het versleutelen van de gegevens die u invoegt of bijwerkt.

### <a name="hardware-linux-host-volume-encryption"></a>Hardware: volume versleuteling van de Linux-host
Implementeer systeem gegevens versleuteling om alle gegevens te beveiligen die zich bevinden op de schijven die worden gebruikt door uw Azure-Arc ingeschakeld Data Services Setup. Meer informatie over dit onderwerp kunt u lezen:
- [Gegevens versleuteling in rust](https://wiki.archlinux.org/index.php/Data-at-rest_encryption) op Linux in het algemeen 
- Schijf versleuteling met `cryptsetup` de opdracht luks Encryption (Linux) (met https://www.cyberciti.biz/security/howto-linux-hard-disk-encryption-with-luks-cryptsetup-command/) name omdat Azure Arc is ingeschakeld Data Services wordt uitgevoerd op de fysieke infra structuur die u opgeeft, bent u verantwoordelijk voor het beveiligen van de infra structuur.

### <a name="software-use-the-postgresql-pgcrypto-extension-in-your-server-group"></a>Software: gebruik de `pgcrypto` uitbrei ding postgresql in uw server groep
Naast het versleutelen van de schijven die worden gebruikt voor het hosten van uw Azure Arc-installatie, kunt u uw Azure-PostgreSQL grootschalige-Server groep configureren om mechanismen beschikbaar te maken die uw toepassingen kunnen gebruiken om gegevens in uw data base (s) te versleutelen. De `pgcrypto` uitbrei ding maakt deel uit van de `contrib` uitbrei dingen van post gres en is beschikbaar in uw grootschalige-Server groep voor Azure-Arc ingeschakelde postgresql. Hier vindt u informatie over `pgcrypto` de [here](https://www.postgresql.org/docs/current/pgcrypto.html)uitbrei ding.
Samen met de volgende opdrachten kunt u de uitbrei ding inschakelt, kunt u deze maken en gebruiken:


#### <a name="create-the-pgcrypto-extension"></a>De `pgcrypto` uitbrei ding maken
Maak verbinding met uw server groep met het client hulpprogramma van uw keuze en voer de standaard PostgreSQL-query uit:
```console
CREATE EXTENSION pgcrypto;
```

> [Hier](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md) vindt u informatie over hoe u verbinding kunt maken.

#### <a name="verify-the-list-the-extensions-ready-to-use-in-your-server-group"></a>Controleer de lijst met uitbrei dingen die u kunt gebruiken in uw server groep
U kunt controleren of de `pgcrypto` uitbrei ding gereed is voor gebruik door de beschik bare uitbrei dingen in uw server groep weer te geven.
Maak verbinding met uw server groep met het client hulpprogramma van uw keuze en voer de standaard PostgreSQL-query uit:
```console
select * from pg_extension;
```
U moet zien `pgcrypto` of u deze hebt ingeschakeld en gemaakt met de opdrachten die hierboven zijn aangegeven.

#### <a name="use-the-pgcrypto-extension"></a>De `pgcrypto` uitbrei ding gebruiken
Nu kunt u de code van uw toepassingen aanpassen zodat ze gebruikmaken van de functies die worden aangeboden door `pgcrypto` :
- Algemene hash-functies
- Wachtwoord hash-functies
- PGP-versleutelings functies
- Onbewerkte versleutelings functies
- Functies met wille keurige gegevens

Als u bijvoorbeeld hash-waarden wilt genereren. Voer de opdracht uit:

```console
Select crypt('Les sanglots longs des violons de l_automne', gen_salt('md5'));
```

Retourneert de volgende hash:

```console
              crypt
------------------------------------
 $1$/9ACBYOV$z52PAGjQ5WTU9xvEECBNv/   
```

Of bijvoorbeeld:

```console
select hmac('Les sanglots longs des violons de l_automne', 'md5', 'sha256');
```

retourneert de volgende hash:

```console
                                hmac
--------------------------------------------------------------------
 \xd4e4790b69d2cc8dbce3385ee63272bc7760f1603640bb211a7b864e695570c5
```

Of bijvoorbeeld om versleutelde gegevens zoals een wacht woord op te slaan:

We gaan ervan uit dat mijn toepassing geheimen opslaat in de volgende tabel:

```console
create table mysecrets(USERid int, USERname char(255), USERpassword char(512));
```

En ik Versleutel hun wacht woord bij het maken van een gebruiker:

```console
insert into mysecrets values (1, 'Me', crypt('MySecretPasswrod', gen_salt('md5')));
```

Ik zie nu dat mijn wacht woord is versleuteld:

```console
select * from mysecrets;
```

Uitvoer:

- Gebruikers-id: 1
- Gebruikers naam: mij
- USERpassword: $1 $ Uc7jzZOp $ NTfcGo7F10zGOkXOwjHy31

Wanneer ik verbinding maak met mijn toepassing en een wacht woord geef, wordt het gezocht in de `mysecrets` tabel en wordt de naam van de gebruiker geretourneerd als er een overeenkomst is tussen het wacht woord dat is opgegeven voor de toepassing en de wacht woorden die in de tabel zijn opgeslagen. Bijvoorbeeld:

- Ik geef het verkeerde wacht woord door:
   ```console
   select USERname from mysecrets where (USERpassword = crypt('WrongPassword', USERpassword));
   ```

   Uitvoer 

   ```returns
    USERname
   ---------
   (0 rows)
   ```
- Ik geef het juiste wacht woord door:

   ```console
   select USERname from mysecrets where (USERpassword = crypt('MySecretPasswrod', USERpassword));
   ``` 

   Uitvoer:

   ```output
    USERname
   ---------
   Me
   (1 row)
   ```

Dit kleine voor beeld laat zien dat u gegevens in rust kunt versleutelen (versleutelde gegevens opslaan) in azure Arc enabled PostgreSQL grootschalige met behulp van de post gres- `pgcrypto` extensie en dat uw toepassingen functies kunnen gebruiken die worden aangeboden door `pgcrypto` om deze versleutelde gegevens te bewerken.

## <a name="user-management"></a>Gebruikersbeheer
### <a name="general-perspectives"></a>Algemene perspectieven
U kunt de standaard post gres manier gebruiken om gebruikers of rollen te maken. Als u dit wel doet, zijn deze artefacten echter alleen beschikbaar voor de rol van de coördinator. Tijdens de preview-periode hebben deze gebruikers/rollen nog geen toegang meer tot gegevens die zijn gedistribueerd buiten het coördinator knooppunt en op de worker-knoop punten van uw server groep. De reden hiervoor is dat de gebruikers definitie in de preview-versie niet wordt gerepliceerd naar de worker-knoop punten.

### <a name="change-the-password-of-the-_postgres_-administrative-user"></a>Het wacht woord van de gebruiker met beheerders rechten voor _post gres_ wijzigen
Azure Arc enabled PostgreSQL grootschalige wordt geleverd met de standaard post gres administratieve gebruiker _post gres_ waarvoor u het wacht woord instelt wanneer u de Server groep maakt.
De algemene indeling van de opdracht voor het wijzigen van het wacht woord is:
```console
azdata arc postgres server edit --name <server group name> --admin-password <new password>
```
Het wacht woord wordt ingesteld op de waarde van de omgevings variabele AZDATA_PASSWORD- **sessie**als deze bestaat. Als dat niet het geval is, wordt de gebruiker gevraagd een waarde op te vragen.
Voer de volgende handelingen uit om te controleren of de omgevings variabele van de AZDATA_PASSWORD-sessie bestaat en/of welke waarde er wordt ingesteld.
```console
printenv AZDATA_PASSWORD
```
Mogelijk wilt u de waarde ervan verwijderen als u wordt gevraagd om een nieuw wacht woord in te voeren.


## <a name="next-steps"></a>Volgende stappen
- Lees hier meer informatie over de `pgcrypto` uitbrei ding. [here](https://www.postgresql.org/docs/current/pgcrypto.html)
- Lees [hier](using-extensions-in-postgresql-hyperscale-server-group.md)meer informatie over het gebruik van post gres-extensies.

