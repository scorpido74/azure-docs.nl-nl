---
title: De SDK voor Android gebruiken
description: De Azure Mobile Apps SDK voor Android gebruiken
ms.assetid: 5352d1e4-7685-4a11-aaf4-10bd2fa9f9fc
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 52e91d900ce0f22862904695ba8adf463219c469
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79249384"
---
# <a name="how-to-use-the-azure-mobile-apps-sdk-for-android"></a>De Azure Mobile Apps SDK voor Android gebruiken

In deze hand leiding wordt uitgelegd hoe u de Android-client-SDK voor Mobile Apps kunt gebruiken voor het implementeren van algemene scenario's, zoals:

* Query's uitvoeren voor gegevens (invoegen, bijwerken en verwijderen).
* Verificatie.
* Het afhandelen van fouten.
* De client aanpassen.

Deze hand leiding is gericht op de client-side Android SDK.  Zie [werken met .net back-end-SDK][10] of [de node. js back-SDK gebruiken][11]voor meer informatie over de sdk's aan de server zijde voor Mobile apps.

## <a name="reference-documentation"></a>Referentiedocumentatie

U kunt de [JAVADOCS API-verwijzing][12] voor de Android-client bibliotheek vinden op github.

## <a name="supported-platforms"></a>Ondersteunde platforms

De Azure Mobile Apps SDK voor Android ondersteunt API-niveaus 19 tot 24 (KitKat tot en met Nougat) voor de vorm van factoren voor telefoon en Tablet.  Bij verificatie wordt met name gebruikgemaakt van een gemeen schappelijke webframework-benadering voor het verzamelen van referenties.  Server-flow-verificatie werkt niet met kleine vorm factor-apparaten, zoals Watches.

## <a name="setup-and-prerequisites"></a>Installatie en vereisten

Voltooi de zelf studie over [Mobile apps Snelstartgids](app-service-mobile-android-get-started.md) .  Deze taak zorgt ervoor dat aan alle vereisten voor het ontwikkelen van Azure Mobile Apps is voldaan.  De Quick Start helpt u ook bij het configureren van uw account en het maken van uw eerste back-end voor mobiele apps.

Als u besluit om de Snelstartgids zelf studie niet te volt ooien, voert u de volgende taken uit:

* [Maak een back-end voor een mobiele app][13] die u kunt gebruiken met uw Android-app.
* Werk in Android Studio [de Gradle-build-bestanden](#gradle-build)bij.
* [Internet machtiging inschakelen](#enable-internet).

### <a name="gradle-build"></a>Het build-bestand van Gradle bijwerken

Wijzig beide **Build. gradle** -bestanden:

1. Voeg deze code toe aan het bestand **Build. gradle** van *project* level:

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

2. Voeg deze code toe aan het bestand *module App* level **Build. gradle** binnen de label *dependencies* :

    ```gradle
    implementation 'com.microsoft.azure:azure-mobile-android:3.4.0@aar'
    ```

    Momenteel is de nieuwste versie 3.4.0. De ondersteunde versies worden vermeld [op bintray][14].

### <a name="enable-internet"></a>Internet machtiging inschakelen

Voor toegang tot Azure moet uw app de machtiging INTERNET hebben ingeschakeld. Als deze nog niet is ingeschakeld, voegt u de volgende regel code toe aan het bestand **AndroidManifest. XML** :

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

## <a name="create-a-client-connection"></a>Een client verbinding maken

Azure Mobile Apps biedt vier functies voor uw mobiele toepassing:

* Gegevens toegang en offline synchronisatie met een Azure Mobile Apps-service.
* Aanroepen van aangepaste Api's die zijn geschreven met de Azure Mobile Apps Server SDK.
* Verificatie met Azure App Service verificatie en autorisatie.
* Registratie van push meldingen met Notification Hubs.

Voor elk van deze functies moet u eerst een `MobileServiceClient`-object maken.  Er moet slechts één `MobileServiceClient` object worden gemaakt binnen uw mobiele client (dat wil zeggen, het moet een singleton patroon zijn).  Een `MobileServiceClient`-object maken:

```java
MobileServiceClient mClient = new MobileServiceClient(
    "<MobileAppUrl>",       // Replace with the Site URL
    this);                  // Your application Context
```

De `<MobileAppUrl>` is een teken reeks of een URL-object dat verwijst naar uw mobiele back-end.  Als u Azure App Service gebruikt om uw mobiele back-end te hosten, moet u ervoor zorgen dat u de beveiligde `https://` versie van de URL gebruikt.

De-client vereist ook toegang tot de activiteit of context-de para meter `this` in het voor beeld.  De Mobileserviceclient te maken-constructie moet plaatsvinden in de `onCreate()` methode van de activiteit waarnaar wordt verwezen in het `AndroidManifest.xml` bestand.

Als best practice moet u een abstracte server communicatie in een eigen klasse (Singleton-patroon) aanbrengen.  In dit geval moet u de activiteit binnen de constructor door geven om de service op de juiste wijze te configureren.  Bijvoorbeeld:

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

U kunt `AzureServiceAdapter.Initialize(this);` nu aanroepen in de `onCreate()`-methode van uw hoofd activiteit.  Alle andere methoden die toegang moeten hebben tot de client, gebruiken `AzureServiceAdapter.getInstance();` om een verwijzing naar de service adapter op te halen.

## <a name="data-operations"></a>Gegevens bewerkingen

De kern van de Azure Mobile Apps SDK is het verlenen van toegang tot gegevens die zijn opgeslagen in SQL Azure op de back-end van de mobiele app.  U kunt deze gegevens openen met sterk getypeerde klassen (voor keur) of niet-getypte query's (niet aanbevolen).  Het meren deel van deze sectie behandelt het gebruik van sterk getypeerde klassen.

### <a name="define-client-data-classes"></a>Client gegevens klassen definiëren

Om toegang te krijgen tot gegevens uit SQL Azure tabellen, definieert u client gegevens klassen die overeenkomen met de tabellen in de back-end van de mobiele app. In de voor beelden in dit onderwerp wordt uitgegaan van een tabel met de naam **MyDataTable**, die de volgende kolommen bevat:

* id
* tekst
* aangevuld

Het overeenkomstige getypte client-side object bevindt zich in een bestand met de naam **MyDataTable. java**:

```java
public class ToDoItem {
    private String id;
    private String text;
    private Boolean complete;
}
```

Getter-en setter-methoden toevoegen voor elk veld dat u toevoegt.  Als uw SQL Azure tabel meer kolommen bevat, voegt u de bijbehorende velden toe aan deze klasse.  Als de DTO (object voor gegevens overdracht) bijvoorbeeld een kolom met een gehele waarde bevat, kunt u dit veld toevoegen, samen met de getter-en setter methoden:

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

Zie [procedure: een tabel controller][15] (.net-back-end) definiëren of [tabellen definiëren met behulp van een dynamisch schema][16] (node. js-back-end) voor meer informatie over het maken van extra tabellen in uw Mobile apps back-end.

Een Azure Mobile Apps-back-end-tabel definieert vijf speciale velden, vier van die beschikbaar zijn voor clients:

* `String id`: de wereld wijd unieke ID voor de record.  Als best practice, maakt u de id de teken reeks representatie van een [uuid][17] -object.
* `DateTimeOffset updatedAt`: de datum/tijd van de laatste update.  Het veld updatedAt wordt ingesteld door de server en moet nooit worden ingesteld door de client code.
* `DateTimeOffset createdAt`: de datum/tijd waarop het object is gemaakt.  Het veld createdAt wordt ingesteld door de server en moet nooit worden ingesteld door de client code.
* `byte[] version`: doorgaans weer gegeven als een teken reeks, wordt de versie ook ingesteld door de-server.
* `boolean deleted`: geeft aan dat de record is verwijderd, maar nog niet is leeg gemaakt.  Gebruik `deleted` niet als eigenschap in uw klasse.

Het veld `id` is vereist.  Het veld `updatedAt` en het `version` veld worden gebruikt voor offline synchronisatie (respectievelijk voor incrementele synchronisatie en conflict oplossing).  Het `createdAt` veld is een referentie veld dat niet door de client wordt gebruikt.  De namen zijn ' in-the-Wire ' namen van de eigenschappen en zijn niet aanpasbaar.  U kunt echter een toewijzing maken tussen uw object en de namen van ' in-the-Wire ' met behulp van de [gson][3] -bibliotheek.  Bijvoorbeeld:

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

### <a name="create-a-table-reference"></a>Een tabel verwijzing maken

Als u toegang wilt krijgen tot een tabel, maakt u eerst een [MobileServiceTable][8] -object door de **GetTable** -methode aan te roepen in de [mobileserviceclient te maken][9].  Deze methode heeft twee Overloads:

```java
public class MobileServiceClient {
    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
}
```

In de volgende code is **mClient** een verwijzing naar uw mobileserviceclient te maken-object.  De eerste overbelasting wordt gebruikt, waarbij de naam van de klasse en de tabel naam hetzelfde zijn, en deze wordt gebruikt in de Quick Start:

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);
```

De tweede overbelasting wordt gebruikt wanneer de naam van de tabel verschilt van de naam van de klasse: de eerste para meter is de tabel naam.

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);
```

## <a name="query"></a>Een query uitvoeren op een back-end-tabel

U moet eerst een tabel verwijzing ophalen.  Voer vervolgens een query uit op de tabel verwijzing.  Een query is een combi natie van:

* Een `.where()`- [filter component](#filtering).
* Een `.orderBy()` [order-component](#sorting).
* Een component voor het selecteren van `.select()` [velden](#selection).
* Een `.skip()` en `.top()` voor [gewisselde resultaten](#paging).

De componenten moeten in de voor gaande volg orde worden weer gegeven.

### <a name="filter"></a>Resultaten filteren

De algemene vorm van een query is:

```java
List<MyDataTable> results = mDataTable
    // More filters here
    .execute()          // Returns a ListenableFuture<E>
    .get()              // Converts the async into a sync result
```

In het voor gaande voor beeld worden alle resultaten geretourneerd (tot de maximale pagina grootte die is ingesteld door de server).  De `.execute()`-methode voert de query uit op de back-end.  De query wordt geconverteerd naar een [OData v3][19] -query voordat deze naar de Mobile apps back-end wordt verzonden.  Bij ontvangst converteert de Mobile Apps back-end de query naar een SQL-instructie voordat deze wordt uitgevoerd op het SQL Azure exemplaar.  Omdat netwerk activiteiten enige tijd duren, retourneert de methode `.execute()` een [`ListenableFuture<E>`][18].

### <a name="filtering"></a>Geretourneerde gegevens filteren

De volgende query-uitvoering retourneert alle items uit de **ToDoItem** -tabel waarbij **complete** gelijk is aan **False**.

```java
List<ToDoItem> result = mToDoTable
    .where()
    .field("complete").eq(false)
    .execute()
    .get();
```

**mToDoTable** is de verwijzing naar de tabel met mobiele services die we eerder hebben gemaakt.

Definieer een filter met behulp van de methode Call **where** voor de tabel verwijzing. De methode **where** wordt gevolgd door een **veld** methode gevolgd door een methode die het logische predicaat specificeert. Mogelijke predikaten-methoden zijn **EQ** (gelijk aan), **ne** (niet gelijk aan), **gt** (groter dan), **ge** (groter dan of gelijk aan), **lt** (kleiner dan), **Le** (kleiner dan of gelijk aan). Met deze methoden kunt u nummer-en teken reeks velden vergelijken met specifieke waarden.

U kunt filteren op datums. Met de volgende methoden kunt u het hele datum veld of delen van de datum vergelijken: **jaar**, **maand**, **dag**, **uur**, **minuut**en **seconde**. In het volgende voor beeld wordt een filter toegevoegd voor items waarvan de *verval datum* gelijk is aan 2013.

```java
List<ToDoItem> results = MToDoTable
    .where()
    .year("due").eq(2013)
    .execute()
    .get();
```

De volgende methoden ondersteunen complexe filters voor teken reeks velden: **startsWith**, **endsWith**, **concat**, **subtekenreeks**, **indexOf**, **replace**, **toLower**, **toUpper**, **Trim**en **Length**. De volgende voorbeeld filters voor tabel rijen waarbij de *tekst* kolom begint met ' PRI0 '.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .startsWith("text", "PRI0")
    .execute()
    .get();
```

De volgende operator methoden worden ondersteund voor numerieke velden: **add**, **Sub**, **mul**, **div**, **mod**, **Floor**, **plafond**en **Round**. De volgende voorbeeld filters voor tabel rijen waarbij de **duur** een even getal is.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .field("duration").mod(2).eq(0)
    .execute()
    .get();
```

U kunt predikaten combi neren met deze logische methoden: **en**, **of** en **niet**. In het volgende voor beeld worden twee van de voor gaande voor beelden gecombineerd.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .year("due").eq(2013).and().startsWith("text", "PRI0")
    .execute()
    .get();
```

Logische Opera tors groeperen en nesten:

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

Zie [de mogelijkheden van het Android-client query model verkennen][20]voor meer gedetailleerde informatie over de bespreking en voor beelden van het filteren.

### <a name="sorting"></a>Geretourneerde gegevens sorteren

Met de volgende code worden alle items uit een tabel **ToDoItems** die oplopend zijn gesorteerd op basis van het *tekst* veld. *mToDoTable* is de verwijzing naar de back-end-tabel die u eerder hebt gemaakt:

```java
List<ToDoItem> results = mToDoTable
    .orderBy("text", QueryOrder.Ascending)
    .execute()
    .get();
```

De eerste para meter van de methode **orderBy** is een teken reeks die gelijk is aan de naam van het veld waarop moet worden gesorteerd. De tweede para meter gebruikt de **QueryOrder** -inventarisatie om op te geven of Oplopend of aflopend moet worden gesorteerd.  Als u filtert met behulp van de methode ***where*** , moet de methode ***where*** worden aangeroepen vóór de ***orderBy*** -methode.

### <a name="selection"></a>Specifieke kolommen selecteren

De volgende code laat zien hoe u alle items uit een tabel met **ToDoItems**retourneert, maar alleen de velden **volledig** en **tekst** worden weer gegeven. **mToDoTable** is de verwijzing naar de back-end-tabel die u eerder hebt gemaakt.

```java
List<ToDoItemNarrow> result = mToDoTable
    .select("complete", "text")
    .execute()
    .get();
```

De para meters voor de functie Select zijn de teken reeks namen van de kolommen van de tabel die u wilt retour neren.  De methode **Select** moet methoden volgen, zoals **waar** en **orderBy**. Het kan worden gevolgd door paginerings methoden zoals **Skip** en **Top**.

### <a name="paging"></a>Gegevens in pagina's retour neren

Gegevens worden **altijd** geretourneerd op pagina's.  Het maximum aantal records dat wordt geretourneerd, is ingesteld door de server.  Als de client meer records aanvraagt, retourneert de server het maximum aantal records.  De maximale pagina grootte op de server is standaard 50 records.

In het eerste voor beeld ziet u hoe u de vijf belangrijkste items uit een tabel selecteert. De query retourneert de items uit een tabel met **ToDoItems**. **mToDoTable** is de verwijzing naar de back-end-tabel die u eerder hebt gemaakt:

```java
List<ToDoItem> result = mToDoTable
    .top(5)
    .execute()
    .get();
```

Hier volgt een query voor het overstappen van de eerste vijf items en wordt vervolgens de volgende vijf weer gegeven:

```java
List<ToDoItem> result = mToDoTable
    .skip(5).top(5)
    .execute()
    .get();
```

Als u alle records in een tabel wilt ophalen, implementeert u code om alle pagina's te herhalen:

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

Een aanvraag voor alle records met behulp van deze methode maakt mini maal twee aanvragen voor de Mobile Apps back-end.

> [!TIP]
> Het kiezen van de juiste pagina grootte is een evenwicht tussen het geheugen gebruik terwijl de aanvraag plaatsvindt, het bandbreedte gebruik en de vertraging bij het volledig ontvangen van de gegevens.  De standaard (50 records) is geschikt voor alle apparaten.  Als u uitsluitend op grotere geheugen apparaten hebt gewerkt, verhoogt u Maxi maal 500.  We hebben vastgesteld dat het verg Roten van de pagina groter dan 500 records leiden tot onaanvaardbare vertragingen en grote geheugen problemen.

### <a name="chaining"></a>Procedure: query methoden samen voegen

De gebruikte methoden voor het uitvoeren van query's op back-end-tabellen kunnen worden samengevoegd. Met het koppelen van query methoden kunt u specifieke kolommen selecteren van gefilterde rijen die zijn gesorteerd en gewisseld. U kunt complexe logische filters maken.  Elke query methode retourneert een query-object. Roep de **Execute** -methode aan om de reeks methoden te beëindigen en de query werkelijk uit te voeren. Bijvoorbeeld:

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

De geketende query methoden moeten als volgt worden gerangschikt:

1. Methoden (**where**) filteren.
2. Sorteer methoden (**orderBy**).
3. Selectie methoden (**selecteren**).
4. methoden voor paginering (**overs Laan** en **Top**).

## <a name="binding"></a>Gegevens binden aan de gebruikers interface

Gegevens binding omvat drie onderdelen:

* De gegevens bron
* De scherm indeling
* De adapter die de twee tegelijk verbindt.

In onze voorbeeld code retour neren we de gegevens uit het Mobile Apps SQL Azure tabel **ToDoItem** in een matrix. Deze activiteit is een gemeen schappelijk patroon voor gegevens toepassingen.  Database query's retour neren vaak een verzameling rijen die de client in een lijst of matrix krijgt. In dit voor beeld is de matrix de gegevens bron.  De code geeft een scherm indeling aan waarmee de weer gave van de gegevens op het apparaat wordt gedefinieerd.  De twee zijn gekoppeld aan een adapter, die in deze code een uitbrei ding is van de **ArrayAdapter&lt;ToDoItem&gt;** -klasse.

#### <a name="layout"></a>De indeling definiëren

De indeling wordt gedefinieerd door verschillende code fragmenten van XML-code. Op basis van een bestaande indeling vertegenwoordigt de volgende code de **List View** die we willen vullen met onze server gegevens.

```xml
    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>
```

In de voor gaande code bevat het kenmerk *lijst item* de id van de indeling voor een afzonderlijke rij in de lijst. Met deze code wordt een selectie vakje en de bijbehorende tekst opgegeven en wordt één keer voor elk item in de lijst een exemplaar gemaakt. In deze indeling wordt het veld **id** niet weer gegeven, en in een complexere indeling worden extra velden in de weer gave opgegeven. Deze code bevindt zich in het bestand **row_list_to_do. XML** .

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

#### <a name="adapter"></a>De adapter definiëren
Omdat de gegevens bron van de weer gave een matrix van **ToDoItem**is, wordt onze adapter van een **ArrayAdapter&lt;ToDoItem&gt;** -klasse. Deze subklasse produceert een weer gave voor elke **ToDoItem** met behulp van de **row_list_to_do** indeling.  In onze code definiëren we de volgende klasse die een uitbrei ding is van de **ArrayAdapter&lt;E&gt;** -klasse:

```java
public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {
}
```

Overschrijf de methode **getView** van adapters. Bijvoorbeeld:

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

We maken als volgt een exemplaar van deze klasse in onze activiteit:

```java
    ToDoItemAdapter mAdapter;
    mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
```

De tweede para meter voor de ToDoItemAdapter-constructor is een verwijzing naar de indeling. We kunnen de **List View** nu instantiëren en de adapter toewijzen aan de **List View**.

```java
    ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
    listViewToDo.setAdapter(mAdapter);
```

#### <a name="use-adapter"></a>De adapter gebruiken om verbinding te maken met de gebruikers interface

U bent nu klaar om gegevens binding te gebruiken. De volgende code laat zien hoe u items in de tabel kunt ophalen en de lokale adapter kunt vullen met de geretourneerde items.

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

Roep de adapter op elke keer dat u de **ToDoItem** -tabel wijzigt. Omdat wijzigingen worden uitgevoerd op record basis, verwerkt u één rij in plaats van een verzameling. Wanneer u een item invoegt, roept u de **add** -methode aan op de adapter. roep de **Remove** -methode aan bij het verwijderen.

U kunt een volledig voor beeld vinden in het [Android Quick][21]start-project.

## <a name="inserting"></a>Gegevens invoegen in de back-end

Exemplaar een instantie van de klasse *ToDoItem* en stel de eigenschappen in.

```java
ToDoItem item = new ToDoItem();
item.text = "Test Program";
item.complete = false;
```

Gebruik vervolgens **Insert ()** om een object in te voegen:

```java
ToDoItem entity = mToDoTable
    .insert(item)       // Returns a ListenableFuture<ToDoItem>
    .get();
```

De geretourneerde entiteit komt overeen met de gegevens die zijn ingevoegd in de back-endserver, inclusief de ID en eventuele andere waarden (zoals de `createdAt`, `updatedAt`en `version` velden) die op de back-end zijn ingesteld.

Voor Mobile Apps tabellen is een primaire-sleutel kolom met de naam **id**vereist. Deze kolom moet een teken reeks zijn. De standaard waarde van de kolom ID is een GUID.  U kunt andere unieke waarden opgeven, zoals e-mail adressen of gebruikers namen. Wanneer een teken reeks-ID-waarde niet wordt gegeven voor een ingevoegde record, wordt door de back-end een nieuwe GUID gegenereerd.

Teken reeks-ID-waarden bieden de volgende voor delen:

* Id's kunnen worden gegenereerd zonder dat er een retour naar de data base wordt gemaakt.
* Records kunnen gemakkelijker worden samengevoegd vanuit verschillende tabellen of data bases.
* ID-waarden zijn beter geïntegreerd met de logica van een toepassing.

Teken reeks-ID-waarden zijn **vereist** voor ondersteuning voor offline synchronisatie.  U kunt een id niet meer wijzigen nadat deze is opgeslagen in de back-end-data base.

## <a name="updating"></a>Gegevens bijwerken in een mobiele app

Als u gegevens in een tabel wilt bijwerken, geeft u het nieuwe object door aan de methode **Update ()** .

```java
mToDoTable
    .update(item)   // Returns a ListenableFuture<ToDoItem>
    .get();
```

In dit voor beeld is *item* een verwijzing naar een rij in de tabel *ToDoItem* , waarvan enkele wijzigingen zijn aangebracht.  De rij met dezelfde **id** wordt bijgewerkt.

## <a name="deleting"></a>Gegevens in een mobiele app verwijderen

De volgende code laat zien hoe u gegevens uit een tabel verwijdert door het gegevens object op te geven.

```java
mToDoTable
    .delete(item);
```

U kunt ook een item verwijderen door het veld **id** op te geven van de rij die u wilt verwijderen.

```java
String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
mToDoTable
    .delete(myRowId);
```

## <a name="lookup"></a>Een specifiek item opzoeken op basis van id

Zoek een item met een specifiek **id-** veld met de methode **lookUp ()** :

```java
ToDoItem result = mToDoTable
    .lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
    .get();
```

## <a name="untyped"></a>Procedure: werken met niet-getypte gegevens

Het niet-getypte programmeer model geeft u de controle over JSON-serialisatie.  Er zijn enkele algemene scenario's waarin u mogelijk een niet-getypt programmeer model wilt gebruiken. Als uw back-endserver bijvoorbeeld veel kolommen bevat, hoeft u alleen te verwijzen naar een subset van de kolommen.  Het getypte model vereist dat u alle kolommen definieert die zijn gedefinieerd in de Mobile Apps back-end in uw gegevens klasse.  De meeste API-aanroepen voor toegang tot gegevens zijn vergelijkbaar met de getypte programmerings aanroepen. Het belangrijkste verschil is dat u in het niet-getypte model methoden aanroept in het object **MobileServiceJsonTable** , in plaats van het **MobileServiceTable** -object.

### <a name="json_instance"></a>Een exemplaar van een niet-getypte tabel maken

Net als bij het getypte model begint u met het ophalen van een tabel verwijzing, maar in dit geval is het een **MobileServicesJsonTable** -object. De referentie verkrijgen door de **GetTable** -methode aan te roepen op een exemplaar van de client:

```java
private MobileServiceJsonTable mJsonToDoTable;
//...
mJsonToDoTable = mClient.getTable("ToDoItem");
```

Wanneer u een instantie van de **MobileServiceJsonTable**hebt gemaakt, heeft deze vrijwel dezelfde API beschikbaar als bij het getypte programmeer model. In sommige gevallen maken de methoden gebruik van een para meter zonder type in plaats van een getypeerde para meter.

### <a name="json_insert"></a>Invoegen in een tabel die geen type is
De volgende code laat zien hoe u een insert-actie kunt uitvoeren. De eerste stap bestaat uit het maken van een [JsonObject][1], die deel uitmaakt van de [gson][3] -bibliotheek.

```java
JsonObject jsonItem = new JsonObject();
jsonItem.addProperty("text", "Wake up");
jsonItem.addProperty("complete", false);
```

Gebruik **Insert ()** om het niet-getypte object in de tabel in te voegen.

```java
JsonObject insertedItem = mJsonToDoTable
    .insert(jsonItem)
    .get();
```

Als u de ID van het ingevoegde object wilt ophalen, gebruikt u de methode **getAsJsonPrimitive ()** .

```java
String id = insertedItem.getAsJsonPrimitive("id").getAsString();
```
### <a name="json_delete"></a>Verwijderen uit een niet-getypte tabel
De volgende code laat zien hoe u een exemplaar verwijdert, in dit geval hetzelfde exemplaar van een **JsonObject** dat is gemaakt in *het voor gaande* voor beeld. De code is hetzelfde als bij het getypte hoofdletter gebruik, maar de methode heeft een andere hand tekening, omdat deze verwijst naar een **JsonObject**.

```java
mToDoTable
    .delete(insertedItem);
```

U kunt een instantie ook rechtstreeks verwijderen met de ID:

```java
mToDoTable.delete(ID);
```

### <a name="json_get"></a>Alle rijen van een niet-getypte tabel retour neren
De volgende code laat zien hoe u een volledige tabel ophaalt. Omdat u een JSON-tabel gebruikt, kunt u selectief slechts een deel van de kolommen van de tabel ophalen.

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

Dezelfde set filters, filters en paginerings methoden die beschikbaar zijn voor het getypte model, zijn beschikbaar voor het model untyped.

## <a name="offline-sync"></a>Offline synchronisatie implementeren

De Azure Mobile Apps client SDK implementeert ook offline synchronisatie van gegevens met behulp van een SQLite-data base om een kopie van de server gegevens lokaal op te slaan.  Voor bewerkingen die zijn uitgevoerd op een offline tabel is geen mobiele verbinding vereist.  Hulp middelen voor offline synchronisatie in flexibiliteit en prestaties tegen de kosten van complexere logica voor conflict oplossing.  De Azure Mobile Apps client-SDK implementeert de volgende functies:

* Incrementele synchronisatie: alleen bijgewerkte en nieuwe records worden gedownload, de band breedte en het geheugen verbruik worden bespaard.
* Optimistische gelijktijdigheid: er wordt van uitgegaan dat er bewerkingen worden uitgevoerd.  Conflict oplossing wordt uitgesteld totdat er updates worden uitgevoerd op de server.
* Conflict oplossing: de SDK detecteert wanneer een conflicterende wijziging is doorgevoerd op de server en levert hooks om de gebruiker te waarschuwen.
* Voorlopig verwijderen: verwijderde records worden gemarkeerd als verwijderd, waardoor andere apparaten hun offline cache kunnen bijwerken.

### <a name="initialize-offline-sync"></a>Offline synchronisatie initialiseren

Elke offline tabel moet vóór gebruik in de offline cache worden gedefinieerd.  Normaal gesp roken wordt de tabel definitie onmiddellijk na het maken van de-client uitgevoerd:

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

### <a name="obtain-a-reference-to-the-offline-cache-table"></a>Een verwijzing naar de offline cache tabel ophalen

Voor een online tabel gebruikt u `.getTable()`.  Voor een offline tabel gebruikt u `.getSyncTable()`:

```java
MobileServiceSyncTable<ToDoItem> mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
```

Alle methoden die beschikbaar zijn voor online tabellen (met inbegrip van het filteren, sorteren, pagineren, invoegen van gegevens, het bijwerken van gegevens en het verwijderen van gegevens), werken even goed op online-en offline tabellen.

### <a name="synchronize-the-local-offline-cache"></a>De lokale offline cache synchroniseren

Synchronisatie bevindt zich binnen het beheer van uw app.  Hier volgt een voor beeld van een synchronisatie methode:

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

Als er een query naam wordt ingesteld voor de `.pull(query, queryname)`-methode, wordt incrementele synchronisatie alleen gebruikt om records te retour neren die zijn gemaakt of gewijzigd sinds de laatste geslaagde pull.

### <a name="handle-conflicts-during-offline-synchronization"></a>Conflicten tijdens offline synchronisatie afhandelen

Als er een conflict optreedt tijdens een `.push()` bewerking, wordt er een `MobileServiceConflictException` gegenereerd.   Het door de server uitgegeven item is inge sloten in de uitzonde ring en kan worden opgehaald door `.getItem()` voor de uitzonde ring.  Pas de push aan door de volgende items aan te roepen in het MobileServiceSyncContext-object:

*  `.cancelAndDiscardItem()`
*  `.cancelAndUpdateItem()`
*  `.updateOperationAndItem()`

Zodra alle conflicten zijn gemarkeerd als gewenst, roept u `.push()` opnieuw aan om alle conflicten op te lossen.

## <a name="custom-api"></a>Een aangepaste API aanroepen

Met een aangepaste API kunt u aangepaste eind punten definiëren die server functionaliteit beschikbaar maken die niet is toegewezen aan een insert-, update-, Delete-of Read-bewerking. U kunt met behulp van een aangepaste API meer controle over berichten, waaronder het lezen en instellen van HTTP-bericht koppen en het definiëren van een andere indeling dan JSON.

Vanuit een Android-client roept u de **invokeApi** -methode aan om het aangepaste API-eind punt aan te roepen. In het volgende voor beeld ziet u hoe u een API-eind punt aanroept met de naam **completeAll**, waarmee een verzamelings klasse met de naam **MarkAllResult**wordt geretourneerd.

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

De methode **invokeApi** wordt aangeroepen op de client, die een post-aanvraag verzendt naar de nieuwe aangepaste API. Het resultaat dat door de aangepaste API wordt geretourneerd, wordt in een bericht venster weer gegeven, evenals eventuele fouten. Met andere versies van **invokeApi** kunt u desgewenst een object verzenden in de hoofd tekst van de aanvraag, de HTTP-methode opgeven en query parameters met de aanvraag verzenden. Er zijn ook niet-getypte versies van **invokeApi** .

## <a name="authentication"></a>Verificatie toevoegen aan uw app

Zelf studies beschrijven al in detail hoe deze functies kunnen worden toegevoegd.

App Service ondersteunt het [verifiëren van app-gebruikers](app-service-mobile-android-get-started-users.md) met behulp van verschillende externe ID-providers: Facebook, Google, micro soft-account, Twitter en Azure Active Directory. U kunt machtigingen voor tabellen instellen om de toegang tot specifieke bewerkingen te beperken tot alleen geverifieerde gebruikers. U kunt ook de identiteit van geverifieerde gebruikers gebruiken voor het implementeren van autorisatie regels in uw back-end.

Twee verificatie stromen worden ondersteund: een **Server** stroom en een **client** stroom. De server stroom biedt de eenvoudigste verificatie-ervaring, omdat deze afhankelijk is van de web-interface van de id-providers.  Er zijn geen extra Sdk's vereist om server stroom verificatie te implementeren. Server flow-verificatie biedt geen diepe integratie in het mobiele apparaat en wordt alleen aanbevolen voor het testen van concept scenario's.

De client stroom biedt een diep gaande integratie met apparaatspecifieke mogelijkheden, zoals eenmalige aanmelding, afhankelijk van de Sdk's die worden geboden door de ID-provider.  U kunt bijvoorbeeld de Facebook-SDK integreren in uw mobiele toepassing.  De mobiele client wisselt naar de Facebook-app en bevestigt uw aanmelding voordat u terugwisselt naar uw mobiele app.

Er zijn vier stappen vereist om verificatie in te scha kelen in uw app:

* Registreer uw app voor verificatie met een id-provider.
* Configureer uw App Service-back-end.
* Beperk de tabel machtigingen voor geverifieerde gebruikers alleen op de App Service back-end.
* Voeg verificatie code toe aan uw app.

U kunt machtigingen voor tabellen instellen om de toegang tot specifieke bewerkingen te beperken tot alleen geverifieerde gebruikers. U kunt ook de SID van een geverifieerde gebruiker gebruiken om aanvragen te wijzigen.  Raadpleeg voor meer informatie aan de [Aan de slag met verificatie] en de documentatie bij de server SDK.

### <a name="caching"></a>Verificatie: Server stroom

Met de volgende code wordt een aanmeldings proces voor Server stromen gestart met de Google-provider.  Aanvullende configuratie is vereist vanwege de beveiligings vereisten voor de Google-provider:

```java
MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
```

Voeg bovendien de volgende methode toe aan de klasse van de hoofd activiteit:

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

De `GOOGLE_LOGIN_REQUEST_CODE` die in uw hoofd activiteit zijn gedefinieerd, wordt gebruikt voor de methode `login()` en binnen de methode `onActivityResult()`.  U kunt een wille keurig uniek nummer kiezen, zolang hetzelfde nummer wordt gebruikt binnen de `login()` methode en de methode `onActivityResult()`.  Als u de client code opsplitst in een service adapter (zoals eerder weer gegeven), moet u de juiste methoden op de service adapter aanroepen.

U moet ook het project voor customtabs configureren.  Geef eerst een omleidings-URL op.  Voeg het volgende fragment toe aan `AndroidManifest.xml`:

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

Voeg de **redirectUriScheme** toe aan het `build.gradle`-bestand voor uw toepassing:

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

Voeg ten slotte `com.android.support:customtabs:28.0.0` toe aan de lijst met afhankelijkheden in het `build.gradle`-bestand:

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

Haal de ID van de aangemelde gebruiker op uit een **MobileServiceUser** met behulp van de methode **getUserId** . Zie [aan de slag met verificatie]voor een voor beeld van het gebruik van toekomst om de asynchrone aanmeld-api's aan te roepen.

> [!WARNING]
> Het vermelde URL-schema is hoofdletter gevoelig.  Zorg ervoor dat alle exemplaren van `{url_scheme_of_you_app}` match-case.

### <a name="caching"></a>Verificatie tokens in de cache

Bij het in cache opslaan van verificatie tokens moet u de gebruikers-ID en het verificatie token lokaal opslaan op het apparaat. De volgende keer dat de app wordt gestart, controleert u de cache en als deze waarden aanwezig zijn, kunt u het logboek in procedure overs Laan en de client met deze gegevens onthydraten. Deze gegevens zijn echter vertrouwelijk en moeten worden opgeslagen als versleuteld voor de beveiliging als de telefoon wordt gestolen.  U kunt een volledig voor beeld bekijken van het in de cache opslaan van verificatie tokens in de [sectie cache-verificatie tokens][7].

Wanneer u een verlopen token probeert te gebruiken, ontvangt u een *401 niet-geautoriseerde* reactie. U kunt verificatie fouten met filters afhandelen.  Filters intercepteer aanvragen naar de App Service back-end. De filter code test het antwoord voor een 401, activeert het aanmeldings proces en hervat vervolgens de aanvraag die de 401 heeft gegenereerd.

### <a name="refresh"></a>Vernieuwings tokens gebruiken

Het token dat wordt geretourneerd door Azure App Service authenticatie en autorisatie, heeft een bepaalde levens duur van één uur.  Na deze periode moet u de gebruiker opnieuw verifiëren.  Als u een langlopend token gebruikt dat u hebt ontvangen via verificatie op basis van de client, kunt u opnieuw verifiëren met Azure App Service verificatie en autorisatie met behulp van hetzelfde token.  Er wordt een ander Azure App Service-token gegenereerd met een nieuwe levens duur.

U kunt de provider ook registreren voor het gebruik van vernieuwings tokens.  Een vernieuwings token is niet altijd beschikbaar.  Aanvullende configuratie is vereist:

* Configureer voor **Azure Active Directory**een client geheim voor de Azure Active Directory-app.  Geef het client geheim op in de Azure App Service bij het configureren van Azure Active Directory-verificatie.  Geef `response_type=code id_token` als een para meter door als u `.login()`aanroept:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("response_type", "code id_token");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.AzureActiveDirectory,
        "{url_scheme_of_your_app}",
        AAD_LOGIN_REQUEST_CODE,
        parameters);
    ```

* Geef voor **Google**de `access_type=offline` door als een para meter:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("access_type", "offline");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.Google,
        "{url_scheme_of_your_app}",
        GOOGLE_LOGIN_REQUEST_CODE,
        parameters);
    ```

* Selecteer voor het **micro soft-account**het `wl.offline_access` bereik.

Als u een token wilt vernieuwen, roept u `.refreshUser()`aan:

```java
MobileServiceUser user = mClient
    .refreshUser()  // async - returns a ListenableFuture<MobileServiceUser>
    .get();
```

Maak als best practice een filter dat een 401-reactie van de server detecteert en probeert het gebruikers token te vernieuwen.

## <a name="log-in-with-client-flow-authentication"></a>Meld u aan met verificatie op basis van de client stroom

Het algemene proces voor het aanmelden met client-flow verificatie is als volgt:

* Configureer Azure App Service verificatie en autorisatie, zoals u verificatie via de server stroom wilt.
* Integreer de verificatie provider-SDK voor verificatie om een toegangs token te maken.
* Roep de `.login()` methode als volgt aan (`result` moet een `AuthenticationResult`zijn):

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

Zie het volledige code voorbeeld in de volgende sectie.

Vervang de `onSuccess()` methode door de code die u wilt gebruiken voor een geslaagde aanmelding.  De `{provider}` teken reeks is een geldige provider: **Aad** (Azure Active Directory) **, Facebook**, **Google**, **MicrosoftAccount**of **Twitter**.  Als u aangepaste verificatie hebt geïmplementeerd, kunt u ook het aangepaste label voor de verificatie provider gebruiken.

### <a name="adal"></a>Gebruikers verifiëren met de Active Directory Authentication Library (ADAL)

U kunt de Active Directory Authentication Library (ADAL) gebruiken voor het ondertekenen van gebruikers in uw toepassing met behulp van Azure Active Directory. Het gebruik van een client flow-aanmelding is vaak de voor keur voor het gebruik van de `loginAsync()`-methoden omdat het een meer systeem eigen UX-ervaring biedt en extra aanpassing mogelijk maakt.

1. Configureer de back-end van uw mobiele app voor AAD-aanmelding door de zelf studie [app service voor Active Directory-aanmelding configureren te][22] volgen. Zorg ervoor dat u de optionele stap voor het registreren van een systeem eigen client toepassing hebt voltooid.
2. Installeer ADAL door uw build. gradle-bestand te wijzigen met de volgende definities:

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

3. Voeg de volgende code toe aan uw toepassing, waardoor de volgende vervangingen worden uitgevoerd:

    * Vervang de **INVOEG instantie** door de naam van de Tenant waarin u uw toepassing hebt ingericht. De notatie moet https://login.microsoftonline.com/contoso.onmicrosoft.comzijn.
    * Vervang de **Insert-resource-id hier** door de client-id voor de back-end van uw mobiele app. U kunt de client-ID verkrijgen via het tabblad **Geavanceerd** onder **Azure Active Directory instellingen** in de portal.
    * Vervang **Insert-client-id-hier** door de client-id die u hebt gekopieerd uit de systeem eigen client toepassing.
    * Vervang **Insert-redirect-Uri-hier** met het */.auth/login/done* -eind punt van uw site met behulp van het HTTPS-schema. Deze waarde moet gelijk zijn aan *https://contoso.azurewebsites.net/.auth/login/done* .

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

## <a name="filters"></a>De communicatie tussen de client en de server aanpassen

De client verbinding is doorgaans een eenvoudige HTTP-verbinding met behulp van de onderliggende HTTP-bibliotheek die is geleverd bij de Android-SDK.  Er zijn verschillende redenen waarom u deze wilt wijzigen:

* U wilt een alternatieve HTTP-bibliotheek gebruiken om time-outs aan te passen.
* U wilt een voortgangs balk opgeven.
* U wilt een aangepaste header toevoegen ter ondersteuning van API management-functionaliteit.
* U wilt een mislukte reactie onderscheppen zodat u de herauthenticatie kunt implementeren.
* U wilt backend-aanvragen registreren bij een analyse service.

### <a name="using-an-alternate-http-library"></a>Een alternatieve HTTP-bibliotheek gebruiken

Roep de `.setAndroidHttpClientFactory()` methode direct aan nadat u uw client verwijzing hebt gemaakt.  Als u bijvoorbeeld de time-out voor de verbinding wilt instellen op 60 seconden (in plaats van de standaard 10 seconden):

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

### <a name="implement-a-progress-filter"></a>Een voortgangs filter implementeren

U kunt een interceptie van elke aanvraag implementeren door een `ServiceFilter`te implementeren.  Met de volgende updates wordt bijvoorbeeld een vooraf gemaakte voortgangs balk bijgewerkt:

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

U kunt dit filter als volgt aan de client koppelen:

```java
mClient = new MobileServiceClient(applicationUrl).withFilter(new ProgressFilter());
```

### <a name="customize-request-headers"></a>Aanvraag headers aanpassen

Gebruik de volgende `ServiceFilter` en koppel het filter op dezelfde manier als de `ProgressFilter`:

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

### <a name="conversions"></a>Automatische serialisatie configureren

U kunt een conversie strategie opgeven die op elke kolom van toepassing is met behulp van de [gson][3] -API. De Android-client bibliotheek gebruikt [gson][3] achter de schermen om Java-objecten te serialiseren naar JSON-gegevens voordat de gegevens naar Azure app service worden verzonden.  In de volgende code wordt de methode **setFieldNamingStrategy ()** gebruikt om de strategie in te stellen. In dit voor beeld wordt het eerste teken (een ' m ') verwijderd en vervolgens voor elke veld naam kleine letters in het volgende teken. Met de functie wordt bijvoorbeeld ' mId ' omgezet in ' id '.  Implementeer een conversie strategie om de `SerializedName()` annotaties voor de meeste velden te verminderen.

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

Deze code moet worden uitgevoerd voordat u een verwijzing naar een mobiele client maakt met behulp van de **mobileserviceclient te maken**.

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
