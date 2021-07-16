Received: from HK2PR02MB3921.apcprd02.prod.outlook.com (2603:1096:202:1f::14)
 by HK2PR02MB4113.apcprd02.prod.outlook.com with HTTPS; Wed, 14 Jul 2021
 08:17:59 +0000
Received: from HK2PR06CA0006.apcprd06.prod.outlook.com (2603:1096:202:2e::18)
 by HK2PR02MB3921.apcprd02.prod.outlook.com (2603:1096:202:1f::14) with
 Microsoft SMTP Server (version=TLS1_2,
 cipher=TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384) id 15.20.4308.20; Wed, 14 Jul
 2021 08:17:57 +0000
Received: from HK2APC01FT029.eop-APC01.prod.protection.outlook.com
 (2603:1096:202:2e:cafe::4c) by HK2PR06CA0006.outlook.office365.com
 (2603:1096:202:2e::18) with Microsoft SMTP Server (version=TLS1_2,
 cipher=TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384) id 15.20.4331.21 via Frontend
 Transport; Wed, 14 Jul 2021 08:17:57 +0000
Authentication-Results: spf=pass (sender IP is 123.126.97.3)
 smtp.mailfrom=163.com; judytmdevorg.onmicrosoft.com; dkim=pass (signature was
 verified) header.d=163.com;judytmdevorg.onmicrosoft.com; dmarc=pass
 action=none header.from=163.com;compauth=pass reason=100
Received-SPF: Pass (protection.outlook.com: domain of 163.com designates
 123.126.97.3 as permitted sender) receiver=protection.outlook.com;
 client-ip=123.126.97.3; helo=mail-m973.mail.163.com;
Received: from mail-m973.mail.163.com (123.126.97.3) by
 HK2APC01FT029.mail.protection.outlook.com (10.152.248.195) with Microsoft
 SMTP Server id 15.20.4331.21 via Frontend Transport; Wed, 14 Jul 2021
 08:17:56 +0000
DKIM-Signature: v=1; a=rsa-sha256; c=relaxed/relaxed; d=163.com;
	s=s110527; h=Date:From:Subject:Message-Id; bh=O0c9kn9mQ7lVGabzea
	Q44BV1/XcucmGNZi4xBH98V0c=; b=pZaPql/zsyn40rCvf0gfXLeFgDsT8Sbzhw
	DciFQJsb3ktBflmxmCiQCVOeTyaFw7nor00Hs5wEgSHjyeKRUnyD0CbxnKNtmAd3
	LfvX25agEsVWr5UbtJK7RsKXTHkSLKVHMAzS7DSIh/108SfWUqvf75fNkzUYXSCV
	L85P+yk2w=
Received: from SMTP.163.com (unknown [40.86.187.30])
	by smtp3 (Coremail) with SMTP id G9xpCgBHwUxhne5gY10HGw--.25117S2;
	Wed, 14 Jul 2021 16:17:11 +0800 (CST)
Date: Thu, 8 July 2021 04:01:03 +0800
From: Thanks <yeyun123@163.com>
Subject: Testing
To: 12344321@judytmdevorg.onmicrosoft.com
X-CM-TRANSID: G9xpCgBHwUxhne5gY10HGw--.25117S2
Message-Id: <60EE9DB4.62F54C.22969@mail-m973.mail.163.com>
X-Coremail-Antispam: 1Uf129KBjDUn29KB7ZKAUJUUUUU529EdanIXcx71UUUUU7v73
	VFW2AGmfu7bjvjm3AaLaJ3UbIYCTnIWIevJa73UjIFyTuYvjxUn8nOUUUUU
X-Originating-IP: [40.86.187.30]
Sender: yeyun0108@163.com
X-CM-SenderInfo: p1h130aqrqmqqrwthudrp/1tbi8Beqm1uocF8pvgADse
Return-Path: yeyun0108@163.com
X-MS-Exchange-Organization-ExpirationStartTime: 14 Jul 2021 08:17:56.9181
 (UTC)
X-MS-Exchange-Organization-ExpirationStartTimeReason: OriginalSubmit
X-MS-Exchange-Organization-ExpirationInterval: 1:00:00:00.0000000
X-MS-Exchange-Organization-ExpirationIntervalReason: OriginalSubmit
X-MS-Exchange-Organization-Network-Message-Id:
 30953f55-e06b-4e55-ea4b-08d9469fe2d7
X-EOPAttributedMessage: 0
X-EOPTenantAttributedMessage: 3beefe83-fe68-4852-80fc-ddda38916366:0
X-MS-Exchange-Organization-MessageDirectionality: Incoming
X-MS-PublicTrafficType: Email
MIME-Version: 1.0
Content-Type: text/plain
X-MS-Exchange-Organization-AuthSource:
 HK2APC01FT029.eop-APC01.prod.protection.outlook.com
X-MS-Exchange-Organization-AuthAs: Anonymous
X-MS-Office365-Filtering-Correlation-Id: 30953f55-e06b-4e55-ea4b-08d9469fe2d7
X-MS-TrafficTypeDiagnostic: HK2PR02MB3921:
X-MS-Oob-TLC-OOBClassifiers: OLM:1051;
X-MS-Exchange-Organization-SCL: 1
X-Microsoft-Antispam: BCL:0;
X-Forefront-Antispam-Report:
 CIP:123.126.97.3;CTRY:CN;LANG:en;SCL:1;SRV:;IPV:NLI;SFV:NSPM;H:mail-m973.mail.163.com;PTR:mail-m973.mail.163.com;CAT:NONE;SFS:(4636009)(1096003)(5660300002)(82430400002)(82202003)(426003)(36906005)(8676002)(7596003)(6916009)(58800400005)(558084003)(7846003)(19618925003)(6666004)(33656002)(3480700007)(26005)(22186003)(7116003)(956004)(336012)(4270600006)(356005)(38606007)(35100500006)(3962001);DIR:INB;
X-MS-Exchange-CrossTenant-OriginalArrivalTime: 14 Jul 2021 08:17:56.6402
 (UTC)
X-MS-Exchange-CrossTenant-Network-Message-Id: 30953f55-e06b-4e55-ea4b-08d9469fe2d7
X-MS-Exchange-CrossTenant-Id: 3beefe83-fe68-4852-80fc-ddda38916366
X-MS-Exchange-CrossTenant-AuthSource:
 HK2APC01FT029.eop-APC01.prod.protection.outlook.com
X-MS-Exchange-CrossTenant-AuthAs: Anonymous
X-MS-Exchange-CrossTenant-FromEntityHeader: Internet
X-MS-Exchange-Transport-CrossTenantHeadersStamped: HK2PR02MB3921
X-MS-Exchange-Transport-EndToEndLatency: 00:00:02.9360908
X-MS-Exchange-Processed-By-BccFoldering: 15.20.4308.027
X-Microsoft-Antispam-Mailbox-Delivery:
	ucf:0;jmr:0;auth:0;dest:I;ENG:(20160514016)(750129)(520011016)(944506458)(944626604);
X-Microsoft-Antispam-Message-Info:
	=?us-ascii?Q?DHONr0kWBqA2h/0l0r/IHmMORhQcSpKW8R9jZECHekm+pDcCXVcvaDGhKOxL?=
 =?us-ascii?Q?iCLwnl64pMNksaz/V8v4w04d1fCAdRURDYBsXpqQZhsGF6f5RA4Zo7YXp922?=
 =?us-ascii?Q?cJy2PPbhjNan350BXYaRmDeTZ1lcydelR0H0SKr5XYbpXwsXz1lXtWo2aKIP?=
 =?us-ascii?Q?gOuNUMtQsml3QRfAB6cBtm/F1+hvlk3VDhtDPTpLCONM3nV4Jps4QoA70qN7?=
 =?us-ascii?Q?6ivSaBooSQKBwzKGJCcNDrq9W4mDaMAXgraYQrv+9ZE1s9hQCqb8m8swDAKl?=
 =?us-ascii?Q?ZjsJRFWdpFO8bzdQVXo86y84bCvEww2QkuVoxHjTB3T71fkU3lLrNm8YVDWw?=
 =?us-ascii?Q?yKe+/r0BYBBy6NuTF4GQQf8fMvTQZAR+MvtHlX5502pbFMnosZBqvoRGvOTj?=
 =?us-ascii?Q?GsGlUzWG6tEhmHP+izKWXHSEypgy85ogAwNhLzY6GAv4JuaMcz9tBoPlN1f+?=
 =?us-ascii?Q?ity2Eth+Pq+HgYOFJh6yF4I8gMcYj2xsvLbLRxotUQGuZqS27W4q3ZXNSuNb?=
 =?us-ascii?Q?f8S/XByHTsApxzQ0KkGOZ9v6HXDG/s+e0ptJdjfVBlBLuS6E+1Jy4gn8jvae?=
 =?us-ascii?Q?+kbCJStrKeNyv32O2HZg5fal1QKlvr3HYIB1hMpnOkLRc6nxRXAluFEtPwOw?=
 =?us-ascii?Q?jWCO6q6zapgSo7uroGF/wZ913kdrDR7HTdDq9QVN4M0v1SqZ86pdFof5JPWP?=
 =?us-ascii?Q?vcw9tqXaLvcERHyagSyn6xHNv6juTU8kDDc5ejvEI8AK/h16Cx6rnT1YuoE8?=
 =?us-ascii?Q?PDugZp+r4sVsq2F7jjMthYUZn27lwxea0TylLQYuMWO1meXKHZe7GNIF5UzZ?=
 =?us-ascii?Q?m+SW/2JIqIYVKOtCX3DU2gO/APVUa+pj13NUmTh/5DXFNrO81rO6okki7mhv?=
 =?us-ascii?Q?bHsHf5j/K18mhM+MfxPUIpjpmVkDHvHynOgxCJNc+NfQSETFZvwAlWis85GM?=
 =?us-ascii?Q?95hrtjG3lic4RbDTA5of/d6gI2uygx7FcNV4N8y9jPbsTOSUZUrLARTATz2l?=
 =?us-ascii?Q?7M5wZgfNAwUA5uduN2Y3aOH7IZfIv++0MFtlzCNqDJZhKLuKyp/rKYkC6xsV?=
 =?us-ascii?Q?Jd0NBVmgsq1Mhfbmuul0Ak4ThFsLZ9rWNCywSLq6dvDqOGORr4sQPdNpd6O4?=
 =?us-ascii?Q?DcicJdX3c1faUhUb2iKzYtjBQ8yDUsQOF9gbulfA2MCxhIzugsiz76QqygX7?=
 =?us-ascii?Q?F2YRWNVrbs/qee9laQ2xOR4RIMKJYMYyWTLMbcC/o68QB58jEuHNXRNPcp2G?=
 =?us-ascii?Q?EFKdFfXo5cora/p8qcwTqUupK9p3E43gPjb7DjgwTSh+IjrBGWVg+hsnbVIP?=
 =?us-ascii?Q?rGXYvQJpX10UnmPSgolQreFtsi0luSCWeAqjXW+fs1yVFHoKaYI77JkVuHzn?=
 =?us-ascii?Q?gDN81TNyNz31687Oqtrsd3YUU6LEMWXF4WEHqHwsNiZSjJmP4tNmq+qUfifG?=
 =?us-ascii?Q?xSUO7YdJ0wqEXxO6zNAs12KiI/WIJrnWCupM0fSEk9MCfgFbxdNgGBUYtmJl?=
 =?us-ascii?Q?6KhOrU3QNLGxKnRvVmVCcaP/M8p8YDgP7PGKO2O61kCHxA7lZZ5Jh47jm7au?=
 =?us-ascii?Q?Vgx2QIxVIC1QX905yYRSYWyWyRLY/hzhyJui3IV5HRZw33YaMGEKw0HzSC0L?=
 =?us-ascii?Q?RCndv/9qD1q/Y39X0ehmqrZ08QWR/E18SIiHd1V3dyau4E8b4jzEk14J86vw?=
 =?us-ascii?Q?V2AgXf2uADtmIoQFOMwGCmfbEwyi87c1xNzVrjVgNkjJ+iT2ud409SkpB1MC?=
 =?us-ascii?Q?3KlDOdxiYyioJRxV5kVViVyg9DCJWlYKJwXSoGjg515qT89sBaesSA+IBK/E?=
 =?us-ascii?Q?NmistLDdBv+Ivg=3D=3D?=
