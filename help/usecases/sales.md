---
title: Hantera försäljningsförslag och avtal
description: Lär dig skapa ett effektivt arbetsflöde för att automatisera och förenkla offerter
role: Developer
level: Intermediate
type: Tutorial
thumbnail: KT-8099.jpg
kt: 8099
exl-id: 219c70de-fec1-4946-b10e-8ab5812562ef
source-git-commit: 799b37e526073893fe7c078db547798d6c31d1b2
workflow-type: tm+mt
source-wordcount: '1395'
ht-degree: 2%

---

# Hantera försäljningsförslag och kontrakt

![BANDEROLL MED ANVÄNDNINGSFALL](assets/UseCaseSalesHero.jpg)

Säljförslag är det första steget i ett företags resa mot kundförvärv. Som med allt varar de första intrycken. Så din första interaktion med kunderna sätter deras förväntningar på verksamheten. Ert förslag måste vara koncist, exakt och bekvämt.

Kontrakt och förslag innehåller olika typer av data i dokumentstrukturen. De innehåller både dynamiska data (klientnamn, offertmängd och så vidare) och statiska data (annan text som företagsfunktioner, teamprofiler och standardvillkor för verksamhetsåtagande). Att skapa malldokument, till exempel försäljningsförslag, innebär ofta monotona uppgifter, som att manuellt ersätta projektinformation i en mall. I den här självstudiekursen använder du dynamiska data och arbetsflöden för att skapa en effektiv process för [skapa försäljningsförslag](https://www.adobe.io/apis/documentcloud/dcsdk/sales-proposals-and-contracts.html).

## Vad du kan lära dig

I den här praktiska självstudiekursen får du lära dig hur du implementerar dynamiska data och arbetsflöden med hjälp av flera verktyg, varav de viktigaste är [!DNL Adobe Acrobat Services] API:er. Dessa API:er används för att göra säljförslag och kontrakt mer praktiska för dig och företaget. I den här självstudiekursen demonstreras praktiska tekniker som visar hur du skapar, sammanfogar och visar PDF-dokument automatiskt. Att utföra dessa uppgifter manuellt är tidskrävande och omständligt. Genom att utnyttja [!DNL Acrobat Services] API:er kan du förkorta tiden som krävs för dessa uppgifter.

## Relevanta API:er och resurser

* [Microsoft Word](https://www.office.com/)

* [Node.js](https://nodejs.org/en/)

* [npm](https://www.npmjs.com/get-npm)

* [[!DNL Acrobat Services] APIs](https://www.adobe.io/apis/documentcloud/dcsdk/)

* [Adobe-API för dokumentgenerering](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html)

* [Adobe Sign API](https://www.adobe.io/apis/documentcloud/sign.html)

* [Adobe-dokumentgenereringstagg](https://opensource.adobe.com/pdftools-sdk-docs/docgen/latest/wordaddin.html#add-in-demo)

## Lösning av problemet

Nu när du har verktygen installerade kan du börja lösa problemet. Förslagen har både statiskt innehåll och dynamiskt innehåll som är unikt för varje kund. Flaskhalsar uppstår eftersom båda typerna av data är nödvändiga varje gång du lägger fram ett förslag. Det är tidskrävande att ange statisk text, så du kommer att automatisera den och bara hantera manuellt med dynamiska data från varje klient.

Skapa först ett datainsamlingsformulär i [Microsoft Forms](https://www.office.com/launch/forms?auth=1) (eller det formulärverktyg du föredrar). Det här formuläret är avsett för dynamiska data från kunder som läggs till i ett försäljningsförslag. Fyll i formuläret med frågor för att få den information du behöver från kunderna - till exempel företagsnamn, datum, adress, projektets omfattning, prissättning och ytterligare kommentarer. Om du vill skapa en egen [form](https://forms.office.com/Pages/ShareFormPage.aspx id=DQSIkWdsW0yxEjajBLZtrQAAAAAAAAAAN__rtiGj5UNElTR0pCQ09ZNkJRUlowSjVQWDNYUEg2RC4u&amp;sharetoken=1AJ eMavBAzzxuISRKmUy). Målet är att potentiella kunder ska fylla i formuläret och sedan exportera sina svar som JSON-filer, som skickas till nästa del av arbetsflödet.

I vissa formulärverktyg kan du bara exportera data som CSV-filer. Det kan vara praktiskt att [konvertera](http://csvjson.com/csv2json) den genererade CSV-filen till en JSON-fil.

De statiska uppgifterna återanvänds i varje försäljningsförslag. Du kan alltså använda en försäljningsförslagsmall i Microsoft Word för att ange statisk text. Du kan använda den här [mall](https://1drv.ms/w/s!AiqaN2pp7giKkmhVu2_2pId9MiPa?e=oeqoQ2), men du kan skapa en egen eller använda en [Adobe-mall](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html).

Nu behöver du något som hämtar både dynamikdata från kunder i JSON-format och statisk text i Microsoft Word-mallen för att kunna skapa ett unikt försäljningsförslag för en kund. Den [!DNL Acrobat Services] API:er används för att sammanfoga de två och generera en PDF som kan signeras.

Du använder taggar för att få detta att fungera. Taggar är lättanvända strängar som kan representera tal, ord, arrayer eller till och med komplexa objekt. Taggar fungerar som platshållare för dynamiska data, vilket i det här fallet är klientdata som anges i formuläret. När du har infogat taggar i mallen kan du mappa formulärfält från JSON-filen till Word-mallen.

## Använda taggar

Öppna mallen för försäljningsförslag och välj **Infoga** -fliken. I dialogrutan **Tillägg** grupp väljer du **Hämta tillägg**. Välj sedan **Adobe-tillägg för dokumentgenerering** för att lägga till den. När du har lagt till taggen Dokumentgenerering visas på **Startsida** -fliken på **Adobe** grupp.

På **Startsida** -fliken på **Adobe** grupp väljer du **Dokumentgenerering** för att komma igång med att tagga dokumentet. En praktisk demonstrationsvideo visas på en panel till höger i fönstret.

![Skärmdump av tillägget Document Tagger i Word](assets/sales_1.png)

Välj **Kom igång**. Du ombeds sedan att tillhandahålla exempeldata. Klistra in eller överför JSON-filen för formulärsvaret enligt nedan.

![Skärmbild av inklistring av exempelkod](assets/sales_2.png)

Välj **Generera taggar** för att hämta en lista med fält från JSON-filen som du klistrade in eller överförde. Taggarna visas nedan i sidopanelen till höger.

![Skärmdump av tillgängliga taggar](assets/sales_3.png)

När du har genererat taggarna kan du infoga dem i dokumentet. Taggar läggs till i dokumentet vid markörens plats. Som visas ovan bör du lägga till **Projektets omfattning** under **Projektomfång** undertitel. På så sätt, när en kund anger projektets omfattning i formuläret, deras svar går under **Projektets omfattning** underrubrik, ersätter taggen som du just lade till. När du har lagt till taggar ska en del av dokumentet se ut som skärmbilden nedan.

![Skärmdump av att lägga till taggar i Word-dokument](assets/sales_4.png)

## Använda API:er

Gå till [!DNL Acrobat Services] API:er [hemsida](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html). Så här börjar du använda [!DNL Acrobat Services] API:er, du behöver autentiseringsuppgifter för programmet. Bläddra nedåt hela vägen och välj **Starta kostnadsfri testversion** för att skapa inloggningsuppgifter. Du kan använda dessa tjänster [utan kostnad i sex månader och sedan betala per användning](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) för bara 0,05 USD per dokumenttransaktion, så du betalar bara för det du behöver.

Välj **PDF Services API** som den tjänst du väljer och fyll i övriga uppgifter som visas nedan.

![Skärmbild för att hämta referenser](assets/sales_5.png)

När du har skapat inloggningsuppgifterna får du några kodexempel. Välj önskat språk (självstudiekursen använder Node.js). Dina API-uppgifter finns i en zip-fil. Extrahera filerna till PDFToolsSDK-Node.jsSamples.

Starta genom att skapa en tom mapp med namnet auto-doc\*\*.\*\* Kör följande kommando i mappen för att initiera ett Node.js -projekt: `npm init`. Ge projektet namnet &quot;auto-doc&quot;*.*

I mappen ./PDFToolsSDK-Node.jsSamples/adobe-dc-pdf-tools-sdk-node-samples finns en fil som heter pdftools-api-credentials.json. Flytta den och private.key till mappen auto-doc . Det innehåller dina API-uppgifter. I mappen auto-doc skapar du också en undermapp som heter &quot;resources&quot;. Den innehåller JSON-formaterade data som tas emot från kunder när du genererar ett försäljningsförslag. Spara försäljningsförslagsmallen från Microsoft Word i samma mapp.

Nu är du redo att göra lite magi! Eftersom du använder Node.js i den här självstudiekursen måste du installera Node.js [!DNL Acrobat Services] SDK. Om du vill göra det kör du garn och lägger till @adobe/documentservices-pdftools-node-sdk i mappen auto-doc.

Skapa nu en fil med namnet merge.js och klistra in följande kod i den.

```
javascript
const PDFToolsSdk = require('@adobe/documentservices-pdftools-node-sdk'),
fs = require('fs');
try {
// Initial setup, create credentials instance.
const credentials = PDFToolsSdk.Credentials
.serviceAccountCredentialsBuilder()
.fromFile("pdftools-api-credentials.json")
.build();
// Setup input data for the document merge process
const jsonString = fs.readFileSync('resources/Proposal.json'),
jsonDataForMerge = JSON.parse(jsonString);
// Create an ExecutionContext using credentials
const executionContext = PDFToolsSdk.ExecutionContext.create(credentials);
// Create a new DocumentMerge options instance
const documentMerge = PDFToolsSdk.DocumentMerge,
documentMergeOptions = documentMerge.options,
options = new documentMergeOptions.DocumentMergeOptions(jsonDataForMerge, documentMergeOptions.OutputFormat.PDF);
// Create a new operation instance using the options instance
const documentMergeOperation = documentMerge.Operation.createNew(options)
// Set operation input document template from a source file.
const input = PDFToolsSdk.FileRef.createFromLocalFile('resources/Proposal.docx');
documentMergeOperation.setInput(input);
// Execute the operation and Save the result to the specified location.
documentMergeOperation.execute(executionContext)
.then(result => result.saveAsFile('output/Proposal.pdf'))
.catch(err => {
if (err instanceof PDFToolsSdk.Error.ServiceApiError
|| err instanceof PDFToolsSdk.Error.ServiceUsageError) {
console.log('Exception encountered while executing operation', err);
} else {
console.log('Exception encountered while executing operation', err);
}
});
} catch (err) {
console.log('Exception encountered while executing operation', err);
}
```

Den här koden hämtar JSON-filen från Microsoft-formuläret med hjälp av de taggar du har skapat med [!DNL Acrobat Services]. Sedan sammanfogas uppgifterna med den säljförslagsmall du har skapat i Microsoft Word för att skapa ett helt nytt PDF. PDF sparas i det nyskapade ./utdatamapp.

Koden använder [Adobe Sign API](https://www.adobe.io/apis/documentcloud/sign.html) om du vill att båda företagen ska signera det genererade försäljningsförslaget. I det här blogginlägget finns en detaljerad förklaring av API:et.

## Nästa steg

Du började med en ineffektiv, långtråkig process som behövde automatiseras. Du gick från att skapa dokument för varje kund manuellt till att skapa ett smidigt arbetsflöde för att automatisera och förenkla [försäljningsförslagsprocessen](https://www.adobe.io/apis/documentcloud/dcsdk/sales-proposals-and-contracts.html).

Med Microsoft Forms får ni viktiga data från era kunder som följer deras egna förslag. Du har skapat en mall för försäljningsförslag i Microsoft Word för att ange statisk text som du inte vill återskapa varje gång. Sedan använde du [!DNL Acrobat Services] API:er för att sammanfoga data från formuläret och mallen för att skapa ett försäljningsförslag PDF för dina kunder på ett effektivare sätt.

Den här praktiska självstudiekursen är bara en glimt av vad som är möjligt med dessa API:er. Om du vill hitta fler lösningar går du till [[!DNL Adobe Acrobat Services]](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) API-sida. Använd alla dessa verktyg är gratis i sex månader. Betala sedan bara 0,05 USD per dokumenttransaktion på [betala per användning](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) så att du bara betalar när teamet lägger till fler potentiella kunder i säljprojektet.
