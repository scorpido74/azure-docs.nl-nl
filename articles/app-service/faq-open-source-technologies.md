---
title: Gestelde vragen over open source-technologieën
description: Krijg antwoorden op veelgestelde vragen over open-sourcetechnologieën in Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 33590f9fc7e6c4d46123cbc7088086a3197d52ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74672431"
---
# <a name="open-source-technologies-faqs-for-web-apps-in-azure"></a>Veelgestelde vragen over opensourcetechnologieën voor webapps in Azure

Dit artikel bevat antwoorden op veelgestelde vragen (veelgestelde vragen) over problemen met open-sourcetechnologieën voor de [Web Apps-functie van Azure App Service.](https://azure.microsoft.com/services/app-service/web/)

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="how-do-i-turn-on-php-logging-to-troubleshoot-php-issues"></a>Hoe schakel ik PHP-logboekregistratie in om PHP-problemen op te lossen?

Ga als nodig de PHP-logboekregistratie inschakelen:

1. Meld u aan op uw [Kudu-website.](https://*yourwebsitename*.scm.azurewebsites.net)
2. Selecteer in het bovenste menu **Debug Console** > **CMD**.
3. Selecteer de **map Site.**
4. Selecteer de **map wwwroot.**
5. Selecteer **+** het pictogram en selecteer **Nieuw bestand**.
6. Stel de bestandsnaam in op **.user.ini**.
7. Selecteer het potloodpictogram naast **.user.ini**.
8. Voeg in het bestand deze code toe:`log_errors=on`
9. Selecteer **Opslaan**.
10. Selecteer het potloodpictogram naast **wp-config.php**.
11. Wijzig de tekst in de volgende code:
    ```php
    //Enable WP_DEBUG modedefine('WP_DEBUG', true);//Enable debug logging to /wp-content/debug.logdefine('WP_DEBUG_LOG', true);
    //Suppress errors and warnings to screendefine('WP_DEBUG_DISPLAY', false);//Suppress PHP errors to screenini_set('display_errors', 0);
    ```
12. Start uw web-app opnieuw in de Azure-portal in het menu van de web-app.

Zie [WordPress-foutlogboeken inschakelen](https://blogs.msdn.microsoft.com/azureossds/2015/10/09/logging-php-errors-in-wordpress-2/)voor meer informatie .

## <a name="how-do-i-log-python-application-errors-in-apps-that-are-hosted-in-app-service"></a>Hoe log ik python-toepassingsfouten aan in apps die worden gehost in App Service?
[!INCLUDE [web-sites-python-troubleshooting-wsgi-error-log](../../includes/web-sites-python-troubleshooting-wsgi-error-log.md)]

## <a name="how-do-i-change-the-version-of-the-nodejs-application-that-is-hosted-in-app-service"></a>Hoe wijzig ik de versie van de Node.js-toepassing die wordt gehost in App-service?

Als u de versie van de toepassing Node.js wilt wijzigen, u een van de volgende opties gebruiken:

* Gebruik **app-instellingen**in de Azure-portal .
  1. Ga in de Azure-portal naar uw web-app.
  2. Selecteer in het blad **Instellingen** de optie **Toepassingsinstellingen**.
  3. In **app-instellingen**u WEBSITE_NODE_DEFAULT_VERSION als sleutel opnemen en de versie van Node.js die u als waarde wilt gebruiken.
  4. Ga naar je [Kudu console.](https://*yourwebsitename*.scm.azurewebsites.net)
  5. Voer de volgende opdracht in om de versie van Node.js te controleren:  
     ```
     node -v
     ```
* Wijzig het iisnode.yml-bestand. Als u de Node.js-versie in het bestand iisnode.yml wijzigt, wordt alleen de runtime-omgeving ingesteld die iisnode gebruikt. Uw Kudu-cmd en anderen gebruiken nog steeds de Node.js-versie die is ingesteld in **app-instellingen** in de Azure-portal.

  Als u de iisnode.yml handmatig wilt instellen, maakt u een iisnode.yml-bestand in de hoofdmap van uw app. Neem in het bestand de volgende regel op:
  ```yml
  nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
  ```
   
* Stel het iisnode.yml-bestand in met behulp van package.json tijdens de implementatie van bronbeheer.
  Het implementatieproces voor Azure-bronbeheer omvat de volgende stappen:
  1. Hiermee verplaatst u inhoud naar de Azure-web-app.
  2. Hiermee maakt u een standaardimplementatiescript als er geen (deploy.cmd, .deployment-bestanden) in de hoofdmap van de web-app is.
  3. Hiermee wordt een implementatiescript uitgevoerd waarin een iisnode.yml-bestand wordt gemaakt als u de Node.js-versie in het bestand package.json >-engine vermeldt`"engines": {"node": "5.9.1","npm": "3.7.3"}`
  4. Het iisnode.yml-bestand heeft de volgende coderegel:
      ```yml
      nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
      ```

## <a name="i-see-the-message-error-establishing-a-database-connection-in-my-wordpress-app-thats-hosted-in-app-service-how-do-i-troubleshoot-this"></a>Ik zie het bericht "Fout tot oprichting van een databaseverbinding" in mijn WordPress-app die wordt gehost in App-service. Hoe los ik dit op?

Als u deze fout ziet in uw Azure WordPress-app om php_errors.log en foutopsporing.log in te schakelen, voert u de stappen uit die worden beschreven in [WordPress-foutlogboeken inschakelen.](https://blogs.msdn.microsoft.com/azureossds/2015/10/09/logging-php-errors-in-wordpress-2/)

Wanneer de logboeken zijn ingeschakeld, voert u de fout weer en controleert u de logboeken om te zien of de verbindingen op raken:
```
[09-Oct-2015 00:03:13 UTC] PHP Warning: mysqli_real_connect(): (HY000/1226): User ‘abcdefghijk79' has exceeded the ‘max_user_connections’ resource (current value: 4) in D:\home\site\wwwroot\wp-includes\wp-db.php on line 1454
```

Als u deze fout ziet in uw bestanden foutopsporing.logboek of php_errors.log, overschrijdt uw app het aantal verbindingen. Als u host op ClearDB, controleert u het aantal verbindingen dat beschikbaar is in uw [serviceplan.](https://www.cleardb.com/pricing.view)

## <a name="how-do-i-debug-a-nodejs-app-thats-hosted-in-app-service"></a>Hoe kan ik een Node.js-app debuggen die wordt gehost in App-service?

1.  Ga naar je [Kudu console.](https://*yourwebsitename*.scm.azurewebsites.net/DebugConsole)
2.  Ga naar de map toepassingslogboeken (D:\home\LogFiles\Application).
3.  Controleer in het logging_errors.txt-bestand op inhoud.

## <a name="how-do-i-install-native-python-modules-in-an-app-service-web-app-or-api-app"></a>Hoe installeer ik native Python-modules in een App Service-web-app of API-app?

Sommige pakketten worden mogelijk niet geïnstalleerd met pip in Azure. Het pakket is mogelijk niet beschikbaar op de Python Package Index of er is mogelijk een compiler vereist (een compiler is niet beschikbaar op de computer waarop de web-app in App Service wordt uitgevoerd). Zie [Python-modules installeren in App Service](https://blogs.msdn.microsoft.com/azureossds/2015/06/29/install-native-python-modules-on-azure-web-apps-api-apps/)voor informatie over het installeren van native modules in App Service.

## <a name="how-do-i-deploy-a-django-app-to-app-service-by-using-git-and-the-new-version-of-python"></a>Hoe implementeer ik een Django-app naar App Service met Git en de nieuwe versie van Python?

Zie [Een Django-app implementeren naar appservice](https://blogs.msdn.microsoft.com/azureossds/2016/08/25/deploying-django-app-to-azure-app-services-using-git-and-new-version-of-python/)voor informatie over het installeren van Django.

## <a name="where-are-the-tomcat-log-files-located"></a>Waar bevinden zich de Tomcat-logbestanden?

Voor Azure Marketplace en aangepaste implementaties:

* Maplocatie: D:\home\site\wwwroot\bin\apache-tomcat-8.0.33\logs
* Dossiers van belang:
    * Catalina. *yyyy-mm-dd*.log
    * gastheer-manager. *yyyy-mm-dd*.log
    * Localhost. *yyyy-mm-dd*.log
    * Manager. *yyyy-mm-dd*.log
    * site_access_log. *yyyy-mm-dd*.log


Voor implementaties **van portal-app-instellingen:**

* Maplocatie: D:\home\LogFiles
* Dossiers van belang:
    * Catalina. *yyyy-mm-dd*.log
    * gastheer-manager. *yyyy-mm-dd*.log
    * Localhost. *yyyy-mm-dd*.log
    * Manager. *yyyy-mm-dd*.log
    * site_access_log. *yyyy-mm-dd*.log

## <a name="how-do-i-troubleshoot-jdbc-driver-connection-errors"></a>Hoe los ik jdbc-stuurprogrammaverbindingsfouten op?

Mogelijk ziet u het volgende bericht in uw Tomcat-logboeken:

```
The web application[ROOT] registered the JDBC driver [com.mysql.jdbc.Driver] but failed to unregister it when the web application was stopped. To prevent a memory leak,the JDBC Driver has been forcibly unregistered
```

Ga als een besluit over de fout:

1. Verwijder het sqljdbc*.jar-bestand uit uw app/lib-map.
2. Als u de aangepaste Tomcat- of Azure Marketplace-tomcat-webserver gebruikt, kopieert u dit .jar-bestand naar de map Tomcat lib.
3. Als u Java inschakelt via de Azure-portal (selecteer **Java 1.8** > **Tomcat-server),** kopieert u het sqljdbc.* jar-bestand in de map die parallel loopt aan uw app. Voeg vervolgens de volgende klassepadinstelling toe aan het bestand web.config:

    ```xml
    <httpPlatform>
    <environmentVariables>
    <environmentVariablename ="JAVA_OPTS" value=" -Djava.net.preferIPv4Stack=true
    -Xms128M -classpath %CLASSPATH%;[Path to the sqljdbc*.jarfile]" />
    </environmentVariables>
    </httpPlatform>
    ```

## <a name="why-do-i-see-errors-when-i-attempt-to-copy-live-log-files"></a>Waarom zie ik fouten wanneer ik live logbestanden probeer te kopiëren?

Als u live-logboekbestanden voor een Java-app probeert te kopiëren (bijvoorbeeld Tomcat), ziet u mogelijk deze FTP-fout:

```
Error transferring file [filename] Copying files from remote side failed.
    
The process cannot access the file because it is being used by another process.
```

Het foutbericht kan variëren, afhankelijk van de FTP-client.

Alle Java-apps hebben dit vergrendelingsprobleem. Alleen Kudu ondersteunt het downloaden van dit bestand terwijl de app wordt uitgevoerd.

Als u de app stopt, heeft FTP ftp-toegang tot deze bestanden.

Een andere tijdelijke oplossing is het schrijven van een WebJob die volgens een schema wordt uitgevoerd en deze bestanden kopieert naar een andere map. Zie het [CopyLogsJob-project](https://github.com/kamilsykora/CopyLogsJob) voor een voorbeeldproject.

## <a name="where-do-i-find-the-log-files-for-jetty"></a>Waar vind ik de logbestanden voor Jetty?

Voor Marketplace- en aangepaste implementaties bevindt het logboekbestand zich in de map D:\home\site\wwwroot\bin\jetty-distribution-9.1.2.v20140210\logs. Houd er rekening mee dat de locatie van de map afhankelijk is van de versie van Jetty die u gebruikt. Het pad dat hier wordt geboden, is bijvoorbeeld voor Jetty 9.1.2. Zoek naar jetty_*YYYY_MM_DD*.stderrout.log.

Voor implementaties van portal-app-instellingen bevindt het logboekbestand zich in D:\home\LogFiles. Zoek naar jetty_*YYYY_MM_DD*.stderrout.log

## <a name="can-i-send-email-from-my-azure-web-app"></a>Kan ik e-mail verzenden vanuit mijn Azure-webapp?

App Service heeft geen ingebouwde e-mailfunctie. Voor een aantal goede alternatieven voor het verzenden van e-mail vanuit uw app, zie deze [Stack Overflow discussie](https://stackoverflow.com/questions/17666161/sending-email-from-azure).

## <a name="why-does-my-wordpress-site-redirect-to-another-url"></a>Waarom wordt mijn WordPress-site omgeleid naar een andere URL?

Als u onlangs bent gemigreerd naar Azure, kan WordPress worden omgeleid naar de oude domein-URL. Dit wordt veroorzaakt door een instelling in de MySQL-database.

WordPress Buddy+ is een Azure-site-extensie die u gebruiken om de omleidings-URL rechtstreeks in de database bij te werken. Zie [WordPress-tools en MySQL-migratie met WordPress Buddy+voor](https://sharepointforum.org/threads/wordpress-tools-and-mysql-migration-with-wordpress-buddy.82929/)meer informatie over het gebruik van WordPress Buddy+.

Als u de omleidings-URL handmatig wilt bijwerken met SQL-query's of PHPMyAdmin, raadpleegt u [Ook WordPress: Omleiden naar de verkeerde URL.](https://blogs.msdn.microsoft.com/azureossds/2016/07/12/wordpress-redirecting-to-wrong-url/)

## <a name="how-do-i-change-my-wordpress-sign-in-password"></a>Hoe wijzig ik mijn aanmeldingswachtwoord voor WordPress?

Als je je WordPress-aanmeldingswachtwoord bent vergeten, kun je WordPress Buddy+ gebruiken om het bij te werken. Als u uw wachtwoord opnieuw wilt instellen, installeert u de WordPress Buddy+ Azure-siteextensie en voert u de stappen uit die zijn beschreven in [WordPress-hulpprogramma's en MySQL-migratie met WordPress Buddy+.](https://sharepointforum.org/threads/wordpress-tools-and-mysql-migration-with-wordpress-buddy.82929/)

## <a name="i-cant-sign-in-to-wordpress-how-do-i-resolve-this"></a>Ik kan me niet aanmelden bij WordPress. Hoe los ik dit op?

Als u merkt dat u buitengesloten van WordPress na onlangs het installeren van een plugin, u een defecte plugin. WordPress Buddy+ is een Azure-site-extensie waarmee u plug-ins in WordPress uitschakelen. Zie [WordPress-tools en MySQL-migratie met WordPress Buddy+](https://sharepointforum.org/threads/wordpress-tools-and-mysql-migration-with-wordpress-buddy.82929/)voor meer informatie.

## <a name="how-do-i-migrate-my-wordpress-database"></a>Hoe migreer ik mijn WordPress-database?

U hebt meerdere opties voor het migreren van de MySQL-database die is verbonden met uw WordPress-website:

* Ontwikkelaars: gebruik de [opdrachtprompt of PHPMyAdmin](https://blogs.msdn.microsoft.com/azureossds/2016/03/02/migrating-data-between-mysql-databases-using-kudu-console-azure-app-service/)
* Niet-ontwikkelaars: [Gebruik WordPress Buddy+](https://sharepointforum.org/threads/wordpress-tools-and-mysql-migration-with-wordpress-buddy.82929/)

## <a name="how-do-i-help-make-wordpress-more-secure"></a>Hoe kan ik WordPress veiliger maken?

Zie [Aanbevolen procedures voor WordPress-beveiliging in Azure](https://blogs.msdn.microsoft.com/azureossds/2016/12/26/best-practices-for-wordpress-security-on-azure/)voor meer informatie over aanbevolen beveiligingsprocedures voor WordPress.

## <a name="i-am-trying-to-use-phpmyadmin-and-i-see-the-message-access-denied-how-do-i-resolve-this"></a>Ik probeer PHPMyAdmin te gebruiken, en ik zie het bericht "Toegang geweigerd." Hoe los ik dit op?

U dit probleem ondervinden als de mysql-in-app-functie nog niet wordt uitgevoerd in dit app-service-exemplaar. Als u het probleem wilt oplossen, probeert u toegang te krijgen tot uw website. Hiermee worden de vereiste processen gestart, waaronder het MySQL in-app-proces. Als u wilt controleren of MySQL in-app wordt uitgevoerd, controleert u in Process Explorer of mysqld.exe in de processen wordt weergegeven.

Nadat u ervoor hebt gezorgd dat MySQL in-app wordt uitgevoerd, probeert u PHPMyAdmin te gebruiken.

## <a name="i-get-an-http-403-error-when-i-try-to-import-or-export-my-mysql-in-app-database-by-using-phpmyadmin-how-do-i-resolve-this"></a>Ik krijg een HTTP 403-fout wanneer ik mijn MySQL in-app-database probeer te importeren of exporteren met PHPMyadmin. Hoe los ik dit op?

Als u een oudere versie van Chrome gebruikt, hebt u mogelijk last van een bekende bug. Als u het probleem wilt oplossen, u upgraden naar een nieuwere versie van Chrome. Probeer ook een andere browser te gebruiken, zoals Internet Explorer of Microsoft Edge, waar het probleem zich niet voordoet.
