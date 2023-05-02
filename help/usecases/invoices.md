---
title: Hantera fakturor
description: Lär dig hur du automatiskt genererar, lösenordsskyddar och levererar kundfakturor
role: Developer
level: Intermediate
type: Tutorial
thumbnail: KT-8145.jpg
kt: 8145
exl-id: 5871ef8d-be9c-459f-9660-e2c9230a6ceb
source-git-commit: 799b37e526073893fe7c078db547798d6c31d1b2
workflow-type: tm+mt
source-wordcount: '1427'
ht-degree: 1%

---

# Hantera fakturor

![BANDEROLL MED ANVÄNDNINGSFALL](assets/UseCaseInvoicesHero.jpg)

Det är fantastiskt när verksamheten blomstrar, men produktiviteten blir lidande när det är dags att förbereda alla dessa fakturor. Manuell generering av fakturor är tidskrävande, plus att du riskerar att göra ett fel, potentiellt förlora pengar eller reta upp en kund med ett felaktigt belopp.

Tänk till exempel på Danielle som arbetar i [redovisningsavdelning](https://www.adobe.io/apis/documentcloud/dcsdk/invoices.html) [för ett läkemedelsföretag](https://www.adobe.io/apis/documentcloud/dcsdk/invoices.html). Det är månadens slut, så hon hämtar information från flera olika system, dubbelkollar dess riktighet och formaterar fakturorna. Efter allt det arbetet är hon äntligen redo att konvertera dokumenten till PDF (så att alla kan se dem utan att köpa något särskilt program) och skicka en personlig faktura till varje kund.

Danielle kan inte komma undan fakturorna. Vissa kunder har faktureringsperioder som inte är månatliga, så hon skapar alltid en faktura för någon. Ibland redigerar en kund sin faktura och betalar för lite. Danielle lägger sedan tid på att felsöka den här felmatchningen av fakturor. I den här takten måste hon anställa en assistent för att hålla jämna steg med allt arbete!

Vad Danielle behöver är ett sätt att generera fakturor snabbt och korrekt, både i batch i slutet av månaden och ad hoc vid andra tillfällen. Helst, om hon kunde skydda dessa fakturor från redigeringar, hon skulle inte behöva oroa sig för att felsöka felmatchande belopp.

## Vad du kan lära dig

I den här praktiska självstudiekursen får du lära dig hur du använder Adobe dokumentgenererings-API för att automatiskt generera fakturor, lösenordsskydda PDF och leverera fakturor till alla användare. Allt som krävs är lite kunskap om Node.js, JavaScript, Express.js, HTML och CSS.

Den fullständiga koden för projektet är [finns på GitHub](https://github.com/afzaal-ahmad-zeeshan/adobe-pdf-invoice-generation). Du måste konfigurera den offentliga katalogen med din mall och rådatamapparna. Under produktionen måste du hämta data från ett externt API. Du kan också utforska denna arkiverade version av programmet som innehåller mallresurserna.

## Relevanta API:er och resurser

* [PDF Services API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [Adobe-API för dokumentgenerering](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html)

* [Adobe Sign API](https://www.adobe.io/apis/documentcloud/sign.html)

* [Projektkod](https://github.com/afzaal-ahmad-zeeshan/adobe-pdf-invoice-generation)

## Förbereda data

Den här självstudiekursen tittar inte på hur data importeras från dina datalager. Era kundorder kan finnas i en databas, ett externt API eller anpassad programvara. Adobe Document Generation API förväntar sig ett JSON-dokument som innehåller faktureringsdata, till exempel information från din CRM- eller e-handelsplattform. I den här självstudiekursen förutsätts att data redan är i JSON-format.

Använd följande JSON-struktur för fakturering för enkelhetens skull:

```
{ 
    "customerName": "John Doe", 
    "customerEmail": "john-doe@example.com", 
    "order": [ 
        { 
            "productId": 26, 
            "productTitle": "Bandages", 
            "price": 15.82 
        }, 
        { 
            "productId": 54, 
            "productTitle": "Masks", 
            "price": 25 
        }, 
        { 
            "productId": 76, 
            "productTitle": "Gloves", 
            "price": 7.59 
        } 
    ] 
} 
```

JSON-dokumentet innehåller kundinformation samt orderinformation. Använd det här strukturerade dokumentet för att bygga upp din faktura och visa elementen i PDF-format.

## Skapa en fakturamall

Adobe Document Generation API förväntar sig en Microsoft Word-baserad mall och ett JSON-dokument för att skapa ett dynamiskt PDF- eller Word-dokument. Skapa en Microsoft Word-mall för faktureringsprogrammet och använd [kostnadsfritt tillägg för dokumentgenereringstaggar](https://opensource.adobe.com/pdftools-sdk-docs/docgen/latest/wordaddin.html#add-in-demo) för att generera malltaggar. Installera tillägget och öppna fliken i Microsoft Word.

![Skärmdump av tillägget Dokumentgenereringstagg](assets/invoices_1.png)

När du har klistrat in JSON-innehållet i tillägget, som visas ovan, klickar du på Generera taggar. Nu visar detta plugin ditt objekts format. I din grundläggande mall kan kundens namn och e-postadress användas, men orderinformationen visas inte. Orderinformationen diskuteras senare i den här självstudiekursen.

![Skärmdump av dokumentgenereringens taggarförfattarmall](assets/invoices_2.png)

Börja skriva fakturamallen i Microsoft Word-dokumentet. Lämna markören där du måste infoga dynamiska data och markera sedan taggen i tilläggsfönstret i Adobe. Klicka **Infoga text** så att tillägget Adobe Document Generation Tagger kan generera och infoga taggar. För personalisering lägger vi in kundens namn och e-postadress.

Gå nu vidare till de data som ändras för varje ny faktura. Välj **Avancerat** fliken för tillägget. Om du vill se de tillgängliga alternativen för att generera en dynamisk tabell baserat på de produkter en kund beställt klickar du på **Tabeller och listor** .

Välj **Ordning** från den första listrutan. Välj kolumnerna för den här tabellen i den andra listrutan. I den här självstudiekursen väljer du alla tre kolumnerna för objektet som ska återge tabellen.

![Skärmdump av fliken Avancerat för dokumentgenereringstagg](assets/invoices_3.png)

API:et för dokumentgenerering kan också utföra komplexa åtgärder som att samla element i en array. I dialogrutan **Avancerat** -fliken väljer du **Numeriska beräkningar** och i **Aggregering** markerar du det fält där du vill använda beräkningen.

![Skärmdump av dokumentgenereringens numeriska taggregat](assets/invoices_4.png)

Klicka på **Infoga beräkning** för att infoga taggen där det behövs i dokumentet. Följande text visas nu i Microsoft Word-filen:

![Skärmdump av taggar i Microsoft Word-dokument](assets/invoices_5.png)

Det här fakturaexemplet innehåller kundinformation, beställda produkter och det totala förfallna beloppet.

## Generera en faktura med Adobe Document Generation API

Använd Adobe PDF Services Node.js Software Development Kit (SDK) för att kombinera Microsoft Word- och JSON-dokument. Skapa ett Node.js-program för att skapa fakturan med hjälp av dokumentgenererings-API:t.

PDF Services API innehåller Document Generation Service, så du kan använda samma autentiseringsuppgifter för båda. Njut av en [sex månaders kostnadsfri testversion](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html)och betala sedan bara 0,05 USD per dokumenttransaktion.

Här är koden för att sammanfoga PDF:

```
async function compileDocFile(json, inputFile, outputPdf) { 
    try { 
        // configurations 
        const credentials =  adobe.Credentials 
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

Den här koden hämtar information från indata-JSON-dokumentet och indatamallfilen. Sedan skapas en dokumentkopplingsåtgärd för att kombinera filerna till en enda PDF-rapport. Slutligen utförs åtgärden med dina API-uppgifter. Om du inte redan har dem [skapa autentiseringsuppgifter](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html#getting-credentials) (Samma autentiseringsuppgifter används för Document Generation och PDF Services API.)

Använd den här koden i Express-routern för att hantera dokumentbegäran:

```
// Create one report and send it back
try {
    console.log(\`[INFO] generating the report...\`);
    const fileContent = fs.readFileSync(\`./public/documents/raw/\${vendor}\`,
    'utf-8');
    const parsedObject = JSON.parse(fileContent);

    await pdf.compileDocFile(parsedObject,
    \`./public/documents/template/Adobe-Invoice-Sample.docx\`,
    \`./public/documents/processed/output.pdf\`);

    await pdf.applyPassword("p@55w0rd", './public/documents/processed/output.pdf',
    './public/documents/processed/output-secured.pdf');

    console.log(\`[INFO] sending the report...\`);
    res.status(200).render("preview", { page: 'invoice', filename: 'output.pdf' });
} catch(error) {
    console.log(\`[ERROR] \${JSON.stringify(error)}\`);
    res.status(500).render("crash", { error: error });
}
```

När koden körs visas ett PDF-dokument som innehåller den dynamiskt genererade fakturan baserat på de data som anges. Med JSON-exempeldata (se ovan) är utdata för den här koden:

![Skärmdump av dynamiskt genererad PDF-faktura](assets/invoices_6.png)

Den här fakturan innehåller dina dynamiska data från JSON-dokumentet.

## Lösenordsskydda fakturor

Eftersom revisorn Danielle är orolig för att kunderna ska ändra fakturan måste du lägga på ett lösenord för att begränsa redigeringen. [PDF Services API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html) kan automatiskt tillämpa ett lösenord på dokument. Här använder du Adobe PDF Services SDK för att skydda dokumenten med ett lösenord. Koden är:

```
async function applyPassword(password, inputFile, outputFile) {
    try {
        // Initial setup, create credentials instance.
        const credentials = adobe.Credentials
        .serviceAccountCredentialsBuilder()
        .fromFile("./src/pdftools-api-credentials.json")
        .build();

        // Create an ExecutionContext using credentials
        const executionContext = adobe.ExecutionContext.create(credentials);
        // Create new permissions instance and add the required permissions
        const protectPDF = adobe.ProtectPDF,
        protectPDFOptions = protectPDF.options;
        // Build ProtectPDF options by setting an Owner/Permissions Password, Permissions,
        // Encryption Algorithm (used for encrypting the PDF file) and specifying the type of content to encrypt.
        const options = new protectPDFOptions.PasswordProtectOptions.Builder()
        .setOwnerPassword(password)
        .setEncryptionAlgorithm(protectPDFOptions.EncryptionAlgorithm.AES_256)
        .build();

        // Create a new operation instance.
        const protectPDFOperation = protectPDF.Operation.createNew(options);

        // Set operation input from a source file.
        const input = adobe.FileRef.createFromLocalFile(inputFile);
        protectPDFOperation.setInput(input);

        // Execute the operation and Save the result to the specified location.
        let result = await protectPDFOperation.execute(executionContext);

        result.saveAsFile(outputFile);
    } catch (err) {
        console.log('Exception encountered while executing operation', err);
    }
}
```

När du använder den här koden skyddas dokumentet med ett lösenord och en ny faktura överförs till systemet. Mer information om hur den här koden används eller hur du testar den finns i [kodexempel](https://github.com/afzaal-ahmad-zeeshan/adobe-pdf-invoice-generation).

När du är klar med fakturan kan du skicka den till klienten automatiskt. Det finns några sätt att uppnå automatiskt e-post dina kunder. Det snabbaste sättet är att använda ett e-post-API från tredje part tillsammans med ett hjälpbibliotek som [sendgrid-nodejs](https://github.com/sendgrid/sendgrid-nodejs). Om du redan har tillgång till en SMTP-server kan du använda [nodemailer](https://www.npmjs.com/package/nodemailer) skicka e-post via SMTP.

## Nästa steg

I den här praktiska självstudiekursen skapade du en enkel app för att hjälpa Danielle med redovisning [fakturering](https://www.adobe.io/apis/documentcloud/dcsdk/invoices.html). Med PDF Services API och Document Generation SDK fyllde du i en Microsoft Word-mall med kundorderinformation från ett JSON-dokument, vilket skapade en PDF-faktura. Lösenordsskydda sedan alla dokument med lösenordsskydd genom att [PDF Services API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html).

Eftersom Danielle kan generera fakturor automatiskt och inte behöver oroa sig för att kunderna ska redigera sina fakturor, behöver hon inte anlita en assistent för att hjälpa till med allt manuellt arbete. Hon kan använda sin extra tid till att hitta kostnadsbesparingar i leverantörsreskontrafilerna.

Nu när du har sett hur enkelt det är kan du expandera den här appen med hjälp av andra Adobe-verktyg för att bädda in fakturor på webbplatsen. Till exempel så att kunderna kan se sina fakturor eller saldon när som helst. [Adobe PDF Embed API](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-embed.html) är gratis att använda. Du kan till och med gå vidare till personalavdelningen eller försäljningsavdelningen och automatisera deras avtal och samla in elektroniska signaturer.

För att utforska alla möjligheter och börja bygga en egen praktisk applikation, skapa en gratis [[!DNL Adobe Acrobat Services]](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) konto för att komma igång idag. Prova sedan en sex månader lång kostnadsfri testversion [betala per användning](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html)
till endast 0,05 USD per dokumenttransaktion när företaget växer.
