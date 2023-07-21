---
title: Skapa en sekretessavtal
description: Lär dig skapa en dynamisk PDF för sekretessavtal för samarbete
role: Developer
level: Intermediate
type: Tutorial
thumbnail: KT-8098.jpg
jira: KT-8098
exl-id: f4ec0182-a46e-43aa-aea3-bf1d19f1a4ec
source-git-commit: 2d1151c17dfcfa67aca05411976f4ef17adf421b
workflow-type: tm+mt
source-wordcount: '1164'
ht-degree: 3%

---

# Skapa en sekretessavtal

![BANDEROLL MED ANVÄNDNINGSFALL](assets/UseCaseNDAHero.jpg)

Organisationer samarbetar med externa medverkande för att bygga sina tjänster och produkter. Ett sekretessavtal är en viktig del av dessa samarbeten. Det binder alla parter från att lämna ut konfidentiell information som kan skada någon av enheterna.

Det vanligaste NDA-formatet är ett PDF-dokument. Organisationer förbereder en sekretessavtal och skickar det till alla parter. Sedan, när alla har skrivit under, initierar de kontraktet. Manuell PDF-generering i ett höghastighetsteam fördröjer utvecklingen.

## Vad du kan lära dig

I den här praktiska självstudiekursen beskrivs hur du skapar en specialiserad Microsoft Word NDA-mall för ditt företag. Adobe kostnadsfria tillägg för Microsoft Word, [Adobe-dokumentgenereringstagg](https://opensource.adobe.com/pdftools-sdk-docs/docgen/latest/wordaddin.html#add-in-demo)infogar &quot;taggar&quot; för att ange de dynamiska värdena. Lär dig hur du skickar JSON-data till mallen och skapar ett dynamiskt PDF. PDF som blir resultatet kan skickas med e-post eller visas för dina medarbetare i webbläsaren, beroende på dina affärskrav och mål. Du behöver bara uppleva Node.js, JavaScript, Express.js, HTML och CSS.

## Relevanta API:er och resurser

Med [!DNL Adobe Acrobat Services], kan du skapa PDF-dokument snabbt med hjälp av dynamiska data. [!DNL Acrobat Services] innehåller en uppsättning PDF-verktyg, inklusive Adobe Document Generation API för automatisering [Skapa sekretessavtal](https://www.adobe.io/apis/documentcloud/dcsdk/nda-creation.html).

* [Adobe-API för dokumentgenerering](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html)

* [Adobe Sign API](https://www.adobe.io/apis/documentcloud/sign.html)

* [Adobe-dokumentgenereringstagg](https://opensource.adobe.com/pdftools-sdk-docs/docgen/latest/wordaddin.html#add-in-demo)

* [Projektkod](https://github.com/afzaal-ahmad-zeeshan/adobe-docugen-sample)

* [[!DNL Acrobat Services] tangenter](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html#getcred)

## Skapa JSON-modellen

Microsoft Word-mallen beror på JSON-modellen, så du skapar den först. I självstudiekursen använder du en grundläggande JSON-struktur som innehåller företagsinformation, till exempel kontaktinformation.

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

Du använder den här strukturen i Microsoft Word för att skapa en mall. Dessa data kan komma från vilken datakälla som helst, så länge de är i JSON-format. För enkelhetens skull skapar du flera filer i Node.js-programmet, men ditt användningsfall kan kräva en databasanslutning för att hämta leverantörsinformation.

## Skapa Microsoft Word-mallen

Skapa NDA-mallen i ett Microsoft Word-dokument. Adobe PDF Services API förväntar sig att Microsoft Word-dokumentet innehåller taggar där tjänsten kan mata in värden från JSON-dokument. Även om mallen är densamma för alla begäranden till Adobe ändras dynamiken i JSON. Med hjälp av dessa taggar kan du skapa PDF-dokument för alla leverantörer i det här fallet, med hjälp av en enda Microsoft Word-mall och snabba upp processen genom att automatisera genereringen av NDA-dokument.

Du kan installera [kostnadsfritt tillägg för dokumentgenereringstaggar](https://opensource.adobe.com/pdftools-sdk-docs/docgen/latest/wordaddin.html#add-in-demo) till Microsoft Word. Om du tillhör en organisation kan du begära att Microsoft Office-administratören installerar det kostnadsfria tillägget för alla.

När du har installerat tillägget hittar du det på fliken Hem under kategorin Adobe. Öppna fliken genom att välja **Dokumentgenerering**:

![Skärmdump av tillägget Dokumentgenerering i Word](assets/nda_1.png)

På fliken kan du överföra JSON-exempeldokumentet. Det här dokumentet kan vara ett exempel eftersom du bara använder det för att skapa en Microsoft Word-mall.

![Skärmdump av exempeldata i tillägget Dokumentgenerering](assets/nda_2.png)

Välj **Generera taggar** för att visa objekt som du kan använda i mallen. Här är de egenskaper som har extraherats från JSON-strukturen och som är klara att användas i mallen:

![Skärmdump av texttaggar i tillägget Dokumentgenerering](assets/nda_3.png)

Det här är funktionerna från `authorizedSigner` fält. Andra fält kapslas in och du kan expandera vyn i Microsoft Word. Tillägget har även avancerade dataalternativ, till exempel tabeller, listor, beräknade värden med mera.

## Skapa taggarna

Skapa en mall eller importera en [befintlig mall](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html#sample-blade) till Microsoft Word. När du har konfigurerat dokumentet lägger du till taggar i varje fält genom att klicka på motsvarande token i tillägget.

Följande mall i en Microsoft Word-fil:

![Skärmbild av exempelmall](assets/nda_4.png)

Den här filen innehåller flera taggar. När du kör programmet fylls dessa fält i med leverantörsinformationen.

Dokumentgenereringstaggen kan integreras med Adobe Sign API. På grund av integreringen kan du automatiskt skapa Sign-texttaggar så att det genererade dokumentet sedan kan skickas till Adobe Sign för signering.

## Generera sekretessavtal för leverantörer

I exempelprogrammet förberedde du mappar för in- och utdata. Som tidigare nämnts använder du JSON-filer, så att det finns två filer som visar de tillgängliga leverantörerna i systemet. Filerna visas i ett formulär som skrivs ut i webbläsaren:

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

När administratören markerar en person använder appen Adobe PDF Services för att generera sekretessavtal när användaren är på språng.

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

Du kan visa [den fullständiga provkoden](https://github.com/afzaal-ahmad-zeeshan/adobe-docugen-sample) på GitHub.

I den här koden används ett JSON-dokument och Microsoft Word-mallen i API-anropet till [!DNL Adobe Acrobat Services] SDK. I svaret får du utdata och sparar dem i programmets filsystem. Du kan vidarebefordra det genererade dokumentet till dina kunder via e-post eller visa en förhandsgranskning i webbläsaren med hjälp av det kostnadsfria [Adobe PDF Embed API](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-embed.html).

Detta anrop skapar följande NDA-dokument:

![Skärmdump av NDA-dokumentförhandsgranskningen](assets/nda_6.png)

[!DNL Adobe Acrobat Services] API:er infogar innehåll för att skapa ett PDF-dokument. Utan dessa verktyg kan du behöva skriva koden för att bearbeta Office-dokument och arbeta med raw PDF-filformat. Med hjälp av Adobe PDF Services kan du utföra alla dessa steg med ett enda API-anrop.

Använd nu [Adobe Sign API](https://www.adobe.io/apis/documentcloud/sign.html) begära underskrifter på sekretessavtal och leverera det slutgiltiga, signerade dokumentet till alla parter. Adobe Sign meddelar dig [använda en webhook](https://www.adobe.io/apis/documentcloud/sign/docs.html#!adobedocs/adobe-sign/master/webhooks.md). När du lyssnar på den här webhooken kan du hämta status för sekretessavtal.

Om du vill veta mer om Adobe Sign-processen kan du [läsa dokumentationen](https://www.adobe.io/apis/documentcloud/sign/docs.html) eller läs det här detaljerade blogginlägget.

## Nästa steg

I den här praktiska självstudiekursen användes taggen Adobe Document Generation för att dynamiskt generera PDF-dokument med Microsoft Word-mallar och JSON-datafiler. Tillägget hjälpte till att [automatiskt skapa sekretessavtal](https://www.adobe.io/apis/documentcloud/dcsdk/nda-creation.html) anpassat för varje part och samla sedan in signaturer med Sign API.

Du kan använda dessa tekniker för att dynamiskt skapa egna sekretessavtal eller andra dokument, vilket frigör teamets tid att fokusera på produktivt arbete. Utforska [[!DNL Adobe Acrobat Services]](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-tools.html) för att hitta API:er och SDK:er för önskat språk och runtime så att du kan lägga till PDF-funktioner direkt i programmen och snabbt skapa PDF-dokument. [Kom igång](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) med en sex månader lång kostnadsfri testversion sedan
[betala per användning](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) för endast 0,05 USD per dokumenttransaktion.
