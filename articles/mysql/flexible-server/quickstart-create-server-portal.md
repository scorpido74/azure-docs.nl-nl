---
title: 'Quickstart: Een Azure DB for MySQL Flexible Server maken - Azure Portal'
description: Dit artikel leidt u stapsgewijs door de Azure Portal zodat u snel, in een paar minuten, een Azure Database for MySQL Flexible Server kunt maken.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 9/21/2020
ms.openlocfilehash: 4d4d65f9ad04ca6bf99375647684a75e8662bb4d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90944527"
---
# <a name="quickstart-use-the-azure-portal-to-create-an-azure-database-for-mysql-flexible-server"></a>Quickstart: Een Azure Database for MySQL Flexible Server maken met behulp van de Azure Portal

Azure Database for MySQL is een beheerde service waarmee u MySQL-servers met hoge beschikbaarheid in de cloud kunt uitvoeren, beheren en schalen. In deze snelstartgids ziet u hoe u met behulp van de Azure Portal een flexibele server kunt maken.

> [!IMPORTANT] 
> Azure Database for MySQL Flexible Server is momenteel beschikbaar als openbare preview

Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal
Open uw webbrowser en ga naar [Azure Portal](https://portal.azure.com/). Voer uw referenties in om u aan te melden bij de portal. De standaardweergave is uw service-dashboard.

## <a name="create-an-azure-database-for-mysql-flexible-server"></a>Een Azure Database for MySQL Flexible Server maken

U maakt een flexibele server met een gedefinieerde set [reken- en opslagresources](./concepts-compute-storage.md). De server wordt gemaakt binnen een [Azure-resourcegroep](https://docs.microsoft.com/azure/azure-resource-manager/management/overview).

Volg deze stappen om een flexibele server te maken:

1. Selecteer in de linkerbovenhoek van de portal **Een resource maken** (+).

2. Selecteer **Databases** > **Azure Database voor MySQL**. U kunt ook **MySQL** in het zoekvak typen om de service te vinden.

    >[!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstart-create-server-portal/navigate-to-mysql.png" alt-text="De optie Azure Database for MySQL":::

3. Selecteer **Flexibele server** als implementatieoptie.
     
    >[!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstart-create-server-portal/deployment-option.png" alt-text="Implementatieoptie kiezen":::    

4. Vul het formulier **Basisbeginselen** in met de volgende gegevens: 

    >[!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstart-create-server-portal/create-form.png" alt-text="Formulier voor het maken van een server"::: 
                                    
    |**Instelling**|**Voorgestelde waarde**|**Beschrijving**|
    |---|---|---|
    Abonnement|De naam van uw abonnement|Het Azure-abonnement dat u wilt gebruiken voor uw server. Als u meerdere abonnementen hebt, kiest u het abonnement waarin u wilt worden gefactureerd voor de resource.|
    Resourcegroep|*myresourcegroup*| Een nieuwe resourcegroepnaam of een bestaande naam uit uw abonnement.|
    Servernaam |*mydemoserver*|Een unieke naam die uw flexibele server identificeert. De domeinnaam *mysql.database.azure.com* wordt toegevoegd aan de naam van de server die u opgeeft. De server mag alleen kleine letters, cijfers en het koppelteken (-) bevatten. Hij moet ten minste 3 en mag maximaal 63 tekens bevatten.|
    Gebruikersnaam van beheerder |*mydemouser*| Uw eigen aanmeldingsaccount dat moet worden gebruikt om verbinding te maken met de server. De aanmeldingsnaam van de beheerder mag niet **azure_superuser**, **admin**, **administrator**, **root**, **guest**of **public** zijn.|
    Wachtwoord |Uw wachtwoord| Een nieuw wachtwoord voor het beheerdersaccount voor de server. Dit wachtwoord moet tussen 8 en 128 tekens bevatten. Uw wachtwoord moet tekens bevatten uit drie van de volgende categorieën: Nederlandse hoofdletters, Nederlandse kleine letters, cijfers (0 tot en met 9) en niet-alfanumerieke tekens (!, $, #, %, enzovoort).|
    Regio|De regio het dichtst bij uw gebruikers| De locatie die zich het dichtst bij uw gebruikers bevindt.|
    Versie|5.7| Primaire MySQL-versie.|
    Berekening en opslag | **Burstable**, **Standard_B1ms**, **10 GiB**, **7 dagen** | De reken-, opslag- en back-upconfiguraties voor de nieuwe server. Selecteer **Server configureren**. *Burstable*, *Standard_B1ms*, *10 GiB* en *7 dagen* zijn de standaardwaarden voor **Rekenlaag**, **Rekengrootte**, **Opslag** en **Bewaartermijn voor back-up**. U kunt deze schuifregelaars laten zoals ze zijn of ze aanpassen. Als u deze berekening en opslagselectie wilt opslaan, selecteert u **Opslaan** om door te gaan met configuraties. In de onderstaande schermafbeelding ziet u de opties voor berekenen en opslaan.|
    
    >[!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstart-create-server-portal/compute-storage.png" alt-text="Berekening en opslag":::

5. Netwerkopties configureren

    Op het tabblad Netwerken kunt u kiezen hoe de server bereikbaar is. Azure Database for MySQL Flexible Server biedt twee opties om verbinding te maken met uw server via *Openbare toegang (toegestane IP-adressen)* en *Persoonlijke toegang (VNet-integratie)* . Met *Openbare toegang (toegestane IP-adressen)* is de toegang tot uw server beperkt tot toegestane IP-adressen die zijn toegevoegd aan een firewallregel. Dit voorkomt dat externe toepassingen en hulpprogramma's verbinding maken met de server of databases op de server, tenzij u een firewallregel maakt om de firewall te openen voor specifieke IP-adressen. Met *Persoonlijke toegang (VNet-integratie)* is de toegang tot uw server beperkt tot het virtuele netwerk. In deze snelstartgids laten we u zien hoe u openbare toegang tot de server kunt inschakelen. Meer informatie over connectiviteitsmethoden vindt u in het [artikel over concepten](./concepts-networking.md).

    > [!NOTE]
    > De verbindingsmethode kan niet worden gewijzigd na het maken van de server. Als u bijvoorbeeld *Openbare toegang (toegestane IP-adressen)* hebt geselecteerd tijdens het maken, kunt u na het maken niet wijzigen naar *Persoonlijke toegang (VNet-integratie)* . U kunt het beste een server met persoonlijke toegang maken om veilig toegang te krijgen tot uw server met behulp van VNet-integratie. Meer informatie over persoonlijke toegang vindt u in het [artikel over concepten](./concepts-networking.md).

    >[!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstart-create-server-portal/networking.png" alt-text="Netwerk configureren":::  

    <!--:::image type="content" source="./media/quickstart-create-database-portal/6-add-client-ip.png" alt-text="Select "Add current client IP address"":::-->

6. Selecteer **Beoordelen + maken** om uw flexibele serverconfiguratie te controleren.

7. Selecteer **Maken** om de server in te richten. Het inrichten kan enkele minuten duren.

8. Selecteer **Meldingen** op de werkbalk (pictogram van een klok) om het implementatieproces te bewaken. Na de implementatie kunt u **Vastmaken aan dashboard** selecteren. Hiermee maakt u een tegel voor deze flexibele server op uw dashboard in de Azure Portal als snelkoppeling naar de **overzichtspagina** van de server. Als u **Naar de resource gaan** selecteert, wordt de **overzichtspagina** van de server weergegeven.

Standaard worden de volgende databases gemaakt voor de server: **information_schema**, **mysql**, **performance_schema** en **sys**.

> [!NOTE]
> Controleer of uw netwerk uitgaand verkeer toestaat via poort 3306, die wordt gebruikt door Azure Database for MySQL Flexible Server om connectiviteitsproblemen te vermijden.  

## <a name="connect-to-using-mysql-command-line-client"></a>Verbinding maken met via de MySQL-opdrachtregel-client

Als u een flexibele server hebt gemaakt met *Persoonlijke toegang (VNet-integratie)* , moet u verbinding maken met uw server vanuit een resource binnen hetzelfde VNet als uw server. U kunt een virtuele machine maken en toevoegen aan het VNet dat is gemaakt met uw flexibele server.

Als u uw flexibele server met *Openbare toegang (toegestane IP-adressen)* hebt gemaakt, kunt u uw lokale IP-adres toevoegen aan de lijst met firewallregels op uw server.

U kunt [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) of [MySQL Workbench](./connect-workbench.md) kiezen om vanuit uw lokale omgeving verbinding met de server te maken. 

Met mysql.exe kunt u verbinding maken met behulp van de onderstaande opdracht. Vervang de waarden door uw werkelijke servernaam en wachtwoord. 

```bash
 mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p
```
## <a name="clean-up-resources"></a>Resources opschonen
U hebt een Azure Database for MySQL Flexible Server in een resourcegroep gemaakt.  Als u deze resources in de toekomst waarschijnlijk niet nodig hebt, kunt u ze verwijderen door de resourcegroep of de MySQL-server te verwijderen. Voer de volgende stappen uit om de resourcegroep te verwijderen:

1. Zoek en selecteer **Resourcegroepen** in de Azure-portal.
1. Kies in de lijst met resourcegroepen de naam van uw resourcegroep.
1. Selecteer op de pagina Overzicht van uw resourcegroep de optie **Resourcegroep verwijderen**.
1. Typ de naam van de resourcegroep in het bevestigingsvenster. Selecteer vervolgens **Verwijderen**.

Om uw server te verwijderen, klikt u op de knop **Verwijderen** op de pagina **Overzicht** van uw server, zoals hieronder weergegeven:

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-server-portal/delete-server.png" alt-text="Uw resources verwijderen":::

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een PHP-web-app (Laravel) bouwen met MySQL](tutorial-php-database-app.md)
