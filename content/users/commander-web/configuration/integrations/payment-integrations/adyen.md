---
title: Adyen
---

## Credit Card validation method

Adyen integration validates credit cards in 2 ways:

- **0-authorization payment**
   - This method is preffered by both MEWS and Adyen.
   - The card is validated in a way that a 0 value authorization is made against the card. Supported by majority of banks.
- **MOTO payment**
   - Only allowed when the bank account doesn't support 0 value authorization (will be notified by Adyen if this is the case).
   - Requires a special account with `Call center` permission for the connection. 

## Credit card storage method

Adyen integration stores credit cards in 2 different ways:

- **Per Payment Gateway**
   - Credit card is validate and stored globally, e.g. is chageable against all current and future bank accounts.
   - Is used by `Merchant` integration.
- **Per Bank Account**
   - Credit card is stored agains each configured bank account and can be charged only in those. If any account is added in future, those cards are not chargeable in those currencies.

It needs to be selected when the account is created. For existing accounts, there exist migration (according to Adyen), but it wasn't requested yet by any MEWS hotel.

## Set up

Once a contract with Adyen is signed, basic setup of the Adyen account is configured Then the MEWS setup steps follows:

- Create `Adyen` integration. Set up bank accounts.
- Verify connection against test environment (with live account Adyen provides a test environment with the similar set up for this step).
- Check all permissions for the system account MEWS uses for communication.
- Set up notifications so MEWS is notified payment operations results.

### Where to obtain data in Adyen

- Log in to [live environment](https://ca-live.adyen.com/ca/ca/login.shtml) with the credentials provided by the hotel or Adyen.
- Select the account within Adyen, which you would like to set up in Mews.
- Select "Settings" and then select `users`. *Note that in order to access this area, the user who gave you the login, must have given you admin rights*.
- Once you are in the "users", select to see the `system` user in the dropdown selection box. Select on the user to open the details (ws@company.{your company name})
- Copy the following data from Adyen user configuration into the Mews Adyen integration. *Note in Adyen, you may have to generate a new password, and also generate the `Client Encryption` Key*.
	- Copy `Client Side Encryption Key` in Adyen into `Public Key` in Mews  (check with adyen if it is enabled).
	- Copy `Username` in Adyen into `Username` in Mews
	- Copy `Password` in Adyen into `Password` in Mews
		- Usually you have to generate a new password and copy it *and save the dialogue with the new password* (without this, it will still have the old password you don't know) and paste the password to MEWS integration.
	- Make sure in Adyen that all merchant accounts are set to **Manual** capture (Settings -> Merchant Settings -> Capture = Manual).
- Add new `Merchant Account` in MEWS for every account in Adyen.
	- Fill in the Settlement currency and account name (same as in Adyen - you will find it under "Accounts" as "Account Code")
	- After saving, you can see generated fields `UserName` and `Password`, which are used to set up `Notifications`.

### Verify connection

1. Get credentials for test account and log in [here](https://ca-test.adyen.com/ca/ca/login.shtml).
2. Follow the setup below and setup this account on a hotel on test/demo.
3. Run a couple of transactions (create gateway payments, refund some, ...). Something aroung 5 transactions is enough.
	- There are test credit cards which should be used, e.g. `4111 1111 1111 1111` with expiry date `08/2018`. 
	- List of all test credit cards is [here](https://www.adyen.com/home/support/knowledgebase/implementation-articles?article=kb_imp_17).
4. Contact Adyen and let them know that we have entered the transactions and they should check if it is fine.
5. Often much later, we get credentials for live environment
6. Follow the setup below and setup this account on production environment.
7. Try adding a credit card to confirm it works fine, then delete it.

### Requirements for web user

Please make sure all of the below roles are set up - you will usually have to ask Adyen to enable these permissions, and make sure that all of them are set up, as they may not set up everything at the first time of asking. Without every single one of those roles being set up, the integration won't work properly:

- API Clientside Encryption Payments role
- General API Payments role
- iDeal Recurring
- Merchant PAL Webservice role
- Merchant Recurring role
- Zero Auth (if hotel wants to be able to charge credit cards which are not chargeable at the moment they are created, e.g. virtual)

Also, the account may need to have Client Side Encryption Key generated. Make sure that the `API Clientside Encryption Payments role` is **active** otherwise you won't be able to integrate! Setting screen:

![Settings](../../../../img/Adyen%20setup.png)

### Setup account

- Go to "Settings" > "Merchant Settings"
- Set Capture Delay to Manual (Select merchant account, Settings -> Merchant Settings)
![Capture delay Settings](../../../../img/AdyenCsetup.png)

### Notifications

This has to be configured **for each** bank account in Adyen that has corresponding enabled payment gateway account in Mews.

1. Select account -> Settings -> Server Communication -> Standard Notification
2. Set URL to `https://www.mews.li/Api/Adyen/v1/notification/process`
3. Set Service Version to `1`.
4. Set Method to `json`.
5. Uncheck `Populate SOAP Action headers`.
6. Set `UserName` and `Password` from MEWS from the corresponding `Merchant account`.
7. Open Additional Settings and enable `Include the originalReference for CHARGEBACK_REVERSED notifications` option.
8. Click Test
   - It should should show list of messages with code 200, which means success.
   - If not credentials or url is wrong, double check the values and repeat this step until success.
9. Check "Active" box.
10. Save.

#### Test Transactions

- When you set up a "test" account, you will need to run several test transactions through the system. This is one of the key requirements for Adyen to allow a test account to go live. Do this with a number of different types of cards. See below some test cards that can be used.
- VISA Card `5100 0811 1222 3332`   Exp: `06/2016`
- MC Card `5100 2900 2900 2909`   Exp: `06/2016`
- Card `5577 0000 5577 0004`  Exp: `06/2016`
- Card `4988 4388 4388 4305`  Exp: `06/2016`
- Card `4111 1111 1111 1111`  Exp: `06/2016`
- Once you have completed the transactions, write to `support@adyen.com` to inform them and check it.
- Once confirmed, the hotel needs to click on the "Go Live" button to go to the next step.

**NOTE**: Often when you try the test transactions, the system will throw an error. This is because the Adyen employee who set up the user rights (as per above) did not do it correctly. If this is the case, go back to Adyen and ask them to check the user settings yet again. Then re-do the configuration, and it should work.
