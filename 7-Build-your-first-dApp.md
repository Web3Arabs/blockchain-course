# بناء اول تطبيق لامركزي على Ethereum

في هذا الدرس ستتعلم كيفية إنشاء موقع ويب لا مركزي بسيط وبناء ونشر عقد ذكي بإستخدام لغة Solidity وربط الموقع بالعقد الذكي. سنقوم بإستخدام محفظة MetaMask و Remix IDE لنشر العقد الذكي و Ethers.js للتفاعل مع العقد في موقع الويب. في نهاية ستكون قادر على إنشاء موقع بسيط يتفاعل مع العقد الذكي بكل سهولة.

المراحل التي سيمر بها الدرس التعليمي: <br/>
- إنشاء صفحة HTML بسيطة. <br/>
- إنشاء عقد ذكي بإستخدام لغة Solidity. <br/>
- ربط العقد الذكي بصفحة الويب بإستخدام مكتبة Ethers.js.

## المتطلبات الأساسية:

1. تحميل محفظة MetaMask على المتصفح الخاص بك إن لم تقم بتثبيتها وإنشاء محفظة عليها. <br/>
2. قم بإضافة شبكة الإختبار على المحفظة التي سنقوم بإستخدامها لرفع العقد الذكي من هنا:
<img src="https://www.web3arabs.com/courses/metamask-testnet.png"/>
3. بعد إضافة شبكات الإختبار قم بتغيير الشبكة من Ethereum Mainnet الى Sepolia.
<img src="https://www.web3arabs.com/courses/metamask-testnet2.png"/>
4. يمكنك الحصول على بعض من العملات لكي تقوم بنشر العقد الذكي والتفاعل معه من هنا - <a href="https://sepoliafaucet.com/" target="_blank">https://sepoliafaucet.com/</a>. <br/>
5. تثبيت محرر التعليمات البرمجية (نوصي باستخدام <a href="https://code.visualstudio.com/" target="_blank">Visual Studio Code</a>). <br/>
6. <a href="https://nodejs.org/en/download" target="_blank">قم بتثبيت Node.js</a> إذا لم يكن لديك بالفعل (استخدم إصدار LTS لأنه مستقر). <br/>
7. افتح موجه الاوامر terminal (في نظام التشغيل Windows او macOS أو Linux) وقم بتثبيت lite-server حتى تتمكن من تشغيل موقع الويب الخاص بك. <br/>

```bash
npm install -g lite-server
```

## إنشاء صفحة HTML بسيطة

أول شيء سنقوم بفعله هو إنشاء صفحة ويب بسيطة.

قم بإنشاء مجلد جديد على جهاز الكمبيوتر الخاص بك، وافتح هذا المجلد في محرر الاكواد الخاص بك. داخل هذا المجلد، قم بإنشاء ملف جديد - **Index.html** - ومن ثم قم بفتحه في محرر الاكواد.

تحتوي كل صفحة HTML على بعض القواعد الأساسية التي نحتاج إلى كتابتها لإخبار متصفح الويب أن هذا مستند HTML. أضف ما يلي إلى ملف **Index.html**.

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>DApp اول تطبيق</title>
  </head>
  <body dir="rtl">
  <!-- سنقوم بإضافة الاكواد الاخرى هنا -->
  </body>
</html>
```

داخل الوسم **body** سنبدأ في كتابة بقية التعليمات البرمجية الخاصة بنا.

سيكون العقد الذكي الذي ننشئه بسيطًا للغاية - ما عليك سوى قراءة وكتابة القيمة من وإلى شبكة اختبار **Sepolia**. لنقم بإنشاء **input** وبعض الأزرار على الصفحة.

قم بتحديث وسم **body** الخاصة بك ليبدو كما يلي:

```html
<body dir="rtl">
  <div>
    <h1>تطبيق لإدخال الفصل السنوي الحالي</h1>

    <label for="season">ادخل الفصل السنوي الحالي:</label> <br />
    <input type="text" id="season" />

    <button onclick="getSeason()">إستدعاء الفصل الحالي</button>
    <button onclick="setSeason()">إرسال الفصل</button>

    <p id="showSeason"></p>
  </div>
</body>
```

يمكننا أيضًا كتابة بعض CSS داخل الوسم **head** لجعل موقعنا يبدو أفضل قليلاً. هذه الخطوة اختيارية، ولكنها مفيدة!

```html
<style>
  body {
    text-align: center;
    font-family: Arial, Helvetica, sans-serif;
  }

  div {
    width: 20%;
    margin: 0 auto;
    display: flex;
    flex-direction: column;
  }

  button {
    width: 100%;
    margin: 10px 0px 5px 0px;
  }
</style>
```

الان يمكنك حفظ الملف - index.html - وتشغيل السيرفر بواسطة terminal في مجلد المشروع (ملف index.js).

```bash
lite-server
```

يمكنك الان رؤية صفحة الويب من هنا - <a href="localhost:3000" target="_blank">localhost:3000</a> - بحيث تبدو بهذا الشكل:

<img src="https://www.web3arabs.com/courses/open-first-dapp.png"/>

## إنشاء العقد الذكي

حان الوقت الآن لكتابة عقد ذكي بسيط من Solidity. يمكنك استخدام أي محرر اكواد تريده، ولكن في الوقت الحالي نوصي باستخدام <a href="https://remix.ethereum.org/" target="_blank">Remix</a>.

داخل **Remix،** قم بإنشاء ملف عقد جديد باسم **Season.sol** واكتب الكود التالي:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.19;

contract SeasonYear {
    string public season;

    function setSeason(string memory _season) public {
        season = _season;
    }

    function getSeason() public view returns (string memory) {
        return season;
    }
}
```

**سيبدو بهذا الشكل:**

<img src="https://www.web3arabs.com/courses/remix-code-dapp.png"/>

الآن دعونا ننشر العقد الذكي على شبكة اختبار Sepolia

> بعد المواضيع السابقة، نفترض أنك قمت بالفعل بإنشاء محفظة جديدة لأغراض التعلم والتطوير وليس لديك اموال على الشبكة الاساسية Ethereum Mainnet. من الضروري أن تستخدم محفظة تطوير لا تحتوي على أموال حقيقية عند التعلم وأنشاء العقود وممارستها واختبارها.

1. تأكد ان محفظة MetaMask تعمل على شبكة Sepolia. <br/>
2. في قسم Compile الخاصة بـ Remix، حدد إصدار المترجم المناسب ليتوافق مع Solidity Code (0.8.19). <br/>
3. قم بعمل Compile للكود في قسم Compile. <br/>
4. قم بنشر العقد الذكي الخاص بك في "Deploy and Run Transactions". <br/>
5. يجب أن ترى الآن العقد الخاص بك وتكون قادرًا على اختباره في قسم العقود المنشورة.

<video width="100%" height="100%" controls="controls">
    <source src="https://www.web3arabs.com/courses/remix-code-dapp.mp4" type="video/mp4" />
</video>

## ربط العقد الذكي بصفحة الويب

الآن، دعنا نعود إلى ملف **index.html** في محرر الاكواد الخاص بك. وهنا سوف نقوم ببعض الأمور:

1. إضافة مكتبة **ethers.js** في الكود الخاص بك. <br/>
2. إستخدام **ethers.js** للتفاعل مع العقد الذكي.

في بداية (أو نهاية) ملف HTML الخاص بك، قم باستيراد مكتبة ethers.js كما يلي، وأضف الوسم script:

```html
<script
  src="https://cdn.ethers.io/lib/ethers-5.7.2.umd.min.js"
  type="application/javascript"
></script>

<script>
// هنا JavaScript سنقوم بكتابة كود
</script>
```

لا تنسى نسخ عنوان (address) العقد الذكي الخاص بك الذي قمنا بنشره سابقاً. بالإضافة الى حفظ ABI العقد الذكي.

يمكنك نسخ ABI العقد الذكي الخاص بك من هنا:

<img src="https://www.web3arabs.com/courses/remix-code-abi.png"/>

سيصبح كود JavaScript بهذا الشكل:

```html
<script>
    // العقد الخاص بك هنا address قم بإضافة
    const SeasonContractAddress = "0x...";
    const SeasonContractABI = [
        {
            "inputs": [],
            "name": "getSeason",
            "outputs": [
                {
                    "internalType": "string",
                    "name": "",
                    "type": "string"
                }
            ],
            "stateMutability": "view",
            "type": "function"
        },
        {
            "inputs": [],
            "name": "season",
            "outputs": [
                {
                    "internalType": "string",
                    "name": "",
                    "type": "string"
                }
            ],
            "stateMutability": "view",
            "type": "function"
        },
        {
            "inputs": [
                {
                    "internalType": "string",
                    "name": "_season",
                    "type": "string"
                }
            ],
            "name": "setSeason",
            "outputs": [],
            "stateMutability": "nonpayable",
            "type": "function"
        }
    ];

    // "undefined" لإعطاء المتغيرين قيمة بدل من Ethers سنقوم بإستخدام
    let SeasonContract = undefined;
    let signer = undefined;

    const provider = new ethers.providers.Web3Provider(window.ethereum, "sepolia");

    provider.send("eth_requestAccounts", []).then(() => {
        provider.listAccounts().then((accounts) => {
            signer = provider.getSigner(accounts[0]);
            SeasonContract = new ethers.Contract(
                SeasonContractAddress,
                SeasonContractABI,
                signer
            );
        });
    });

    async function getSeason() {
        const season = await SeasonContract.getSeason();
        document.getElementById("showSeason").innerText = `الفصل الحالي: ${season}`;
        console.log(season);
    }

    async function setSeason() {
        const season = document.getElementById("season").value;
        await SeasonContract.setSeason(season);
    }
</script>
```

**دعونا نقوم بتوضيح ما يحدث في الكود**

نقوم بتحديد موفر Web3 - وهذا هو اتصالنا بشبكة (Sepolia Testnet) - ويتم ذلك من خلال MetaMask.

```javascript
const provider = new ethers.providers.Web3Provider(window.ethereum, "sepolia");
```

نقوم بطلب الوصول إلى محفظة المستخدم وتعيين قيم للمتغير SeasonContract و signer الذين تم تعيينهم مسبقًا undefined.

```javascript
provider.send("eth_requestAccounts", []).then(() => {
    provider.listAccounts().then((accounts) => {
        signer = provider.getSigner(accounts[0]);
        SeasonContract = new ethers.Contract(
            SeasonContractAddress,
            SeasonContractABI,
            signer
        );
    });
});
```

الآن بعد أن أصبح لدينا signer وSeasonContract - يمكننا إنشاء function لاستدعاء دالة getSeason من العقد الذكي والتي تقوم بجلب الفصل الحالي و function لاستدعاء الدالة setSeason من العقد الذكي والتي تقوم بإرسال القيمة الذي يدخلها المستخدم.

```javascript
async function getSeason() {
    const season = await SeasonContract.getSeason();
    document.getElementById("showSeason").innerText = `الفصل الحالي: ${season}`;
    console.log(season);
}

async function setSeason() {
    const season = document.getElementById("season").value;
    await SeasonContract.setSeason(season);
}
```

أخيرًا، قم بتحديث الأزرار الخاصة بك بحيث تقوم باستدعاء هذه الدوال عند النقر عليها

```html
<button onclick="getSeason()">إستدعاء الفصل الحالي</button>
<button onclick="setSeason()">إرسال الفصل</button>
```

الشكل النهائي للكود:

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>DApp اول تطبيق</title>

    <style>
        body {
          text-align: center;
          font-family: Arial, Helvetica, sans-serif;
        }
      
        div {
          width: 20%;
          margin: 0 auto;
          display: flex;
          flex-direction: column;
        }
      
        button {
          width: 100%;
          margin: 10px 0px 5px 0px;
        }
    </style>      
  </head>
  <body dir="rtl">
    <div>
      <h1>تطبيق لإدخال الفصل السنوي الحالي</h1>
  
      <label for="season">ادخل الفصل السنوي الحالي:</label> <br />
      <input type="text" id="season" />
  
      <button onclick="getSeason()">إستدعاء الفصل الحالي</button>
      <button onclick="setSeason()">إرسال الفصل</button>
  
      <p id="showSeason"></p>
    </div>
  </body>  
</html>

<script
  src="https://cdn.ethers.io/lib/ethers-5.7.2.umd.min.js"
  type="application/javascript"
></script>

<script>
    // العقد الخاص بك هنا address قم بإضافة
    const SeasonContractAddress = "0x...";
    const SeasonContractABI = [
        {
            "inputs": [],
            "name": "getSeason",
            "outputs": [
                {
                    "internalType": "string",
                    "name": "",
                    "type": "string"
                }
            ],
            "stateMutability": "view",
            "type": "function"
        },
        {
            "inputs": [],
            "name": "season",
            "outputs": [
                {
                    "internalType": "string",
                    "name": "",
                    "type": "string"
                }
            ],
            "stateMutability": "view",
            "type": "function"
        },
        {
            "inputs": [
                {
                    "internalType": "string",
                    "name": "_season",
                    "type": "string"
                }
            ],
            "name": "setSeason",
            "outputs": [],
            "stateMutability": "nonpayable",
            "type": "function"
        }
    ];

    // "undefined" لإعطاء المتغيرين قيمة بدل من Ethers سنقوم بإستخدام
    let SeasonContract = undefined;
    let signer = undefined;

    const provider = new ethers.providers.Web3Provider(window.ethereum, "sepolia");

    provider.send("eth_requestAccounts", []).then(() => {
        provider.listAccounts().then((accounts) => {
            signer = provider.getSigner(accounts[0]);
            SeasonContract = new ethers.Contract(
                SeasonContractAddress,
                SeasonContractABI,
                signer
            );
        });
    });

    async function getSeason() {
        const season = await SeasonContract.getSeason();
        document.getElementById("showSeason").innerText = `الفصل الحالي: ${season}`;
        console.log(season);
    }

    async function setSeason() {
        const season = document.getElementById("season").value;
        await SeasonContract.setSeason(season);
    }
</script>
```

احفظ الكود الخاص بك، والآن حان الوقت لاختباره!

يمكنك الان رؤية صفحة الويب من هنا - <a href="http://localhost:3000" target="_blank">localhost:3000</a> - وإختبارها بالكامل.

يمكنك الوصول الى المشروع بشكل مباشر على <a href="https://github.com/Web3Arabs/first-dApp" target="_blank"> GitHub من هنا</a>

كما هو الحال دائمًا، إذا كانت لديك أي أسئلة أو شعرت بالتعثر أو أردت فقط أن تقول مرحبًا، فقم بالإنضمام على <a href="https://t.me/Web3ArabsDAO" target="_blank">Telegram</a> او <a href="https://discord.gg/ykgUvqMc4Q" target="_blank">Discord</a> وسنكون أكثر من سعداء لمساعدتك!
