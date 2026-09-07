# Git remote helpers — o‘zbekcha tarjima

> Manba: `git help remote-helpers` (Git 2.50.1). Bu hujjat helper mualliflari uchun protokol tavsifidir.

## NAME — NOMI

`gitremote-helpers` — masofaviy repozitoriylar bilan ishlovchi yordamchi dasturlar.

## DESCRIPTION — TAVSIF

Remote helper Git’ga ichki qo‘llab-quvvatlanmaydigan transport yoki tashqi tizim bilan ishlash imkonini beradi. `<transport>::<address>` URL ko‘rilganda Git `git-remote-<transport>` dasturini ishga tushiradi.

Masalan:

```console
$ git clone hg::https://example.com/repo
```

`git-remote-hg` helperini chaqiradi. Remote konfiguratsiyasida ham ishlatilishi mumkin:

```ini
[remote "example"]
    url = foo::bar
```

## INVOCATION — CHAQIRILISHI

Git helperni quyidagicha ishga tushiradi:

```console
git remote-<transport> <remote-name> [<url>]
```

Git va helper standart kirish/chiqish orqali satrli protokolda gaplashadi. Buyruq va javob bloklari bo‘sh satr bilan tugaydi. Helper diagnostikani standard error’ga yozishi kerak; protokol chiqishiga ortiqcha matn yozmasligi shart.

## INPUT FORMAT — KIRISH FORMATI

Har bir satr bir buyruq va argumentlardan iborat. Yo‘l/ref nomlarida protokolga tegishli cheklovlar amal qiladi. Helper noma’lum majburiy buyruqqa mos xato bilan javob beradi; imkoniyatlar oldindan `capabilities` orqali e’lon qilinadi.

## COMMANDS — BUYRUQLAR

### capabilities

Helper qo‘llaydigan imkoniyatlarni satrma-satr chiqaradi va bo‘sh satr bilan tugatadi. Keng tarqalganlari:

- `fetch` — obyektlarni ref bo‘yicha olish;
- `push` — ref’larni yuborish;
- `import` — fast-import oqimi orqali olish;
- `export` — fast-export oqimi orqali yuborish;
- `connect` — native Git servisiga ikki tomonlama ulanish;
- `stateless-connect` — stateless native servis;
- `option` — qo‘shimcha parametrlarni qabul qilish;
- `refspec` — import qilingan ref’larni joylashtirish qoidasi;
- `check-connectivity` — helper obyekt bog‘liqligini tekshiradi;
- `signed-tags`, `export-marks`, `import-marks` — teg va fast-import marker imkoniyatlari.

`*` bilan boshlangan capability Git tushunishi shart bo‘lgan majburiy imkoniyatdir; tushunilmasa Git operatsiyani to‘xtatadi.

### list

Remote ref’larni ro‘yxatlaydi:

```text
list

<value> <name> [<attr> ...]
...

```

`<value>` obyekt ID, `@<symref-target>`, `?` (noma’lum) yoki push uchun maxsus holat bo‘lishi mumkin.

`list for-push` push nuqtayi nazaridan ref’larni so‘raydi.

### fetch

```text
fetch <sha1> <name>
fetch <sha1> <name>

```

Helper so‘ralgan obyektlar va ulardan erishiladigan tarixni mahalliy obyektlar bazasiga olib keladi. Bir nechta `fetch` satri batch bo‘lib, bo‘sh satr bilan bajariladi.

### push

```text
push [+]<src>:<dst>
push :<dst>

```

Birinchisi ref’ni yuboradi; bosh `+` force yangilashga ruxsat beradi. Ikkinchisi remote ref’ni o‘chirishni so‘raydi. Helper har ref uchun `ok <dst>` yoki `error <dst> <message>` qaytaradi.

### import

```text
import <name>
```

Helper standard output’da `git fast-import` oqimini beradi. E’lon qilingan `refspec` import qilingan ref’larning xususiy namespace’da qayerga yozilishini bildiradi.

### export

```text
export
```

Git helper’ga `git fast-export` oqimini beradi. Helper uni tashqi tizimga yozadi. `export-marks` va `import-marks` incremental sinxronlashni tezlashtiradi.

### connect

```text
connect git-upload-pack
connect git-receive-pack
connect git-upload-archive
```

Helper remote servis bilan ikki tomonlama kanal ochadi. Muvaffaqiyatda bo‘sh satr yuborib, keyin standard input/output’ni native Git protokoliga topshiradi. Ulanish mumkin bo‘lmasa `fallback` qaytarishi mumkin.

### stateless-connect

Stateless transportlar uchun `connect` varianti. So‘rov va javoblar pkt-line formatida alohida raundlar sifatida uzatiladi.

### option

```text
option <name> <value>
```

Helper:

- `ok` — parametr qabul qilindi;
- `unsupported` — tanilmadi;
- `error <message>` — tanildi, ammo qiymat yaroqsiz,

deb javob beradi.

Keng tarqalgan optionlar: `verbosity`, `progress`, `depth`, `deepen-since`, `deepen-not`, `deepen-relative`, `followtags`, `dry-run`, `servpath`, `check-connectivity`, `force`, `cloning`, `update-shallow`, `push-option`.

## REF LIST ATTRIBUTES — REF ATRIBUTLARI

`list` natijasidagi atributlar ref haqida qo‘shimcha ma’lumot beradi:

- `unchanged` — helper bu ref o‘zgarmaganini biladi;
- `for-push` bilan remote qiymat push qarorini optimallashtirishi mumkin.

Symref `@refs/heads/main HEAD` ko‘rinishida berilishi mumkin. Peeled annotated tag odatda `refs/tags/v1.0^{}` ko‘rinishidagi qo‘shimcha yozuv bilan ifodalanadi.

## REF LIST KEYWORDS — MAXSUS KALIT SO‘ZLAR

Helper `list` boshida `:<keyword> <value>` shaklida ma’lumot yuborishi mumkin. Masalan `:object-format` remote obyekt xesh formatini, `:server-option` server qabul qiladigan parametrni bildirishi mumkin. Git versiyasi qo‘llaydigan aniq kalitlar protokol evolyutsiyasiga bog‘liq.

## IMPLEMENTATSIYA BO‘YICHA TAVSIYALAR

- Protokol stdout’ini diagnostika bilan ifloslantirmang.
- Ref va URL’larni ishonchsiz kirish deb hisoblab, shell injection’dan himoyalaning.
- Credentialni loglamang; Git credential subsystem’dan foydalaning.
- Force push, ref o‘chirish va signed tag semantikasini aniq saqlang.
- Jarayon uzilsa vaqtinchalik fayl va tashqi tranzaksiyalarni xavfsiz yakunlang.
- Imkon bo‘lsa batch fetch/push va markerlardan foydalaning.

## SEE ALSO

`git-remote(1)`, `git-fast-import(1)`, `git-fast-export(1)`, `gitcredentials(7)`.

## GIT

`git(1)` buyruqlar to‘plamining bir qismi.
