---
title: Veelvoorkomende fouten oplossen - Azure Database for MySQL
description: Meer informatie over het oplossen van veelvoorkomende migratiefouten voor nieuwe gebruikers van de Azure Database for MySQL-service
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 8/20/2020
ms.openlocfilehash: ebe9f936e3d0dfafec23842fcdbfd225995d546b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88719777"
---
# <a name="common-errors"></a>Algemene fouten

Azure Database for MySQL is een volledig beheerde service die wordt ondersteund door de communityversie van MySQL. De MySQL-ervaring in een beheerde service-omgeving kan afwijken van het uitvoeren van MySQL in uw eigen omgeving. In dit artikel ziet u enkele veelvoorkomende fouten die gebruikers kunnen tegenkomen tijdens het voor de eerste keer migreren naar of ontwikkelen met de Azure Database for MySQL-service.

## <a name="errors-due-to-lack-of-super-privilege-and-dba-role"></a>Fouten vanwege ontbreken van SUPER-machtiging en DBA-rol

De SUPER-machtiging en DBA-rol worden niet ondersteund in de service. Als gevolg hiervan kunt u een aantal veelvoorkomende fouten tegenkomen die hieronder worden vermeld:

#### <a name="error-1419-you-do-not-have-the-super-privilege-and-binary-logging-is-enabled-you-might-want-to-use-the-less-safe-log_bin_trust_function_creators-variable"></a>ERROR 1419: You do not have the SUPER privilege and binary logging is enabled (you *might* want to use the less safe log_bin_trust_function_creators variable)

De bovenstaande fout kan optreden bij het maken van een functie of trigger als hieronder of het importeren van een schema. DDL-instructies als CREATE FUNCTION en CREATE TRIGGER worden naar het binaire logboek geschreven, zodat de secundaire replica deze kan uitvoeren. De SQL-replicathread heeft volledige bevoegdheid, die kan worden gebruikt om machtigingen uit te breiden. Om servers waarop binaire logboekregistratie is ingeschakeld tegen dit risico te beschermen, vereist MySQL-engine dat opgeslagen functiemakers de machtiging SUPER hebben, naast de gebruikelijke vereiste machtiging CREATE ROUTINE. 

```sql
CREATE FUNCTION f1(i INT)
RETURNS INT
DETERMINISTIC
READS SQL DATA
BEGIN
  RETURN i;
END;
```

**Oplossing**:  Om de fout op te lossen, stelt u log_bin_trust_function_creators in op 1 op de blade [Serverparameters](howto-server-parameters.md) in de portal, voert u de DDL-instructies uit of importeert u het schema om de gewenste objecten te maken en zet u de parameter log_bin_trust_function_creators terug naar de vorige waarde na het maken.

#### <a name="error-1227-42000-at-line-101-access-denied-you-need-at-least-one-of-the-super-privileges-for-this-operation-operation-failed-with-exitcode-1"></a>ERROR 1227 (42000) at line 101: Access denied; you need (at least one of) the SUPER privilege(s) for this operation. Operation failed with exitcode 1

De bovenstaande fout kan optreden bij het importeren van een dump-bestand of het maken van een procedure met [definities](https://dev.mysql.com/doc/refman/5.7/en/create-procedure.html). 

**Oplossing**:  Om deze fout op te lossen, kan de gebruiker met beheerdersrechten machtigingen verlenen om procedures te maken of uit te voeren door de opdracht GRANT uit te voeren, zoals in de volgende voorbeelden:

```sql
GRANT CREATE ROUTINE ON mydb.* TO 'someuser'@'somehost';
GRANT EXECUTE ON PROCEDURE mydb.myproc TO 'someuser'@'somehost';
```
U kunt ook de definities vervangen door de naam van de gebruiker met beheerdersrechten die het importproces uitvoert, zoals hieronder wordt weergegeven.

```sql
DELIMITER;;
/*!50003 CREATE*/ /*!50017 DEFINER=`root`@`127.0.0.1`*/ /*!50003
DELIMITER;;

/* Modified to */

DELIMITER ;;
/*!50003 CREATE*/ /*!50017 DEFINER=`AdminUserName`@`ServerName`*/ /*!50003
DELIMITER ;
```

## <a name="next-steps"></a>Volgende stappen
Als u het antwoord op uw vraag niet hebt gevonden, kunt u het volgende doen:
- Plaats uw vraag op [de Microsoft Q&A-pagina](https://docs.microsoft.com/answers/topics/azure-database-mysql.html) of op [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).
- Stuur een e-mail naar het Azure Database for MySQL-team [@Ask Azure DB for MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com). Dit e-mailadres is geen alias voor technische ondersteuning.
- Als u contact wilt opnemen met Ondersteuning voor Azure, kunt u een [ticket indienen vanuit Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Als u een probleem met uw account wilt oplossen, kunt u een [ondersteuningsaanvraag](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) indienen in Azure Portal.
- Als u feedback wilt geven of een nieuwe functie wilt aanvragen, maakt u een vermelding via [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).
