ARC-Seal: i=2; a=rsa-sha256; s=arcselector9901; d=microsoft.com; cv=pass;
 b=CO7PqBuffA52Wzx6S1+fnIIIR6V3dREhjtrnaxAnKMNDKuVkP2gQyCVHICEDYtTCPrRixZ2EsRd59FPLtg+9TuGHwD6lbMvuDuKj09iOM51Q/wH+48uWSUEO4fuJ0DonC4H+4JGeSmqkOpPC7207hGEuhaJh78ZeiqNjsXYOJMVrZoTy7OXSTRp15M/wzBElGsuAEhznqLvXkoiSZxOU0DksD9AhV9ukoY7bfRZ9HPny14/yR8dOuXVtPgxLjYSGYYPZt2dogPb+i/ER3xUM5a84TLE0GU30G5//y1fiRGieOgkNmtOmfsRYmd9Md7nBuWIx31F4plcM3SNbOxhCBQ==
ARC-Message-Signature: i=2; a=rsa-sha256; c=relaxed/relaxed; d=microsoft.com;
 s=arcselector9901;
 h=From:Date:Subject:Message-ID:Content-Type:MIME-Version:X-MS-Exchange-SenderADCheck;
 bh=6aRaLyQ9ADwfDtg6vXcLynE4LmsRwYAOAxbOiVO5bC0=;
 b=LgNnx6hBO6mdTQpsEBtggpUJ/KRxCOX8eOkGqRARQYbzPbsZTz38qziQNA3Nt7ch8Rt7peYbOPPYxLNwqDIBDkmkYUV+r7aW/m5xDNEenKbLkj1gCrTkGIvQD3s3aEm6b11KZWeU+3bf5VPv4PcFa88B40e0eGOaBe2wUPwcfE7+kbAT8XmDz2es+6Z8oWg5X4tdY7rxgWYQeaCIsISaqMwK+0xmt4QOWKtUkIOUjnWW21ikW64k3ricClYtFCzJ/GkpxtaNsjEW1PZqna+P3Rz9BgFa9+F4nTOMAqV4wtbuah3LtZ6pBH7uu71hgN4TZ4EZ2DTG+HoAV6boEM0v8g==
ARC-Authentication-Results: i=2; mx.microsoft.com 1; spf=pass (sender ip is
 40.107.0.80) smtp.rcpttodomain=battistolli.it smtp.mailfrom=itstrategy.it;
 dmarc=pass (p=reject sp=none pct=100) action=none header.from=itstrategy.it;
 dkim=pass (signature was verified) header.d=itstrategy.it; arc=pass (0 oda=1
 ltdi=1 spf=[1,1,smtp.mailfrom=itstrategy.it]
 dkim=[1,1,header.d=itstrategy.it] dmarc=[1,1,header.from=itstrategy.it])
Received: from MR2P264CA0141.FRAP264.PROD.OUTLOOK.COM (2603:10a6:500:30::33)
 by VI1PR0602MB2768.eurprd06.prod.outlook.com (2603:10a6:800:b2::13) with
 Microsoft SMTP Server (version=TLS1_2,
 cipher=TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384) id 15.20.3763.11; Fri, 15 Jan
 2021 13:12:36 +0000
Received: from VE1EUR01FT021.eop-EUR01.prod.protection.outlook.com
 (2603:10a6:500:30:cafe::af) by MR2P264CA0141.outlook.office365.com
 (2603:10a6:500:30::33) with Microsoft SMTP Server (version=TLS1_2,
 cipher=TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384) id 15.20.3742.12 via Frontend
 Transport; Fri, 15 Jan 2021 13:12:36 +0000
Authentication-Results: spf=pass (sender IP is 40.107.0.80)
 smtp.mailfrom=itstrategy.it; battistolli.it; dkim=pass (signature was
 verified) header.d=itstrategy.it;battistolli.it; dmarc=pass action=none
 header.from=itstrategy.it;
Received-SPF: Pass (protection.outlook.com: domain of itstrategy.it designates
 40.107.0.80 as permitted sender) receiver=protection.outlook.com;
 client-ip=40.107.0.80; helo=EUR02-AM5-obe.outbound.protection.outlook.com;
Received: from EUR02-AM5-obe.outbound.protection.outlook.com (40.107.0.80) by
 VE1EUR01FT021.mail.protection.outlook.com (10.152.2.223) with Microsoft SMTP
 Server (version=TLS1_2, cipher=TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384) id
 15.20.3763.12 via Frontend Transport; Fri, 15 Jan 2021 13:12:35 +0000
ARC-Seal: i=1; a=rsa-sha256; s=arcselector9901; d=microsoft.com; cv=none;
 b=BS//C15nmrHKK7XfE5Il7HmDDLL1g9/F7b0sreVTHJzPEcWnMiywvfFpfuQO3YZTIkdG0rIPxm5ushcAyq8CPrJSUUOCcgD9d98AfIC3n/OMW3wFWvxoC9K9nK5/L+0889UqHDK9BzxLWRfR4sbLzmPVs3PVfyywd1rg0j61JtFAMEZ5cB1aEfaYXgKQTFTp1mUp8M2cNW35LAQYFPST34F3QB4Ioz3WifL6dASmTpW0DEQnykQL7XEbMHty3pVGaeHV1+pOTW/u2OfqcutxSMt7NjgPZFRy+7+ooCCSG9l7raslPNhcfs0VP5Z5AUREVvBm+bhx1NQD7+hhQaoiNw==
ARC-Message-Signature: i=1; a=rsa-sha256; c=relaxed/relaxed; d=microsoft.com;
 s=arcselector9901;
 h=From:Date:Subject:Message-ID:Content-Type:MIME-Version:X-MS-Exchange-SenderADCheck;
 bh=6aRaLyQ9ADwfDtg6vXcLynE4LmsRwYAOAxbOiVO5bC0=;
 b=djOgI9vG8zPaZectXdss9tEY0WI/Mu+VYzSn0DU1RYJb6WV0iNasSWWSa9k7b4+3tKbebwpP1r3S6xgBebVT42tQmD2EcM9GbcbXISDXUfNARMs/xqTNtP0IZ7wCtLVtTIfDkkopNPmQPJQM11Al1eH5KVSOCx8ENVr+F0PMuK5V/zwwAjUqTZ7UTt4bJ+lF2wM1h5CIq3GE0SKVkhdwmoOR7ZNMXl+24CejcOeVbPYtTVFP2Jk0EcljFf2LWsHX9pmZ2rJragTARzgU+svoNLacs6Ic3Vwg4oEaI4uhCU0VVoyg2O/qTdzCn947Nm2uVJMmLYEuZvpFpWgQonyBrA==
ARC-Authentication-Results: i=1; mx.microsoft.com 1; spf=pass
 smtp.mailfrom=itstrategy.it; dmarc=pass action=none
 header.from=itstrategy.it; dkim=pass header.d=itstrategy.it; arc=none
DKIM-Signature: v=1; a=rsa-sha256; c=relaxed/relaxed; d=itstrategy.it;
 s=selector1;
 h=From:Date:Subject:Message-ID:Content-Type:MIME-Version:X-MS-Exchange-SenderADCheck;
 bh=6aRaLyQ9ADwfDtg6vXcLynE4LmsRwYAOAxbOiVO5bC0=;
 b=Nc4Aus5rTscNGePUam2rmHcj4V1U5EHRYxzhy2dXew+57YwQbt+CrAQ1EFYNrwB+euPXUe0um7inTLrqNLGpa7cHXojiHB3/sV1g5kfZxoYp2J26E67alRCx7Hx4bm4kaAPBRVNh47RPK21fUi3eQcmhkZj41hQwUMt/ew4QFag=
Received: from AM0PR04MB5443.eurprd04.prod.outlook.com (2603:10a6:208:119::33)
 by AM0PR04MB4257.eurprd04.prod.outlook.com (2603:10a6:208:63::19) with
 Microsoft SMTP Server (version=TLS1_2,
 cipher=TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384) id 15.20.3763.11; Fri, 15 Jan
 2021 13:12:31 +0000
Received: from AM0PR04MB5443.eurprd04.prod.outlook.com
 ([fe80::6413:cf8e:5f89:9fec]) by AM0PR04MB5443.eurprd04.prod.outlook.com
 ([fe80::6413:cf8e:5f89:9fec%7]) with mapi id 15.20.3763.011; Fri, 15 Jan 2021
 13:12:31 +0000
From: Sales IT Strategy <sales@itstrategy.it>
To: VI - Davide Toniolo <davide.toniolo@battistolli.it>
Subject: R: Richiesta licenze O365 tipo E1 - rif #44194 e il #44191
Thread-Topic: Richiesta licenze O365 tipo E1 - rif #44194 e il #44191
Thread-Index: Adbqk34vQLuOcLhvQCerEsrLSq0vvAArAmUA
Date: Fri, 15 Jan 2021 13:12:30 +0000
Message-ID: <AM0PR04MB544329426079C43FEB4FC17982A70@AM0PR04MB5443.eurprd04.prod.outlook.com>
References: <AM6PR0602MB363826D01E0C76C3DA9210B4E7A80@AM6PR0602MB3638.eurprd06.prod.outlook.com>
In-Reply-To: <AM6PR0602MB363826D01E0C76C3DA9210B4E7A80@AM6PR0602MB3638.eurprd06.prod.outlook.com>
Accept-Language: it-IT, en-US
Content-Language: it-IT
X-MS-Has-Attach: yes
X-MS-TNEF-Correlator:
Authentication-Results-Original: battistolli.it; dkim=none (message not
 signed) header.d=none;battistolli.it; dmarc=none action=none
 header.from=itstrategy.it;
x-ms-exchange-messagesentrepresentingtype: 1
x-originating-ip: [188.152.231.210]
x-ms-publictraffictype: Email
X-MS-Office365-Filtering-Correlation-Id: 7c42926d-3699-403e-4d52-08d8b95739f0
x-ms-traffictypediagnostic: AM0PR04MB4257:|VI1PR0602MB2768:
x-ms-exchange-sharedmailbox-routingagent-processed: True
x-ld-processed: b8d9847d-8c89-45e6-9a9c-023fdf199ce5,ExtAddr
x-ms-exchange-transport-forked: True
x-microsoft-antispam-prvs: <AM0PR04MB42572672D0BB2F23F6FC9769A7A70@AM0PR04MB4257.eurprd04.prod.outlook.com>
x-ms-oob-tlc-oobclassifiers: OLM:1728;
x-ms-exchange-senderadcheck: 1
X-Microsoft-Antispam-Untrusted: BCL:0;
X-Microsoft-Antispam-Message-Info-Original: gaeIhaewPemzO6IVF3s2Ci3ZyO6Jq+s8dxEuTVCMSEl057uRevPTvuHQ3fKQzDTHL7bFiTo1jkVv5v+bSa3s62GRQrx8o2y2ASobatS/XYk53EyfSt0tuNg0kelnucp7a36xlbJchY4Y75QAiSjy4nGF6CVNaCOpRNrRnFzn5IrVc+bSqvQwi46tSxcGZE/o9mQGrAPj9b2+jwmBN76Bw8F60MlqCd1mYN1j8LQOJoykJGG7l28Da5qnfdncAG/USJ1aQdlunDt/LZ23C78gRnv0gXzKPOW34FNeRfX3vv0=
X-Forefront-Antispam-Report-Untrusted: CIP:255.255.255.255;CTRY:;LANG:it;SCL:1;SRV:;IPV:NLI;SFV:NSPM;H:AM0PR04MB5443.eurprd04.prod.outlook.com;PTR:;CAT:NONE;SFS:(4636009)(136003)(366004)(346002)(376002)(396003)(39830400003)(66616009)(71200400001)(186003)(7696005)(64756008)(8936002)(86362001)(6506007)(4270600006)(66446008)(9686003)(52536014)(621065003)(73894004)(26005)(66556008)(2906002)(66476007)(33656002)(316002)(66946007)(76116006)(8676002)(6916009)(99936003)(478600001)(55016002);DIR:OUT;SFP:1101;
X-MS-Exchange-AntiSpam-MessageData-Original: =?us-ascii?Q?uSOxgeabHloDncfkHxRc0Cxmbj3pQVT+EJVxNxt0q5Djc+BOMM78L7m6KxwF?=
 =?us-ascii?Q?fL6mY+qJkb6PeFtIQ8xBaEyWiS18BSg1EzPCrWdyjODipzox0ZweANWc72in?=
 =?us-ascii?Q?XwG3TwHf/mPzeZUtd4FHVc3ZSZET5bjtu6tiNmzj1Xcf41DVQ2nNB1eGrljA?=
 =?us-ascii?Q?TAS2+meSf9eBpZcqE+giJBKK6b0Ot7c4yudJg7cvf5cMKx+hsXqTCSOvQUXJ?=
 =?us-ascii?Q?302JADlU4rQ96iH9DENXIHoZ7PQpEcTlIuEw+fZAxjRY8C9DHu1VHSJLBaWC?=
 =?us-ascii?Q?S8bLDbC0p2Hrp4Su0J65bpp7vaB/3gEZ0d41KziNaMaZCfc06/7LkUjEcyEE?=
 =?us-ascii?Q?mt2EUW1f/qn1IUQ5VMxA5lJBz4zYXo+yl4+Eamg1F0dW0mdt3P644vp0FjVB?=
 =?us-ascii?Q?TEjf/twRY4WvhpmhyygsUXbI1hBDDasWzHZvgGg4Okq5D3H8Y6spK03laA5G?=
 =?us-ascii?Q?CkX2pKLRgWDS8+Lvr7nnD8i65IBCSW8m6TWC4Fskmj7B7dMaJ4Q882Y9t1Ya?=
 =?us-ascii?Q?R83PFYG+ZmwuKvaa72muoF9IpogxaPd0xhhXh5f1XWjgMi4H6o401um7esek?=
 =?us-ascii?Q?HsgGHI+bN3m6EwRq6cwyrgvnKCk79PvaZncoo9c9EqM1j50KAtWw77MuD/8G?=
 =?us-ascii?Q?2nBT/1FK53DQEuLEAuRw+8ueXfjDmyaqykeEMklTwIqyBZf2oK5sDO62K5PM?=
 =?us-ascii?Q?ybga2TDU26JrOThvGJd407iBs3O+OBwLWfyK3Uqr3czG+DvU+4pNdfdevaqq?=
 =?us-ascii?Q?iW9BHSFY36Dab/8nTTWmOryjZOSiwB9d7I3k+1wTbAktOZM5tKcqRDHVTY0A?=
 =?us-ascii?Q?LOVoa0W/UvzIoZByTnle6DKyVmC/77LAg/kGrpwH2r2ihkCTQdZuEaukQ+Yn?=
 =?us-ascii?Q?F2kkUQCDQdSesyrwP8QXqHnqNtrWu+ZR0huduPRYW3VU5+QsedLcWYwm5M9M?=
 =?us-ascii?Q?oq9Urj6Vu81hv5Fah81N4nhkn78wjQD7rfRCfiMHzEo=3D?=
Content-Type: application/pkcs7-mime; smime-type=signed-data; name="smime.p7m"
Content-Disposition: attachment; filename="smime.p7m"
Content-Transfer-Encoding: base64
X-MS-Exchange-Transport-CrossTenantHeadersStamped: AM0PR04MB4257
Return-Path: sales@itstrategy.it
X-MS-Exchange-Organization-ExpirationStartTime: 15 Jan 2021 13:12:35.8268
 (UTC)
X-MS-Exchange-Organization-ExpirationStartTimeReason: OriginalSubmit
X-MS-Exchange-Organization-ExpirationInterval: 1:00:00:00.0000000
X-MS-Exchange-Organization-ExpirationIntervalReason: OriginalSubmit
X-MS-Exchange-Organization-Network-Message-Id: 7c42926d-3699-403e-4d52-08d8b95739f0
X-EOPAttributedMessage: 0
X-EOPTenantAttributedMessage: 456654f0-4d1f-4c49-8277-ff600620ea3d:0
X-MS-Exchange-Organization-MessageDirectionality: Incoming
X-MS-Exchange-Transport-CrossTenantHeadersStripped: VE1EUR01FT021.eop-EUR01.prod.protection.outlook.com
X-MS-Exchange-Transport-CrossTenantHeadersPromoted: VE1EUR01FT021.eop-EUR01.prod.protection.outlook.com
X-MS-Exchange-Organization-AuthSource: VE1EUR01FT021.eop-EUR01.prod.protection.outlook.com
X-MS-Exchange-Organization-AuthAs: Anonymous
X-MS-Office365-Filtering-Correlation-Id-Prvs: 6bc48a80-fcd3-455c-c6b5-08d8b9573732
X-MS-Exchange-AtpMessageProperties: SA
MIME-Version: 1.0