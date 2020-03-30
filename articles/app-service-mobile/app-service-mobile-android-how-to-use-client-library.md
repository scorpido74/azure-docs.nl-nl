---
title: De SDK voor Android gebruiken
description: De Azure Mobile Apps SDK voor Android gebruiken
ms.assetid: 5352d1e4-7685-4a11-aaf4-10bd2fa9f9fc
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 52e91d900ce0f22862904695ba8adf463219c469
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249384"
---
# <a name="how-to-use-the-azure-mobile-apps-sdk-for-android"></a>De Azure Mobile Apps SDK voor Android gebruiken

In deze handleiding ziet u hoe u de Sdk van de Android-client voor mobiele apps gebruiken om veelvoorkomende scenario's te implementeren, zoals:

* Query's voor gegevens (invoegen, bijwerken en verwijderen).
* Verificatie.
* Fouten verwerken.
* De client aanpassen.

Deze gids richt zich op de client-kant Android SDK.  Zie [Werken met .NET backend SDK][10] of Hoe gebruik je de [Node.js backend SDK][11]voor meer informatie over de sdks aan de serverzijde voor mobiele apps.

## <a name="reference-documentation"></a>Referentiedocumentatie

U vindt de [Javadocs API-referentie][12] voor de Android-clientbibliotheek op GitHub.

## <a name="supported-platforms"></a>Ondersteunde platformen

De Azure Mobile Apps SDK voor Android ondersteunt API-niveaus 19 tot en met 24 (KitKat via Nougat) voor telefoon- en tabletvormfactoren.  Verificatie, in het bijzonder, maakt gebruik van een gemeenschappelijke web framework aanpak om referenties te verzamelen.  Server-flow-verificatie werkt niet met apparaten met een kleine vormfactor, zoals horloges.

## <a name="setup-and-prerequisites"></a>Instellingen en vereisten

Voltooi de [snelstartzelfstudie van Mobiele apps.](app-service-mobile-android-get-started.md)  Deze taak zorgt ervoor dat aan alle vereisten voor het ontwikkelen van Azure Mobile Apps is voldaan.  Met De Quickstart u ook uw account configureren en uw eerste back-end voor mobiele apps maken.

Als u besluit de snelstart-zelfstudie niet te voltooien, voert u de volgende taken uit:

* [maak een back-end van een mobiele app][13] om te gebruiken met je Android-app.
* Werk in Android Studio [de Gradle-buildbestanden bij.](#gradle-build)
* [Internettoestemming inschakelen](#enable-internet).

### <a name="update-the-gradle-build-file"></a><a name="gradle-build"></a>Het bouwbestand van Gradle bijwerken

Beide **build.gradle-bestanden wijzigen:**

1. Voeg deze code toe aan het bestand *Projectlevel* **build.gradle:**

    ```gradle
    buildscript {
        repositories {
            jcenter()
            google()
        }
    }

    allprojects {
        repositories {
            jcenter()
            google()
        }
    }
    ```

2. Voeg deze code toe aan het **bestand build.gradle van** *module-appniveau* in de *afhankelijkheidstag:*

    ```gradle
    implementation 'com.microsoft.azure:azure-mobile-android:3.4.0@aar'
    ```

    Momenteel is de nieuwste versie 3.4.0. De ondersteunde versies staan vermeld [op de bakbak.][14]

### <a name="enable-internet-permission"></a><a name="enable-internet"></a>Internettoestemming inschakelen

Als u toegang wilt krijgen tot Azure, moet de internetmachtiging zijn ingeschakeld. Als deze nog niet is ingeschakeld, voegt u de volgende coderegel toe aan uw **AndroidManifest.xml-bestand:**

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

## <a name="create-a-client-connection"></a>Een clientverbinding maken

Azure Mobile Apps biedt vier functies voor uw mobiele toepassing:

* Gegevenstoegang en offlinesynchronisatie met een Azure Mobile Apps-service.
* Oproep aangepaste API's die zijn geschreven met de Azure Mobile Apps Server SDK.
* Verificatie met verificatie en autorisatie van Azure App Service.
* Pushmeldingsregistratie met meldingshubs.

Voor elk van deze functies `MobileServiceClient` moet u eerst een object maken.  Er `MobileServiceClient` mag slechts één object worden gemaakt binnen uw mobiele client (dat wil zeggen dat het een Singleton-patroon moet zijn).  Ga als `MobileServiceClient` lid van het nieuwe object als een ander:

```java
MobileServiceClient mClient = new MobileServiceClient(
    "<MobileAppUrl>",       // Replace with the Site URL
    this);                  // Your application Context
```

Het `<MobileAppUrl>` is een tekenreeks of een URL-object dat naar uw mobiele backend verwijst.  Als u Azure App Service gebruikt om uw mobiele back-end te hosten, moet u ervoor zorgen dat u de beveiligde `https://` versie van de URL gebruikt.

De client vereist ook toegang tot `this` de activiteit of context - de parameter in het voorbeeld.  De MobileServiceClient-constructie moet `onCreate()` plaatsvinden binnen de methode `AndroidManifest.xml` van de activiteit waarnaar in het bestand wordt verwezen.

Als best practice moet u servercommunicatie abstraheren in de eigen klasse (singleton-patroon).  In dit geval moet u de activiteit binnen de constructor doorgeven om de service op de juiste manier te configureren.  Bijvoorbeeld:

```java
package com.example.appname.services;

import android.content.Context;
import com.microsoft.windowsazure.mobileservices.*;

public class AzureServiceAdapter {
    private String mMobileBackendUrl = "https://myappname.azurewebsites.net";
    private Context mContext;
    private MobileServiceClient mClient;
    private static AzureServiceAdapter mInstance = null;

    private AzureServiceAdapter(Context context) {
        mContext = context;
        mClient = new MobileServiceClient(mMobileBackendUrl, mContext);
    }

    public static void Initialize(Context context) {
        if (mInstance == null) {
            mInstance = new AzureServiceAdapter(context);
        } else {
            throw new IllegalStateException("AzureServiceAdapter is already initialized");
        }
    }

    public static AzureServiceAdapter getInstance() {
        if (mInstance == null) {
            throw new IllegalStateException("AzureServiceAdapter is not initialized");
        }
        return mInstance;
    }

    public MobileServiceClient getClient() {
        return mClient;
    }

    // Place any public methods that operate on mClient here.
}
```

U `AzureServiceAdapter.Initialize(this);` nu `onCreate()` een beroep doen op de methode van uw hoofdactiviteit.  Alle andere methoden die toegang tot `AzureServiceAdapter.getInstance();` het clientgebruik nodig hebben om een verwijzing naar de serviceadapter te verkrijgen.

## <a name="data-operations"></a>Gegevensbewerkingen

De kern van de Azure Mobile Apps SDK is om toegang te bieden tot gegevens die zijn opgeslagen in SQL Azure op de backend van de mobiele app.  U hebt toegang tot deze gegevens met sterk getypte klassen (voorkeurs) of niet-getypte query's (niet aanbevolen).  Het grootste deel van deze sectie gaat over het gebruik van sterk getypte klassen.

### <a name="define-client-data-classes"></a>Clientgegevensklassen definiëren

Als u toegang wilt krijgen tot gegevens uit SQL Azure-tabellen, definieert u clientgegevensklassen die overeenkomen met de tabellen in de back-end van de mobiele app. Voorbeelden in dit onderwerp gaan uit van een tabel met de naam **MyDataTable**, die de volgende kolommen heeft:

* id
* tekst
* voltooid

Het overeenkomstige getypte client-side object bevindt zich in een bestand genaamd **MyDataTable.java:**

```java
public class ToDoItem {
    private String id;
    private String text;
    private Boolean complete;
}
```

Voeg getter- en settermethoden toe voor elk veld dat u toevoegt.  Als uw SQL Azure-tabel meer kolommen bevat, voegt u de bijbehorende velden toe aan deze klasse.  Als het DTO-object (object gegevensoverdracht) bijvoorbeeld een kolom integer Prioriteit had, u dit veld toevoegen, samen met de getter- en settermethoden:

```java
private Integer priority;

/**
* Returns the item priority
*/
public Integer getPriority() {
    return mPriority;
}

/**
* Sets the item priority
*
* @param priority
*            priority to set
*/
public final void setPriority(Integer priority) {
    mPriority = priority;
}
```

Zie [Een tabelcontroller][15] definiëren (.NET backend) of Tabellen definiëren met een dynamisch [schema][16] (Backend van Node.js) voor meer informatie over het maken van extra tabellen in de back-end van mobiele apps.

In een backendtabel van Azure Mobile Apps worden vijf speciale velden gedefinieerd, waarvan er vier beschikbaar zijn voor clients:

* `String id`: De wereldwijd unieke ID voor de goede orde.  Als aanbevolen praktijk maakt u de id de tekenreeksweergave van een [UUID-object.][17]
* `DateTimeOffset updatedAt`: De datum/tijd van de laatste update.  Het veld updatedAt is ingesteld door de server en mag nooit worden ingesteld door uw clientcode.
* `DateTimeOffset createdAt`: de datum/tijd waarop het object is gemaakt.  Het veld createdAt wordt ingesteld door de server en mag nooit worden ingesteld door uw clientcode.
* `byte[] version`: Normaal gesproken weergegeven als een string, wordt de versie ook ingesteld door de server.
* `boolean deleted`: Geeft aan dat de record is verwijderd, maar nog niet is verwijderd.  Niet gebruiken `deleted` als een eigenschap in uw klas.

Het veld `id` is vereist.  Het `updatedAt` veld `version` en het veld worden gebruikt voor offline synchronisatie (respectievelijk voor incrementele synchronisatie en conflictoplossing).  Het `createdAt` veld is een referentieveld en wordt niet gebruikt door de client.  De namen zijn "over-the-wire" namen van de eigenschappen en zijn niet verstelbaar.  U echter een toewijzing maken tussen uw object en de over-the-wire namen met behulp van de [gson-bibliotheek.][3]  Bijvoorbeeld:

```java
package com.example.zumoappname;

import com.microsoft.windowsazure.mobileservices.table.DateTimeOffset;

public class ToDoItem
{
    @com.google.gson.annotations.SerializedName("id")
    private String mId;
    public String getId() { return mId; }
    public final void setId(String id) { mId = id; }

    @com.google.gson.annotations.SerializedName("complete")
    private boolean mComplete;
    public boolean isComplete() { return mComplete; }
    public void setComplete(boolean complete) { mComplete = complete; }

    @com.google.gson.annotations.SerializedName("text")
    private String mText;
    public String getText() { return mText; }
    public final void setText(String text) { mText = text; }

    @com.google.gson.annotations.SerializedName("createdAt")
    private DateTimeOffset mCreatedAt;
    public DateTimeOffset getCreatedAt() { return mCreatedAt; }
    protected void setCreatedAt(DateTimeOffset createdAt) { mCreatedAt = createdAt; }

    @com.google.gson.annotations.SerializedName("updatedAt")
    private DateTimeOffset mUpdatedAt;
    public DateTimeOffset getUpdatedAt() { return mUpdatedAt; }
    protected void setUpdatedAt(DateTimeOffset updatedAt) { mUpdatedAt = updatedAt; }

    @com.google.gson.annotations.SerializedName("version")
    private String mVersion;
    public String getVersion() { return mVersion; }
    public final void setVersion(String version) { mVersion = version; }

    public ToDoItem() { }

    public ToDoItem(String id, String text) {
        this.setId(id);
        this.setText(text);
    }

    @Override
    public boolean equals(Object o) {
        return o instanceof ToDoItem && ((ToDoItem) o).mId == mId;
    }

    @Override
    public String toString() {
        return getText();
    }
}
```

### <a name="create-a-table-reference"></a>Een tabelverwijzing maken

Als u toegang wilt krijgen tot een tabel, maakt u eerst een [MobileServiceTable-object][8] door de **getTable-methode** op de [MobileServiceClient][9]aan te roepen.  Deze methode heeft twee overbelastingen:

```java
public class MobileServiceClient {
    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
}
```

In de volgende code is **mClient** een verwijzing naar uw MobileServiceClient-object.  De eerste overbelasting wordt gebruikt waarbij de klassenaam en de tabelnaam hetzelfde zijn en die wordt gebruikt in de Quickstart:

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);
```

De tweede overbelasting wordt gebruikt wanneer de tabelnaam verschilt van de klassenaam: de eerste parameter is de tabelnaam.

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);
```

## <a name="query-a-backend-table"></a><a name="query"></a>Een back-endtabel opvragen

Verkrijg eerst een tabelverwijzing.  Voer vervolgens een query uit op de tabelverwijzing.  Een query is een combinatie van:

* Een `.where()` [filterclausule](#filtering).
* Een `.orderBy()` [bestelclausule](#sorting).
* Een `.select()` [veldselectieclausule](#selection).
* A `.skip()` `.top()` en voor [opgepiepte resultaten](#paging).

De clausules moeten in de voorafgaande volgorde worden ingediend.

### <a name="filtering-results"></a><a name="filter"></a>Resultaten filteren

De algemene vorm van een query is:

```java
List<MyDataTable> results = mDataTable
    // More filters here
    .execute()          // Returns a ListenableFuture<E>
    .get()              // Converts the async into a sync result
```

In het voorgaande voorbeeld worden alle resultaten geretourneerd (tot het maximale paginaformaat dat door de server is ingesteld).  De `.execute()` methode voert de query uit op de backend.  De query wordt geconverteerd naar een [OData v3-query][19] voordat deze wordt verzonden naar de backend van Mobiele apps.  Bij ontvangst zet de backend van Mobile Apps de query om in een SQL-instructie voordat deze wordt uitgevoerd op de SQL Azure-instantie.  Aangezien netwerkactiviteit enige tijd `.execute()` in [`ListenableFuture<E>`][18]beslag neemt, retourneert de methode een .

### <a name="filter-returned-data"></a><a name="filtering"></a>Geretourneerde gegevens filteren

Met de volgende query-uitvoering worden alle items uit de **todoitem-tabel** geretourneerd waarin **volledig** gelijk is aan **false**.

```java
List<ToDoItem> result = mToDoTable
    .where()
    .field("complete").eq(false)
    .execute()
    .get();
```

**mToDoTable** is de verwijzing naar de mobiele servicetabel die we eerder hebben gemaakt.

Definieer een filter met de **aanroep waar** de aanroep op de tabelverwijzing wordt aangenomen. De **waarmethode** wordt gevolgd door een **veldmethode** gevolgd door een methode die het logische predicaat aangeeft. Mogelijke predicaatmethoden zijn **eq** (gelijken), **ne** (niet gelijk), **gt** (groter dan), **ge** (groter dan of gelijk aan), **lt** (minder dan), **le** (minder dan of gelijk aan). Met deze methoden u getal- en tekenreeksvelden vergelijken met specifieke waarden.

U filteren op datums. Met de volgende methoden u het volledige datumveld of delen van de datum vergelijken: **jaar**, **maand**, **dag**, **uur**, **minuut**en **tweede**. In het volgende voorbeeld wordt een filter toegevoegd voor items waarvan *de vervaldatum* gelijk is aan 2013.

```java
List<ToDoItem> results = MToDoTable
    .where()
    .year("due").eq(2013)
    .execute()
    .get();
```

De volgende methoden ondersteunen complexe filters op tekenreeksvelden: **startsWith**, **endsWith**, **concat**, **subString**, **indexOf**, **replace**, **toLower**, **toUpper,** **trim**, and **length**. In het volgende voorbeeld wordt gefilterd voor tabelrijen waarin de *tekstkolom* begint met 'PRI0'.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .startsWith("text", "PRI0")
    .execute()
    .get();
```

De volgende operatormethoden worden ondersteund op getalvelden: **toevoegen**, **sub**, **mul**, **div**, **mod**, **vloer**, **plafond**en **rond**. In het volgende voorbeeld wordt gefilterd voor tabelrijen waarbij de **duur** een even getal is.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .field("duration").mod(2).eq(0)
    .execute()
    .get();
```

U predicaten combineren met deze logische methoden: **en**, **of** **niet**. In het volgende voorbeeld worden twee van de voorgaande voorbeelden gecombineerd.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .year("due").eq(2013).and().startsWith("text", "PRI0")
    .execute()
    .get();
```

Logische groeps- en nestlogische operatoren:

```java
List<ToDoItem> results = mToDoTable
    .where()
    .year("due").eq(2013)
    .and(
        startsWith("text", "PRI0")
        .or()
        .field("duration").gt(10)
    )
    .execute().get();
```

Zie [De rijkdom van het Android-clientquerymodel verkennen][20]voor meer gedetailleerde discussies en voorbeelden van filteren.

### <a name="sort-returned-data"></a><a name="sorting"></a>Geretourneerde gegevens sorteren

Met de volgende code worden alle items geretourneerd uit een tabel **met ToDoItems** die is gesorteerd oplopend naar het *tekstveld.* *mToDoTable* is de verwijzing naar de backendtabel die u eerder hebt gemaakt:

```java
List<ToDoItem> results = mToDoTable
    .orderBy("text", QueryOrder.Ascending)
    .execute()
    .get();
```

De eerste parameter van de **orderDoor** methode is een tekenreeks gelijk aan de naam van het veld waarop te sorteren. De tweede parameter gebruikt **de** queryorderopsomming om op te geven of oplopend of aflopend moet worden gesorteerd.  Als u filtert met behulp van de ***waar*** methode, de ***waar*** methode moet worden aangeroepen vóór de ***orderBy*** methode.

### <a name="select-specific-columns"></a><a name="selection"></a>Specifieke kolommen selecteren

In de volgende code ziet u hoe u alle items uit een tabel van **ToDoItems retourneren,** maar alleen de **volledige** en **tekstvelden** weergeeft. **mToDoTable** is de verwijzing naar de backendtabel die we eerder hebben gemaakt.

```java
List<ToDoItemNarrow> result = mToDoTable
    .select("complete", "text")
    .execute()
    .get();
```

De parameters voor de functie selecteren zijn de tekenreeksnamen van de kolommen van de tabel die u wilt retourneren.  De **selecte** methode moet methoden volgen zoals **waar** en **orderBy**. Het kan worden gevolgd door paging methoden zoals **overslaan** en **boven**.

### <a name="return-data-in-pages"></a><a name="paging"></a>Gegevens retourneren op pagina's

Gegevens **worden ALTIJD** geretourneerd in pagina's.  Het maximum aantal geretourneerde records wordt door de server ingesteld.  Als de client meer records aanvraagt, retourneert de server het maximum aantal records.  Standaard is de maximale paginagrootte op de server 50 records.

In het eerste voorbeeld ziet u hoe u de vijf bovenste items uit een tabel selecteert. De query retourneert de items uit een tabel **van ToDoItems**. **mToDoTable** is de verwijzing naar de backendtabel die u eerder hebt gemaakt:

```java
List<ToDoItem> result = mToDoTable
    .top(5)
    .execute()
    .get();
```

Hier volgt een query die de eerste vijf items overslaat en vervolgens de volgende vijf retourneert:

```java
List<ToDoItem> result = mToDoTable
    .skip(5).top(5)
    .execute()
    .get();
```

Als u alle records in een tabel wilt opnemen, implementeert u code om over alle pagina's te herhalen:

```java
List<MyDataModel> results = new ArrayList<>();
int nResults;
do {
    int currentCount = results.size();
    List<MyDataModel> pagedResults = mDataTable
        .skip(currentCount).top(500)
        .execute().get();
    nResults = pagedResults.size();
    if (nResults > 0) {
        results.addAll(pagedResults);
    }
} while (nResults > 0);
```

Met een aanvraag voor alle records met deze methode worden minimaal twee aanvragen gemaakt voor de backend van Mobiele apps.

> [!TIP]
> Het kiezen van de juiste paginagrootte is een balans tussen geheugengebruik terwijl de aanvraag plaatsvindt, bandbreedtegebruik en vertraging bij het volledig ontvangen van de gegevens.  De standaardwaarde (50 records) is geschikt voor alle apparaten.  Als u uitsluitend op grotere geheugenapparaten werkt, verhoogt u de toename tot 500.  We hebben geconstateerd dat het vergroten van de paginagrootte boven 500 records resulteert in onaanvaardbare vertragingen en grote geheugenproblemen.

### <a name="how-to-concatenate-query-methods"></a><a name="chaining"></a>Hoe: Querymethoden concatenate

De methoden die worden gebruikt bij het opvragen van backendtabellen kunnen worden samengevoegd. Met querymethoden voor het vastketenen u specifieke kolommen van gefilterde rijen selecteren die zijn gesorteerd en opgepiept. U complexe logische filters maken.  Elke querymethode retourneert een object Query. Als u de reeks methoden wilt beëindigen en de query daadwerkelijk wilt uitvoeren, roept u de **uitvoermethode aan.** Bijvoorbeeld:

```java
List<ToDoItem> results = mToDoTable
        .where()
        .year("due").eq(2013)
        .and(
            startsWith("text", "PRI0").or().field("duration").gt(10)
        )
        .orderBy(duration, QueryOrder.Ascending)
        .select("id", "complete", "text", "duration")
        .skip(200).top(100)
        .execute()
        .get();
```

De geketende querymethoden moeten als volgt worden besteld:

1. Filteren (**waar)** methoden.
2. Sorteermethoden **(orderBy).**
3. Selectie (**selecteer)** methoden.
4. paging (**overslaan** en **top)** methoden.

## <a name="bind-data-to-the-user-interface"></a><a name="binding"></a>Gegevens binden aan de gebruikersinterface

Gegevensbinding omvat drie componenten:

* De gegevensbron
* De schermindeling
* De adapter die de twee aan elkaar bindt.

In onze voorbeeldcode retourneren we de gegevens van de SQL Azure-tabel Voor mobiele apps **naar** een array. Deze activiteit is een veelvoorkomend patroon voor gegevenstoepassingen.  Databasequery's retourneren vaak een verzameling rijen die de client in een lijst of array krijgt. In dit voorbeeld is de array de gegevensbron.  De code geeft een schermindeling op die de weergave definieert van de gegevens die op het apparaat worden weergegeven.  De twee zijn aan elkaar gekoppeld aan een adapter, die in deze code is een uitbreiding van de **ArrayAdapter&lt;&gt; ToDoItem** klasse.

#### <a name="define-the-layout"></a><a name="layout"></a>De indeling definiëren

De lay-out wordt gedefinieerd door verschillende fragmenten van XML-code. Met een bestaande lay-out vertegenwoordigt de volgende code de **ListView die** we met onze servergegevens willen vullen.

```xml
    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>
```

In de voorgaande code geeft het kenmerk *listitem* de id van de indeling voor een afzonderlijke rij in de lijst op. Deze code geeft een selectievakje en de bijbehorende tekst op en wordt één keer geinstantieerd voor elk item in de lijst. In deze indeling wordt het **id-veld** niet weergegeven en in een complexere indeling worden extra velden in het display opgegeven. Deze code bevindt zich in het **bestand row_list_to_do.xml.**

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="horizontal">
    <CheckBox
        android:id="@+id/checkToDoItem"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/checkbox_text" />
</LinearLayout>
```

#### <a name="define-the-adapter"></a><a name="adapter"></a>De adapter definiëren
Aangezien de gegevensbron van onze weergave een array van **ToDoItem**is, combineren we onze adapter van een **&lt;ArrayAdapter&gt; ToDoItem-klasse.** Deze subklasse produceert een weergave voor elke **ToDoItem** met behulp van de **row_list_to_do-indeling.**  In onze code definiëren we de volgende klasse die een uitbreiding is van de klasse **&lt;ArrayAdapter E:&gt; **

```java
public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {
}
```

Overschrijf de **getView-methode voor** adapters. Bijvoorbeeld:

```java
    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        View row = convertView;

        final ToDoItem currentItem = getItem(position);

        if (row == null) {
            LayoutInflater inflater = ((Activity) mContext).getLayoutInflater();
            row = inflater.inflate(R.layout.row_list_to_do, parent, false);
        }
        row.setTag(currentItem);

        final CheckBox checkBox = (CheckBox) row.findViewById(R.id.checkToDoItem);
        checkBox.setText(currentItem.getText());
        checkBox.setChecked(false);
        checkBox.setEnabled(true);

        checkBox.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View arg0) {
                if (checkBox.isChecked()) {
                    checkBox.setEnabled(false);
                    if (mContext instanceof ToDoActivity) {
                        ToDoActivity activity = (ToDoActivity) mContext;
                        activity.checkItem(currentItem);
                    }
                }
            }
        });
        return row;
    }
```

We maken een instantie van deze klasse in onze activiteit als volgt:

```java
    ToDoItemAdapter mAdapter;
    mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
```

De tweede parameter voor de ToDoItemAdapter-constructor is een verwijzing naar de lay-out. We kunnen nu de **ListView instantiëren** en de adapter toewijzen aan de **ListView**.

```java
    ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
    listViewToDo.setAdapter(mAdapter);
```

#### <a name="use-the-adapter-to-bind-to-the-ui"></a><a name="use-adapter"></a>De adapter gebruiken om aan de gebruikersinterface te binden

U bent nu klaar om gegevensbinding te gebruiken. In de volgende code ziet u hoe u items in de tabel krijgt en de lokale adapter vult met de geretourneerde items.

```java
    public void showAll(View view) {
        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final List<ToDoItem> results = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : results) {
                                mAdapter.add(item);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        };
        runAsyncTask(task);
    }
```

Bel de adapter wanneer u de **tabel ToDoItem** wijzigt. Aangezien wijzigingen worden uitgevoerd op een record op recordbasis, behandelt u een enkele rij in plaats van een verzameling. Wanneer u een item invoegt, roept u de **methode toevoegen** aan op de adapter. roep bij het verwijderen de **methode verwijderen** aan.

U vindt een compleet voorbeeld in het [Android Quickstart Project.][21]

## <a name="insert-data-into-the-backend"></a><a name="inserting"></a>Gegevens invoegen in de backend

Instantiate een instantie van de klasse *ToDoItem* en stel de eigenschappen ervan in.

```java
ToDoItem item = new ToDoItem();
item.text = "Test Program";
item.complete = false;
```

Gebruik vervolgens **invoegen()** om een object in te voegen:

```java
ToDoItem entity = mToDoTable
    .insert(item)       // Returns a ListenableFuture<ToDoItem>
    .get();
```

De geretourneerde entiteit komt overeen met de gegevens die in de `createdAt`backendtabel zijn ingevoegd, inclusief de id en alle andere waarden (zoals de , `updatedAt`en `version` velden) die op de backend zijn ingesteld.

Voor tabellen voor mobiele apps is een primaire sleutelkolom met de naam **id**vereist. Deze kolom moet een tekenreeks zijn. De standaardwaarde van de kolom ID is een GUID.  U andere unieke waarden opgeven, zoals e-mailadressen of gebruikersnamen. Wanneer een tekenreeks-ID-waarde niet is opgegeven voor een ingevoegde record, genereert de backend een nieuwe GUID.

Tekenreeks-ID-waarden bieden de volgende voordelen:

* Id's kunnen worden gegenereerd zonder een retourtje naar de database te maken.
* Records zijn gemakkelijker te samenvoegen vanuit verschillende tabellen of databases.
* ID-waarden integreren beter met de logica van een toepassing.

String ID-waarden zijn **vereist** voor offline synchronisatieondersteuning.  U een id niet wijzigen nadat deze is opgeslagen in de backenddatabase.

## <a name="update-data-in-a-mobile-app"></a><a name="updating"></a>Gegevens bijwerken in een mobiele app

Als u gegevens in een tabel wilt bijwerken, geeft u het nieuwe object door aan de methode **update().**

```java
mToDoTable
    .update(item)   // Returns a ListenableFuture<ToDoItem>
    .get();
```

In dit voorbeeld is *item* een verwijzing naar een rij in de *ToDoItem-tabel,* waarin enkele wijzigingen zijn aangebracht.  De rij met dezelfde **id** wordt bijgewerkt.

## <a name="delete-data-in-a-mobile-app"></a><a name="deleting"></a>Gegevens in een mobiele app verwijderen

In de volgende code ziet u hoe u gegevens uit een tabel verwijdert door het gegevensobject op te geven.

```java
mToDoTable
    .delete(item);
```

U een item ook verwijderen door het **id-veld** van de rij op te geven dat moet worden verwijderd.

```java
String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
mToDoTable
    .delete(myRowId);
```

## <a name="look-up-a-specific-item-by-id"></a><a name="lookup"></a>Een specifiek item opzoeken op id

Zoek een item op met een specifiek **id-veld** met de methode **lookUp():**

```java
ToDoItem result = mToDoTable
    .lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
    .get();
```

## <a name="how-to-work-with-untyped-data"></a><a name="untyped"></a>Hoe: Werken met niet-getypte gegevens

Het ongetypte programmeermodel geeft u exacte controle over JSON serialisatie.  Er zijn enkele veelvoorkomende scenario's waarin u een ongetypt programmeermodel wilt gebruiken. Als uw backendtabel bijvoorbeeld veel kolommen bevat en u alleen hoeft te verwijzen naar een subset van de kolommen.  Het getypte model vereist dat u alle kolommen definieert die zijn gedefinieerd in de backend van Mobiele apps in uw gegevensklasse.  De meeste API-aanroepen voor toegang tot gegevens zijn vergelijkbaar met de getypte programmeeroproepen. Het belangrijkste verschil is dat u in het niet-getypte model methoden aanroept op het object **MobileServiceJsonTable,** in plaats van het object **MobileServiceTable.**

### <a name="create-an-instance-of-an-untyped-table"></a><a name="json_instance"></a>Een instantie van een niet-getypte tabel maken

Net als bij het getypte model, begint u met het krijgen van een tabelverwijzing, maar in dit geval is het een **MobileServicesJsonTable-object.** Behaal de referentie door de **getTable-methode** aan te roepen voor een instantie van de client:

```java
private MobileServiceJsonTable mJsonToDoTable;
//...
mJsonToDoTable = mClient.getTable("ToDoItem");
```

Zodra u een exemplaar van de **MobileServiceJsonTable**hebt gemaakt, heeft deze vrijwel dezelfde API beschikbaar als bij het getypte programmeermodel. In sommige gevallen nemen de methoden een niet-getypte parameter in plaats van een getypte parameter.

### <a name="insert-into-an-untyped-table"></a><a name="json_insert"></a>Invoegen in een niet-getypte tabel
In de volgende code ziet u hoe u een invoeging doen. De eerste stap is het maken van een [JsonObject][1], dat deel uitmaakt van de [gson-bibliotheek.][3]

```java
JsonObject jsonItem = new JsonObject();
jsonItem.addProperty("text", "Wake up");
jsonItem.addProperty("complete", false);
```

Gebruik vervolgens **invoegen()** om het niet-getypte object in de tabel in te voegen.

```java
JsonObject insertedItem = mJsonToDoTable
    .insert(jsonItem)
    .get();
```

Als u de id van het ingevoegde object wilt hebben, gebruikt u de methode **getAsJsonPrimitive().**

```java
String id = insertedItem.getAsJsonPrimitive("id").getAsString();
```
### <a name="delete-from-an-untyped-table"></a><a name="json_delete"></a>Verwijderen uit een niet-getypte tabel
In de volgende code ziet u hoe u een instantie verwijdert, in dit geval hetzelfde exemplaar van een **JsonObject** dat is gemaakt in het eerdere *voorbeeld van invoeging.* De code is hetzelfde als bij de getypte case, maar de methode heeft een andere handtekening omdat deze verwijst naar een **JsonObject.**

```java
mToDoTable
    .delete(insertedItem);
```

U een instantie ook rechtstreeks verwijderen met de id:

```java
mToDoTable.delete(ID);
```

### <a name="return-all-rows-from-an-untyped-table"></a><a name="json_get"></a>Alle rijen uit een niet-getypte tabel retourneren
In de volgende code ziet u hoe u een hele tabel ophaalt. Aangezien u een JSON-tabel gebruikt, u selectief slechts enkele kolommen van de tabel ophalen.

```java
public void showAllUntyped(View view) {
    new AsyncTask<Void, Void, Void>() {
        @Override
        protected Void doInBackground(Void... params) {
            try {
                final JsonElement result = mJsonToDoTable.execute().get();
                final JsonArray results = result.getAsJsonArray();
                runOnUiThread(new Runnable() {

                    @Override
                    public void run() {
                        mAdapter.clear();
                        for (JsonElement item : results) {
                            String ID = item.getAsJsonObject().getAsJsonPrimitive("id").getAsString();
                            String mText = item.getAsJsonObject().getAsJsonPrimitive("text").getAsString();
                            Boolean mComplete = item.getAsJsonObject().getAsJsonPrimitive("complete").getAsBoolean();
                            ToDoItem mToDoItem = new ToDoItem();
                            mToDoItem.setId(ID);
                            mToDoItem.setText(mText);
                            mToDoItem.setComplete(mComplete);
                            mAdapter.add(mToDoItem);
                        }
                    }
                });
            } catch (Exception exception) {
                createAndShowDialog(exception, "Error");
            }
            return null;
        }
    }.execute();
}
```

Dezelfde set filter-, filter- en pagingmethoden die beschikbaar zijn voor het getypte model zijn beschikbaar voor het niet-getypte model.

## <a name="implement-offline-sync"></a><a name="offline-sync"></a>Offlinesynchronisatie implementeren

De Azure Mobile Apps Client SDK implementeert ook offline synchronisatie van gegevens met behulp van een SQLite-database om een kopie van de servergegevens lokaal op te slaan.  Bewerkingen die worden uitgevoerd op een offlinetabel vereisen geen mobiele connectiviteit om te werken.  Offline synchronisatie helpt bij veerkracht en prestaties ten koste van complexere logica voor conflictoplossing.  De Azure Mobile Apps Client SDK implementeert de volgende functies:

* Incrementele synchronisatie: Alleen bijgewerkte en nieuwe records worden gedownload, waardoor bandbreedte en geheugenverbruik worden bespaard.
* Optimistische gelijktijdigheid: Operaties worden verondersteld te slagen.  Conflictoplossing wordt uitgesteld totdat updates op de server worden uitgevoerd.
* Conflictoplossing: De SDK detecteert wanneer er een conflicterende wijziging op de server is aangebracht en biedt haken om de gebruiker te waarschuwen.
* Soft Delete: Verwijderde records zijn gemarkeerd als verwijderd, zodat andere apparaten hun offline cache kunnen bijwerken.

### <a name="initialize-offline-sync"></a>Offlinesynchronisatie initialiseren

Elke offline tabel moet worden gedefinieerd in de offline cache voor gebruik.  Normaal gesproken wordt tabeldefinitie onmiddellijk na de creatie van de client uitgevoerd:

```java
AsyncTask<Void, Void, Void> initializeStore(MobileServiceClient mClient)
    throws MobileServiceLocalStoreException, ExecutionException, InterruptedException
{
    AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>() {
        @Override
        protected void doInBackground(Void... params) {
            try {
                MobileServiceSyncContext syncContext = mClient.getSyncContext();
                if (syncContext.isInitialized()) {
                    return null;
                }
                SQLiteLocalStore localStore = new SQLiteLocalStore(mClient.getContext(), "offlineStore", null, 1);

                // Create a table definition.  As a best practice, store this with the model definition and return it via
                // a static method
                Map<String, ColumnDataType> toDoItemDefinition = new HashMap<String, ColumnDataType>();
                toDoItemDefinition.put("id", ColumnDataType.String);
                toDoItemDefinition.put("complete", ColumnDataType.Boolean);
                toDoItemDefinition.put("text", ColumnDataType.String);
                toDoItemDefinition.put("version", ColumnDataType.String);
                toDoItemDefinition.put("updatedAt", ColumnDataType.DateTimeOffset);

                // Now define the table in the local store
                localStore.defineTable("ToDoItem", toDoItemDefinition);

                // Specify a sync handler for conflict resolution
                SimpleSyncHandler handler = new SimpleSyncHandler();

                // Initialize the local store
                syncContext.initialize(localStore, handler).get();
            } catch (final Exception e) {
                createAndShowDialogFromTask(e, "Error");
            }
            return null;
        }
    };
    return runAsyncTask(task);
}
```

### <a name="obtain-a-reference-to-the-offline-cache-table"></a>Een verwijzing naar de offline cachetabel verkrijgen

Voor een online tafel `.getTable()`gebruikt u .  Gebruik voor `.getSyncTable()`een offline tabel :

```java
MobileServiceSyncTable<ToDoItem> mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
```

Alle methoden die beschikbaar zijn voor online tabellen (inclusief filteren, sorteren, paging, gegevens invoegen, gegevens bijwerken en gegevens verwijderen) werken even goed op online en offline tabellen.

### <a name="synchronize-the-local-offline-cache"></a>De lokale offlinecache synchroniseren

Synchronisatie bevindt zich binnen de controle van uw app.  Hier is een voorbeeldsynchronisatiemethode:

```java
private AsyncTask<Void, Void, Void> sync(MobileServiceClient mClient) {
    AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
        @Override
        protected Void doInBackground(Void... params) {
            try {
                MobileServiceSyncContext syncContext = mClient.getSyncContext();
                syncContext.push().get();
                mToDoTable.pull(null, "todoitem").get();
            } catch (final Exception e) {
                createAndShowDialogFromTask(e, "Error");
            }
            return null;
        }
    };
    return runAsyncTask(task);
}
```

Als de `.pull(query, queryname)` methode een querynaam wordt opgegeven, wordt incrementele synchronisatie gebruikt om alleen records terug te sturen die zijn gemaakt of gewijzigd sinds de laatste voltooide pull.

### <a name="handle-conflicts-during-offline-synchronization"></a>Conflicten afhandelen tijdens offlinesynchronisatie

Als er een `.push()` conflict optreedt `MobileServiceConflictException` tijdens een bewerking, wordt een   Het door de server uitgegeven item is ingebed in `.getItem()` de uitzondering en kan worden opgehaald door op de uitzondering.  Pas de push aan door de volgende items aan te roepen op het object MobileServiceSyncContext:

*  `.cancelAndDiscardItem()`
*  `.cancelAndUpdateItem()`
*  `.updateOperationAndItem()`

Zodra alle conflicten zijn gemarkeerd `.push()` zoals u wilt, belt u opnieuw om alle conflicten op te lossen.

## <a name="call-a-custom-api"></a><a name="custom-api"></a>Een aangepaste API aanroepen

Met een aangepaste API u aangepaste eindpunten definiëren die serverfunctionaliteit blootstellen die niet wordt toegewezen aan een bewerking voor invoegen, bijwerken, verwijderen of lezen. Door een aangepaste API te gebruiken, u meer controle hebben over berichten, waaronder het lezen en instellen van HTTP-berichtkoppen en het definiëren van een andere berichthoofdindeling dan JSON.

Vanuit een Android-client roept u de **invokeApi-methode** aan om het aangepaste API-eindpunt aan te roepen. In het volgende voorbeeld ziet u hoe u een API-eindpunt met de naam **completeAll**aanroept, waarmee een verzamelingsklasse met de naam **MarkAllResult**wordt geretourneerd .

```java
public void completeItem(View view) {
    ListenableFuture<MarkAllResult> result = mClient.invokeApi("completeAll", MarkAllResult.class);
    Futures.addCallback(result, new FutureCallback<MarkAllResult>() {
        @Override
        public void onFailure(Throwable exc) {
            createAndShowDialog((Exception) exc, "Error");
        }

        @Override
        public void onSuccess(MarkAllResult result) {
            createAndShowDialog(result.getCount() + " item(s) marked as complete.", "Completed Items");
            refreshItemsFromTable();
        }
    });
}
```

De **invokeApi-methode** wordt aangeroepen op de client, die een POST-aanvraag naar de nieuwe aangepaste API verzendt. Het resultaat dat wordt geretourneerd door de aangepaste API wordt weergegeven in een berichtdialoogvenster, evenals eventuele fouten. In andere versies van **invokeApi** u optioneel een object verzenden in de aanvraaginstantie, de HTTP-methode opgeven en queryparameters met de aanvraag verzenden. Niet-getypte versies van **invokeApi** zijn ook aanwezig.

## <a name="add-authentication-to-your-app"></a><a name="authentication"></a>Verificatie toevoegen aan uw app

Tutorials beschrijven al in detail hoe u deze functies toevoegen.

App Service ondersteunt [het authenticeren van app-gebruikers](app-service-mobile-android-get-started-users.md) met behulp van verschillende externe identiteitsproviders: Facebook, Google, Microsoft-account, Twitter en Azure Active Directory. U machtigingen voor tabellen instellen om de toegang voor specifieke bewerkingen te beperken tot alleen geverifieerde gebruikers. U ook de identiteit van geverifieerde gebruikers gebruiken om autorisatieregels in uw backend te implementeren.

Twee verificatiestromen worden ondersteund: een **serverstroom** en een **clientstroom.** De serverstroom biedt de eenvoudigste verificatie-ervaring, omdat deze is gebaseerd op de webinterface van identiteitsproviders.  Er zijn geen extra SDK's nodig om serverflow-verificatie te implementeren. Serverflowauthenticatie biedt geen diepe integratie in het mobiele apparaat en wordt alleen aanbevolen voor proof of concept-scenario's.

De clientflow zorgt voor een diepere integratie met apparaatspecifieke mogelijkheden, zoals eenmalige aanmelding, omdat deze afhankelijk is van SDK's die door de identiteitsprovider worden geleverd.  U de Facebook SDK bijvoorbeeld integreren in uw mobiele applicatie.  De mobiele client wisselt in de Facebook-app en bevestigt je aanmelding voordat je terugkeert naar je mobiele app.

Er zijn vier stappen nodig om verificatie in uw app in te schakelen:

* Registreer uw app voor verificatie bij een identiteitsprovider.
* Configureer de backend van de appservice.
* Tabelmachtigingen beperken tot geverifieerde gebruikers alleen op de backend van de app-service.
* Voeg verificatiecode toe aan uw app.

U machtigingen voor tabellen instellen om de toegang voor specifieke bewerkingen te beperken tot alleen geverifieerde gebruikers. U ook de SID van een geverifieerde gebruiker gebruiken om aanvragen te wijzigen.  Bekijk aan de [slag met verificatie] en de Server SDK HOWTO-documentatie voor meer informatie.

### <a name="authentication-server-flow"></a><a name="caching"></a>Verificatie: serverstroom

Met de volgende code wordt een inlogproces voor serverstromen gestart met de Google-provider.  Aanvullende configuratie is vereist vanwege de beveiligingsvereisten voor de Google-provider:

```java
MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
```

Voeg bovendien de volgende methode toe aan de hoofdklasse Activiteit:

```java
// You can choose any unique number here to differentiate auth providers from each other. Note this is the same code at login() and onActivityResult().
public static final int GOOGLE_LOGIN_REQUEST_CODE = 1;

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    // When request completes
    if (resultCode == RESULT_OK) {
        // Check the request code matches the one we send in the login request
        if (requestCode == GOOGLE_LOGIN_REQUEST_CODE) {
            MobileServiceActivityResult result = mClient.onActivityResult(data);
            if (result.isLoggedIn()) {
                // login succeeded
                createAndShowDialog(String.format("You are now logged in - %1$2s", mClient.getCurrentUser().getUserId()), "Success");
                createTable();
            } else {
                // login failed, check the error message
                String errorMessage = result.getErrorMessage();
                createAndShowDialog(errorMessage, "Error");
            }
        }
    }
}
```

De `GOOGLE_LOGIN_REQUEST_CODE` gedefinieerde in uw hoofdactiviteit `login()` wordt gebruikt `onActivityResult()` voor de methode en binnen de methode.  U een uniek nummer kiezen, zolang hetzelfde `login()` nummer `onActivityResult()` wordt gebruikt binnen de methode en de methode.  Als u de clientcode abstraheert naar een serviceadapter (zoals eerder is weergegeven), moet u de juiste methoden op de serviceadapter aanroepen.

U moet het project ook configureren voor aangepaste tabbladen.  Geef eerst een omleiding-URL op.  Voeg het volgende `AndroidManifest.xml`fragment toe aan:

```xml
<activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback"/>
    </intent-filter>
</activity>
```

Voeg de **redirectUriScheme** toe aan het `build.gradle` bestand voor uw toepassing:

```gradle
android {
    buildTypes {
        release {
            // … …
            manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
        }
        debug {
            // … …
            manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
        }
    }
}
```

Voeg ten `com.android.support:customtabs:28.0.0` slotte toe aan `build.gradle` de lijst met afhankelijkheden in het bestand:

```gradle
dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])
    implementation 'com.google.code.gson:gson:2.3'
    implementation 'com.google.guava:guava:18.0'
    implementation 'com.android.support:customtabs:28.0.0'
    implementation 'com.squareup.okhttp:okhttp:2.5.0'
    implementation 'com.microsoft.azure:azure-mobile-android:3.4.0@aar'
    implementation 'com.microsoft.azure:azure-notifications-handler:1.0.1@jar'
}
```

Verkrijg de ID van de ingelogde gebruiker van een **MobileServiceUser** met behulp van de **getUserId-methode.** Zie Aan de [slag met verificatie]voor een voorbeeld van het gebruik van Futures om de asynchrone inlog-API's aan te roepen.

> [!WARNING]
> Het genoemde URL-schema is hoofdlettergevoelig.  Zorg ervoor dat `{url_scheme_of_you_app}` alle gebeurtenissen van de wedstrijd geval.

### <a name="cache-authentication-tokens"></a><a name="caching"></a>Verificatietokens voor cache

Voor caching-verificatietokens moet u de gebruikersnaam en het verificatietoken lokaal op het apparaat opslaan. De volgende keer dat de app wordt gestart, controleert u de cache en als deze waarden aanwezig zijn, u de inlogprocedure overslaan en de client met deze gegevens rehydrateren. Maar deze gegevens is gevoelig, en het moet worden opgeslagen versleuteld voor de veiligheid in het geval de telefoon wordt gestolen.  U een volledig voorbeeld zien van het cache-tokens in [de sectie Cache-verificatietokens.][7]

Wanneer u een verlopen token probeert te gebruiken, ontvangt u een ongeautoriseerde reactie *van 401.* U verificatiefouten verwerken met behulp van filters.  Filters onderscheppen aanvragen naar de backend van de appservice. De filtercode test het antwoord op een 401, activeert het aanmeldingsproces en hervat vervolgens het verzoek dat de 401 heeft gegenereerd.

### <a name="use-refresh-tokens"></a><a name="refresh"></a>Tokens vernieuwen gebruiken

Het token dat wordt geretourneerd door Azure App Service Authentication and Authorization heeft een gedefinieerde levensduur van een uur.  Na deze periode moet u de gebruiker opnieuw verifiëren.  Als u een langjarig token gebruikt dat u hebt ontvangen via client-flow-verificatie, u opnieuw verifiëren met Azure App Service-verificatie en autorisatie met dezelfde token.  Een ander Azure App Service-token wordt gegenereerd met een nieuwe levensduur.

U de provider ook registreren om Vernieuwingstokens te gebruiken.  Een Vernieuwingstoken is niet altijd beschikbaar.  Aanvullende configuratie is vereist:

* Configureer voor **Azure Active Directory**een clientgeheim voor de Azure Active Directory App.  Geef het clientgeheim op in de Azure App-service bij het configureren van Azure Active Directory Authentication.  Bij `.login()`het `response_type=code id_token` aanroepen, pass als parameter:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("response_type", "code id_token");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.AzureActiveDirectory,
        "{url_scheme_of_your_app}",
        AAD_LOGIN_REQUEST_CODE,
        parameters);
    ```

* Geef **Google**voor Google `access_type=offline` de as-parameter door:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("access_type", "offline");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.Google,
        "{url_scheme_of_your_app}",
        GOOGLE_LOGIN_REQUEST_CODE,
        parameters);
    ```

* Selecteer voor **Microsoft-account**het `wl.offline_access` bereik.

Als u een `.refreshUser()`token wilt vernieuwen, roept u:

```java
MobileServiceUser user = mClient
    .refreshUser()  // async - returns a ListenableFuture<MobileServiceUser>
    .get();
```

Maak als best practice een filter dat een 401-respons van de server detecteert en probeert het gebruikerstoken te vernieuwen.

## <a name="log-in-with-client-flow-authentication"></a>Inloggen met client-flow-verificatie

Het algemene proces voor het inloggen met client-flow authenticatie is als volgt:

* Configureer Azure App Service Authentication and Authorization zoals u server-flow-verificatie zou doen.
* Integreer de verificatieprovider SDK voor verificatie om een toegangstoken te produceren.
* Roep `.login()` de methode`result` als volgt `AuthenticationResult`aan ( moet een):

    ```java
    JSONObject payload = new JSONObject();
    payload.put("access_token", result.getAccessToken());
    ListenableFuture<MobileServiceUser> mLogin = mClient.login("{provider}", payload.toString());
    Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
        @Override
        public void onFailure(Throwable exc) {
            exc.printStackTrace();
        }
        @Override
        public void onSuccess(MobileServiceUser user) {
            Log.d(TAG, "Login Complete");
        }
    });
    ```

Zie het volledige codevoorbeeld in de volgende sectie.

Vervang `onSuccess()` de methode door welke code u wilt gebruiken bij een succesvolle login.  De `{provider}` tekenreeks is een geldige provider: **aad** (Azure Active Directory), **facebook,** **google,** **microsoftaccount**of **twitter**.  Als u aangepaste verificatie hebt geïmplementeerd, u ook de aangepaste tag van de verificatieprovider gebruiken.

### <a name="authenticate-users-with-the-active-directory-authentication-library-adal"></a><a name="adal"></a>Gebruikers verifiëren met de Active Directory Authentication Library (ADAL)

U de Active Directory Authentication Library (ADAL) gebruiken om gebruikers aan te melden bij uw toepassing met Azure Active Directory. Met behulp van een client flow `loginAsync()` login is vaak de voorkeur aan het gebruik van de methoden als het biedt een meer native UX-gevoel en zorgt voor extra maatwerk.

1. Configureer de backend van uw mobiele app voor AAD-aanmelding door de zelfstudie [App-service voor Active Directory-aanmelding te configureren.][22] Zorg ervoor dat u de optionele stap van het registreren van een native clienttoepassing voltooit.
2. Installeer ADAL door uw build.gradle-bestand te wijzigen met de volgende definities:

    ```gradle
    repositories {
        mavenCentral()
        flatDir {
            dirs 'libs'
        }
        maven {
            url "YourLocalMavenRepoPath\\.m2\\repository"
        }
    }
    packagingOptions {
        exclude 'META-INF/MSFTSIG.RSA'
        exclude 'META-INF/MSFTSIG.SF'
    }
    dependencies {
        implementation fileTree(dir: 'libs', include: ['*.jar'])
        implementation('com.microsoft.aad:adal:1.16.1') {
            exclude group: 'com.android.support'
        } // Recent version is 1.16.1
        implementation 'com.android.support:support-v4:28.0.0'
    }
    ```

3. Voeg de volgende code toe aan uw toepassing en maak de volgende vervangingen:

    * Vervang **INSERT-AUTHORITY-HERE** door de naam van de tenant waarin u uw aanvraag heeft ingericht. Het formaat https://login.microsoftonline.com/contoso.onmicrosoft.commoet zijn .
    * Vervang **INSERT-RESOURCE-ID-HERE** door de client-ID voor uw mobiele app back-end. U de client-id verkrijgen via het tabblad **Geavanceerd** onder **Azure Active Directory-instellingen** in de portal.
    * Vervang **INSERT-CLIENT-ID-HERE** door de client-id die u hebt gekopieerd van de native clienttoepassing.
    * Vervang **INSERT-REDIRECT-URI-HERE** door het *eindpunt /.auth/login/done van* uw site, met behulp van het HTTPS-schema. Deze waarde moet *https://contoso.azurewebsites.net/.auth/login/done*vergelijkbaar zijn met .

```java
private AuthenticationContext mContext;

private void authenticate() {
    String authority = "INSERT-AUTHORITY-HERE";
    String resourceId = "INSERT-RESOURCE-ID-HERE";
    String clientId = "INSERT-CLIENT-ID-HERE";
    String redirectUri = "INSERT-REDIRECT-URI-HERE";
    try {
        mContext = new AuthenticationContext(this, authority, true);
        mContext.acquireToken(this, resourceId, clientId, redirectUri, PromptBehavior.Auto, "", callback);
    } catch (Exception exc) {
        exc.printStackTrace();
    }
}

private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {
    @Override
    public void onError(Exception exc) {
        if (exc instanceof AuthenticationException) {
            Log.d(TAG, "Cancelled");
        } else {
            Log.d(TAG, "Authentication error:" + exc.getMessage());
        }
    }

    @Override
    public void onSuccess(AuthenticationResult result) {
        if (result == null || result.getAccessToken() == null
                || result.getAccessToken().isEmpty()) {
            Log.d(TAG, "Token is empty");
        } else {
            try {
                JSONObject payload = new JSONObject();
                payload.put("access_token", result.getAccessToken());
                ListenableFuture<MobileServiceUser> mLogin = mClient.login("aad", payload.toString());
                Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                    @Override
                    public void onFailure(Throwable exc) {
                        exc.printStackTrace();
                    }
                    @Override
                    public void onSuccess(MobileServiceUser user) {
                        Log.d(TAG, "Login Complete");
                    }
                });
            }
            catch (Exception exc){
                Log.d(TAG, "Authentication error:" + exc.getMessage());
            }
        }
    }
};

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);
    if (mContext != null) {
        mContext.onActivityResult(requestCode, resultCode, data);
    }
}
```

## <a name="adjust-the-client-server-communication"></a><a name="filters"></a>De clientservercommunicatie aanpassen

De clientverbinding is normaal gesproken een basis-HTTP-verbinding met behulp van de onderliggende HTTP-bibliotheek die wordt geleverd met de Android SDK.  Er zijn verschillende redenen waarom u dat zou willen wijzigen:

* U wilt een alternatieve HTTP-bibliotheek gebruiken om time-outs aan te passen.
* U wilt een voortgangsbalk bieden.
* U wilt een aangepaste header toevoegen om api-beheerfunctionaliteit te ondersteunen.
* U wilt een mislukte reactie onderscheppen, zodat u herverificatie implementeren.
* U wilt backendaanvragen aanmelden bij een analyseservice.

### <a name="using-an-alternate-http-library"></a>Een alternatieve HTTP-bibliotheek gebruiken

Bel `.setAndroidHttpClientFactory()` de methode onmiddellijk na het maken van uw clientreferentie.  Bijvoorbeeld om de time-out van de verbinding in te stellen op 60 seconden (in plaats van de standaard 10 seconden):

```java
mClient = new MobileServiceClient("https://myappname.azurewebsites.net");
mClient.setAndroidHttpClientFactory(new OkHttpClientFactory() {
    @Override
    public OkHttpClient createOkHttpClient() {
        OkHttpClient client = new OkHttpClient();
        client.setReadTimeout(60, TimeUnit.SECONDS);
        client.setWriteTimeout(60, TimeUnit.SECONDS);
        return client;
    }
});
```

### <a name="implement-a-progress-filter"></a>Een voortgangsfilter implementeren

U een onderschepping van elk `ServiceFilter`verzoek implementeren door een.  De volgende updates worden bijvoorbeeld bijgewerkt op een vooraf gemaakte voortgangsbalk:

```java
private class ProgressFilter implements ServiceFilter {
    @Override
    public ListenableFuture<ServiceFilterResponse> handleRequest(ServiceFilterRequest request, NextServiceFilterCallback next) {
        final SettableFuture<ServiceFilterResponse> resultFuture = SettableFuture.create();
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                if (mProgressBar != null) mProgressBar.setVisibility(ProgressBar.VISIBLE);
            }
        });

        ListenableFuture<ServiceFilterResponse> future = next.onNext(request);
        Futures.addCallback(future, new FutureCallback<ServiceFilterResponse>() {
            @Override
            public void onFailure(Throwable e) {
                resultFuture.setException(e);
            }
            @Override
            public void onSuccess(ServiceFilterResponse response) {
                runOnUiThread(new Runnable() {
                    @Override
                    public void run() {
                        if (mProgressBar != null)
                            mProgressBar.setVisibility(ProgressBar.GONE);
                    }
                });
                resultFuture.set(response);
            }
        });
        return resultFuture;
    }
}
```

U dit filter als volgt aan de client koppelen:

```java
mClient = new MobileServiceClient(applicationUrl).withFilter(new ProgressFilter());
```

### <a name="customize-request-headers"></a>Aanvraagkoppen aanpassen

Gebruik het `ServiceFilter` volgende en bevestig het filter `ProgressFilter`op dezelfde manier als de :

```java
private class CustomHeaderFilter implements ServiceFilter {
    @Override
    public ListenableFuture<ServiceFilterResponse> handleRequest(ServiceFilterRequest request, NextServiceFilterCallback next) {
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                request.addHeader("X-APIM-Router", "mobileBackend");
            }
        });
        SettableFuture<ServiceFilterResponse> result = SettableFuture.create();
        try {
            ServiceFilterResponse response = next.onNext(request).get();
            result.set(response);
        } catch (Exception exc) {
            result.setException(exc);
        }
    }
}
```

### <a name="configure-automatic-serialization"></a><a name="conversions"></a>Automatische serialisatie configureren

U een conversiestrategie opgeven die op elke kolom van toepassing is met behulp van de [gson-API.][3] De Android-clientbibliotheek gebruikt [gson][3] achter de schermen om Java-objecten te serialiseren voor JSON-gegevens voordat de gegevens naar Azure App Service worden verzonden.  De volgende code gebruikt de methode **setFieldNamingStrategy()** om de strategie in te stellen. In dit voorbeeld wordt het oorspronkelijke teken (een 'm') verwijderd en vervolgens het volgende teken voor elke veldnaam verwijderd. Het zou bijvoorbeeld 'mId' omzetten in 'id'.  Implementeer een conversiestrategie om `SerializedName()` de behoefte aan annotaties op de meeste velden te verminderen.

```java
FieldNamingStrategy namingStrategy = new FieldNamingStrategy() {
    public String translateName(File field) {
        String name = field.getName();
        return Character.toLowerCase(name.charAt(1)) + name.substring(2);
    }
}

client.setGsonBuilder(
    MobileServiceClient
        .createMobileServiceGsonBuilder()
        .setFieldNamingStrategy(namingStrategy)
);
```

Deze code moet worden uitgevoerd voordat u een verwijzing naar de mobiele client maakt met behulp van de **MobileServiceClient.**

<!-- URLs. -->
[Get started with Azure Mobile Apps]: app-service-mobile-android-get-started.md
[ASCII control codes C0 and C1]: https://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Mobile Services SDK for Android]: https://go.microsoft.com/fwlink/p/?LinkID=717033
[Azure portal]: https://portal.azure.com
[Aan de slag met verificatie]: app-service-mobile-android-get-started-users.md
[1]: https://static.javadoc.io/com.google.code.gson/gson/2.8.5/com/google/gson/JsonObject.html
[2]: https://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson
[3]: https://www.javadoc.io/doc/com.google.code.gson/gson/2.8.5
[4]: https://go.microsoft.com/fwlink/p/?LinkId=296840
[5]: app-service-mobile-android-get-started-push.md
[6]: ../notification-hubs/notification-hubs-push-notification-overview.md#integration-with-app-service-mobile-apps
[7]: app-service-mobile-android-get-started-users.md#cache-tokens
[8]: https://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/table/MobileServiceTable.html
[9]: https://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html
[10]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[11]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[12]: https://azure.github.io/azure-mobile-apps-android-client/
[13]: app-service-mobile-android-get-started.md#create-a-new-azure-mobile-app-backend
[14]: https://go.microsoft.com/fwlink/p/?LinkID=717034
[15]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[17]: https://developer.android.com/reference/java/util/UUID.html
[18]: https://github.com/google/guava/wiki/ListenableFutureExplained
[19]: https://www.odata.org/documentation/odata-version-3-0/
[20]: https://hashtagfail.com/post/46493261719/mobile-services-android-querying
[21]: https://github.com/Azure-Samples/azure-mobile-apps-android-quickstart
[22]: ../app-service/configure-authentication-provider-aad.md
[Future]: https://developer.android.com/reference/java/util/concurrent/Future.html
[AsyncTask]: https://developer.android.com/reference/android/os/AsyncTask.html
