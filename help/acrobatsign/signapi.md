---
title: Komma igång med Acrobat Sign API
description: Lär dig inkludera Acrobat Sign API i ditt program för att samla in signaturer och annan information
feature: Acrobat Sign API
role: Developer
level: Intermediate
type: Tutorial
jira: KT-8089
thumbnail: KT-8089.jpg
exl-id: ae1cd9db-9f00-4129-a2a1-ceff1c899a83
source-git-commit: c6272ee4ec33f89f5db27023d78d1f08005b04ef
workflow-type: tm+mt
source-wordcount: '1906'
ht-degree: 0%

---

# Komma igång med Adobe Sign API

[Acrobat Sign API](https://developer.adobe.com/adobesign-api/) är ett bra sätt att förbättra hur du hanterar signerade avtal. Utvecklare kan enkelt integrera sina system med Sign API, som ger ett tillförlitligt och enkelt sätt att ladda upp dokument, skicka dem för signering, skicka påminnelser och samla in e-signaturer.

## Vad du kan lära dig

I den här praktiska självstudiekursen beskrivs hur utvecklare kan använda Sign API för att förbättra program och arbetsflöden som skapats med [!DNL Adobe Acrobat Services]. [!DNL Acrobat Services] innehåller [Adobe PDF Services API](https://developer.adobe.com/document-services/apis/pdf-services), [Adobe PDF Embed API](https://developer.adobe.com/document-services/apis/pdf-embed/) (kostnadsfritt) och [Adobe Document Generation API](https://developer.adobe.com/document-services/apis/doc-generation).

Lär dig mer specifikt hur du inkluderar Acrobat Sign API i programmet för att samla in signaturer och annan information, till exempel information om anställda i ett försäkringsformulär. Allmänna steg med förenklade HTTP-begäranden och -svar används. Du kan implementera de här förfrågningarna på ditt favoritspråk. Du kan skapa en PDF med en kombination av [[!DNL Acrobat Services] API:er](https://developer.adobe.com/document-services/homepage/), överföra den till Sign-API:et som ett [tillfälligt](https://opensource.adobe.com/acrobat-sign/developer_guide/index.html#!adobedocs/adobe-sign/master/overview/terminology.md) dokument och begära slutanvändarsignaturer med avtalet eller [widgeten](https://opensource.adobe.com/acrobat-sign/developer_guide/index.html#!adobedocs/adobe-sign/master/overview/terminology.md).

## Skapa ett PDF-dokument

Börja med att skapa en Microsoft Word-mall och spara den som en PDF. Du kan också automatisera pipelinen med dokumentgenererings-API för att överföra en mall som har skapats i Word och sedan generera ett PDF-dokument. Dokumentgenererings-API:t ingår i [!DNL Acrobat Services], [kostnadsfritt i sex månader och sedan betala per användning för bara eller $0,05 per dokumenttransaktion](https://developer.adobe.com/document-services/pricing/main).

I det här exemplet är mallen bara ett enkelt dokument med några fält för undertecknare att fylla i. Namnge fälten nu och infoga sedan de faktiska fälten i den här självstudiekursen.

![Skärmbild av försäkringsformulär med några fält](assets/GSASAPI_1.png)

## Identifierar den giltiga API-åtkomstpunkten

Innan du arbetar med Sign-API:et måste du [skapa ett kostnadsfritt utvecklarkonto](https://acrobat.adobe.com/ca/en/sign/developer-form.html) för att få åtkomst till API:et, testa dokumentutbytet och körningen och testa e-postfunktionen.

Adobe distribuerar Acrobat Sign API över hela världen i många driftsättningsenheter som kallas &quot;shards&quot;. Varje shard betjänar en kunds konto, t.ex. NA1, NA2, NA3, EU1, JP1, AU1, IN1 och andra. Delningsnamnen motsvarar geografiska platser. Dessa shards utgör bas-URI:n (åtkomstpunkter) för API-slutpunkterna.

Om du vill använda Sign API måste du först identifiera rätt åtkomstpunkt för ditt konto, vilken kan vara api.na1.adobesign.com, api.na4.adobesign.com, api.eu1.adobesign.com eller andra, beroende på din plats.

```
  GET /api/rest/v6/baseUris HTTP/1.1
  Host: https://api.adobesign.com
  Authorization: Bearer {YOUR-INTEGRATION-KEY-HERE}
  Accept: application/json

  Response Body (example):

  {
    "apiAccessPoint": "https://api.na4.adobesign.com/", 
    "webAccessPoint": "https://secure.na4.adobesign.com/" 
  }
```

I ovanstående exempel är ett svar med värdet som åtkomstpunkt.

>[!IMPORTANT]
>
>I detta fall måste alla efterföljande begäranden som du gör till Sign API använda den åtkomstpunkten. Om du använder en åtkomstpunkt som inte fungerar i din region visas ett felmeddelande.

## Ladda upp ett tillfälligt dokument

Med Adobe Sign kan du skapa olika flöden som förbereder dokument för signaturer eller datainsamling. Oavsett programmets flöde måste du först överföra ett dokument som bara är tillgängligt i sju dagar. De efterföljande API-anropen måste sedan referera till det här tillfälliga dokumentet.

Dokumentet laddas upp med en begäran om POST till slutpunkten `/transientDocuments`. Multipart-begäran består av filnamnet, en filström och dokumentfilens MIME-typ (media). Slutpunktssvaret innehåller ett ID som identifierar dokumentet.

Dessutom kan ditt program ange en återanropsadress för Acrobat Sign till ping, som meddelar programmet när signeringsprocessen är klar.


```
  POST /api/rest/v6/transientDocuments HTTP/1.1
  Host: {YOUR-API-ACCESS-POINT}
  Authorization: Bearer {YOUR-INTEGRATION-KEY-HERE}
  x-api-user: email:your-api-user@your-domain.com
  Content-Type: multipart/form-data
  File-Name: "Insurance Form.pdf"
  File: "[path]\Insurance Form.pdf"
  Accept: application/json

  Response Body (example):

  {
     "transientDocumentId": "3AAA...BRZuM"
  }
```

## Skapa ett webbformulär

Webbformulär (som tidigare kallades signeringswidgetar) är värddokument som alla som har åtkomst kan signera. Exempel på webbformulär är registreringsblad, undantag och andra dokument som många har åtkomst till och signerar online.

Om du vill skapa ett nytt webbformulär med Sign-API:et måste du först överföra ett tillfälligt dokument. Begäran om POST till slutpunkten `/widgets` använder den returnerade `transientDocumentId`.

I det här exemplet är webbformuläret `ACTIVE`, men du kan skapa det i ett av tre olika tillstånd:

* UTKAST - för att stegvis skapa webbformuläret

* REDIGERING - för att lägga till eller redigera formulärfält i webbformuläret

* ACTIVE - för att omedelbart vara värd för webbformuläret

Informationen om formulärets deltagare måste också definieras. Egenskapen `memberInfos` innehåller data om deltagarna, t.ex. e-post. För närvarande stöder den här uppsättningen inte fler än en medlem. Men eftersom e-postadressen till webbformulärssigneraren är okänd när webbformuläret skapas, ska e-postadressen lämnas tom, som i följande exempel. Egenskapen `role` definierar rollen som antas av medlemmarna i `memberInfos` (till exempel SIGNER och APPROVER).

```
  POST /api/rest/v6/widgets HTTP/1.1
  Host: {YOUR-API-ACCESS-POINT}
  Authorization: Bearer {YOUR-INTEGRATION-KEY-HERE}
  x-api-user: email:your-api-user@your-domain.com
  Content-Type: application/json

  Request Body:

  {
    "fileInfos": [
      {
      "transientDocumentId": "YOUR-TRANSIENT-DOCUMENT-ID"
      }
     ],
    "name": "Insurance Form",
      "widgetParticipantSetInfo": {
          "memberInfos": [{
              "email": ""
          }],
      "role": "SIGNER"
      },
      "state": "ACTIVE"
  }

  Response Body (example):

  {
     "id": "CBJ...PXoK2o"
  }
```

Du kan skapa ett webbformulär som `DRAFT` eller `AUTHORING` och sedan ändra dess tillstånd när formuläret passerar genom din programpipeline. Om du vill ändra ett webbformulärsläge går du till slutpunkten [PUT /widgets/{widgetId}/state](https://secure.na4.adobesign.com/public/docs/restapi/v6#!/widgets/updateWidgetState).

## Läsa webbformulärets URL

Nästa steg är att identifiera den URL som är värd för webbformuläret. Slutpunkten /widgets hämtar en lista med webbformulärdata, inklusive den värdbaserade URL:en för webbformuläret som du vidarebefordrar till användarna, för att samla in signaturer och annan formulärdata.

Den här slutpunkten returnerar en lista, så du kan hitta det specifika formuläret med dess ID i `userWidgetList` innan du hämtar URL:en som är värd för webbformuläret:

```
  GET /api/rest/v6/widgets HTTP/1.1
  Host: {YOUR-API-ACCESS-POINT}
  Authorization: Bearer {YOUR-INTEGRATION-KEY-HERE}
  Accept: application/json

  Response Body:

  {
    "userWidgetList": [
      {
        "id": "CBJCHB...FGf",
        "name": "Insurance Form",
        "groupId": "CBJCHB...W86",
        "javascript": "<script type='text/javascript' ...
        "modifiedDate": "2021-03-13T15:52:41Z",
        "status": "ACTIVE",
        "Url":
        "https://secure.na4.adobesign.com/public/esignWidget?wid=CBFCIB...Rag*",
        "hidden": false
      },
      {
        "id": "CBJCHB...I8_",
        "name": "Insurance Form",
        "groupId": "CBJCHBCAABAAyhgaehdJ9GTzvNRchxQEGH_H1ya0xW86",
        "javascript": "<script type='text/javascript' language='JavaScript'
        src='https://sec
        "modifiedDate": "2021-03-13T02:47:32Z",
        "status": "ACTIVE",
        "Url":
        "https://secure.na4.adobesign.com/public/esignWidget?wid=CBFCIB...AAB",
        "hidden": false
      },
      {
        "id": "CBJCHB...Wmc",
```

## Hantera webbformuläret

Det här formuläret är ett PDF-dokument som användare kan fylla i. Du måste dock fortfarande tala om för formulärets redigerare vilka fält användarna måste fylla i och var de finns i dokumentet:

![Skärmbild av försäkringsformulär med några fält](assets/GSASAPI_1.png)

Dokumentet ovan visar inte fälten - än. De läggs till när du definierar vilka fält som samlar in signerarens information samt deras storlek och position.

Gå nu till fliken [Webbformulär](https://secure.na4.adobesign.com/public/agreements/#agreement_type=webform) på sidan Dina avtal och leta reda på formuläret du skapade.

![Skärmbild av fliken Hantera i Acrobat Sign](assets/GSASAPI_2.png)

![Skärmbild av fliken Hantera i Acrobat Sign med markerade webbformulär](assets/GSASAPI_3.png)

Klicka på **Redigera** för att öppna dokumentredigeringssidan. De tillgängliga fördefinierade fälten finns på den högra panelen.

![Skärmbild av redigeringsmiljön för Acrobat Sign-formulär](assets/GSASAPI_4.png)

I redigeraren kan du dra och släppa text- och signaturfält. När du har lagt till alla nödvändiga fält kan du ändra storlek på och justera dem för att finputsa formuläret. Klicka slutligen på **Spara** för att skapa formuläret.

![Skärmbild av redigeringsmiljön för Acrobat Sign-formulär med tillagda formulärfält](assets/GSASAPI_5.png)

## Skicka ett webbformulär för signering

När du har slutfört webbformuläret måste du skicka in det så att användarna kan fylla i och signera det. När du har sparat formuläret kan du visa och kopiera webbadressen och den inbäddade koden.

**Kopiera URL för webbformulär**: Använd denna URL för att skicka användare till en värdversion av avtalet för granskning och signering. Till exempel:

[https://secure.na4.adobesign.com/public/esignWidget?wid=CBFCIBAA3...babw\*](https://secure.na4.adobesign.com/public/esignWidget?wid=CBFCIBAA3AAABLblqZhCndYscuKcDMPiVfQlpaGPb-5D7ebE9NUTQ6x6jK7PIs8HCtTzr3HOx8U6D5qqbabw*)

**Kopiera webbformulärets inbäddningskod**: lägg till avtalet på webbplatsen genom att kopiera och klistra in det i HTML.

Till exempel:

```
<iframe
src="https://secure.na4.adobesign.com/public/esignWidget?wid=CBFC
...yx8*&hosted=false" width="100%" height="100%" frameborder="0"
style="border: 0;
overflow: hidden; min-height: 500px; min-width: 600px;"></iframe>
```

![Skärmbild av det slutliga webbformuläret](assets/GSASAPI_6.png)

När användarna öppnar den värdbaserade versionen av formuläret granskar de det tillfälliga dokumentet som först laddats upp med fälten placerade enligt vad som angetts.

![Skärmbild av det slutliga webbformuläret](assets/GSASAPI_7.png)

Användaren fyller sedan i fälten och signerar formuläret.

![Skärmbild av användaren som väljer signaturfält](assets/GSASAPI_8.png)

Därefter signerar användaren dokumentet med en tidigare lagrad signatur eller med en ny.

![Skärmbild av signeringsupplevelsen](assets/GSASAPI_9.png)

![Skärmbild av signatur](assets/GSASAPI_10.png)

När användaren klickar på **Tillämpa** instruerar Adobe användaren att öppna sin e-postadress och bekräfta signaturen. Signaturen förblir väntande tills bekräftelsen kommer.

![Skärmbild av bara ett steg till](assets/GSASAPI_11.png)

Denna autentisering lägger till multifaktorautentisering och stärker signeringsprocessens säkerhet.

![Skärmbild av bekräftelsemeddelande](assets/GSASAPI_12.png)

![Skärmbild av meddelande om slutförande](assets/GSASAPI_13.png)

## Läser slutförda webbformulär

Nu är det dags att hämta de formulärdata som användarna har fyllt i. Slutpunkten `/widgets/{widgetId}/formData` hämtar data som anges av användaren till ett interaktivt formulär när denne signerade formuläret.

```
GET /api/rest/v6/widgets/{widgetId}/formData HTTP/1.1
Host: {YOUR-API-ACCESS-POINT}
Authorization: Bearer {YOUR-INTEGRATION-KEY-HERE}
Accept: text/csv
```

CSV-filströmmen som skapas innehåller formulärdata.

```
Response Body:
"Agreement
name","completed","email","role","first","last","title","company","agreementId",
"email verified","web form signed/approved"
"Insurance Form","","myemail@email.com","SIGNER","John","Doe","My Job Title","My
Company Name","","","2021-03-07 19:32:59"
```

## Skapa ett avtal

Som ett alternativ till webbformulär kan du skapa avtal. I följande avsnitt visas några enkla steg för att hantera avtal med Sign-API:et.

Om du skickar ett dokument till angivna mottagare för signering eller godkännande skapas ett avtal. Du kan spåra status och slutförande av ett avtal med API:er.

Du kan skapa ett avtal med ett [tillfälligt dokument](https://helpx.adobe.com/sign/kb/how-to-send-an-agreement-through-REST-API.html), [biblioteksdokument](https://opensource.adobe.com/acrobat-sign/developer_guide/index.html#!adobedocs/adobe-sign/master/samples/send_using_library_doc.md) eller URL. I det här exemplet baseras avtalet på `transientDocumentId`, precis som webbformuläret som skapades tidigare.

```
POST /api/rest/v6/agreements HTTP/1.1
Host: {YOUR-API-ACCESS-POINT}
Authorization: Bearer {YOUR-INTEGRATION-KEY-HERE}
x-api-user: email:your-api-user@your-domain.com
Content-Type: application/json
Accept: application/json
Request Body:
{
    "fileInfos": [
      {
      "transientDocumentId": "{transientDocumentId}"
      }
     ],
    "name": "{agreementName}",
    "participantSetsInfo": [
      {
      "memberInfos": [
          {
          "email": "{signerEmail}"
          }
        ],
        "order": 1,
        "role": "SIGNER"
      }
    ],
    "signatureType": "ESIGN",
    "state": "IN_PROCESS"
  }
```

I det här exemplet skapas avtalet som IN_PROCESS, men du kan skapa det i ett av tre olika tillstånd:

* DRAFT - för att stegvis skapa avtalet innan det skickas ut

* REDIGERING - för att lägga till eller redigera formulärfält i avtalet

* IN_PROCESS - för att omedelbart skicka avtalet

Om du vill ändra ett avtalstillstånd använder du slutpunkten `PUT /agreements/{agreementId}/state` för att utföra en av de tillåtna tillståndsövergångarna nedan:

* UTKAST TILL REDIGERING

* REDIGERA TILL IN_PROCESS

* PÅGÅENDE_PROCESS TILL AVBRUTEN

Egenskapen `participantSetsInfo` ovan innehåller e-postmeddelanden från personer som förväntas delta i avtalet och vilken åtgärd de utför (signera, godkänna, bekräfta och så vidare). I exemplet ovan finns det bara en deltagare: undertecknaren. Skriftliga signaturer är begränsade till fyra per dokument.

När du skapar ett avtal skickas det automatiskt ut för signering i Adobe, till skillnad från webbformulär. Slutpunkten returnerar avtalets unika identifierare.


```
  Response Body:

  {
     id (string): The unique identifier of the agreement
  }
```

## Hämtar information om avtalsmedlemmar

När du har skapat ett avtal kan du använda slutpunkten `/agreements/{agreementId}/members` för att hämta information om medlemmar i avtalet. Du kan till exempel kontrollera om en deltagare har signerat avtalet.

```
GET /api/rest/v6/agreements/{agreementId}/members HTTP/1.1
Host: {YOUR-API-ACCESS-POINT}
Authorization: Bearer {YOUR-INTEGRATION-KEY-HERE}
Accept: application/json
```

JSON-svarstexten som skapas innehåller information om deltagarna.

```
  Response Body:

  {
     "participantSets":[
        {
           "memberInfos":[
              {
                 "id":"CBJ...xvM",
                 "email":"participant@email.com",
                 "self":false,
                 "securityOption":{
                    "authenticationMethod":"NONE"
                 },
                 "name":"John Doe",
                 "status":"ACTIVE",
                 "createdDate":"2021-03-16T03:48:39Z",
                 "userId":"CBJ...vPv"
              }
           ],
           "id":"CBJ...81x",
           "role":"SIGNER",
           "status":"WAITING_FOR_MY_SIGNATURE",
           "order":1
        }
     ],
```

## Skicka avtalspåminnelser

Beroende på affärsreglerna kan en deadline hindra deltagarna från att signera avtalet efter ett visst datum. Om avtalet har ett förfallodatum kan du påminna deltagarna när det datumet närmar sig.

Baserat på informationen om avtalsmedlemmarna som du fick efter samtalet till slutpunkten `/agreements/{agreementId}/members` i det sista avsnittet kan du utfärda e-postpåminnelser till alla deltagare som fortfarande inte har signerat avtalet.

En påminnelsebegäran till slutpunkten `/agreements/{agreementId}/reminders` skapar en POST för de angivna deltagarna i ett avtal som identifieras av parametern `agreementId`.

```
POST /agreements/{agreementId}/reminders HTTP/1.1
Host: {YOUR-API-ACCESS-POINT}
Authorization: Bearer {YOUR-INTEGRATION-KEY-HERE}
x-api-user: email:your-api-user@your-domain.com
Content-Type: application/json
Accept: application/json
  Request Body:

  {
    "recipientParticipantIds": [{agreementMemberIdList}],
    "agreementId": "{agreementId}",
    "note": "This is a reminder that you haven't signed the agreement yet.",
    "status": "ACTIVE"
  }

  Response Body:

  {
     id (string, optional): An identifier of the reminder resource created on the
     server. If provided in POST or PUT, it will be ignored
  }
```

När du har publicerat påminnelsen får användarna ett e-postmeddelande med avtalsinformationen och en länk till avtalet.

![Skärmbild av påminnelsemeddelande](assets/GSASAPI_14.png)

## Läser slutförda avtal

Precis som webbformulär kan du läsa information om avtal som mottagarna har signerat. Slutpunkten `/agreements/{agreementId}/formData` hämtar data som anges av användaren när webbformuläret signerades.

```
GET /api/rest/v6/agreements/{agreementId}/formData HTTP/1.1
Host: {YOUR-API-ACCESS-POINT}
Authorization: Bearer {YOUR-INTEGRATION-KEY-HERE}
Accept: text/csv
Response Body:
"completed","email","role","first","last","title","company","agreementId"
"2021-03-16 18:11:45","myemail@email.com","SIGNER","John","Doe","My Job Title","My
Company Name","CBJCHBCAABAA5Z84zy69q_Ilpuy5DzUAahVfcNZillDt"
```

## Nästa steg

Med Acrobat Sign API kan du hantera dokument, webbformulär och avtal. De förenklade men kompletta arbetsflödena som skapas med hjälp av webbformulär och avtal görs på ett allmänt sätt som gör att utvecklare kan implementera dem på valfritt språk.

Du hittar exempel på hur Sign API fungerar i [användarhandboken för utvecklare av API](https://opensource.adobe.com/acrobat-sign/developer_guide/index.html#!adobedocs/adobe-sign/master/api_usage.md) om du vill ha en översikt. Den här dokumentationen innehåller korta artiklar om många av de steg som beskrivs i artikeln samt andra relaterade ämnen.

Acrobat Sign API finns tillgängligt via flera nivåer av [e-signeringsplaner för en eller flera användare](https://acrobat.adobe.com/se/sv/sign/pricing/plans.html), så att du kan välja en prismodell som passar dina behov bäst. Nu när du vet hur enkelt det är att införliva Sign API i dina appar kan du vara intresserad av andra funktioner som [Acrobat Sign Webhooks](https://opensource.adobe.com/acrobat-sign/developer_guide/index.html#!adobedocs/adobe-sign/master/webhooks.md), en push-baserad programmeringsmodell. I stället för att kräva att appen utför frekventa kontroller i Acrobat Sign-händelser kan du med webhookar registrera en HTTP-URL som Sign API kör en återanropsbegäran för POST för när en händelse inträffar. Webhookar möjliggör robust programmering genom att driva ditt program med realtids- och direktuppdateringar.

Ta en titt på [fördelningspriset](https://developer.adobe.com/document-services/pricing/main), när din sex månader långa kostnadsfria provperiod på Adobe PDF Services API tar slut, och det kostnadsfria Adobe PDF Embed API.

Kom igång med [[!DNL Adobe Acrobat Services]](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) om du vill lägga till spännande funktioner som automatiskt dokumentskapande och dokumentsignering i programmet.
