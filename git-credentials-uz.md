# Git credentials — o‘zbekcha tarjima

> Manba: `git help credentials` (Git 2.50.1).

## NAME — NOMI

`gitcredentials` — Git’ga foydalanuvchi nomi va parollarni taqdim etish.

## DESCRIPTION — TAVSIF

Git ba’zan masofaviy repozitoriyga ulanish uchun credential talab qiladi. HTTP transportida bu odatda foydalanuvchi nomi va parol yoki token; SSH’da esa alohida SSH kalit mexanizmi ishlaydi.

Git credentialni quyidagilardan olishi mumkin:

1. URL ichidagi foydalanuvchi nomi: `https://user@example.com/repo.git`.
2. `credential.*` konfiguratsiyasi.
3. Credential helper — xavfsiz saqlash yoki tashqi tizimdan olish dasturi.
4. Terminaldagi interaktiv so‘rov (`GIT_ASKPASS`, `core.askPass`, `SSH_ASKPASS` yoki terminal prompt’i).

Parol yoki tokenni URL ichiga yozish tavsiya etilmaydi: u shell tarixi, jarayonlar ro‘yxati yoki loglarda oshkor bo‘lishi mumkin.

## REQUESTING CREDENTIALS — CREDENTIAL SO‘RASH

Git credential subsystem plumbing interfeysi:

```console
$ git credential fill
protocol=https
host=example.com

```

Kirish bo‘sh satr bilan tugaydi. Git mos credentialni topsa, `username=` va `password=` maydonlarini chiqaradi.

Credential muvaffaqiyatli ishlaganini helper’ga bildirish:

```console
$ git credential approve
protocol=https
host=example.com
username=alice
password=secret

```

Credential rad etilganini yoki yaroqsizligini bildirish:

```console
$ git credential reject
protocol=https
host=example.com
username=alice

```

## AVOIDING REPETITION — TAKRORIY SO‘ROVLARDAN QOCHISH

Credential helper sozlanadi:

```console
$ git config --global credential.helper cache
```

`cache` credentialni vaqtincha xotirada saqlaydi:

```console
$ git config --global credential.helper 'cache --timeout=3600'
```

`store` credentialni diskda shifrlamasdan saqlaydi:

```console
$ git config --global credential.helper store
```

> `store` xavfsiz emas: credential odatda `~/.git-credentials` da ochiq matn ko‘rinishida turadi. Operatsion tizim keychain’i yoki maxsus credential manager afzal.

Bir nechta helper ketma-ket sozlanishi mumkin. Git foydalanuvchi nomi va maxfiy qiymat topilguncha ularni tartib bilan chaqiradi:

```ini
[credential]
    helper = cache --timeout=300
    helper = manager
```

## CREDENTIAL CONTEXTS — CREDENTIAL KONTEKSTLARI

Credential URL konteksti bo‘yicha tanlanadi. Masalan:

```ini
[credential "https://example.com"]
    username = alice
```

Bu `example.com` dagi HTTPS so‘rovlariga `alice` nomini beradi. Protokol va host odatda aniq mos keladi. HTTP(S) yo‘li standart holatda hisobga olinmaydi.

Yo‘lni ham credential kontekstiga qo‘shish:

```console
$ git config --global credential.useHttpPath true
```

Shunda `https://example.com/foo.git` va `https://example.com/bar.git` alohida credential ishlatishi mumkin.

## CONFIGURATION OPTIONS — SOZLAMALAR

- `credential.helper` — chaqiriladigan helperlar.
- `credential.username` — standart foydalanuvchi nomi.
- `credential.useHttpPath` — HTTP(S) yo‘lini kontekstga kiritish.
- `credential.sanitizePrompt` — prompt’dagi URL boshqaruv belgilarini xavfsiz ko‘rsatish.
- `credential.protectProtocol` — credential protokolidagi satr ajratgichlarni himoyalash.

URL’ga xos sozlama:

```console
$ git config --global credential.https://example.com.username alice
```

## CUSTOM HELPERS — MAXSUS HELPERLAR

Helper nomi quyidagicha talqin qilinadi:

- `foo` → `git credential-foo` dasturi;
- mutlaq yo‘l → aynan shu dastur;
- `!shell command` → berilgan shell kodi.

Masalan:

```ini
[credential]
    helper = foo --bar=baz
```

Git helper’ni `get`, `store` yoki `erase` amali bilan chaqiradi:

```console
git credential-foo get
git credential-foo store
git credential-foo erase
```

Maydonlar standart kirish/chiqishda `key=value` satrlari bilan uzatiladi va bo‘sh satr bilan tugaydi. Helper bilmagan maydonni tashlab ketishi, bilgan qiymatlarini chiqarishi mumkin. Maxfiy qiymatlarni logga yozmaslik va xavfsiz saqlash helper muallifining vazifasidir.

## GIT

`git(1)` buyruqlar to‘plamining bir qismi.

