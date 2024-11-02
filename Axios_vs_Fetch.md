_English version:_ https://blog.logrocket.com/axios-vs-fetch-best-http-requests/

## Is Axios better than fetch()?

Men yaqinda “Axios bilan HTTP so'rovlarini professional darajada qanday qilish mumkin” nomli maqolamda Axios kutubxonasidan foydalanishning afzalliklarini muhokama qildim. Shunga qaramasdan, ba'zi holatlarda Axios ideal yechim bo‘lmasligi va HTTP so'rovlarini amalga oshirish uchun undan ham yaxshi variantlar mavjudligini tan olish muhimdir.

Ba'zi dasturchilar Axios'ni ichki API'larga nisbatan qulayroq deb hisoblashadi, ammo ko‘pchilik bunday kutubxonaga ehtiyojni ortiqcha baholaydi. Aslida, `fetch()` API'si Axios’ning asosiy xususiyatlarini takrorlashga qodir va u zamonaviy brauzerlarda tayyor holatda mavjud bo‘lish afzalligiga ega.

Ushbu maqolada biz `fetch()` va Axios'ni solishtirib, ularning turli vazifalarni qanday bajarishi mumkinligini ko‘rib chiqamiz. Umid qilamanki, maqola oxiriga kelib, siz ikkala API haqida yaxshiroq tushunchaga ega bo'lasiz.

### Asosiy sintaksis
Axios ning yanada murakkab xususiyatlariga o‘tishdan oldin, uning asosiy sintaksisini `fetch()` bilan solishtirib ko‘ramiz.

Quyida qanday qilib Axios yordamida URL manzilga maxsus sarlavhalar (headers) bilan POST so‘rovi yuborishingiz mumkinligi ko'rsatilgan. Axios ma'lumotlarni avtomatik ravishda JSON formatiga o'giradi, shuning uchun buni qo'lda qilishingizga hojat yo'q:

#### Axios yordamida

```javascript
// axios
  const url = 'https://jsonplaceholder.typicode.com/posts';
  const data = {
    userId: 1,
    title: 'Any Title',
    description: 'Some Description',
  };
  const options = {
    headers: {
      Accept: 'application/json',
      'Content-Type': 'application/json;charset=UTF-8',
    },
  };
  axios.post(url, data, options).then(({ data }) => {
    console.log('axios: ', data);
  });
```

Endi esa, yuqoridagi kodning `fetch()` bilan yozilgan versiyasini ko'rib chiqamiz, bu ham xuddi shu natijani beradi:

#### Fetch yordamida

```javascript
// fetch()

const url = "https://jsonplaceholder.typicode.com/todos";
const options = {
  method: "POST",
  headers: {
    Accept: "application/json",
    "Content-Type": "application/json;charset=UTF-8",
  },
  body: JSON.stringify({
    a: 10,
    b: 20,
  }),
};
fetch(url, options)
  .then((response) => response.json())
  .then((data) => {
    console.log(data);
  });
```

#### Diqqat qiling:

- Ma'lumot yuborishda `fetch()` `body` xususiyatidan foydalanadi, bu orqali post so'rovida endpointga ma'lumot yuboriladi, `Axios` esa `data` xususiyatidan foydalanadi.
- `fetch()`da ma'lumotlarni JSON formatida yuborish uchun `JSON.stringify` metodidan foydalaniladi.
- Axios serverdan qaytgan ma'lumotlarni avtomatik ravishda JavaScript obyektiga aylantiradi, lekin `fetch()`da esa `response.json` metodini chaqirib, ma'lumotlarni JavaScript obyektiga o‘girish kerak.
- Axiosda serverdan kelgan ma'lumot `data` obyektida saqlanadi, `fetch()`da esa ma'lumot nomi istalgan o'zgaruvchiga berilishi mumkin.

### Moslikni ta'minlash

Axios’ning asosiy afzalliklaridan biri keng ko‘lamli brauzer qo‘llab-quvvatlashi hisoblanadi. Hatto eski brauzerlar, masalan, Internet Explorer 11 ham hech qanday muammosiz Axios'ni ishga tushira oladi. Buning sababi, Axios asosida `XMLHttpRequest` texnologiyasidan foydalanadi.

Boshqa tomondan, `fetch()` faqat quyidagi brauzerlarda ishlaydi: Chrome 42+, Firefox 39+, Edge 14+ va Safari 10.3+. (To'liq moslik jadvalini [CanIUse.com](https://caniuse.com) saytida ko‘rishingiz mumkin.)

Agar faqat orqaga moslikni ta'minlash uchun Axios'ni ishlatayotgan bo'lsangiz, unda HTTP kutubxonasi kerak emas. Buning o'rniga, fetch()'ni polyfill bilan birga ishlatishingiz mumkin, bu fetch() ni qo‘llab-quvvatlamaydigan brauzerlarda ham xuddi shunday funksionallikni taqdim etadi.

#### `fetch()` polyfill'dan foydalanish

`fetch()` polyfill’dan foydalanishni boshlash uchun uni npm orqali o‘rnatishingiz mumkin:

```bash
npm install whatwg-fetch --save
```

Keyin, shunday qilib so'rovlar qilishingiz mumkin:

```javascript
import 'whatwg-fetch';
window.fetch(...)
```

E'tibor bering, ba'zi eski brauzerlarda `fetch()` bilan ishlash uchun `promise` polyfill ham kerak bo'lishi mumkin.

### Javob kutish vaqti

Axios’da so'rovning kutish vaqtini o‘rnatish juda sodda, bu ayrim dasturchilarni `fetch()`ga nisbatan Axios’dan foydalanishga jalb qiladi. Axios’da `timeout` xususiyatini sozlash orqali so'rov qancha vaqt ichida bajarilmasa to'xtatilishini osonlik bilan belgilash mumkin.

#### Axios’da so'rov vaqtini belgilash

Quyidagi misolda so'rovning kutish vaqti 4 soniya qilib o‘rnatilgan:

```javascript
axios({
  method: 'post',
  url: '/login',
  timeout: 4000,    // 4 soniya kutish vaqti
  data: {
    firstName: 'David',
    lastName: 'Pollock'
  }
})
.then(response => {/* javobni qayta ishlash */}
.catch(error => console.error('kutish vaqti oshib ketdi'));
```

#### Fetch() da so'rov vaqtini belgilash

`fetch()`da shunga o'xshash funksionallik `AbortController` interfeysi yordamida amalga oshiriladi. Ammo bu biroz murakkabroq:

```javascript
const controller = new AbortController();
const options = {
  method: 'POST',
  signal: controller.signal,
  body: JSON.stringify({
    firstName: 'David',
    lastName: 'Pollock'
  })
};  
const promise = fetch('/login', options);
const timeoutId = setTimeout(() => controller.abort(), 4000);

promise
  .then(response => {/* javobni qayta ishlash */})
  .catch(error => console.error('kutish vaqti oshib ketdi'));
```

Bu yerda biz `AbortController` obyektini `AbortController.abort()` konstruktoridan foydalanib yaratdik, bu esa so‘rovni keyinchalik to'xtatishga imkon beradi. `signal` — `AbortController`ning faqat o‘qish uchun mo'ljallangan xususiyati bo‘lib, so‘rov bilan aloqada bo‘lish yoki uni bekor qilish imkoniyatini beradi. Agar server 4 soniyadan kamroq vaqt ichida javob bermasa, `controller.abort()` chaqiriladi va operatsiya to‘xtatiladi.

### Avtomatik JSON ma'lumotlarni o‘zgartirish

Yuqorida ko‘rib o‘tganimizdek, Axios so‘rov yuborayotganda ma'lumotlarni avtomatik ravishda JSON formatiga o‘giradi (agar kerak bo‘lsa, bu standart xatti-harakatni o‘zgartirib, boshqacha transformatsiya mexanizmini belgilash ham mumkin). Ammo `fetch()`da buni qo'lda bajarishingiz kerak bo‘ladi.

#### Taqqoslash

Quyidagi misollarda Axios va fetch()ning JSON formatiga o‘girishdagi farqini ko‘rish mumkin:

```javascript
// axios
axios.get('https://api.github.com/orgs/axios')
  .then(response => {
    console.log(response.data);
  })
  .catch(error => {
    console.log(error);
  });
```

```javascript
// fetch()
fetch('https://api.github.com/orgs/axios')
  .then(response => response.json())    // qo‘shimcha qadam
  .then(data => {
    console.log(data);
  })
  .catch(error => console.error(error));
```

Bu yerda Axios avtomatik ravishda serverdan qaytgan ma'lumotlarni JavaScript obyektiga aylantirib beradi, shuning uchun qo'shimcha hech narsa qilish shart emas. `fetch()`da esa, `response.json()` metodini chaqirishingiz kerak bo‘ladi, bu esa bir qadam ortiqcha bo‘lishi mumkin.

Avtomatik ma'lumot transformatsiyasi juda foydali xususiyatdir, lekin `fetch()` yordamida buni qo‘lda amalga oshirish mumkin, shuning uchun bu unchalik katta cheklov emas.

### HTTP Interceptor'lar

Axios'ning asosiy afzalliklaridan biri uning HTTP so‘rovlarni ushlab qolish (intercept) qobiliyatidir. HTTP interceptor'lar HTTP so‘rovlarini tahlil qilish yoki o‘zgartirish kerak bo‘lgan holatlarda juda foydali, masalan, loglash, autentifikatsiya, yoki muvaffaqiyatsiz so‘rovlarni qayta yuborish uchun ishlatiladi.

Interceptor'lar yordamida har bir so‘rov uchun alohida kod yozish kerak bo‘lmaydi. Interceptor'lar ilovangizda barcha so‘rov va javoblarni qanday boshqarishni global strategiya sifatida belgilash uchun qulay.

#### Axios’da So'rov Interceptor'ni e'lon qilish

Quyidagi kodda, har qanday HTTP so‘rov yuborilishidan oldin ishlatiladigan interceptor qanday e’lon qilinishi ko'rsatilgan:

```javascript
axios.interceptors.request.use(config => {
  // HTTP so'rov yuborilishidan oldin xabar loglash
  console.log('Request was sent');
  return config;
});

// GET so'rov yuborish
axios.get('https://api.github.com/users/sideshowbarker')
  .then(response => {
    console.log(response.data);
  });
```

Bu yerda `axios.interceptors.request.use()` metodi HTTP so'rov yuborilishidan oldin bajarilishi kerak bo‘lgan kodni aniqlaydi. Shuningdek, `axios.interceptors.response.use()` yordamida serverdan keladigan javobni intercept qilish mumkin. Masalan, tarmoqda xatolik yuz bersa, bu orqali so‘rovni qayta urinish mumkin.

#### Fetch()da Interceptor'lar bilan ishlash

`fetch()`da interceptor’lar o‘rnatish uchun maxsus mexanizm mavjud emas, lekin bu muammoni aylanma yo‘l bilan hal qilish mumkin. Global `fetch()` metodini qayta yozib, interceptor kabi ishlaydigan funksiyani yaratishingiz mumkin:

```javascript
fetch = (originalFetch => {
  return (...args) => {
    console.log('Request was sent');
    const result = originalFetch.apply(this, args);
    return result;
  };
})(fetch);

fetch('https://api.github.com/orgs/axios')
  .then(response => response.json())
  .then(data => {
    console.log(data);
  });
```

Bu kodda biz `fetch()` metodini o‘zgartirib, har bir so‘rov oldidan `"Request was sent"` logini chiqarib turamiz. Bu kod `fetch()` metodini qayta belgilab, barcha HTTP so‘rovlar uchun bir xil xatti-harakatni ta'minlaydi. 

Bu usul to'liq interceptor kabi ishlamasa-da, har bir `fetch()` so'rovini kuzatish va loglash imkonini beradi.

### Yuklash jarayonini kuzatish

Yirik fayllarni yuklashda yuklash jarayoni ko'rsatkichi (progress indicator) foydalanuvchilar, ayniqsa, sekin internet tezligida ulanishda foydali hisoblanadi. JavaScript dasturchilari ilgari `XMLHttpRequest.onprogress` callback'ini ishlatib, yuklash jarayoni ko'rsatkichini yaratishgan.

`Fetch` API’da `onprogress` handler mavjud emas. Buning o‘rniga `body` xususiyati orqali `ReadableStream` obyektini foydalanish mumkin.

Quyidagi misol, `ReadableStream` yordamida foydalanuvchiga rasm yuklash jarayonida darhol ma'lumot berish uchun foydalanish usulini ko'rsatadi:

#### HTML fayli (`index.html`)

```html
<div id="progress">progress</div>
<img id="img">
```

#### JavaScript fayli (`script.js`)

```javascript
'use strict'
const element = document.getElementById('progress');

fetch('https://fetch-progress.anthum.com/30kbps/images/sunrise-baseline.jpg')
  .then(response => {
    if (!response.ok) {
      throw Error(response.status + ' ' + response.statusText);
    }
    if (!response.body) {
      throw Error('ReadableStream not yet supported in this browser.');
    }
    const contentLength = response.headers.get('content-length');
    if (!contentLength) {
      throw Error('Content-Length response header unavailable');
    }
    const total = parseInt(contentLength, 10);
    let loaded = 0;
    return new Response(
      new ReadableStream({
        start(controller) {
          const reader = response.body.getReader();
          read();
          function read() {
            reader.read().then(({ done, value }) => {
              if (done) {
                controller.close();
                return;
              }
              loaded += value.byteLength;
              progress({ loaded, total });
              controller.enqueue(value);
              read();
            }).catch(error => {
              console.error(error);
              controller.error(error);
            });
          }
        }
      })
    );
  })
  .then(response => response.blob())
  .then(data => {
    document.getElementById('img').src = URL.createObjectURL(data);
  })
  .catch(error => {
    console.error(error);
  });

function progress({ loaded, total }) {
  element.innerHTML = Math.round((loaded / total) * 100) + '%';
}
```

Bu kod yuklash jarayonini kuzatib, progress indikatorni yangilab boradi.

### Axios’da Progress Bar o‘rnatish

`Axios`da yuklash jarayonini kuzatish osonroq, ayniqsa `Axios Progress Bar` moduli yordamida. Dastlab quyidagi stil va skriptlarni HTML faylga qo'shish kerak:

#### HTML Fayli (`index.html`)

```html
<head>
  <link rel="stylesheet" type="text/css" href="https://cdn.rawgit.com/rikmms/progress-bar-4-axios/0a3acf92/dist/nprogress.css" />
</head>
<body>
  <img id="img" />
  <button onclick="downloadFile()">Get Resource</button>
  <script src="https://unpkg.com/axios/dist/axios.min.js"></script>
  <script src="https://cdn.rawgit.com/rikmms/progress-bar-4-axios/0a3acf92/dist/index.js"></script>
</body>
```

#### Custom Style

```html
<style>
  #nprogress .bar {
    background: red !important;
  }
  #nprogress .peg {
    box-shadow: 0 0 10px red, 0 0 5px red !important;
  }
  #nprogress .spinner-icon {
    border-top-color: red !important;
    border-left-color: red !important;
  }
</style>
```

#### JavaScript

```javascript
<script type="text/javascript">
  loadProgressBar();

  function downloadFile() {
    getRequest("https://fetch-progress.anthum.com/30kbps/images/sunrise-baseline.jpg");
  }

  function getRequest(url) {
    axios
      .get(url, { responseType: "blob" })
      .then(function (response) {
        const reader = new window.FileReader();
        reader.readAsDataURL(response.data);
        reader.onload = () => {
          document.getElementById("img").setAttribute("src", reader.result);
        };
      })
      .catch(function (error) {
        console.log(error);
      });
  }
</script>
```

Bu yerda `FileReader` API yordamida yuklangan rasmni asinxron tarzda o'qib, `Base64` formatida qaytaradi va `img` tegi `src` atributiga joylashtiradi. Bu usul `fetch()` bilan solishtirganda soddaroq va progress-bar bilan yuklash jarayonini kuzatishni osonlashtiradi.

### Bir vaqtda bir nechta so‘rovlarni yuborish

Bir vaqtda bir nechta so‘rovlarni yuborish uchun Axios `axios.all()` metodini taklif qiladi. Shu metodga so‘rovlar massivini yuborish orqali bir nechta HTTP so‘rovni parallel ravishda amalga oshirishingiz mumkin. `axios.spread()` yordamida javoblar massivini alohida o‘zgaruvchilarga ajratish mumkin:

#### Axios yordamida

```javascript
axios.all([
  axios.get('https://api.github.com/users/iliakan'), 
  axios.get('https://api.github.com/users/taylorotwell')
])
.then(axios.spread((obj1, obj2) => {
  // Har ikki so'rov ham tugadi
  console.log(obj1.data.login + ' has ' + obj1.data.public_repos + ' public repos on GitHub');
  console.log(obj2.data.login + ' has ' + obj2.data.public_repos + ' public repos on GitHub');
}));
```

#### Fetch yordamida `Promise.all()` bilan

Xuddi shunday natijani `Promise.all()` yordamida olish mumkin. Barcha `fetch` so‘rovlarini massiv sifatida `Promise.all()`ga yuborasiz, keyin javoblarni `async` funksiyada qayta ishlaysiz:

```javascript
Promise.all([
  fetch('https://api.github.com/users/iliakan'),
  fetch('https://api.github.com/users/taylorotwell')
])
.then(async ([res1, res2]) => {
  const a = await res1.json();
  const b = await res2.json();
  console.log(a.login + ' has ' + a.public_repos + ' public repos on GitHub');
  console.log(b.login + ' has ' + b.public_repos + ' public repos on GitHub');
})
.catch(error => {
  console.log(error);
});
```

#### Taqqoslash

- `axios.all()` va `Promise.all()` bir vaqtning o'zida bir nechta so'rovlarni bajarishga imkon beradi.
- `axios.spread()` javoblarni o‘zgaruvchilarga ajratishni osonlashtiradi.
- `fetch()` bilan `Promise.all()` ishlatishda, `json()` metodini qo‘lda chaqirish talab etiladi, bu esa Axios’da avtomatik tarzda amalga oshiriladi. 

Ikkala usul ham bir nechta so'rovlarni parallel ravishda amalga oshirish imkonini beradi.

### CORS (Cross-Origin Resource Sharing)ni sozlash

CORS — bu serverlarga o‘z resurslarini o‘zidan tashqari boshqa domenlardan yuklanishiga ruxsat berishga imkon beradigan HTTP mexanizmi. CORS’ga ehtiyoj, asosan, boshqa domenlardan umumiy yoki autentifikatsiya qilingan API’lardan ma’lumot olishda yuzaga keladi.

Agar CORS mexanizmi serverda to‘g‘ri sozlanmagan bo‘lsa, har qanday boshqa serverdan amalga oshirilgan so‘rov (Axios yoki `fetch()` orqali) `No Access-Control-Header-Present` xatosini qaytaradi.

#### CORS muammosini hal qilish

CORS’ni to‘g‘ri boshqarish uchun birinchi qadam — bu serverni to‘g‘ri sozlashdir. Sozlash usuli sizning muhit yoki server turiga bog'liq bo‘ladi. Server to‘g‘ri sozlangach, `Access-Control-Allow-Origin` sarlavhasini barcha so‘rovlarga javob sifatida qo‘shadi. Bu sarlavha yordamida server qaysi domenlarga kirish ruxsatini belgilaydi.

**Diqqat**: So'rovga `Access-Control-Allow-Origin` sarlavhasini qo‘shmaslik kerak. Bu sarlavha faqat **javob** uchun ishlatiladi va server tomonidan qaysi domenlarga kirish ruxsati berilganligini aniqlash uchun ishlatiladi.

#### Preflight so'rovlar va OPTIONS metodi

Brauzer CORS bilan bog'liq so‘rovlar uchun so'rov yuborishdan oldin "preflight" (oldindan tayyorlovchi) so'rovni yuboradi. Bu so'rov `OPTIONS` metodi orqali amalga oshiriladi va serverdan CORS qoidalari bajarilayotganligini va so'rov xavfsiz ekanligini tasdiqlaydi. Shunday qilib, asosiy so‘rov yuborilishidan oldin server CORS shartlariga rioya qilayotganini tekshiradi.

#### CORS sozlashning umumiy bosqichlari

1. **Server konfiguratsiyasi**: Serveringizda `Access-Control-Allow-Origin`, `Access-Control-Allow-Methods`, va `Access-Control-Allow-Headers` kabi kerakli sarlavhalarni sozlang. Misol uchun:
   ```javascript
   // Node.js (Express.js) misol
   app.use((req, res, next) => {
     res.header("Access-Control-Allow-Origin", "*");
     res.header("Access-Control-Allow-Methods", "GET, POST, PUT, DELETE, OPTIONS");
     res.header("Access-Control-Allow-Headers", "Content-Type, Authorization");
     next();
   });
   ```

2. **OPTIONS so'rovlariga javob**: Agar CORS so‘rovi `OPTIONS` so‘rovini yuborsa, unga mos ravishda javob qaytarish kerak. Ko‘pchilik serverlar avtomatik ravishda buni bajaradi, lekin kerak bo‘lsa qo‘lda ham sozlashingiz mumkin:
   ```javascript
   app.options('*', (req, res) => {
     res.header("Access-Control-Allow-Origin", "*");
     res.header("Access-Control-Allow-Methods", "GET, POST, PUT, DELETE, OPTIONS");
     res.header("Access-Control-Allow-Headers", "Content-Type, Authorization");
     res.send();
   });
   ```

3. **Axios yoki `fetch()` orqali so'rov yuborish**: Klient tomonidan CORS konfiguratsiyasini maxsus sozlash shart emas, lekin kerak bo'lganda maxsus sarlavhalar yoki autentifikatsiya sozlanmalarini yuborishingiz mumkin:
   ```javascript
   axios.get('https://api.example.com/data', {
     headers: {
       'Authorization': 'Bearer <your-token>',
       'Content-Type': 'application/json'
     }
   })
   .then(response => console.log(response.data))
   .catch(error => console.error(error));
   ```

#### Xulosa

CORS’ni to‘g‘ri boshqarish uchun, birinchi navbatda serveringiz sozlanmalari to‘g‘ri ekanligiga ishonch hosil qiling. So‘rov yuborishda `Access-Control-Allow-Origin` kabi javob sarlavhalarini so‘rovga qo‘shish kerak emas — bu sarlavhalar server tomonidan avtomatik qo‘shiladi. Brauzer CORS shartlariga mos kelgan so‘rovlar uchun `OPTIONS` preflight so'rovini bajarishini ham yodda tuting.

### Javoblarni samarali boshqarish

API’lardan javoblarni boshqarish har qanday ilova uchun muhim ahamiyatga ega. Bu jarayon ikkita asosiy qismga bo'linadi: xatolik kodlarini boshqarish va javob ma'lumotlarini qayta ishlash.

#### Xatoliklarni boshqarish

Axios va `fetch()`dagi xatoliklarni boshqarish o‘ziga xos farqlarga ega. Axios server xatoliklari (masalan, HTTP 404 yoki 500) yuz berganda avtomatik ravishda `Promise`ni rad etadi va `.catch()` blokini ishga tushiradi. `fetch()`da esa bunday emas — server tomonidan qaytarilgan xatoliklar (masalan, 4xx yoki 5xx status kodi) `.catch()` blokini ishga tushirmaydi. Buning o‘rniga, `fetch()` so‘rovi oddiy `Promise`ni qaytaradi, ammo `ok` bayrog‘i `false` bo‘ladi. `fetch()` faqat tarmoq xatoliklarida yoki boshqa so‘rovni to‘xtatib qo‘yadigan muammolar yuz berganda muvaffaqiyatsizlikka uchraydi.

Quyidagi misolda `fetch()` yordamida xatoliklarni qanday boshqarish ko‘rsatilgan:

```javascript
try {
  const res = await fetch('...');

  if (!res.ok) {
    // Javobdagi xatolik (5xx, 4xx)
    switch (res.status) {
      case 400: /* Xatolikni boshqarish */ break;
      case 401: /* Xatolikni boshqarish */ break;
      case 404: /* Xatolikni boshqarish */ break;
      case 500: /* Xatolikni boshqarish */ break;
    }
  }

  // Javob ma'lumotlarini bu yerda qayta ishlash mumkin
} catch (err) {
  // So‘rovdagi xatolik (tarmoq xatoligi)
}
```

Axios’da esa barcha xatoliklarni `.catch()` blokida ajratish mumkin:

```javascript
try {
  let res = await axios.get('...');
  // Javob ma'lumotlarini bu yerda qayta ishlash mumkin
} catch (err) {
  if (err.response) {
    // Javobdagi xatolik (5xx, 4xx)
  } else if (err.request) {
    // So‘rovdagi xatolik (tarmoq xatoligi)
  }
}
```

#### Javob ma'lumotlarini qayta ishlash

Javob muvaffaqiyatli qaytganidan keyin, javob tarkibidagi ma'lumotlarni ishlatish usullari `fetch()` va Axios’da farqlanadi:

- `fetch()`da so‘rov/javob ma'lumotlari `body` maydonida bo‘ladi va `json()` metodi yordamida JSON formatidagi ma'lumotni JavaScript obyektiga aylantirish talab qilinadi.
- Axios esa `data` maydonida avtomatik ravishda JavaScript obyektiga aylantirilgan holda ma'lumotlarni taqdim etadi.

Bu farq quyidagi ikkita kod namunasi orqali tushuntirilgan:

```javascript
// Fetch API yordamida
fetch('...')
  .then(response => response.json())   // json() metodidan foydalanish
  .then(data => console.log(data))
  .catch(error => console.error('Xatolik:', error));
```

```javascript
// Axios yordamida
axios.get('...')
  .then(response => console.log(response.data))  // data to'g'ridan-to'g'ri o'qiladi
  .catch(error => console.error('Xatolik:', error));
```

`fetch()`dagi `.json()` metodi JSON formatidagi ma'lumotni kiritish sifatida qabul qilib, uni JavaScript obyektiga aylantiradi. Axios esa bu jarayonni avtomatik ravishda bajaradi, bu esa `fetch()`ga qaraganda kamroq kod yozishni ta'minlaydi. 

### Xulosa
- **Xatolik boshqaruvi**: Axios server xatoliklarida avtomatik `Promise`ni rad etadi va `.catch()` blokini ishga tushiradi. `fetch()` esa xatoliklarni qo‘lda tekshirishni talab qiladi.
- **Ma'lumotlarni o‘zgartirish**: `fetch()`da `json()` metodidan foydalanish talab qilinadi, Axios esa `data` maydonida to‘g‘ridan-to‘g‘ri obyektni qaytaradi.
