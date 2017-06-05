---
title: Czech Republic Foreign Police Integration (English version)
---

## Contents

- [Introduction](#introduction)
- [Settings](#settings)
	- [Example setting](#example-setting)
- [Customer profiles report](#customer-profiles-report)
   - [Incomplete report](#incomplete-report)
- [How the automatic reporting works](#how-everything-works)
   - [What's happening in one day?](#one-day)
   - [Which guests are reported?](#who-is-reported)
- [UbyPort](#ubyport)
   - [How to Upload a Report](#how-to-upload-a-report)
   - [Statement from the police of the Czech Republic](#statement)
      - [How to handle a statement](#handle-a-Statement)
- [Advice and comments to conclude](#hints)


<a name="introduction"></a>
## Introduction

This document serves as a guide for hotels for reporting the accommodated foreigners to the Foreign Police of the Czech Republic. Basic information provided by the Police of the Czech Republic is available [here](http://www.policie.cz/clanek/oznamovani-ubytovanych-cizincu-ubytovatelem.aspx). This document assumes that the hotel staff members are already familiar with the guide of the Police of the Czech Republic.


<a name="settings"></a>
## Settings

The below points should be arranged by a hotel:

 - Register with the Foreign Police of the Czech Republic (see their manual) to get IDUB and short IDUB hotel identifiers and get the access to the UbyPort application.
 
Mews “Foreign Police Integration” requires:

- Enter IDUB and short IDUB - hotel identifiers in the Czech Police system
- Enter Recipient’s Email - At least one email address to which Mews will send daily a copy of the submitted report. It is necessary to check these email boxes daily!
- Enter Manager’s Email - at least one email address to which Mews will send a reminder to check the Customer Profiles report. It is necessary to check these email boxes daily!
- Set time when the report will be generated- we recommend setting 9:00, which gives you plenty of time to resolve potential data issues.

<a name="example-setting"></a>
### Example setting

![Example setting](https://github.com/MewsSystems/public/blob/gh-pages/content/users/commander-web/configuration/integrations/foreign-police-integrations/img/IntegrationDetail_en.png)

- E-mail addresses, to which reminder will be send are filled in in the Email Manager field.
- E-mail addresses, to which report will be send are listed in Recipient Email box.

The recipients of this report are required to check daily that guest’s data is filled in.
Individual email addresses need to be separated by semicolons.

<a name="customer-profiles-report"></a>
## Customer Profiles Report in Mews

[Customer profiles report](#report-profily-hostu) can be accessed directly from the homepage after logging in to Mews. Report shows the list of guests who are staying (or coming) in the selected time interval - depending on the selected report parameters. The correct filter for the Police report is the **1** day and the `Arrivals` mode.

![Customer profiles report](https://github.com/MewsSystems/public/blob/gh-pages/content/users/commander-web/configuration/integrations/foreign-police-integrations/img/Report.png)

This is an example of the Customer profiles report. It shows that `John Smith` and `Last Name` are in the room `203`, `Nicolas Cage` is accommodated in the room `106`, a companion is not filled in (reservation is for 2 persons), and the room `206` is hosted by `Mena Suvari`.
The report further states that:

- All the necessary details for `John Smith` are filled in - *the guest profile is 100% filled*.
- For other guests, it is necessary to fill in some data. The missing fields are labelled as **Required**.
- You need to find out who is staying in room 106 with `Nicolas Cage`.

All missing (red) data should ideally be filled in during the Check-in process. If even after check-in some data is missing, it must be filled in until the report is generated - see [How the automatic reporting works](#how-everything-works). The goal is to have the report filled in at 100%.

### Mandatory data

- Name and surname
- Birthdate
- Nationality
- Address (at least Line 1, City, ZIP, and Country)
- Personal ID number
- The visa number is NOT mandatory in the report, however, for the travellers from the countries where visa is required, this data may be considered as mandatory.
*Note*: If a longer period than one day is selected in the report, the report for the Police will be generated for the first day only.

<a name="incomplete-report"></a>
### Incomplete report

If the Customer Profiles report is not 100% filled even after all guests have arrived you may check the guests' registration cards and fill in the details accordingly. If there is no data, it means that the receptionist did not ask for it or did not review the card. You need to contact the guest and find out the details. Additionally, the reception desk is required to check all the details at check-in to ensure that this does not happen again.

A more complex problem may be with the guest in room `106`.

There could be several different situations:

 1. The second guest (companion1) will not come.
 2. The second guest arrived but was not created in the system.
 3. Both guests arrived, both were created in the system, both guests are on the reservation screen, yet there is a data missing.

#### Solution

 1. Manually adjust the booking to one person only and apply some fees if applicable.
 2. Create a new guest and assign him into the reservation.
 3. First, you need to be aware of the difference between the “booking owner” and the “guest assigned” to the reservation. The owner is the one on whose behalf the reservation is created. It can even be someone who will be not accommodate in the hotel at all, just did a reservation for somebody else. The guest is someone who has arrived and who stays in the room. See picture:

![Group module](https://github.com/MewsSystems/public/blob/gh-pages/content/users/commander-web/configuration/integrations/foreign-police-integrations/img/GroupModule.png)

This picture shows how to resolve the third case - room `106`. `Nicolas Cage` is listed as the booking owner. You need to add his companion to the room by clicking `>` (marked in red). This adds the guest to the selected room. One guest can book more rooms, however, the system will not automatically assign him to all rooms as one guest can not be physically in more than one room. Therefore, it is necessary to assign the owner during a check-in process to the reservation (make it a guest). Again, you need to set up a check-in process in a way that prevents any issues in the future.

<a name="how-everything-works"></a>
## How the automatic reporting works

If the integration is set and approved by a Mews Support member, everything is ready for reporting customers to the Foreign Police. The report runs every day at 9 in the morning on the Mews background and always does two things:

- It will send an email reminder to check the arrival report for **yesterday**.
- It will generate a report for **the day before yesterday** and will send it to the Recipient's email address.
*Note*: Before the first run, it is important to keep in mind that the first report will be sent without any notice, therefore, the Customer profile report from **the day before yesterday** needs to be already checked.

<a name="one-day"></a>
### What's happening in one day?

Let’s imagine it is **17.2.2014 at 9:00** and the following things happen (using settings from the Example setting)

- Mews will send a message to check the Customer Profiles report for **16.2.2014**.
It is necessary to check that the arrival log for **16.2.2014** is 100% complete. 
If the report is not 100% complete, it needs to be completed within 24 hours (tomorrow Mews will generate and send the hotel as it is).
 
- Mews generates the report for **the day before yesterday 15.2.2014** (which is supposed to be checked from the previous day) and sends it to the Recipient’s email.

This report needs to be **uploaded immediately** in UbyPort as described in this manual.

At this moment, you need to upload the report manually. Police is working on an API through which the report could be uploaded automatically.

- The Police will process the report and generate a Report Statement, which can be found directly in the UbyPort application.
   - If the Statement says that the data is correct and complete, the hotel has successfully done its job.
   - If the Statement is negative, hotel needs to complete the report as soon as possible, regenerate and re-upload to the [UbyPort](#ubyport) system.
      - see [How to Handle a Statement](#handle-a-Statement).
      
*Note*: If the hotel did not receive an email with a report in the morning (please check SPAM folder), , it is also possible that the report was not sent or processed. In that case please contact MEWS Support.

<a name="who-is-reported"></a>
### Which guests are reported?

According to the law, all foreign guests with a non-Czech nationality should be reported to the Foreign Police. Mews will include every guest with a completed nationality field other than Czech in the report, **regardless of whether the profile is filled in 100% or not**.

<a name="ubyport"></a>
## UbyPort

The generated reports must be uploaded to the Police [here](https://ubyport.policie.cz).

<a name="how-to-upload-a-report"></a>
### How to Upload a Report

Uploading a report is easy, just click on this button to select the right report and upload it.

![UbyPort](https://github.com/MewsSystems/public/blob/gh-pages/content/users/commander-web/configuration/integrations/foreign-police-integrations/img/UbyPort.PNG)

<a name="statement"></a>
### Statement from the Police of the Czech Republic

The statement is a feedback from the Police of the Czech Republic about the report and the data contained therein. The opinion may be:
 
 1. All data is okay, the notification obligation **was** fulfilled.
 2. Some details are still missing (e.g. first name, date of birth), nevertheless the notification obligation **was** fulfilled.
 3. Serious errors were detected, so the reporting obligation **was not** met - In this case, you need to respond quickly. It is important, that the email is being daily checked and the missing data is corrected and immediately uploaded to the Police via UbyPort.
 
<a name="handle-a-Statement"></a>
#### How to Handle a Statement

- If the Statement of the Czech Police is one of the first two mentioned above, everything is fine (in the second case, it is good to correct the missing data in the system) and there is no need to upload the report again.
- In the third case, you need to fix the bug in the system and also upload the updated report to UbyPort - just select the appropriate day in the [Customer Profiles Report](#customer-profiles-report) (Note that the statements may be related to the reports which were sent at least 2 days ago - see [How the automatic reporting works](#how-everything-works)).

<a name="hints"></a>
## Advice and comments to conclude

- Inform all receptionists to fill in all the necessary details at a check-in (First Name, Last Name, Date of Birth, Nationality, Passport and Visa Numbers, Place of Residence etc.) and correctly assign all guests to the rooms.
- It is necessary to check a yesterday's report daily.
- It is necessary to check [the statements](#statement) on a daily basis and, in the case of a negative statement, immediately fill in the missing data
- Before the first run, you need to check the report for the last 2 days (from the launch date), because no reminder email will be sent.
- The button in the [Customer profiles report](#customer-profiles-report) serves primarily to generate an updated report.
