---
title: De integratie van AWS-en gebruiks rapporten instellen en configureren met Azure Cost Management
description: Dit artikel begeleidt u bij het instellen en configureren van AWS voor de integratie van kosten-en gebruiks rapporten met Azure Cost Management.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 08/15/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: 66dbe45ff1a8ee773fdf7fcb0aa7cfe8e6ad6437
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74219693"
---
# <a name="set-up-and-configure-aws-cost-and-usage-report-integration"></a>AWS voor kosten-en gebruiks rapporten instellen en configureren

Met Amazon Web Services (AWS)-integratie voor kosten-en gebruiks rapporten (CUR) kunt u uw AWS-uitgaven in Azure Cost Management bewaken en beheren. Met de integratie kunt u één locatie in het Azure Portal waar u de bestedingen bewaken en beheren voor Azure en AWS. In dit artikel wordt uitgelegd hoe u de integratie instelt en configureert zodat u Azure Cost Management functies kunt gebruiken om kosten te analyseren en budgetten te controleren.

Cost Management verwerkt het AWS-kosten-en gebruiks rapport dat is opgeslagen in een S3-Bucket door gebruik te maken van uw AWS-toegangs referenties om rapport definities op te halen en rapport-GZIP CSV-bestanden te downloaden.

## <a name="create-a-cost-and-usage-report-in-aws"></a>Een kosten-en gebruiks rapport maken in AWS

Het gebruik van een kosten-en gebruiks rapport is de AWS-aanbevolen manier om AWS-kosten te verzamelen en te verwerken. Zie de documentatie over kosten- [en gebruiks rapporten van AWS](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-reports-costusage.html) voor meer informatie.

Gebruik de pagina **kosten & gebruiks rapporten** van de console facturering en Cost Management in AWS om een kosten-en gebruiks rapport te maken met de volgende stappen:

1. Meld u aan bij de AWS-beheer console en open de [console facturering en Cost Management](https://console.aws.amazon.com/billing).
2. Selecteer in het navigatie deel venster **kosten & gebruiks rapporten**.
3. Selecteer **rapport maken**.
4. Voer bij **rapport naam**een naam in voor uw rapport.
5. Onder **aanvullende rapport Details**selecteert u **resource-id's toevoegen**.
6. Voor **instellingen voor gegevens vernieuwing**selecteert u of u het AWS-kosten-en gebruiks rapport wilt vernieuwen als AWS restituties, tegoeden of ondersteunings kosten voor uw account toepast nadat uw factuur is voltooid. Wanneer een rapport wordt vernieuwd, wordt een nieuw rapport geüpload naar Amazon S3. U wordt aangeraden de instelling geselecteerd te laten.
7. Selecteer **Volgende**.
8. Kies **configureren**voor **S3 Bucket**.
9. Voer in het dialoog venster S3-Bucket configureren een van de volgende taken uit:
    1. Selecteer een bestaande Bucket in de vervolg keuzelijst en kies **volgende**.
    2. Voer de naam van een Bucket in en de regio waar u een nieuwe Bucket wilt maken en klik op **volgende**.
10. Selecteer **Ik heb bevestigd dat dit beleid juist is**en klik vervolgens op **Opslaan**.
11. Beschrijving Voer voor het voor voegsel van het rapport-pad het pad naar het rapport traject in dat u wilt toevoegen aan de naam van het rapport.
Als u geen voor voegsel opgeeft, is het standaard voorvoegsel de naam die u voor het rapport hebt opgegeven. Het datum bereik heeft de `/report-name/date-range/` indeling.
12. Voor **tijds eenheid**kiest u **elk uur**.
13. Voor **rapport versie beheer**kiest u of u wilt dat elke versie van het rapport de vorige versie overschrijft, of dat u aanvullende nieuwe rapporten wilt.
14. Voor het **inschakelen van gegevens integratie voor**is het niet nodig om te selecteren.
15. Selecteer voor **compressie**de optie **gzip**.
16. Selecteer **Volgende**.
17. Nadat u de instellingen voor uw rapport hebt gecontroleerd, selecteert u **controleren en volt ooien**.

    Noteer de naam van het rapport. U gebruikt deze in latere stappen.

Het kan tot 24 uur duren voordat AWS rapporten aan uw Amazon S3-Bucket leveren. Nadat de levering is gestart, werkt AWS de AWS-kosten-en gebruiks rapport bestanden ten minste één keer per dag bij. U kunt door gaan met het configureren van uw AWS-omgeving zonder te wachten tot de levering wordt gestart.

## <a name="create-a-role-and-policy-in-aws"></a>Een rol en beleid maken in AWS

Azure Cost Management heeft toegang tot de S3-Bucket waar het kosten-en gebruiks rapport meerdere keren per dag is opgeslagen. De service moet toegang hebben tot referenties om te controleren op nieuwe gegevens. U maakt een rol en beleid in AWS om Cost Management toegang te geven.

Om op rollen gebaseerde toegang tot een AWS-account in Cost Management in te scha kelen, wordt de rol gemaakt in de AWS-console. U moet de _rol Arn_ en de _externe ID_ hebben in de AWS-console. Later kunt u ze gebruiken op de pagina **een AWS-connector maken** in cost management.

Gebruik de wizard een nieuwe rol maken:

1. Meld u aan bij uw AWS-console en selecteer **Services**.
2. Selecteer **iam**in de lijst met Services.
3. Selecteer **rollen** en selecteer vervolgens **rol maken**.
4. Selecteer op de volgende pagina **een ander AWS-account**.
5. Voer in **account-ID** **432263259397**in.
6. Selecteer in **Opties** **de optie externe ID vereisen (aanbevolen procedure wanneer een derde partij deze rol zal aannemen)** .
7. Voer in **externe ID**de externe ID in. De externe ID is een gedeelde wachtwoord code tussen de AWS-functie en de Azure Cost Management. Dezelfde externe ID wordt ook gebruikt op de **nieuwe connector** pagina in cost management. Een externe ID lijkt bijvoorbeeld op _Companyname1234567890123_.

    > [!NOTE]
    > Wijzig de selectie niet voor het **vereisen van MFA**. Deze moet leeg blijven.
8. Selecteer **volgende: machtigingen**.
9. Selecteer **beleid maken**. Er wordt een nieuw browsertabblad geopend. Zo maakt u een beleid.
10. Selecteer **een service kiezen**.

Machtiging voor het kosten-en gebruiks rapport configureren:

1. Voer het **kosten-en gebruiks rapport**in.
2. Selecteer **toegangs niveau** > **Lees** > **DescribeReportDefinitions**. Met deze stap kunt Cost Management lezen wat de huidige rapporten zijn gedefinieerd en bepalen of ze overeenkomen met de vereiste voor de rapport definitie.
3. Selecteer **extra machtigingen toevoegen**.

Machtiging voor uw S3-Bucket en-objecten configureren:

1. Selecteer **een service kiezen**.
2. Voer **S3**in.
3. Selecteer **toegangs niveau** > **lijst** > **ListBucket**. Met deze actie wordt de lijst met objecten in de S3-Bucket opgehaald.
4. Selecteer **toegangs niveau** > **lezen** > **GetObject**. Met deze actie kunnen facturerings bestanden worden gedownload.
5. **Resources**selecteren.
6. Selecteer **Bucket – Arn toevoegen**.
7. Voer in **Bucket naam**de Bucket in die wordt gebruikt voor het opslaan van de gecurte bestanden.
8. Selecteer **object – Arn toevoegen**.
9. Voer in **Bucket naam**de Bucket in die wordt gebruikt voor het opslaan van de gecurte bestanden.
10. Selecteer in **object naam** **een**.
11. Selecteer **extra machtigingen toevoegen**.

Machtiging voor de kosten Verkenner configureren:

1. Selecteer **een service kiezen**.
2. Voer de **service cost Explorer**in.
3. **Alle service acties van cost Explorer selecteren (CE:\*)** . Met deze actie wordt gecontroleerd of de verzameling juist is.
4. Selecteer **extra machtigingen toevoegen**.

Machtiging toevoegen voor AWS-organisaties:

1. Voer **organisaties**in.
2. Selecteer **toegangs niveau** > **lijst** > **ListAccounts**. Met deze actie worden de namen van de accounts opgehaald.
3. Voer in **beleid controleren**een naam in voor het nieuwe beleid. Controleer of u de juiste gegevens hebt ingevoerd en selecteer vervolgens **beleid maken**.
4. Ga terug naar het vorige tabblad en vernieuw de webpagina van uw browser. Zoek op de zoek balk naar het nieuwe beleid.
5. Selecteer **volgende: controleren**.
6. Voer een naam in voor de nieuwe rol. Controleer of u de juiste gegevens hebt ingevoerd en selecteer **rol maken**.

    Let op de functie ARN en de externe ID die in de voor gaande stappen worden gebruikt tijdens het maken van de rol. U gebruikt deze later bij het instellen van de Azure Cost Management-connector.

De JSON van het beleid moet eruitzien als in het volgende voor beeld. Vervang _Bucket_ door de naam van uw S3-Bucket.

```JSON
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
"organizations:ListAccounts",
            "ce:*",
            "cur:DescribeReportDefinitions"
            ],
            "Resource": "*"
        },
        {
            "Sid": "VisualEditor1",
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::bucketname",
                "arn:aws:s3:::bucketname/*"
            ]
        }
    ]
}
```

## <a name="set-up-a-new-aws-connector-in-azure"></a>Een nieuwe AWS-connector instellen in azure

Gebruik de volgende informatie om een AWS-connector te maken en te beginnen met het bewaken van uw AWS-kosten:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Ga naar **Cost Management en facturering** > **Cost Management**.
3. Selecteer onder **instellingen**de optie **Cloud connectors (preview)** .  
    ![-voor beeld waarin de instelling voor Cloud connectors (preview) wordt weer gegeven](./media/aws-integration-setup-configure/cloud-connectors-preview01.png).
4. Selecteer **+ toevoegen** boven aan de pagina om een connector te maken.
5. Voer op de pagina **een AWS-connector maken** in **weergave naam**een naam in voor de connector.  
    ![voor beeld van de pagina voor het maken van een AWS-connector](./media/aws-integration-setup-configure/create-aws-connector01.png)
6. Selecteer desgewenst de standaard beheer groep. Alle gedetecteerde gekoppelde accounts worden opgeslagen. U kunt deze later instellen.
7. Selecteer in de sectie **facturering** **automatisch het bedrag van 1% bij algemene Beschik baarheid** als u een doorlopende werking wilt garanderen wanneer de preview-periode verloopt. Als u de automatische optie selecteert, moet u een facturerings abonnement selecteren.
8. Voer voor **Role Arn**de waarde in die u hebt gebruikt bij het instellen van de functie in AWS.
9. Voer voor **externe ID**de waarde in die u hebt gebruikt bij het instellen van de functie in AWS.
10. Voer bij **rapport naam**de naam in die u hebt gemaakt in AWS.
11. Selecteer **volgende** en selecteer vervolgens **maken**.

Het kan enkele uren duren voordat de nieuwe AWS-scopes, het AWS geconsolideerde account, de AWS gekoppelde accounts en de bijbehorende kosten gegevens worden weer gegeven.

Wanneer u de connector hebt gemaakt, wordt u aangeraden toegangs beheer aan toe te wijzen. Aan gebruikers worden machtigingen toegewezen voor de nieuwe gedetecteerde bereiken: AWS geconsolideerde account en AWS gekoppelde accounts. De gebruiker die de connector maakt, is de eigenaar van de connector, het geconsolideerde account en alle gekoppelde accounts.

Het toewijzen van connector machtigingen aan gebruikers na detectie vindt geen machtigingen voor de bestaande AWS-bereiken. In plaats daarvan worden alleen aan nieuwe gekoppelde accounts machtigingen toegewezen.

## <a name="take-additional-steps"></a>Aanvullende stappen uitvoeren

- [Stel beheer groepen](../governance/management-groups/overview.md#initial-setup-of-management-groups)in, als u dat nog niet hebt gedaan.
- Controleer of er nieuwe scopes zijn toegevoegd aan de bereik kiezer. Selecteer **vernieuwen** om de meest recente gegevens weer te geven.
- Selecteer op de pagina **Cloud connectors** de connector en selecteer **naar facturerings account gaan** om het gekoppelde account aan beheer groepen toe te wijzen.

## <a name="manage-cloud-connectors"></a>Cloud-connectors beheren

Wanneer u een connector selecteert op de pagina **Cloud connectors** , kunt u het volgende doen:

- Selecteer **naar het facturerings account gaan** om informatie weer te geven voor het geconsolideerde AWS-account.
- Selecteer **Access Control** om de roltoewijzing voor de connector te beheren.
- Selecteer **bewerken** om de connector bij te werken. U kunt het AWS-account nummer niet wijzigen omdat het wordt weer gegeven in de functie ARN. U kunt echter wel een nieuwe connector maken.
- Selecteer **verifiëren** om de verificatie test opnieuw uit te voeren om ervoor te zorgen dat Cost Management gegevens kan verzamelen met behulp van de connector instellingen.

![Voor beeld van een lijst met gemaakte AWS-connectors](./media/aws-integration-setup-configure/list-aws-connectors.png)

## <a name="set-up-azure-management-groups"></a>Azure-beheer groepen instellen

Plaats uw Azure-abonnementen en AWS gekoppelde accounts in dezelfde beheer groep om één locatie te maken waar u gegevens over de cross-Cloud provider kunt zien. Als u uw Azure-omgeving nog niet hebt geconfigureerd met beheer groepen, raadpleegt u de [eerste instellingen van beheer groepen](../governance/management-groups/overview.md#initial-setup-of-management-groups).

Als u kosten wilt scheiden, kunt u een beheer groep maken die alleen AWS gekoppelde accounts bevat.

## <a name="set-up-an-aws-consolidated-account"></a>Een geconsolideerd AWS-account instellen

Het geconsolideerde AWS-account combineert facturering en betaling voor meerdere AWS-accounts. Het fungeert ook als een AWS gekoppeld account.

![Voor beeld van Details voor een geconsolideerd AWS-account](./media/aws-integration-setup-configure/aws-consolidated-account01.png)

Op de pagina kunt u het volgende doen:

- Selecteer **bijwerken** om de koppeling van AWS gekoppelde accounts bij een beheer groep bulksgewijs bij te werken.
- Selecteer **Access Control** om de roltoewijzing voor het bereik in te stellen.

### <a name="permissions-for-an-aws-consolidated-account"></a>Machtigingen voor een geconsolideerd AWS-account

Machtigingen voor een AWS-geconsolideerd account worden standaard ingesteld op basis van de AWS-connector machtigingen. De maker van de connector is de eigenaar.

U beheert het toegangs niveau met behulp van de pagina **toegangs niveau** van het geconsolideerde AWS-account. AWS gekoppelde accounts nemen echter geen machtigingen over van het geconsolideerde AWS-account.

## <a name="set-up-an-aws-linked-account"></a>Een AWS-gekoppeld account instellen

Het gekoppelde AWS-account is waar AWS-resources worden gemaakt en beheerd. Een gekoppeld account fungeert ook als een beveiligings grens.

Op deze pagina kunt u het volgende doen:

- Selecteer **bijwerken** om de koppeling van een AWS gekoppeld account bij een beheer groep bij te werken.
- Selecteer **Access Control** om een roltoewijzing voor het bereik in te stellen.

![Voor beeld van de pagina gekoppeld AWS-account](./media/aws-integration-setup-configure/aws-linked-account01.png)

### <a name="permissions-for-an-aws-linked-account"></a>Machtigingen voor een AWS gekoppeld account

Standaard worden machtigingen voor een AWS gekoppeld account ingesteld bij het maken, op basis van de AWS-connector machtigingen. De maker van de connector is de eigenaar. U beheert het toegangs niveau met behulp van de pagina **toegangs niveau** van het gekoppelde AWS-account. AWS gekoppelde accounts nemen geen machtigingen over van een geconsolideerd AWS-account.

AWS gekoppelde accounts nemen altijd machtigingen over van de beheer groep waarvan ze deel uitmaken.

## <a name="next-steps"></a>Volgende stappen

- Nu u de integratie van de kosten en het gebruiks rapport van AWS hebt ingesteld en geconfigureerd, kunt u de [kosten en het gebruik van AWS blijven beheren](aws-integration-manage.md).
- Als u niet bekend bent met cost analysis, raadpleegt u [kosten verkennen en analyseren met de Snelstartgids voor kosten analyse](quick-acm-cost-analysis.md) .
- Zie [Azure-budgetten maken en beheren](tutorial-acm-create-budgets.md)als u niet bekend bent met budgetten in Azure.
