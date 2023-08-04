---
title: Modernisera medarbetarnas introduktion
description: Lär dig modernisera medarbetarnas introduktion med [!DNL Adobe Acrobat Services] API:er
feature: Use Cases
role: Developer
level: Intermediate
type: Tutorial
jira: KT-10203
thumbnail: KT-10203.jpg
exl-id: 0186b3ee-4915-4edd-8c05-1cbf65648239
source-git-commit: 5222e1626f4e79c02298e81d621216469753ca72
workflow-type: tm+mt
source-wordcount: '1514'
ht-degree: 1%

---

# Modernisera medarbetarnas introduktion

![Banderoll för användningsfall](assets/usecaseemployeeonboardinghero.jpg)

I en stor organisation kan de anställdas introduktion vara en stor och långsam process. Vanligtvis finns det en blandning av skräddarsydd dokumentation tillsammans med brödplattematerial som måste presenteras och undertecknas av en ny anställd. Denna blandning av skräddarsytt material och material med kokplatta kräver flera steg - vilket tar värdefull tid från människor som är inblandade i processen. [!DNL Adobe Acrobat Services] och Acrobat Sign kan modernisera och automatisera den här metoden, så att du kan frigöra personal inom HR för viktigare uppgifter. Låt oss titta på hur detta uppnås.

## Vad är [!DNL Adobe Acrobat Services]?

[[!DNL Adobe Acrobat Services]](https://developer.adobe.com/document-services/homepage) är en uppsättning API:er som är relaterade till att arbeta med dokument (och inte bara PDF). I stort sett kan denna uppsättning tjänster delas in i tre huvudkategorier:

* De första är [PDF-tjänster](https://developer.adobe.com/document-services/apis/pdf-services/) uppsättning verktyg. Det här är &quot;nyttometoder&quot; för att arbeta med PDF och andra dokument. I tjänsterna ingår saker som att konvertera till och från PDF, utföra OCR och optimering, sammanfoga och dela PDF och så vidare. Det är verktygslådan för dokumentbearbetning funktioner.
* [PDF Extract API](https://developer.adobe.com/document-services/apis/pdf-extract/) använder kraftfulla AI-/ML-tekniker för att analysera en PDF och returnera en otrolig mängd information om innehållet. Detta inkluderar text, formatering och positionsinformation och kan även returnera tabelldata i CSV/XLS-format samt hämta bilder.
* Slutligen [API för dokumentgenerering](https://developer.adobe.com/document-services/apis/doc-generation/) gör att utvecklare kan använda Microsoft Word som en &quot;mall&quot;, blanda med sina data (från vilken källa som helst) och generera dynamiska personliga dokument (PDF och Word).

Utvecklare kan [bli medlem](https://documentcloud.adobe.com/dc-integration-creation-app-cdn/main.html) och prova alla dessa tjänster med en kostnadsfri provperiod. Inställningen [!DNL Acrobat Services] plattformen använder ett REST-baserat API men stöder även SDK:er för Node, Java, .NET och Python (endast Extract för närvarande).

Även om de inte är ett API kan utvecklare också använda det kostnadsfria [PDF Embed API](https://developer.adobe.com/document-services/apis/pdf-embed/), som ger en konsekvent och flexibel visningsupplevelse av dokument med dina webbsidor.

## Vad är Acrobat Sign?

[Acrobat Sign](https://www.adobe.com/se/sign.html) är världsledande inom tjänster för elektroniska signaturer. Du kan skicka dokument för signering med olika arbetsflöden, inklusive flera signaturer. Acrobat Sign stöder även arbetsflöden som kräver signaturer och ytterligare information. Alla dessa funktioner stöds av en kraftfull instrumentpanel med ett flexibelt redigeringssystem.

Som med [!DNL Acrobat Services], Acrobat Sign har en [kostnadsfri testversion](https://www.adobe.com/sign.html#sign_free_trial) som gör det möjligt för utvecklare att testa signeringsprocessen både via kontrollpanelen och med ett lättanvänt REST-baserat API.

## Ett introduktionsscenario

Tänk på ett realistiskt scenario som visar hur Adobe tjänster kan hjälpa dig. När en nyanställd går med i ett företag behöver de skräddarsydd information som passar deras roll. Dessutom behöver de material som omfattar hela företaget. Slutligen måste de visa att de accepterar företagspolicyer genom att underteckna dokumenten. Nu ska vi dela upp det i konkreta steg:

* Först behövs ett anpassat följebrev som hälsar den nye medarbetaren med namn. Brevet ska innehålla information om medarbetarens namn, roll, lön och plats.
* Det anpassade brevet måste kombineras med en PDF som innehåller grundläggande företagsinformation (tänk olika HR-policyer, förmåner etc.)
* Ett slutdokument måste inkluderas som frågar efter den anställdes signatur och datum.
* Alla ovanstående dokument ska visas som ett dokument som skickas till medarbetaren för signering.

Nu går vi in på detaljer om hur du gör det.

## Generera dynamiska dokument

Adobe [Dokumentgenerering](https://developer.adobe.com/document-services/apis/doc-generation/) Med API kan utvecklare skapa dynamiska dokument genom att använda Microsoft Word och ett enkelt mallspråk som bas för att skapa PDF- och Word-dokument. Här är ett exempel på hur detta fungerar.

Låt oss börja med ett Word-dokument som har hårdkodade värden. Du kan formatera dokumentet som du vill, t.ex. som grafik, tabeller osv. Här är det ursprungliga dokumentet.

![Skärmbild av det första dokumentet](assets/onboarding_1.png)

Dokumentgenerering fungerar genom att lägga till &quot;tokens&quot; i ett Word-dokument som ersätts med dina data. Även om dessa token kan anges manuellt finns det en [Microsoft Word-tillägg](https://developer.adobe.com/document-services/docs/overview/document-generation-api/wordaddin/) som gör detta lättare att göra. Om du öppnar det får du ett verktyg som författare kan använda för att definiera taggar, eller datauppsättningar, som kan användas i ditt dokument.

![Skärmbild av dokumenttagg](assets/onboarding_2.png)

Du kan antingen överföra JSON-information från en lokal fil, kopiera i JSON-text eller välja att fortsätta med ursprungliga data. På så sätt kan du definiera dina taggar ad hoc-baserat på dina särskilda behov. I det här exemplet behövs bara en tagg för namn, roll, lön och plats. Detta görs genom att använda **Skapa tagg** knapp:

![Skärmbild av definition av en tagg](assets/onboarding_3.png)

När du har definierat det första märkordet kan du fortsätta att definiera så många du behöver:

![Skärmbild av definierade taggar](assets/onboarding_4.png)

Med märkorden definierade markerar du texten i dokumentet och ersätter den med märkorden vid behov. I det här exemplet läggs taggar till för namn, roll och lön.

![Skärmbild av taggar](assets/onboarding_5.png)

Dokumentgenerering stöder inte bara enkla taggar utan även logiska uttryck. Det andra stycket i dokumentet har en text som endast gäller för personer i Louisiana. Du kan lägga till ett villkorsuttryck genom att gå till fliken Avancerat i dokumenttaggen och definiera ett villkor. Så här definierar du ett enkelt jämlikhetsvillkor, men observera att numeriska jämförelser och andra jämförelsetyper också stöds.

![Skärmbild av tillståndet](assets/onboarding_6.png)

Detta kan sedan infogas och lindas runt stycket:

![Skärmbild av tillståndet i dokument](assets/onboarding_7.png)

Om du vill testa hur det fungerar väljer du **Generera dokument**. Första gången du gör det måste du logga in med ett Adobe ID. När du har loggat in visas standard-JSON som kan redigeras manuellt.

![Skärmbild av data](assets/onboarding_8.png)

En PDF genereras som sedan kan visas eller hämtas.

![Skärmbild av det genererade PDF](assets/onboarding_9.png)

Med Document Tagger kan du snabbt designa och testa, när det är klart och under produktion, men du kan använda en av SDK:erna för att automatisera denna process. Även om den faktiska koden skiljer sig åt beroende på specifika behov, här är ett exempel på hur den här koden ser ut i Node.js:

```js
 const PDFServicesSdk = require('@adobe/pdfservices-node-sdk');

const credentials =  PDFServicesSdk.Credentials
    .serviceAccountCredentialsBuilder()
    .fromFile("pdfservices-api-credentials.json")
    .build();

// Data would be dynamic...
let data = {
    "name":"Raymond Camden",
    "role":"Lead Developer",
    "salary":9000,
    "location":"Louisiana"
}

// Create an ExecutionContext using credentials.
const executionContext = PDFServicesSdk.ExecutionContext.create(credentials);

// Create a new DocumentMerge options instance.
const documentMerge = PDFServicesSdk.DocumentMerge,
    documentMergeOptions = documentMerge.options,
    options = new documentMergeOptions.DocumentMergeOptions(jsonDataForMerge, documentMergeOptions.OutputFormat.PDF);

// Create a new operation instance using the options instance.
const documentMergeOperation = documentMerge.Operation.createNew(options);

// Set operation input document template from a source file.
const input = PDFServicesSdk.FileRef.createFromLocalFile('documentMergeTemplate.docx');
documentMergeOperation.setInput(input);

// Execute the operation and Save the result to the specified location.
documentMergeOperation.execute(executionContext)
    .then(result => result.saveAsFile('documentOutput.pdf'))
    .catch(err => {
        if(err instanceof PDFServicesSdk.Error.ServiceApiError
            || err instanceof PDFServicesSdk.Error.ServiceUsageError) {
            console.log('Exception encountered while executing operation', err);
        } else {
            console.log('Exception encountered while executing operation', err);
        }
    });
```

Kort sagt ställer koden in autentiseringsuppgifter, skapar ett åtgärdsobjekt och ställer in indata och alternativ och anropar sedan åtgärden. Slutligen sparar det resultatet som en PDF. (Resultat kan skrivas ut som Word också.)

Dokumentgenerering stöder mycket mer komplexa användningsfall, inklusive möjligheten att ha helt dynamiska tabeller och bilder. Se [dokumentation](https://developer.adobe.com/document-services/docs/overview/document-generation-api/) för mer information.

## Utföra PDF-åtgärder

Inställningen [PDF Services API](https://developer.adobe.com/document-services/apis/pdf-services/) tillhandahåller en stor uppsättning &quot;nyttoåtgärder&quot; för att arbeta med PDF. Dessa åtgärder omfattar:

* Skapa PDF från Office-dokument
* Exportera PDF till Office-dokument
* Kombinera och dela upp PDF
* Använda OCR på PDF
* Ställa in, ta bort och ändra skydd på PDF
* Ta bort, infoga, ordna om och rotera sidor
* Optimera PDF via komprimering eller linearisering
* Hämtar egenskaper för PDF

I det här scenariot måste resultatet av dokumentgenereringsanropet sammanfogas med ett standard-PDF. Denna operation är ganska enkel med SDK:erna. Här är ett exempel på i Node.js:

```js
const PDFServicesSdk = require('@adobe/pdfservices-node-sdk');
 
// Initial setup, create credentials instance.
const credentials = PDFServicesSdk.Credentials
    .serviceAccountCredentialsBuilder()
    .fromFile("pdfservices-api-credentials.json")
    .build();
 
// Create an ExecutionContext using credentials and create a new operation instance.
const executionContext = PDFServicesSdk.ExecutionContext.create(credentials),
    combineFilesOperation = PDFServicesSdk.CombineFiles.Operation.createNew();
 
// Set operation input from a source file.
const combineSource1 = PDFServicesSdk.FileRef.createFromLocalFile('documentOutput.pdf'),
      combineSource2 = PDFServicesSdk.FileRef.createFromLocalFile('standardCorporate.pdf');

combineFilesOperation.addInput(combineSource1);
combineFilesOperation.addInput(combineSource2);
 
// Execute the operation and Save the result to the specified location.
combineFilesOperation.execute(executionContext)
    .then(result => result.saveAsFile('combineFilesOutput.pdf'))
    .catch(err => {
        if (err instanceof PDFServicesSdk.Error.ServiceApiError
            || err instanceof PDFServicesSdk.Error.ServiceUsageError) {
            console.log('Exception encountered while executing operation', err);
        } else {
            console.log('Exception encountered while executing operation', err);
        }
    });
```

I den här koden tas de två PDF, de slås samman och resultatet sparas i ett nytt PDF. Enkelt och enkelt! Se [dokument](https://developer.adobe.com/document-services/docs/overview/pdf-services-api/) för exempel på vad som kan göras.

## Signeringsprocessen

I det sista steget i registreringsprocessen måste medarbetaren signera ett avtal som anger att de har läst och godkänner alla policyer som definieras i. [Acrobat Sign](https://www.adobe.com/se/sign.html) stöder många olika arbetsflöden och integrationer, inklusive ett automatiserat via en [API](https://opensource.adobe.com/acrobat-sign/developer_guide/index.html). I stort sett kan den sista delen av scenariot kompletteras på följande sätt:

Designa först dokumentet som innehåller formuläret som behöver signeras. Det finns flera sätt att göra det på, bland annat en visuell bild som är utformad på Adobe Sign användarkontrollpanel. Ett annat alternativ är att använda Word-tillägget för dokumentgenerering för att infoga taggarna åt dig. I det här exemplet begärs en signatur och ett datum.

![Skärmbild av dokument med Sign-taggar](assets/onboarding_10.png)

Det här dokumentet kan sparas som en PDF och användas på samma sätt som beskrivs ovan tillsammans med alla dokument. Den här processen skapar ett sammanhängande paket som innehåller en personlig hälsning, standarddokumentation för företaget och en sista sida som är lämplig för signering.

Mallen kan överföras till Acrobat Sign-kontrollpanelen och sedan användas för nya avtal. Genom att använda REST API:et kan det här dokumentet sedan skickas till den potentiella medarbetaren för att begära deras signatur.

![Skärmbild av signerat dokument](assets/onboarding_11.png)

## Upplev det själv

Allt som beskrivs i den här artikeln kan testas just nu. Inställningen [!DNL Adobe Acrobat Services] API [kostnadsfri testversion](https://documentcloud.adobe.com/dc-integration-creation-app-cdn/main.html) för närvarande får du 1 000 kostnadsfria förfrågningar under en sexmånadersperiod. Acrobat Sign [kostnadsfri testversion](https://www.adobe.com/sign.html#sign_free_trial) låter dig skicka vattenstämplade avtal för teständamål.

Har du frågor? Inställningen [supportforum](https://community.adobe.com/t5/document-services-apis/ct-p/ct-Document-Cloud-SDK) övervakas av Adobe utvecklare och support folk varje dag. Om du vill bli mer inspirerad kan du fånga nästa [Pappersklipp](https://www.youtube.com/playlist?list=PLcVEYUqU7VRe4sT-Bf8flvRz1XXUyGmtF) avsnitt. Det hålls regelbundna livemöten med nyheter, demos och samtal med kunder.
