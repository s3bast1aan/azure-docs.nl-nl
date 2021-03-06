---
title: Inzicht in de Azure IoT Hub-querytaal | Microsoft Docs
description: Handleiding voor ontwikkelaars - beschrijving van de SQL-achtige IoT-Hub query language gebruikt voor het ophalen van informatie over het apparaat/module dubbels en taken van uw IoT-hub.
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/26/2018
ms.author: elioda
ms.openlocfilehash: 321d70a04e3c524e578a01e8531d63733d088c3f
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444181"
---
# <a name="iot-hub-query-language-for-device-and-module-twins-jobs-and-message-routing"></a>IoT Hub-querytaal voor dubbels voor apparaat- en -module, taken en berichtroutering

IoT Hub biedt een krachtige SQL-achtige taal verkrijgen van informatie met betrekking tot [apparaatdubbels] [ lnk-twins] en [taken][lnk-jobs], en [berichtroutering][lnk-devguide-messaging-routes]. In dit artikel geeft:

* Een inleiding tot de belangrijkste functies van de IoT Hub-querytaal en
* De gedetailleerde beschrijving van de taal.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="device-and-module-twin-queries"></a>Apparaat- en -module apparaatdubbel-query 's
[Apparaatdubbels] [ lnk-twins] en moduledubbels willekeurige JSON-objecten als zowel labels en eigenschappen kunnen bevatten. IoT Hub kunt u query apparaatdubbels en moduledubbels als een enkel JSON-document met alle dubbele gegevens.
Stel bijvoorbeeld dat de apparaatdubbels van uw IoT-hub de volgende structuur hebben (moduledubbel zouden zijn vergelijkbaar met een extra moduleId alleen):

```json
{
    "deviceId": "myDeviceId",
    "etag": "AAAAAAAAAAc=",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00",    
    "connectionState": "Disconnected",    
    "lastActivityTime": "0001-01-01T00:00:00",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",    
    "x509Thumbprint": {    
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "version": 2,
    "tags": {
        "location": {
            "region": "US",
            "plant": "Redmond43"
        }
    },
    "properties": {
        "desired": {
            "telemetryConfig": {
                "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",
                "sendFrequencyInSecs": 300
            },
            "$metadata": {
            ...
            },
            "$version": 4
        },
        "reported": {
            "connectivity": {
                "type": "cellular"
            },
            "telemetryConfig": {
                "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",
                "sendFrequencyInSecs": 300,
                "status": "Success"
            },
            "$metadata": {
            ...
            },
            "$version": 7
        }
    }
}
```

### <a name="device-twin-queries"></a>Apparaatdubbel-query 's

IoT-Hub toont de apparaatdubbels als een documentverzameling met de naam **apparaten**.
Dus haalt de volgende query de hele set dubbele apparaten:

```sql
SELECT * FROM devices
```

> [!NOTE]
> [Azure IoT SDK's] [ lnk-hub-sdks] ondersteuning voor paginering van veel resultaten.

IoT Hub kunt u om op te halen van apparaatdubbels filteren met willekeurig voorwaarden. Bijvoorbeeld voor het ontvangen van device twins waar de **location.region** label is ingesteld op **VS** gebruik de volgende query:

```sql
SELECT * FROM devices
WHERE tags.location.region = 'US'
```

Booleaanse operators en rekenkundige vergelijkingen worden ook ondersteund. Om op te halen van apparaat bijvoorbeeld, gebruik dubbele zich in de Verenigde Staten en geconfigureerd voor het verzenden van telemetrie minder dan elke minuut de volgende query:

```sql
SELECT * FROM devices
WHERE tags.location.region = 'US'
    AND properties.reported.telemetryConfig.sendFrequencyInSecs >= 60
```

Uw gemak, het is ook mogelijk met gebruik van de matrixconstanten met de **IN** en **NIN** operators (niet-in). Om op te halen, gebruik dubbele apparaten die rapporteren van Wi-Fi of bekabelde verbindingen de volgende query:

```sql
SELECT * FROM devices
WHERE properties.reported.connectivity IN ['wired', 'wifi']
```

Het is vaak nodig om u te identificeren van alle dubbele apparaten die een bepaalde eigenschap bevatten. IoT Hub biedt ondersteuning voor de functie `is_defined()` voor dit doel. Bijvoorbeeld, voor apparaatdubbels ophalen die definieert de `connectivity` eigenschap gebruik de volgende query:

```SQL
SELECT * FROM devices
WHERE is_defined(properties.reported.connectivity)
```

Raadpleeg de [WHERE-component] [ lnk-query-where] sectie voor de volledige verwijzing van de filters gebruiken om mogelijkheden.

Groeperen en aggregaties worden ook ondersteund. Informatie over het aantal apparaten in elke telemetrie configuratiestatus bijvoorbeeld, gebruik de volgende query:

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
FROM devices
GROUP BY properties.reported.telemetryConfig.status
```

Deze groepering query retourneert een resultaat vergelijkbaar met het volgende voorbeeld:

```json
[
    {
        "numberOfDevices": 3,
        "status": "Success"
    },
    {
        "numberOfDevices": 2,
        "status": "Pending"
    },
    {
        "numberOfDevices": 1,
        "status": "Error"
    }
]
```

In dit voorbeeld drie apparaten gerapporteerd geslaagde configuratie, twee zijn nog steeds de configuratie van de toepassing en een heeft een fout gerapporteerd.

Projectie van query's kunnen ontwikkelaars om terug te keren alleen de eigenschappen die het belangrijkst. Bijvoorbeeld, verbroken om op te halen van de laatste activiteitstijd van alle apparaten gebruik de volgende query uit:

```sql
SELECT LastActivityTime FROM devices WHERE status = 'enabled'
```

### <a name="module-twin-queries"></a>Module apparaatdubbel-query 's

Query's over moduledubbels is vergelijkbaar met de query op dubbele apparaten, maar met behulp van een andere verzameling/naamruimte, dat wil zeggen in plaats van 'van apparaten' u kunt een query

```sql
SELECT * FROM devices.modules
```

We kunnen geen koppeling tussen de apparaten en devices.modules verzamelingen. Als u moduledubbels query alle apparaten wilt, moet u doen op basis van tags. Deze query retourneert alle moduledubbels voor alle apparaten met de status van de scannen:

```sql
Select * from devices.modules where properties.reported.status = 'scanning'
```

Deze query retourneert alle moduledubbels met de status van de scans, maar alleen op het opgegeven aantal apparaten.

```sql
Select * from devices.modules where properties.reported.status = 'scanning' and deviceId IN ('device1', 'device2')  
```

### <a name="c-example"></a>C#-voorbeeld
De queryfunctionaliteit van de wordt weergegeven door de [C# service-SDK] [ lnk-hub-sdks] in de **RegistryManager** klasse.
Hier volgt een voorbeeld van een eenvoudige query:

```csharp
var query = registryManager.CreateQuery("SELECT * FROM devices", 100);
while (query.HasMoreResults)
{
    var page = await query.GetNextAsTwinAsync();
    foreach (var twin in page)
    {
        // do work on twin object
    }
}
```

De **query** object is gemaakt met een paginagrootte (maximaal 100). Klik meerdere pagina's worden opgehaald door het aanroepen van de **GetNextAsTwinAsync** methoden meerdere keren.

De query-object wordt aangegeven dat meerdere **volgende** waarden, afhankelijk van de deserialisatie-optie vereist door de query. Bijvoorbeeld, apparaatobjecten dubbel of de taak, of gewoon JSON bij het gebruik van projecties.

### <a name="nodejs-example"></a>Node.js-voorbeeld
De queryfunctionaliteit van de wordt weergegeven door de [Azure IoT service SDK voor Node.js] [ lnk-hub-sdks] in de **register** object.
Hier volgt een voorbeeld van een eenvoudige query:

```nodejs
var query = registry.createQuery('SELECT * FROM devices', 100);
var onResults = function(err, results) {
    if (err) {
        console.error('Failed to fetch the results: ' + err.message);
    } else {
        // Do something with the results
        results.forEach(function(twin) {
            console.log(twin.deviceId);
        });

        if (query.hasMoreResults) {
            query.nextAsTwin(onResults);
        }
    }
};
query.nextAsTwin(onResults);
```

De **query** object is gemaakt met een paginagrootte (maximaal 100). Klik meerdere pagina's worden opgehaald door het aanroepen van de **nextAsTwin** methode meerdere keren.

De query-object wordt aangegeven dat meerdere **volgende** waarden, afhankelijk van de deserialisatie-optie vereist door de query. Bijvoorbeeld, apparaatobjecten dubbel of de taak, of gewoon JSON bij het gebruik van projecties.

### <a name="limitations"></a>Beperkingen

> [!IMPORTANT]
> Apparaatdubbels queryresultaten, kunnen een paar minuten vertraging met betrekking tot de meest recente waarden bevatten. Als het uitvoeren van query's is een afzonderlijke apparaatdubbels door-ID, moet u de ophalen-apparaat dubbele API gebruiken. Deze API is altijd de meest recente waarden bevat en is hoger beperkingslimieten.

Op dit moment vergelijkingen bijvoorbeeld alleen tussen primitieve typen (geen objecten), worden ondersteund `... WHERE properties.desired.config = properties.reported.config` wordt alleen ondersteund als deze eigenschappen primitieve waarden hebben.

## <a name="get-started-with-jobs-queries"></a>Aan de slag met taken query 's

[Taken] [ lnk-jobs] bieden een manier voor het uitvoeren van bewerkingen voor sets met apparaten. Elke apparaatdubbel bevat de informatie van de taken die het onderdeel in een verzameling met de naam is **taken**.
Logisch gezien

```json
{
    "deviceId": "myDeviceId",
    "etag": "AAAAAAAAAAc=",
    "tags": {
        ...
    },
    "properties": {
        ...
    },
    "jobs": [
        {
            "deviceId": "myDeviceId",
            "jobId": "myJobId",
            "jobType": "scheduleTwinUpdate",
            "status": "completed",
            "startTimeUtc": "2016-09-29T18:18:52.7418462",
            "endTimeUtc": "2016-09-29T18:20:52.7418462",
            "createdDateTimeUtc": "2016-09-29T18:18:56.7787107Z",
            "lastUpdatedDateTimeUtc": "2016-09-29T18:18:56.8894408Z",
            "outcome": {
                "deviceMethodResponse": null
            }
        },
        ...
    ]
}
```

Deze verzameling is momenteel waarin u kunt zoeken als **devices.jobs** in de IoT Hub-querytaal.

> [!IMPORTANT]
> Op dit moment wordt de eigenschap taken nooit geretourneerd bij het opvragen van apparaatdubbels. Dat wil zeggen, de query's die bevatten ' apparaten'. De eigenschap taken alleen kan worden geopend rechtstreeks met query's met behulp van `FROM devices.jobs`.
>
>

Bijvoorbeeld, als u alle taken (afgelopen en geplande) die invloed hebben op één apparaat, kunt u de volgende query uit:

```sql
SELECT * FROM devices.jobs
WHERE devices.jobs.deviceId = 'myDeviceId'
```

Houd er rekening mee hoe deze query geeft de status van de apparaat-specifieke (en eventueel ook het antwoord van de directe methode) van elke taak die wordt geretourneerd.
Het is ook mogelijk om te filteren met willekeurig Booleaanse voorwaarden op alle eigenschappen van het object in de **devices.jobs** verzameling.
Bijvoorbeeld, om op te halen van alle voltooide device twin update taken die na September 2016 zijn gemaakt voor een specifiek apparaat, gebruikt u de volgende query uit:

```sql
SELECT * FROM devices.jobs
WHERE devices.jobs.deviceId = 'myDeviceId'
    AND devices.jobs.jobType = 'scheduleTwinUpdate'
    AND devices.jobs.status = 'completed'
    AND devices.jobs.createdTimeUtc > '2016-09-01'
```

U kunt ook de resultaten per apparaat van een enkele taak ophalen.

```sql
SELECT * FROM devices.jobs
WHERE devices.jobs.jobId = 'myJobId'
```

### <a name="limitations"></a>Beperkingen
Op dit moment een query uitgevoerd op **devices.jobs** bieden geen ondersteuning voor:

* Projecties, daarom alleen `SELECT *` is mogelijk.
* De voorwaarden die naar de apparaatdubbel naast taakeigenschappen verwijzen (Zie de vorige sectie).
* Uitvoeren van de aggregaties, zoals het aantal, gemiddelde, groeperen op.

## <a name="device-to-cloud-message-routes-query-expressions"></a>Apparaat-naar-cloud berichtroutes query-expressies

Met behulp van [apparaat-naar-cloud-routes][lnk-devguide-messaging-routes], kunt u IoT-Hub voor het verzenden van apparaat-naar-cloud-berichten naar verschillende eindpunten configureren. Verzenden is gebaseerd op expressies geëvalueerd op basis van afzonderlijke berichten.

De route [voorwaarde] [ lnk-query-expressions] maakt gebruik van de dezelfde IoT Hub-querytaal als voorwaarden in dubbel en taak query's. Route voorwaarden worden geëvalueerd op de berichtheaders en hoofdtekst. Uw routering query-expressie kan alleen berichtkoppen, omvatten de hoofdtekst van het bericht of beide. IoT Hub wordt ervan uitgegaan dat een specifieke schema voor de headers en hoofdtekst van het bericht om te routeren van berichten. De volgende secties wordt beschreven wat is vereist voor IoT Hub voor het routeren van goed.

### <a name="routing-on-message-headers"></a>Routering op berichtkoppen

IoT Hub wordt ervan uitgegaan dat de volgende JSON-weergave van berichtkoppen voor het routeren van berichten:

```json
{
  "message": {
    "systemProperties": {
      "contentType": "application/json",
      "contentEncoding": "utf-8",
      "iothub-message-source": "deviceMessages",
      "iothub-enqueuedtime": "2017-05-08T18:55:31.8514657Z"
    },
    "appProperties": {
      "processingPath": "<optional>",
      "verbose": "<optional>",
      "severity": "<optional>",
      "testDevice": "<optional>"
    },
    "body": "{\"Weather\":{\"Temperature\":50}}"
  }
}
```

Systeemeigenschappen bericht worden voorafgegaan door de `'$'` symbool.
Eigenschappen van de gebruiker zijn altijd toegankelijk met hun naam. Als de naam van een eigenschap met een systeemeigenschap overeenkomt (zoals `$contentType`), de eigenschap wordt opgehaald met de `$contentType` expressie.
U kunt altijd toegang tot de eigenschap van het systeem met behulp van haakjes `{}`: bijvoorbeeld, kunt u de expressie `{$contentType}` voor toegang tot de systeemeigenschap `contentType`. Eigenschapnamen ophalen altijd de bijbehorende systeemeigenschap.

Houd er rekening mee dat de namen van eigenschappen zijn niet hoofdlettergevoelig.

> [!NOTE]
> Eigenschappen van alle berichten zijn tekenreeksen. Eigenschappen, zoals beschreven in de [ontwikkelaarshandleiding][lnk-devguide-messaging-format], zijn momenteel niet beschikbaar voor gebruik in query's.
>

Als u bijvoorbeeld een `messageType` eigenschap, kunt u voor het routeren van alle telemetrie naar één eindpunt en alle meldingen naar een ander eindpunt. U kunt de volgende expressie om te routeren van de telemetrie schrijven:

```sql
messageType = 'telemetry'
```

En de volgende expressie voor het routeren van de waarschuwingsberichten:

```sql
messageType = 'alert'
```

Booleaanse expressies en functies worden ook ondersteund. Deze functie kunt u onderscheid maken tussen de ernst op, bijvoorbeeld:

```sql
messageType = 'alerts' AND as_number(severity) <= 2
```

Raadpleeg de [expressie en voorwaarden] [ lnk-query-expressions] sectie voor een volledige lijst van ondersteunde operators en -functies.

### <a name="routing-on-message-bodies"></a>Routering op de berichttekst

IoT Hub kunnen alleen worden gerouteerd op basis van de hoofdtekst van bericht inhoud als hoofdtekst van het bericht correct is gevormd JSON gecodeerd in UTF-8-, UTF-16- of UTF-32. Instellen van het type inhoud van het bericht `application/json`. Stel de inhoud coderen naar een van de ondersteunde UTF-coderingen in de berichtkoppen. Als een van de headers is niet opgegeven, probeert IoT-Hub niet evalueren van een query-expressie met betrekking tot de instantie voor het bericht. Als uw bericht is niet een JSON-bericht of als het bericht het inhoudstype en inhoudscodering niet is opgegeven, kunt u nog steeds berichtroutering gebruiken voor het routeren van het bericht op basis van de berichtkoppen.

Het volgende voorbeeld laat zien hoe een bericht maken met een juist opgemaakte en gecodeerde JSON-hoofdtekst:

```csharp
string messageBody = @"{ 
                            ""Weather"":{ 
                                ""Temperature"":50, 
                                ""Time"":""2017-03-09T00:00:00.000Z"", 
                                ""PrevTemperatures"":[ 
                                    20, 
                                    30, 
                                    40 
                                ], 
                                ""IsEnabled"":true, 
                                ""Location"":{ 
                                    ""Street"":""One Microsoft Way"", 
                                    ""City"":""Redmond"", 
                                    ""State"":""WA"" 
                                }, 
                                ""HistoricalData"":[ 
                                    { 
                                    ""Month"":""Feb"", 
                                    ""Temperature"":40 
                                    }, 
                                    { 
                                    ""Month"":""Jan"", 
                                    ""Temperature"":30 
                                    } 
                                ] 
                            } 
                        }"; 
 
// Encode message body using UTF-8 
byte[] messageBytes = Encoding.UTF8.GetBytes(messageBody); 
 
using (var message = new Message(messageBytes)) 
{ 
    // Set message body type and content encoding. 
    message.ContentEncoding = "utf-8"; 
    message.ContentType = "application/json"; 
 
    // Add other custom application properties.  
    message.Properties["Status"] = "Active";    
 
    await deviceClient.SendEventAsync(message); 
}
```

U kunt `$body` in de query-expressie voor het routeren van het bericht. U kunt de verwijzing naar een eenvoudige hoofdtekst, hoofdtekst van de verwijzing naar een matrix of meerdere hoofdtekst verwijzingen gebruiken in de query-expressie. Uw query-expressie kunt ook een verwijzing hoofdtekst met een verwijzing naar een bericht-header combineren. De volgende zijn bijvoorbeeld alle geldige query-expressies:

```sql
$body.Weather.HistoricalData[0].Month = 'Feb'
$body.Weather.Temperature = 50 AND $body.Weather.IsEnabled
length($body.Weather.Location.State) = 2
$body.Weather.Temperature = 50 AND Status = 'Active'
```

## <a name="basics-of-an-iot-hub-query"></a>Basisprincipes van een IoT Hub-query
Elke IoT Hub query bestaat van selecteren en van de EU, met optionele waar en GROUP BY-componenten. Elke query wordt uitgevoerd op een verzameling van JSON-documenten, bijvoorbeeld apparaatdubbels. De component FROM geeft aan dat de documentenverzameling om te worden herhaald op (**apparaten** of **devices.jobs**). Het filter in de component WHERE wordt vervolgens toegepast. Met aggregaties, worden de resultaten van deze stap zijn gegroepeerd zoals opgegeven in de component GROUP BY. Voor elke groep een rij wordt gegenereerd zoals opgegeven in de component SELECT.

```sql
SELECT <select_list>
FROM <from_specification>
[WHERE <filter_condition>]
[GROUP BY <group_specification>]
```

## <a name="from-clause"></a>FROM-component
De **van < from_specification >** component kunt ervan uitgaan dat slechts twee waarden: **van apparaten** naar query apparaatdubbels, of **van devices.jobs** op query-taakdetails per apparaat.

## <a name="where-clause"></a>WHERE-component
De **waarbij < filter_condition >** component is optioneel. Hiermee geeft u een of meer voorwaarden dat de JSON-in de verzameling van documenten moeten voldoen om te worden opgenomen als onderdeel van het resultaat. Elk JSON-document moet de opgegeven voorwaarden op "true" moet worden opgenomen in het resultaat opleveren.

De toegestane voorwaarden worden beschreven in de sectie [expressies en voorwaarden][lnk-query-expressions].

## <a name="select-clause"></a>SELECT-component
De **Selecteer < select_list >** is verplicht en Hiermee geeft u de waarden die worden opgehaald uit de query. Het geeft de JSON-waarden moet worden gebruikt voor het genereren van nieuwe JSON-objecten.
De Projectiefase wordt voor elk element van de gefilterde (en eventueel gegroepeerde) subset van de verzameling van een nieuwe JSON-object gegenereerd. Dit object is gemaakt met de opgegeven waarden in de component SELECT.

Hieronder vindt u de grammatica van de component SELECT:

```
SELECT [TOP <max number>] <projection list>

<projection_list> ::=
    '*'
    | <projection_element> AS alias [, <projection_element> AS alias]+

<projection_element> :==
    attribute_name
    | <projection_element> '.' attribute_name
    | <aggregate>

<aggregate> :==
    count()
    | avg(<projection_element>)
    | sum(<projection_element>)
    | min(<projection_element>)
    | max(<projection_element>)
```

**%{Attribute_name/** verwijst naar een eigenschap van het JSON-document in de verzameling van. Enkele voorbeelden van SELECT-component kunnen worden gevonden in de [aan de slag met query's voor apparaattwins] [ lnk-query-getstarted] sectie.

Op dit moment selectie van de EU anders dan **Selecteer*** worden alleen ondersteund in statistische query's op dubbele apparaten.

## <a name="group-by-clause"></a>GROUP BY-component
De **GROUP BY < group_specification >** component is een optionele stap die wordt uitgevoerd na het opgegeven in de WHERE-component en voordat de projectie Selecteer in het opgegeven filter. Deze groepen documenten op basis van de waarde van een kenmerk. Deze groepen worden gebruikt voor het genereren van geaggregeerde waarden die zijn opgegeven in de component SELECT.

Een voorbeeld van een query met GROUP BY is:

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
FROM devices
GROUP BY properties.reported.telemetryConfig.status
```

De formele syntaxis voor GROUP BY is:

```
GROUP BY <group_by_element>
<group_by_element> :==
    attribute_name
    | < group_by_element > '.' attribute_name
```

**%{Attribute_name/** verwijst naar een eigenschap van het JSON-document in de verzameling van.

De component GROUP BY is momenteel alleen ondersteund bij het opvragen van apparaatdubbels.

## <a name="expressions-and-conditions"></a>Expressies en voorwaarden
Op hoog niveau, een *expressie*:

* Resulteert in een exemplaar van een JSON-type (zoals een Booleaanse waarde, getal, tekenreeks, matrix of object).
* Wordt gedefinieerd door het bewerken van gegevens die afkomstig zijn van het apparaat JSON-document en constanten met behulp van ingebouwde operatoren en functies.

*Voorwaarden* expressies die worden geëvalueerd tot een Booleaanse waarde zijn. Een constante anders dan de Booleaanse waarde **waar** wordt beschouwd als **false**. Deze regel bevat **null**, **niet-gedefinieerde**, een object of een matrix-exemplaar, een willekeurige tekenreeks en de Booleaanse waarde **false**.

De syntaxis voor expressies is:

```
<expression> ::=
    <constant> |
    attribute_name |
    <function_call> |
    <expression> binary_operator <expression> |
    <create_array_expression> |
    '(' <expression> ')'

<function_call> ::=
    <function_name> '(' expression ')'

<constant> ::=
    <undefined_constant>
    | <null_constant>
    | <number_constant>
    | <string_constant>
    | <array_constant>

<undefined_constant> ::= undefined
<null_constant> ::= null
<number_constant> ::= decimal_literal | hexadecimal_literal
<string_constant> ::= string_literal
<array_constant> ::= '[' <constant> [, <constant>]+ ']'
```

Om te begrijpen wat elke symbool in de syntaxis van de expressies voor staat, raadpleegt u de volgende tabel:

| Symbool | Definitie |
| --- | --- |
| %{attribute_name/ | Een eigenschap van het JSON-document in de **FROM** verzameling. |
| binary_operator | Een binaire operator die worden vermeld in de [Operators](#operators) sectie. |
| functie_naam| Een functie die worden vermeld in de [functies](#functions) sectie. |
| decimal_literal |Een float-waarde, uitgedrukt in decimale notatie. |
| hexadecimal_literal |Een getal dat wordt uitgedrukt in de tekenreeks '0 x' gevolgd door een reeks van hexadecimale cijfers. |
| string_literal |Letterlijke tekenreeks zijn vertegenwoordigd door een reeks van nul of meer Unicode-tekens of escapereeksen Unicode-tekenreeksen. Letterlijke tekenreeks zijn ingesloten in enkele aanhalingstekens of dubbele aanhalingstekens. Hiermee heft toegestaan: `\'`, `\"`, `\\`, `\uXXXX` voor Unicode-tekens die zijn gedefinieerd door 4 hexadecimale cijfers. |

### <a name="operators"></a>Operators
De volgende operators worden ondersteund:

| Familie | Operators |
| --- | --- |
| Rekenkundige |+, -, *, /, % |
| Logische |EN, OF NIET |
| Vergelijking |=, !=, <, >, <=, >=, <> |

### <a name="functions"></a>Functions
Bij het opvragen van dubbels en taken die de enige ondersteunde is functie:

| Functie | Beschrijving |
| -------- | ----------- |
| IS_DEFINED(Property) | Retourneert een Booleaanse waarde waarmee wordt aangegeven als de eigenschap een waarde is toegewezen (met inbegrip van `null`). |

In situaties, routes, worden de volgende wiskundige functies ondersteund:

| Functie | Beschrijving |
| -------- | ----------- |
| ABS(x) | Retourneert de absolute (positief) waarde van de opgegeven numerieke expressie. |
| EXP(x) | Retourneert de exponentiële waarde van de opgegeven numerieke expressie (e ^ x). |
| Power(x,y) | Retourneert de waarde van de opgegeven expressie voor de opgegeven macht (x ^ y).|
| Square(x) | Retourneert het kwadraat van de opgegeven numerieke waarde. |
| CEILING(x) | Retourneert de kleinste integer-waarde groter dan of gelijk zijn aan de opgegeven numerieke expressie. |
| FLOOR(x) | Retourneert het grootste gehele getal kleiner dan of gelijk zijn aan de opgegeven numerieke expressie. |
| Sign(x) | Retourneert de positief (+ 1), nul (0) of minteken (-1) van de opgegeven numerieke expressie.|
| WORTEL(x) | Retourneert de vierkantswortel van de opgegeven numerieke waarde. |

In situaties, routes, worden de volgende controle van het type en de casten functies ondersteund:

| Functie | Beschrijving |
| -------- | ----------- |
| AS_NUMBER | De invoertekenreeks converteert naar een getal. `noop` Als de invoer een getal is. `Undefined` als tekenreeks niet voor een getal staat.|
| IS_ARRAY | Retourneert een Booleaanse waarde die aangeeft of het type van de opgegeven expressie een matrix is. |
| IS_BOOL | Retourneert een Booleaanse waarde die aangeeft of het type van de opgegeven expressie een Booleaanse waarde is. |
| IS_DEFINED | Retourneert een Booleaanse waarde waarmee wordt aangegeven als de eigenschap een waarde is toegewezen. |
| IS_NULL | Retourneert een Booleaanse waarde die aangeeft of het type van de opgegeven expressie null is. |
| IS_NUMBER | Retourneert een Booleaanse waarde die aangeeft of het type van de opgegeven expressie een getal is. |
| IS_OBJECT | Retourneert een Booleaanse waarde die aangeeft of het type van de opgegeven expressie een JSON-object is. |
| IS_PRIMITIVE | Retourneert een Booleaanse waarde die aangeeft of het type van de opgegeven expressie een primitieve nemen is (tekenreeks, Boole-waarde, numerieke, of `null`). |
| IS_STRING | Retourneert een Booleaanse waarde die aangeeft of het type van de opgegeven expressie een tekenreeks is. |

In situaties, routes, worden de volgende reeks functies ondersteund:

| Functie | Beschrijving |
| -------- | ----------- |
| CONCAT (x, y,...) | Retourneert een tekenreeks die het resultaat van het samenvoegen van twee of meer tekenreekswaarden. |
| LENGTH(x) | Retourneert het aantal tekens van de opgegeven tekenreeksexpressie.|
| LOWER(x) | Retourneert een tekenreeksexpressie na hoofdletter gegevens converteren naar kleine letters. |
| UPPER(x) | Retourneert een tekenreeksexpressie na kleine letter gegevens converteren naar hoofdletters. |
| De SUBTEKENREEKS (tekenreeks, start [, lengte]) | Onderdeel van een tekenreeksexpressie vanaf de op nul gebaseerde positie van het opgegeven teken geretourneerd en blijft aan de opgegeven lengte of aan het einde van de tekenreeks. |
| INDEX_OF (tekenreeks, fragment) | Retourneert de beginpositie van het eerste exemplaar van de tweede tekenreeksexpressie binnen de eerste expressie in de opgegeven tekenreeks, of -1 als de tekenreeks is niet gevonden.|
| STARTS_WITH (x, y) | Retourneert een Booleaanse waarde die aangeeft of de eerste expressie tekenreeks begint met de tweede. |
| ENDS_WITH (x, y) | Retourneert een Booleaanse waarde die aangeeft of de eerste expressie tekenreeks eindigt met de tweede. |
| CONTAINS(x,y) | Retourneert een Booleaanse waarde die aangeeft of de eerste expressie tekenreeks de tweede bevat. |

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het uitvoeren van query's in uw apps met behulp van [Azure IoT SDK's][lnk-hub-sdks].

[lnk-query-where]: iot-hub-devguide-query-language.md#where-clause
[lnk-query-expressions]: iot-hub-devguide-query-language.md#expressions-and-conditions
[lnk-query-getstarted]: iot-hub-devguide-query-language.md#get-started-with-device-twin-queries

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-devguide-messaging-routes]: iot-hub-devguide-messages-read-custom.md
[lnk-devguide-messaging-format]: iot-hub-devguide-messages-construct.md
[lnk-devguide-messaging-routes]: ./iot-hub-devguide-messages-read-custom.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
