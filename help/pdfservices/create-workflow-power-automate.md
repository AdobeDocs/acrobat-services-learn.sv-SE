---
title: Skapa ditt första arbetsflöde i Microsoft Power Automate
description: Lär dig hur du använder Adobe PDF Services-anslutningen i Microsoft Power Automate
feature: PDF Services API
role: Developer
level: Beginner
type: Tutorial
jira: KT-10379
thumbnail: KT-10379.jpg
exl-id: 095b705f-c380-42cc-9329-44ef7de655ee
source-git-commit: 5222e1626f4e79c02298e81d621216469753ca72
workflow-type: tm+mt
source-wordcount: '1955'
ht-degree: 1%

---


# Skapa ditt första arbetsflöde i Microsoft Power Automate

Lär dig hur du skapar ditt första flöde i [Microsoft Power Automate](https://flow.microsoft.com) med hjälp av [Adobe PDF Services](https://us.flow.microsoft.com/en-us/connectors/shared_adobepdftools/adobe-pdf-services/)-anslutningen.

I den här praktiska självstudien lär du dig att:

* Konvertera Word-dokument till PDF
* Kombinera PDF-dokument till ett PDF
* Protect ett PDF-dokument med ett lösenord

## Förberedelse

### Vad du behöver

* **Autentiseringsuppgifter för provperiod eller produktion för Adobe PDF-tjänster**
Läs mer om hur du hämtar och konfigurerar autentiseringsuppgifter i Microsoft Power Automate [här](https://experienceleague.adobe.com/docs/document-services/tutorials/pdfservices/getting-credentials-power-automate.html).
* **Microsoft Power Automate med premiumanslutningar**
Lär dig kontrollera licensnivån för Power Automate [här](https://docs.microsoft.com/en-us/power-platform/admin/power-automate-licensing/types).
* **OneDrive**
I den här självstudiekursen används OneDrive-lagringskontakten, men du kan byta ut en valfri lagringskontakt.

### Exempelfiler

Det finns två [exempelfiler](assets/sample-assets.zip) som du måste packa upp och överföra till OneDrive:

* WordDocument01.docx
* WordDocument02.docx

### Hämtar autentiseringsuppgifter

För att slutföra den här självstudiekursen behöver du dina inloggningsuppgifter som redan har konfigurerats i Microsoft Power Automate för Adobe PDF-tjänster. Om du inte har slutfört det här steget kan du läsa [anvisningarna här](https://experienceleague.adobe.com/docs/document-services/tutorials/pdfservices/getting-credentials-power-automate.html).

## Del 1: Skapa nytt flöde och konvertera Word till PDF

### Skapa flödet

I den här delen skapar du ett nytt flöde i [Microsoft Power Automate](https://flow.microsoft.com) med ett direktflöde, lägger till parametrar, hämtar filer från OneDrive och konverterar dem till PDF.

1. Gå till [Microsoft Power Automate](https://flow.microsoft.com) och logga in med dina inloggningsuppgifter.
1. Välj **[!UICONTROL Skapa]** i sidofältet.

   ![Skapa knapp](assets/createButtonPowerAutomate.png)

1. Välj **[!UICONTROL Omedelbart flöde]**.
1. Ge ditt flöde ett namn.
1. Under *Välj hur det här flödet ska utlösas* väljer du **[!UICONTROL Starta ett flöde manuellt]**.
1. Välj **[!UICONTROL Skapa]**.

### Hämta filinnehåll för filer

Hämta sedan filinnehållet för exempelfilerna.

>[!PREREQUISITES]
>
>Om du inte har överfört [exempelfilerna](assets/sample-assets.zip) till OneDrive packar du upp dem och överför dem.


1. Välj **[!UICONTROL + nytt steg]** i [Power Automate](https://flow.microsoft.com).
1. Sök efter *OneDrive* i sökfältet.
1. Välj antingen ditt arbets- eller personliga OneDrive-konto genom att välja **[!UICONTROL OneDrive för företag]** eller **[!UICONTROL OneDrive]**.
1. Sök efter *Hämta filinnehåll* i sökfältet.
1. I fältet **[!UICONTROL Fil]** väljer du mappikonen för att gå till filen *WordDocument01.docx* i OneDrive.

   ![Hämta OneDrive-filinnehållsåtgärd i Microsoft Power Automate](assets/getFileContentOneDrive.png)

### Konvertera fil till PDF

Nu när du har filinnehållet kan du konvertera dokumentet till PDF.

1. Välj **[!UICONTROL + nytt steg]** i [Power Automate](https://flow.microsoft.com).
1. Sök efter *Adobe PDF-tjänster* i sökfältet.
1. Markera **[!UICONTROL Adobe PDF-tjänster]**.
1. Sök efter *Konvertera Word till PDF* i sökfältet.
1. Ge filen ett namn i **[!UICONTROL Filnamn]**, men den måste sluta med *.docx*. Det här tillägget krävs för att konvertera dokument från Word till PDF.
1. Placera markören i fältet **[!UICONTROL Filinnehåll]**.
1. Välj **[!UICONTROL Filinnehåll]** med hjälp av panelen **[!UICONTROL Dynamiskt innehåll]**.

   ![Åtgärden Konvertera Word till PDF i Microsoft Power Automate](assets/convertWordToPDFActionPowerAutomate.png)

### Spara filen på OneDrive

Spara filen i OneDrive när dokumentet har skapats.

1. I [Microsoft Power Automate](https://flow.microsoft.com) väljer du **[!UICONTROL + Nytt steg]**.
1. Sök efter *OneDrive* i sökfältet.
1. Välj antingen ditt arbets- eller personliga OneDrive-konto genom att välja **[!UICONTROL OneDrive för företag]** eller **[!UICONTROL OneDrive]**.
1. Sök efter *Hämta filinnehåll* i sökfältet.
1. Sök efter *Skapa fil* i sökfältet.
1. Välj **[!UICONTROL Skapa fil]**.
1. I fältet **[!UICONTROL Mappsökväg]** väljer du mappikonen för att ange var filen ska sparas i OneDrive.
1. Ge filen ett namn i **[!UICONTROL Filnamn]**, men den måste sluta med *.docx*. Det här tillägget krävs för att konvertera dokument från Word till PDF.
1. I fältet **[!UICONTROL Filinnehåll]** använder du panelen **[!UICONTROL Dynamiskt innehåll]** för att infoga variabeln för filinnehåll i PDF.

### Testa flöde

1. I det övre vänstra hörnet väljer du **[!UICONTROL Namnlös]** för att byta namn på flödet.
1. Välj **[!UICONTROL Spara]**.
1. Välj **[!UICONTROL Test]**.
1. Välj **[!UICONTROL Manuellt]** och sedan **[!UICONTROL Spara och testa]**.
1. Välj **[!UICONTROL Fortsätt]**.
1. Välj **[!UICONTROL Kör flöde]**.

I OneDrive-mappen bör du nu se det konverterade PDF.

![Markerat konverterat PDF-dokument i OneDrive](assets/selectedGeneratedFileInOneDrive.png)

## Del 2: Generera ett dynamiskt dokument från en mall

Nästa del bygger på del 1 och använder mallen *Generera dokument från Word* för att sammanfoga data dynamiskt i dokumentet.

### Granska dokumentmallen

Öppna *WordDocument02_.docx* från dina exempelfiler i OneDrive. Word-dokumentet innehåller flera olika texttaggar som representerar platser där data fylls i i dokumentet.

### Lägg till parametrar som ska utlösas

Om du vill att dynamiska data ska överföras till dokumentet måste du skapa några parametrar som utlösaren kan använda för att fråga efter värden.

1. När du redigerar flödet väljer du **[!UICONTROL Starta ett flöde manuellt]** för att utöka åtgärden.
1. Välj **[!UICONTROL Lägg till indata]**.
1. Välj **[!UICONTROL Text]**.
1. Ge fältet namnet *Förnamn*.

Upprepa steg 2-4 för att lägga till följande fält:

* Efternamn
* Lön

![Utlösare i Power Automate med parameterfält](assets/triggerParametersInPowerAutomate.png)

### Hämta filinnehåll för en mall

Om du vill generera ett dokument måste du först hämta Word-mallens filinnehåll.

1. I Power Automate väljer du + **[!UICONTROL Nytt steg]**.
1. Sök efter *OneDrive* i sökfältet.
1. Välj antingen ditt arbets- eller personliga OneDrive-konto genom att välja **[!UICONTROL OneDrive för företag]** eller **[!UICONTROL OneDrive]**.
1. Sök efter *Hämta filinnehåll* i sökfältet.
1. I fältet **[!UICONTROL Fil]** väljer du mappikonen för att gå till filen *WordDocument02.docx* i OneDrive.

![Hämta filinnehållsåtgärd från OneDrive i Microsoft Power Automate](assets/getFileContentAction02.png)

### Generera dokument från mall

1. I Power Automate väljer du **[!UICONTROL + Nytt steg]**.
1. Sök efter *Adobe PDF-tjänster* i sökfältet.
1. Markera **[!UICONTROL Adobe PDF-tjänster]**.
1. Välj åtgärden **[!UICONTROL Generera dokument från Word-mall]** .
1. I fältet **[!UICONTROL Mallfilnamn]** ger du filen ett namn som du vill ha, men det måste sluta med *.docx*.

#### Sammanfoga data

Med åtgärden *Generera dokument från Word-mall* kan du sammanfoga data i dokumentet från någon av de olika variablerna tidigare i flödet med hjälp av Dynamiskt innehåll.

Kopiera JSON-data nedan till fältet **Sammanfoga data**:

```
{
    "FirstName": "",
    "LastName": "",
    "Salary": ""
}
```

1. Placera markören i fältet mellan de två citattecknen för värdet *FirstName*.
1. Använd panelen **[!UICONTROL Dynamiskt innehåll]** och infoga värdet *Förnamn* från utlösa en flödesåtgärd manuellt.

   ![Generera dokument med datataggar i JSON](assets/generateDocumentJSONAction.png)

1. Upprepa steg 7-8 för fälten **[!UICONTROL LastName]** och **[!UICONTROL Salary]**.
1. I fältet **[!UICONTROL Mallfilsinnehåll]** använder du panelen **[!UICONTROL Dynamiskt innehåll]** för att infoga värdet **[!UICONTROL Filinnehåll]** från steget *Hämta filinnehåll*.

![Åtgärden Generera dokument från Word-mall i Power Automate med alla värden slutförda](assets/generateDocumentJSONActionCompleted.png)

>[!TIP]
>
>Åtgärden *Generera dokument från Word-mall* använder Adobe-dokumentgenererings-API. Om du vill lära dig mer om hur du skapar mallar kan du använda följande resurser:
>
>* [Läs mer om dokumentgenerering i Adobe](https://developer.adobe.com/document-services/apis/doc-generation/)
>* [Genereringstagg för Adobe-dokument för Microsoft Word](https://appsource.microsoft.com/en-US/product/office/WA200002654)
>* [API-dokumentation för dokumentgenerering i Adobe](https://developer.adobe.com/document-services/docs/overview/document-generation-api/)

### Spara filen på OneDrive

När dokumentet har skapats kan du spara filen tillbaka i OneDrive.

1. I Power Automate väljer du **+ [!UICONTROL Nytt steg]**.
1. Sök efter *OneDrive* i sökfältet.
1. Välj antingen ditt arbets- eller personliga OneDrive-konto genom att välja **[!UICONTROL OneDrive för företag]** eller **[!UICONTROL OneDrive]**.
1. Sök efter *Skapa fil* i sökfältet.
1. Välj **[!UICONTROL Skapa fil]**.
1. I fältet **[!UICONTROL Mappsökväg]** väljer du mappikonen för att ange var filen ska sparas i OneDrive.
1. Ange filnamnet i fältet **[!UICONTROL Filnamn]**. Eftersom resultatet är ett PDF måste filnamnet sluta med filtillägget .pdf.
1. Använd panelen **[!UICONTROL Dynamiskt innehåll]** för att infoga PDF-filens innehållsvariabel i fältet **[!UICONTROL Filinnehåll]**.

### Testa flöde

![Kör flödesskärm i Microsoft Power Automate och fråga efter indata](assets/runFlowParameters.png)

1. Välj **[!UICONTROL Spara]**.
1. Välj **[!UICONTROL Test]**.
1. Välj **[!UICONTROL Manuellt]** och sedan **[!UICONTROL Spara och testa]**.
1. Välj **[!UICONTROL Fortsätt]**.
1. Ange värden för *Förnamn*, *Efternamn* och *Lön*.
1. Välj **[!UICONTROL Kör flöde]**.

I mappen OneDrive visas nu en PDF som genereras från Word-dokumentet. När du öppnar PDF-dokumentet i OneDrive ser du att data sammanfogas med platserna för texttaggar.


## Del 3: Kombinera PDF till en

Nu när du har skapat och konverterat ett Word-dokument till en PDF är nästa steg att kombinera flera PDF-dokument.

>[!NOTE]
>
>I föregående åtgärder sparade du en kopia av dokumentet som en fil i OneDrive. Om du vill använda verktyg som Merge PDF behöver du inte spara filen i OneDrive. I stället kan du skicka utdata direkt från en åtgärd till nästa, vilket är bättre än att spara till OneDrive efter varje åtgärd. Men du sparar dessa filer till OneDrive, som en demonstration.

### Steg för Lägg till sammanfogning PDF

1. Välj **[!UICONTROL + Nästa steg]** när du redigerar flödet för att lägga till en åtgärd i slutet av flödet.
1. Sök efter *Adobe PDF-tjänster* i sökfältet.
1. Markera **[!UICONTROL Adobe PDF-tjänster]**.
1. Markera åtgärden **[!UICONTROL Sammanfoga PDF]**.
1. I fältet **[!UICONTROL Sammanfoga PDF-filnamn]** anger du önskat filnamn (dvs. *CombinedDocument.pdf*).
1. I fältet **[!UICONTROL Filinnehåll -1]** använder du panelen **[!UICONTROL Dynamiskt innehåll]** för att infoga filinnehållet *PDF* från steget **[!UICONTROL Konvertera Word till PDF]**.
1. Om du vill lägga till nästa dokument väljer du **+ [!UICONTROL lägg till nytt objekt]**.
1. I fältet **[!UICONTROL Filinnehåll - 2]** använder du panelen **[!UICONTROL Dynamiskt innehåll]** för att infoga värdet **[!UICONTROL Utdatafilinnehåll]** från steget *Generera dokument från Word-mall*.

![Åtgärden Sammanfoga PDF i Microsoft Power Automate](assets/mergePDFAction.png)

### Spara sammanfogad PDF till OneDrive

När dokumentet har kombinerats kan du spara tillbaka dokumentet till OneDrive.

1. I Power Automate väljer du **+ [!UICONTROL Nytt steg]**.
1. Sök efter *OneDrive* i sökfältet.
1. Välj antingen ditt arbets- eller personliga OneDrive-konto genom att välja **[!UICONTROL OneDrive för företag]** eller **[!UICONTROL OneDrive]**.
1. Sök efter *Skapa fil* i sökfältet.
1. Välj **[!UICONTROL Skapa fil]**.
1. I fältet **[!UICONTROL Mappsökväg]** väljer du mappikonen för att ange var filen ska sparas i OneDrive.
1. Ange filnamnet i fältet **[!UICONTROL Filnamn]**. Eftersom utdata är en PDF måste filnamnet sluta med .pdf.
1. I fältet **[!UICONTROL Filinnehåll]** använder du panelen **[!UICONTROL Dynamiskt innehåll]** för att infoga värdet *PDF-filinnehåll* från steget **[!UICONTROL Sammanfoga PDF]**.

   ![Flöde i Microsoft Power Automate - översikt](assets/flowOverviewSavedMergedDocument.png)

### Testa flöde

1. Välj **[!UICONTROL Spara]**.
1. Välj **[!UICONTROL Test]**.
1. Välj **[!UICONTROL Manuellt]** och sedan **[!UICONTROL Spara och testa]**.
1. Välj **[!UICONTROL Fortsätt]**.
1. Ange värden för *Förnamn*, *Efternamn* och *Lön*.
1. Välj **[!UICONTROL Kör flöde]**.

I OneDrive-mappen visas det kombinerade PDF med sidor från det första och andra dokumentet.

## Del 4: Protect-dokument från PDF

När du har genererat ett dokument kan du skydda det från redigering genom att inkludera ett extra steg innan du sparar det i OneDrive.

### Skydda en PDF-fil

1. När du redigerar ditt flöde i Power Automate väljer du **+** mellan åtgärden **[!UICONTROL Sammanfoga PDF]** och åtgärden **[!UICONTROL Skapa fil 3]**.

   ![Plus-symbol mellan två åtgärder för att lägga till en ny åtgärd](assets/addActionToProtect.png)

1. Välj **[!UICONTROL Lägg till en åtgärd]**.
1. Sök efter *Adobe PDF-tjänster* i sökfältet.
1. Markera **[!UICONTROL Adobe PDF-tjänster]**.
1. Markera åtgärden **[!UICONTROL Protect PDF från visning]**.
1. I fältet **[!UICONTROL Filnamn]** anger du önskat namn, förutsatt att det slutar med filtillägget .pdf.
1. Ställ in fältet **[!UICONTROL Lösenord]** på det angivna lösenordet för att öppna dokumentet.
1. I fältet **[!UICONTROL Filinnehåll]** använder du panelen **[!UICONTROL Dynamiskt innehåll]** för att infoga filinnehållet *PDF* från steget **[!UICONTROL Sammanfoga PDF]**.

### Uppdatera Spara till OneDrive

När dokumentet är skyddat kan du spara filen tillbaka i OneDrive. I det här exemplet uppdaterar du den befintliga åtgärden **Skapa fil 3** med ett nytt *Filinnehåll*.

1. Välj markör i fältet **[!UICONTROL Filinnehåll]** i åtgärden **[!UICONTROL Skapa fil 3]**.
1. Använd panelen **[!UICONTROL Dynamiskt innehåll]** för att infoga värdet *PDF-filinnehåll* från steget **Protect PDF från visning**.

### Testa flöde

1. Välj **[!UICONTROL Spara]**.
1. Välj **[!UICONTROL Test]**.
1. Välj **[!UICONTROL Manuellt]** och sedan **[!UICONTROL Spara och testa]**.
1. Välj **[!UICONTROL Fortsätt]**.
1. Ange värden för *Förnamn*, *Efternamn* och *Lön*.
1. Välj **[!UICONTROL Kör flöde]**.

I OneDrive-mappen visas det kombinerade PDF som nu uppmanar dig att ange ett lösenord för att visa dokumentet.

## Nästa steg

I den här självstudiekursen har du konverterat ett Word-dokument till en PDF, genererat ett dokument baserat på data, sammanfogat dokument och lösenordsskyddat. Utforska några av de andra åtgärderna som finns i anslutningen Adobe PDF Services i Microsoft Power Automate om du vill veta mer:

* Visa de färdiga mallarna som finns i Microsoft Power Automate.
* Läs mer i [artiklar](https://medium.com/adobetech/tagged/microsoft-power-automate) på Adobe Tech Blog.
* Granska [dokumentationen](https://developer.adobe.com/document-services/docs/overview/document-generation-api/) för Adobe-dokumentgenererings-API:t.
