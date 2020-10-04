# DapiConnect-Xamarin.Android
Financial APIs to connect users' bank accounts

# Dapi Xamarin.Android SDK

## Overview

### Introduction

Dapi for Xamarin.Android is a prebuilt SDK that reduces the time it takes to integrate with Dapi's API and gain access to your users financial data.

The SDK provides direct access to Dapi endpoints and offers optional UI to manage users' accounts, subaccounts, balance and money transfer.

### Requirement

- minSdkVersion 21
- App key (obtain from [Dapi Dashboard](https://dashboard.dapi.co/))

## Integration




## How it Works

DapiConnect SDK communicates with API endpoints to make network requests. Requests do NOT go to Dapi servers directly. Instead, requests first go to your server and then to Dapi servers. See the example gif below:
> *(don't worry, we also have an [SDK for your server](https://github.com/dapi-co/sdk-server))*

![dfd](https://github.com/dapi-co/DapiConnect-iOS/raw/master/DapiConnectGIF.gif)

This is a security feature that keeps control in your hands. Your server is responsible for maintaining access tokens by creating, storing, and refreshing them.

## Integration

1. Initialize the SDK 

	```c#
	 DapiConfigurations dapiConfigurations = new DapiConfigurations(
            appKey, //Your app key
            baseUrl,
            environment, //DapiEnvironment.SANDBOX or DapiEnvironment.PRODUCTION
            supportedCountriesCodes, //List of supported countries, fill up the countries you want to support using two-letter country codes (ISO 3166-1 alpha-2)
            autoTruncate, //OPTIONAl. to auto truncate beneficiary and transfer info 
            clientUserID, //OPTIONAL. your user ID, used to destinguish between different users on the same device
            userID, //OPTIONAL. you can obtain userID using dapiApp.connect.getConnections. Initially it will be null, but you can use this as the default userID afterwards.
            isExperimental, //OPTIONAL. for showing experimental banks.
            theme, //OPTIONAL. DapiTheme.GENERAL or DapiTheme.ELEGANT or DapiTheme.ELECTRIC
            extraHeaders, //OPTIONAL. Headers to add to all requests
            extraParams, //OPTIONAL. Params to add to all requests
            extraBody, //OPTIONAL. Body to add to all requests
            dapiEndPoints //OPTIONAL. DapiEndpoints settings object for different endpoints
        );

            DapiClient dapiClient = new DapiClient(this.Application, dapiConfigurations);
	```
	>See `DapiClient` for more


	You can get your app key [from here](https://dashboard.dapi.co/)

2. If you're NOT using the SDK-Server:

	We provide an SDK for your server so Dapi-Xamarin.Android can talk to it. By default, Dapi-Xamarin.Android talks to the endpoints specified in [Dapi docs](https://docs.dapi.co/). 


## Components


1. DapiConnect

	Responsible for showing credential input, authorization, authentication and a list of banks. You can receive callbacks by implementing `OnDapiConnectListener` interface.

	```c#
  dapiClient.Connect.Present();
	```

	```c#
  dapiClient.Connect.SetOnConnectListener(new OnDapiConnectListener());
  
	class OnDapiConnectListener : Java.Lang.Object, IOnDapiConnectListener
    {
        public void OnConnectionFailure(DapiError error, string bankID)
        {

        }

        public void OnConnectionSuccessful(string userID, string bankID)
        {

        }

        public void OnProceed(string userID, string bankID)
        {

        }

        public DapiBeneficiaryInfo SetBeneficiaryInfoOnConnect(string bankID)
        {
            LinesAddress lineAddress = new LinesAddress();
            lineAddress.Line1 = "line1";
            lineAddress.Line2 = "line2";
            lineAddress.Line3 = "line3";

            DapiBeneficiaryInfo info = new DapiBeneficiaryInfo(
                lineAddress,
                "xxxxxxxxx",
                "xxxxx",
                "xxxx",
                "xxxxx",
                "xxxxxxxxxxxxxxxxxxxxxxxxx",
                "UNITED ARAB EMIRATES",
                "branchAddress",
                "branchName",
                "xxxxxxxxxxx"
            );

            return info;
        }
    }
	```
	
	```c#
dapiClient.Connect.GetConnections(new OnGetConnectionsSuccess(), new OnGetConnectionsFailure());

 class OnGetConnectionsSuccess : Java.Lang.Object, Kotlin.Jvm.Functions.IFunction1
    {
        public Java.Lang.Object Invoke(Java.Lang.Object p0)
        {
            return p0;
        }
    }

    class OnGetConnectionsFailure : Java.Lang.Object, Kotlin.Jvm.Functions.IFunction1
    {
        public Java.Lang.Object Invoke(Java.Lang.Object p0)
        {
            return p0;
        }
    }
	 ```

2. DapiAutoFlow

	You can use autoflow to display the connected accounts, balance for each subaccount and a numpad to make a transaction.

	```c#
dapiClient.AutoFlow.Present(null, 0);

	```

	```c#
dapiClient.AutoFlow.SetOnTransferListener(new OnDapiTransferListener());
class OnDapiTransferListener : Java.Lang.Object, IOnDapiTransferListener
    {
        public void OnAutoFlowFailure(DapiError error, string senderAccountID, string recipientAccountID)
        {

        }

        public void OnAutoFlowSuccessful(double amount, string senderAccountID, string recipientAccountID, string jobID)
        {

        }

        public void OnPaymentStarted(string bankID)
        {

        }

        public DapiBeneficiaryInfo SetBeneficiaryInfoOnAutoFlow(string bankID)
        {
            LinesAddress lineAddress = new LinesAddress();
            lineAddress.Line1 = "line1";
            lineAddress.Line2 = "line2";
            lineAddress.Line3 = "line3";

            DapiBeneficiaryInfo info = new DapiBeneficiaryInfo(
                lineAddress,
                "xxxxxxxxx",
                "xxxxx",
                "xxxx",
                "xxxxx",
                "xxxxxxxxxxxxxxxxxxxxxxxxx",
                "UNITED ARAB EMIRATES",
                "branchAddress",
                "branchName",
                "xxxxxxxxxxx"
            );

            return info;
        }
    }
	```
3. Data, Payment, Metadata, Auth

	You can use these to use our functions separately and build your own flow and UI.

	```c#
		 dapiClient.Data.GetIdentity(new OnSuccess(), new OnFailure());
     dapiClient.Data.GetAccounts(new OnSuccess(), new OnFailure());
     dapiClient.Data.GetBalance(new OnSuccess(), new OnFailure());
     dapiClient.Data.GetTransactions(accountID, fromDate, toDate, new OnSuccess(), new OnFailure());
     dapiClient.Payment.CreateBeneficiary(new DapiBeneficiaryInfo(), new OnSuccess(), new OnFailure());
     dapiClient.Payment.CreateTransfer(receiverID, senderID, amount, remark, new OnSuccess(), new OnFailure());
     dapiClient.Payment.CreateTransfer(iban, name, senderID, amount, remark, new OnSuccess(), new OnFailure());
     dapiClient.Payment.GetBeneficiaries(new OnSuccess(), new OnFailure());
     dapiClient.Metadata.GetAccountMetaData(new OnSuccess(), new OnFailure());
     dapiClient.Auth.Delink(new OnSuccess(), new OnFailure());
	```
	
Finally, you should release the SDK when your app closes using

```c#
dapiClient.Release();
```
