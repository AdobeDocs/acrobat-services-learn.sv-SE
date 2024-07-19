---
title: Skapa ett sekretessavtal
description: Lär dig skapa en dynamisk PDF för sekretessavtal för samarbete
feature: Use Cases
role: Developer
level: Intermediate
type: Tutorial
jira: KT-8098
thumbnail: KT-8098.jpg
exl-id: f4ec0182-a46e-43aa-aea3-bf1d19f1a4ec
source-git-commit: 5222e1626f4e79c02298e81d621216469753ca72
workflow-type: tm+mt
source-wordcount: '1072'
ht-degree: 0%

---

# Skapa ett sekretessavtal

![Banderoll för användningsfall](assets/UseCaseNDAHero.jpg)

Organisationer samarbetar med externa bidragsgivare för att bygga upp sina tjänster och produkter. Ett sekretessavtal är en viktig del av dessa samarbeten. Det binder alla parter från att lämna ut konfidentiell information som kan skada någon av enheterna.

Det vanligaste NDA-formatet är PDF-dokument. Organisationer förbereder ett sekretessavtal och skickar det till alla parter. Sedan, när alla har undertecknat, de initiera kontraktet. I ett höghastighetsteam saktar manuellt skapande av PDF ner utvecklingen.

## Vad du kan lära dig

I den här praktiska självstudiekursen beskrivs hur du skapar en specialiserad Microsoft Word NDA-mall för ditt företag. Adobe kostnadsfria tillägg för Microsoft Word, [Taggen för dokumentgenerering i Adobe](https://opensource.adobe.com/pdftools-sdk-docs/docgen/latest/wordaddin.html#add-in-demo), infogar &quot;taggar&quot; för att mata in de dynamiska värdena. Lär dig skicka JSON-data till mallen och skapa ett dynamiskt PDF. PDF som skapas kan skickas med e-post eller visas för dina medarbetare i deras webbläsare, beroende på dina affärsbehov och mål. Häng med! Du behöver bara uppleva lite med Node.js, JavaScript, Express.js, HTML och CSS.

## Relevanta API:er och resurser

Med [!DNL Adobe Acrobat Services] kan du generera PDF-dokument snabbt med hjälp av dynamiska data. [!DNL Acrobat Services] har en uppsättning PDF-verktyg, inklusive dokumentgenererings-API för Adobe för att automatisera [skapande av NDA](https://www.adobe.io/apis/documentcloud/dcsdk/nda-creation.html).

* [Adobe-dokumentgenererings-API](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html)

* [Adobe Sign API](https://www.adobe.io/apis/documentcloud/sign.html)

* [Genereringstagg för Adobe-dokument](https://opensource.adobe.com/pdftools-sdk-docs/docgen/latest/wordaddin.html#add-in-demo)

* [Projektkod](https://github.com/afzaal-ahmad-zeeshan/adobe-docugen-sample)

* [[!DNL Acrobat Services] nycklar](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html#getcred)

## Skapa JSON-modellen

Microsoft Word-mallen beror på JSON-modellen, så du måste skapa den först. I självstudiekursen använder du en grundläggande JSON-struktur som innehåller företagsinformation, till exempel kontaktinformation.

```
{
"vendor": {
"companyName": "GlobalCorp",
"street": "123 Any Street",
"street2": "",
"city":"Anywhere",
"state":"CA",
"primaryContact": {
"firstName":"John",
"lastName":"Doe",
"email":"john-doe@example.com",
"phone":"123-456-7890"
}
},
"authorizedSigner": {
"firstName": "Sarah",
"lastName": "Rose",
"email": "sarah@example.com",
"phone":"555-555-1234"
}
}
```

Du använder den här strukturen i Microsoft Word för att skapa en mall. Dessa data kan komma från vilken datakälla som helst, bara de är i JSON-format. För enkelhetens skull skapar du flera filer i programmet Node.js, men ditt användningsfall kan kräva en databasanslutning för att hämta leverantörsinformation.

## Skapa Microsoft Word-mallen

Skapa NDA-mallen i ett Microsoft Word-dokument. Adobe PDF Services API förväntar sig att Microsoft Word-dokumentet innehåller taggar där tjänsten kan mata in värden från JSON-dokument. Även om mallen är densamma för alla förfrågningar till Adobe, ändras dynamiken i JSON. Med hjälp av dessa taggar kan du skapa PDF-dokument för alla leverantörer i det här fallet genom att använda en enda Microsoft Word-mall och påskynda processen genom att automatisera genereringen av NDA-dokument.

Du kan installera [det kostnadsfria tillägget för dokumentgenereringstaggar](https://opensource.adobe.com/pdftools-sdk-docs/docgen/latest/wordaddin.html#add-in-demo) i Microsoft Word. Om du ingår i ett företag kan du begära att din Microsoft Office-administratör installerar det kostnadsfria tillägget för alla.

När du har installerat tillägget hittar du det på fliken Hem under kategorin Adobe. Välj **Dokumentgenerering** för att öppna fliken:

![Skärmbild av tillägget för dokumentgenerering i Word](assets/nda_1.png)

På fliken kan du överföra JSON-exempeldokumentet. Det här dokumentet kan vara ett exempel eftersom du bara använder det för att skapa en Microsoft Word-mall.

![Skärmbild av exempeldata i tillägget för dokumentgenerering](assets/nda_2.png)

Välj **Generera taggar** för att visa objekt som du kan använda i mallen. Här är egenskaperna som extraherats från JSON-strukturen och är klara att användas i mallen:

![Skärmbild av texttaggar i tillägget för dokumentgenerering](assets/nda_3.png)

Detta är funktionerna från fältet `authorizedSigner`. Andra fält kapslas och du kan expandera vyn i Microsoft Word. Tillägget erbjuder även avancerade dataalternativ, till exempel tabeller, listor, beräknade värden och mycket mer.

## Skapa taggarna

Du kan skapa en mall eller importera en [befintlig mall](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html#sample-blade) till Microsoft Word. När du har konfigurerat dokumentet lägger du till taggar i varje fält genom att klicka på motsvarande token i tillägget.

Följande mall i en Microsoft Word-fil:

![Skärmbild av exempelmall](assets/nda_4.png)

Den här filen innehåller flera taggar. När du kör programmet fylls dessa fält med leverantörsinformationen.

Taggen för dokumentgenerering kan integreras med Adobe Sign API. På grund av denna integrering kan du automatiskt skapa Sign-texttaggar så att det genererade dokumentet sedan kan skickas till Adobe Sign för signering.

## Genererar sekretessavtal för leverantörer

I exempelprogrammet har du förberett mappar för in- och utdata. Som tidigare nämnts använder du JSON-filer så att det finns två filer för att visa de tillgängliga leverantörerna i systemet. Filerna visas i ett formulär som skrivs ut i webbläsaren:

```
<h1><b>NDA</b>: Generate for vendor.</h1>
<hr />
<p>Following ({{files.length}}) vendors are ready, select to generate NDA and deliver for signature:</p>
<form method="POST">
<ul>
{{#each files }}
<li><input type="checkbox" name="vendor" value="{{this}}" id="file-{{@index}}" /> <label for="file-{{@index}}">{{this}}</label></li>
{{/each}}
</ul>
<input type="submit" value="Create NDA" />
</form>
```

Den här koden genererar följande användargränssnitt (UI) i webbläsaren:

![Skärmbild av användargränssnittet Skapa NDA](assets/nda_5.png)

När administratören väljer en person använder appen Adobe PDF-tjänster för att generera sekretessavtal var som helst.

```
async function compileDocFile(json, inputFile, outputPdf) {
try {
// configurations
const credentials = adobe.Credentials
.serviceAccountCredentialsBuilder()
.fromFile("./src/pdftools-api-credentials.json")
.build();
// Capture the credential from app and show create the context
const executionContext = adobe.ExecutionContext.create(credentials);
// create the operation
const documentMerge = adobe.DocumentMerge,
documentMergeOptions = documentMerge.options,
options = new documentMergeOptions.DocumentMergeOptions(json, documentMergeOptions.OutputFormat.PDF);
const operation = documentMerge.Operation.createNew(options);
// Pass the content as input (stream)
const input = adobe.FileRef.createFromLocalFile(inputFile);
operation.setInput(input);
// Async create the PDF
let result = await operation.execute(executionContext);
await result.saveAsFile(outputPdf);
} catch (err) {
console.log('Exception encountered while executing operation', err);
}
}
```

Använd den här koden i Express-routern:

```
// Create one report and send it back
try {
console.log(`[INFO] generating the report...`);
const fileContent = fs.readFileSync(`./public/documents/raw/${vendor}`, 'utf-8');
const parsedObject = JSON.parse(fileContent);
await pdf.compileDocFile(parsedObject, `./public/documents/template/Adobe-NDA-Sample.docx`, `./public/documents/processed/output.pdf`);
console.log(`[INFO] sending the report...`);
res.status(200).render("preview", { page: 'nda', filename: 'output.pdf' });
} catch(error) {
console.log(`[ERROR] ${JSON.stringify(error)}`);
res.status(500).render("crash", { error: error });
}
```

Du kan visa [den fullständiga exempelkoden](https://github.com/afzaal-ahmad-zeeshan/adobe-docugen-sample) på GitHub.

Den här koden använder ett JSON-dokument och Microsoft Word-mallen i API-anropet till SDK:et [!DNL Adobe Acrobat Services]. I svaret får du utdata och sparar dem i appens filsystem. Du kan vidarebefordra det genererade dokumentet till dina kunder via e-post eller visa dem en förhandsvisning i webbläsaren med det kostnadsfria [Adobe PDF Embed API](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-embed.html).

Detta anrop skapar följande NDA-dokument:

![Skärmbild av NDA-dokumentets förhandsgranskning](assets/nda_6.png)

[!DNL Adobe Acrobat Services] API:er infogar innehåll för att skapa ett PDF-dokument. Utan de här verktygen kan du behöva skriva koden för att bearbeta Office-dokument och arbeta med raw PDF-filformat. Med hjälp av Adobe PDF-tjänster kan du utföra alla dessa steg med ett enda API-anrop.

Använd nu [Adobe Sign API](https://www.adobe.io/apis/documentcloud/sign.html) för att begära signaturer i sekretessavtal och leverera det slutgiltiga signerade dokumentet till alla parter. Adobe Sign meddelar dig [med hjälp av en webhook](https://www.adobe.io/apis/documentcloud/sign/docs.html#!adobedocs/adobe-sign/master/webhooks.md). När du lyssnar på den här webhooken kan du hämta kärnavvecklingsbyråns status.

[Mer information om Adobe Sign finns i dokumentationen](https://www.adobe.io/apis/documentcloud/sign/docs.html) eller i det här detaljerade blogginlägget.

## Nästa steg

I den här praktiska självstudiekursen användes taggen för dokumentgenerering i Adobe för att dynamiskt generera PDF-dokument med Microsoft Word-mallar och JSON-datafiler. Tillägget hjälpte till att [skapa sekretessavtal](https://www.adobe.io/apis/documentcloud/dcsdk/nda-creation.html) automatiskt för varje part och samla sedan in signaturer med Sign API.

Du kan använda de här teknikerna för att dynamiskt skapa egna sekretessavtal eller andra dokument, vilket frigör tid för teamet att fokusera på produktivt arbete. Utforska [[!DNL Adobe Acrobat Services]](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-tools.html) för att hitta API:er och SDK:er för önskat språk och vald körmiljö så att du kan lägga till PDF-funktioner direkt i dina program och snabbt skapa PDF-dokument. [Kom igång](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) med en sex månader lång kostnadsfri provperiod och sedan
[betala per användning](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) för endast $0,05 per dokumenttransaktion.
