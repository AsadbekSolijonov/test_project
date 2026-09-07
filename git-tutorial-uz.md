# Git tutorial — asl matn va o‘zbekcha tarjima

> Manba: `git help tutorial` (Git 2.50.1). Buyruqlar asl holida saqlangan.

## NAME — NOMI

**English:** `gittutorial` — A tutorial introduction to Git.

**O‘zbekcha:** `gittutorial` — Git bilan tanishtiruvchi qo‘llanma.

## SYNOPSIS — QISQACHA ISHLATILISHI

```console
git *
```

## DESCRIPTION — TAVSIF

**English:** This tutorial explains how to import a new project into Git, make changes to it, and share changes with other developers.

**O‘zbekcha:** Ushbu qo‘llanma yangi loyihani Git’ga kiritish, unga o‘zgartirishlar kiritish va bu o‘zgartirishlarni boshqa dasturchilar bilan ulashishni tushuntiradi.

**English:** If you are primarily interested in using Git to fetch a project—for example, to test its latest version—you may prefer to start with the first two chapters of *The Git User’s Manual*.

**O‘zbekcha:** Agar siz Git’dan, asosan, loyihani yuklab olish (masalan, uning eng so‘nggi versiyasini sinash) uchun foydalanmoqchi bo‘lsangiz, avval *Git foydalanuvchi qo‘llanmasi*ning dastlabki ikki bobini o‘qishingiz ma’qul.

Buyruq hujjatini olish:

```console
$ man git-log
# yoki
$ git help log
```

Ikkinchi usulda o‘zingiz xohlagan qo‘llanma ko‘ruvchisidan foydalanishingiz mumkin; batafsil ma’lumot uchun `git-help(1)` ga qarang.

Git bilan ishlashdan oldin ismingiz va ochiq elektron pochta manzilingizni tanishtirish yaxshi amaliyotdir:

```console
$ git config --global user.name "Your Name Comes Here"
$ git config --global user.email you@yourdomain.example.com
```

## IMPORTING A NEW PROJECT — YANGI LOYIHANI KIRITISH

**English:** Assume you have a `project.tar.gz` archive containing your initial work. Put it under Git revision control as follows:

**O‘zbekcha:** Dastlabki ishingiz saqlangan `project.tar.gz` arxivi bor deb faraz qilamiz. Uni Git versiya nazoratiga quyidagicha qo‘shing:

```console
$ tar xzf project.tar.gz
$ cd project
$ git init
```

Git javobi:

```text
Initialized empty Git repository in .git/
```

Ishchi katalog endi Git uchun tayyorlandi; yangi `.git` katalogi yaratilganini ko‘rishingiz mumkin.

Keyin joriy katalog ostidagi barcha fayllar tarkibining suratini (`.` joriy katalogni bildiradi) Git’ga bering:

```console
$ git add .
```

Bu surat vaqtinchalik tayyorlash maydonida saqlanadi; Git uni **index** deb ataydi. Indeks tarkibini repozitoriyda doimiy saqlash uchun:

```console
$ git commit
```

Git commit xabarini kiritishni so‘raydi. Shu bilan loyihangizning birinchi versiyasi Git’da saqlanadi.

## MAKING CHANGES — O‘ZGARTIRISHLAR KIRITISH

Bir nechta faylni o‘zgartiring, so‘ng yangilangan tarkibini indeksga qo‘shing:

```console
$ git add file1 file2 file3
```

Endi commit qilishga tayyorsiz. Commitga nimalar kirishini ko‘rish uchun:

```console
$ git diff --cached
```

`--cached` bo‘lmasa, `git diff` siz o‘zgartirgan, ammo hali indeksga qo‘shmagan o‘zgarishlarni ko‘rsatadi. Holatning qisqa xulosasi:

```console
$ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)

        modified:   file1
        modified:   file2
        modified:   file3
```

Qo‘shimcha tuzatish zarur bo‘lsa, hozir bajaring va yangi o‘zgargan tarkibni yana indeksga qo‘shing. So‘ng:

```console
$ git commit
```

Git yana o‘zgarishni tavsiflovchi xabarni so‘raydi va loyihaning yangi versiyasini yozib qo‘yadi.

Oldindan `git add` ishlatish o‘rniga:

```console
$ git commit -a
```

Bu buyruq o‘zgartirilgan (ammo yangi bo‘lmagan) fayllarni avtomatik aniqlaydi, indeksga qo‘shadi va bitta qadamda commit qiladi.

### Commit xabarlari haqida

Majburiy bo‘lmasa-da, commit xabarini o‘zgarishni qisqacha ifodalovchi bitta satr (50 belgidan oshmagan) bilan boshlash, undan keyin bo‘sh satr va batafsil tavsif yozish tavsiya etiladi. Birinchi bo‘sh satrgacha bo‘lgan matn commit sarlavhasi hisoblanadi va Git bo‘ylab ishlatiladi. Masalan, `git-format-patch(1)` commitni emailga aylantirganda sarlavhani **Subject** qatorida, qolgan matnni esa xat tanasida ishlatadi.

## GIT TRACKS CONTENT, NOT FILES — GIT FAYLLARNI EMAS, TARKIBNI KUZATADI

Ko‘p versiya nazorati tizimlarida `add` buyrug‘i tizimga yangi fayl o‘zgarishlarini kuzatishni boshlashni aytadi. Git’dagi `add` sodda va kuchliroq: `git add` ham yangi, ham yaqinda o‘zgartirilgan fayllar uchun ishlatiladi. Har ikki holatda u berilgan fayllarning suratini olib, ularning tarkibini keyingi commit uchun indeksga tayyorlaydi.

## VIEWING PROJECT HISTORY — LOYIHA TARIXINI KO‘RISH

```console
$ git log
```

Har bir bosqichdagi to‘liq farqlarni ko‘rish:

```console
$ git log -p
```

Har bir bosqich haqida umumiy tasavvur beruvchi o‘zgarishlar xulosasi:

```console
$ git log --stat --summary
```

## MANAGING BRANCHES — TARMOQLARNI BOSHQARISH

Bitta Git repozitoriysi bir nechta rivojlantirish tarmog‘ini saqlashi mumkin. `experimental` nomli tarmoq yarating:

```console
$ git branch experimental
$ git branch
  experimental
* master
```

`experimental` — hozirgina yaratilgan tarmoq, `master` esa avtomatik yaratilgan standart tarmoq. Yulduzcha joriy tarmoqni ko‘rsatadi. `experimental` ga o‘tish:

```console
$ git switch experimental
```

Endi faylni tahrirlang, o‘zgarishni commit qiling va `master` ga qayting:

```console
(edit file)
$ git commit -a
$ git switch master
```

Kiritgan o‘zgarishingiz endi ko‘rinmayotganini tekshiring: u `experimental` tarmog‘ida qilingan, siz esa `master` ga qaytdingiz.

`master` tarmog‘ida boshqa o‘zgarish qilishingiz mumkin:

```console
(edit file)
$ git commit -a
```

Endi ikki tarmoq ajraldi va har birida boshqa-boshqa o‘zgarish bor. `experimental` dagi o‘zgarishlarni `master` ga birlashtirish:

```console
$ git merge experimental
```

O‘zgarishlar to‘qnashmasa, ish tugadi. To‘qnashuv bo‘lsa, muammoli fayllarda konflikt belgilari qoladi. Ularni ko‘rish:

```console
$ git diff
```

Fayllarni tahrirlab konfliktlarni hal qilgach, birlashtirish natijasini commit qiling:

```console
$ git commit -a
$ gitk
```

`gitk` hosil bo‘lgan tarixning qulay grafik ko‘rinishini beradi. Endi `experimental` tarmog‘ini o‘chirish mumkin:

```console
$ git branch -d experimental
```

Bu buyruq `experimental` dagi o‘zgarishlar allaqachon joriy tarmoqda borligini tekshiradi.

Agar `crazy-idea` tarmog‘idagi ishingizdan voz kechsangiz, uni majburan o‘chirishingiz mumkin:

```console
$ git branch -D crazy-idea
```

Tarmoqlar arzon va oson yaratiladi, shu sabab ular yangi g‘oyalarni sinashning yaxshi usulidir.

## USING GIT FOR COLLABORATION — GIT’DAN HAMKORLIKDA FOYDALANISH

Alice `/home/alice/project` da yangi Git loyihasini boshlagan, shu kompyuterda uy katalogiga ega Bob esa unga hissa qo‘shmoqchi deb faraz qilamiz.

Bob boshlaydi:

```console
bob$ git clone /home/alice/project myrepo
```

Bu Alice repozitoriysining klonini saqlovchi `myrepo` katalogini yaratadi. Klon asl loyiha bilan teng huquqli bo‘lib, loyiha tarixining o‘z nusxasiga ega.

Bob o‘zgarishlar kiritib, ularni commit qiladi:

```console
(edit files)
bob$ git commit -a
(repeat as necessary)
```

Tayyor bo‘lgach, Bob Alice’ga `/home/bob/myrepo` dan o‘zgarishlarni tortib olishni aytadi:

```console
alice$ cd /home/alice/project
alice$ git pull /home/bob/myrepo master
```

Bu Bobning `master` tarmog‘idagi o‘zgarishlarni Alice’ning joriy tarmog‘iga birlashtiradi. Alice bu orada o‘z o‘zgarishlarini qilgan bo‘lsa, konfliktlarni qo‘lda tuzatishi mumkin.

`pull` ikki amalni bajaradi: masofaviy tarmoqdan o‘zgarishlarni oladi (**fetch**), keyin ularni joriy tarmoqqa birlashtiradi (**merge**).

Odatda Alice `pull` dan oldin mahalliy o‘zgarishlarini commit qilishi kerak. Bobning ishi tarixlar ajralganidan keyin Alice qilgan ish bilan to‘qnashsa, Alice konfliktni ishchi daraxt va indeks orqali hal qiladi. Commit qilinmagan mahalliy o‘zgarishlar bu jarayonga xalaqit beradi: Git o‘zgarishlarni oladi, ammo birlashtirishni rad etadi; Alice mahalliy o‘zgarishlarini bir yo‘l bilan chetga olishi va yana `pull` qilishi kerak.

Alice birlashtirmasdan Bobning ishini ko‘rib chiqishi mumkin:

```console
alice$ git fetch /home/bob/myrepo master
alice$ git log -p HEAD..FETCH_HEAD
```

Bu amal Alice’da commit qilinmagan o‘zgarishlar bo‘lsa ham xavfsiz. `HEAD..FETCH_HEAD` — “`FETCH_HEAD` dan erishish mumkin bo‘lgan hamma narsani ko‘rsat, ammo `HEAD` dan erishish mumkin bo‘lganlarini chiqarib tashla” degani. Alice o‘zining joriy holatiga (`HEAD`) olib kelgan tarixni biladi va Bobning hali ko‘rmagan holatini (`FETCH_HEAD`) tekshiradi.

Tarixlar ajralganidan beri Bob nima qilganini grafikda ko‘rish:

```console
$ gitk HEAD..FETCH_HEAD
```

Bu `git log` dagidek ikki nuqtali oraliq yozuvidir. Ikkala tomon qilgan ishni ko‘rish uchun uch nuqta ishlatiladi:

```console
$ gitk HEAD...FETCH_HEAD
```

Bu “ikkalasidan istalgan biri orqali erishiladigan hamma narsani ko‘rsat, ammo ikkalasidan ham erishiladiganlarini chiqarib tashla” degani. Bu oraliq yozuvlari `gitk` va `git log` bilan ishlaydi.

Bobning ishini tekshirgach, shoshilinch narsa bo‘lmasa, Alice uni tortib olmasdan ishlashda davom etishi mumkin. Zarur o‘zgarish bo‘lsa, Alice avval tugallanmagan ishini **stash** qiladi, `pull` bajaradi va so‘ng ishini hosil bo‘lgan tarix ustiga stash’dan qaytaradi.

Bir xil repozitoriy bilan tez-tez ishlaganda masofaviy repozitoriy uchun qisqa nom yaratish qulay:

```console
alice$ git remote add bob /home/bob/myrepo
alice$ git fetch bob
```

To‘liq yo‘ldan farqli ravishda, `git remote` bilan belgilangan qisqa nom orqali olingan ma’lumot masofaviy kuzatuv tarmog‘ida — bu holatda `bob/master` da — saqlanadi:

```console
alice$ git log -p master..bob/master
```

Bu Bob Alice’ning `master` tarmog‘idan ajralganidan beri qilgan barcha o‘zgarishlarini ko‘rsatadi. Ularni tekshirgach, Alice birlashtirishi mumkin:

```console
alice$ git merge bob/master
```

O‘z masofaviy kuzatuv tarmog‘idan `pull` qilish orqali ham birlashtirish mumkin:

```console
alice$ git pull . remotes/bob/master
```

`git pull` buyruq satrida yana nima berilganidan qat’i nazar, doim joriy tarmoqqa birlashtiradi.

Keyin Bob Alice’ning so‘nggi o‘zgarishlari bilan repozitoriysini yangilaydi:

```console
bob$ git pull
```

Bob Alice repozitoriysining yo‘lini berishi shart emas: klonlash vaqtida Git bu manzilni konfiguratsiyada saqlagan:

```console
bob$ git config --get remote.origin.url
/home/alice/project
```

`git clone` yaratgan to‘liq konfiguratsiya `git config -l` orqali ko‘rinadi; har bir parametr ma’nosi `git-config(1)` da tushuntirilgan.

Git Alice’ning `master` tarmog‘i toza nusxasini `origin/master` nomi bilan ham saqlaydi:

```console
bob$ git branch -r
  origin/master
```

Bob boshqa kompyuterdan ishlasa, SSH orqali klonlash va tortib olish mumkin:

```console
bob$ git clone alice.org:/home/alice/project myrepo
```

Git o‘zining mahalliy protokolidan yoki HTTP’dan ham foydalana oladi; `git-pull(1)` ga qarang. Git markaziy repozitoriyga turli foydalanuvchilar o‘zgarishlarni **push** qiladigan CVS’ga o‘xshash rejimda ham ishlaydi; `git-push(1)` va `gitcvs-migration(7)` ga qarang.

## EXPLORING HISTORY — TARIXNI O‘RGANISH

Git tarixi o‘zaro bog‘langan commitlar ketma-ketligidir. `git log` ularni ro‘yxatlaydi. Har bir yozuvning birinchi satri commit nomi — identifikatorini ham beradi:

```console
$ git log
commit c82a22c39cbc32576f64f5c6b3f24b99ea8149c7
Author: Junio C Hamano <junkio@cox.net>
Date:   Tue May 16 17:18:22 2006 -0700

    merge-base: Clarify the comments on post processing.
```

Commit tafsilotlarini ko‘rish:

```console
$ git show c82a22c39cbc32576f64f5c6b3f24b99ea8149c7
```

Commitga murojaat qilishning boshqa usullari ham bor. Uni yagona aniqlashga yetadigan boshlang‘ich qismdan foydalanish mumkin:

```console
$ git show c82a22c39c   # nomning dastlabki bir nechta belgisi odatda yetadi
$ git show HEAD         # joriy tarmoqning eng so‘nggi nuqtasi
$ git show experimental # "experimental" tarmog‘ining eng so‘nggi nuqtasi
```

Har bir commit odatda loyihaning oldingi holatiga ishora qiluvchi bitta **parent** commitga ega:

```console
$ git show HEAD^  # HEAD ota commitini ko‘rish
$ git show HEAD^^ # HEAD bobo commitini ko‘rish
$ git show HEAD~4 # HEAD dan to‘rt avvalgi commitni ko‘rish
```

Merge commitlarda bir nechta parent bo‘lishi mumkin:

```console
$ git show HEAD^1 # HEAD ning birinchi parenti (HEAD^ bilan bir xil)
$ git show HEAD^2 # HEAD ning ikkinchi parenti
```

Commitlarga o‘zingiz nom berishingiz ham mumkin:

```console
$ git tag v2.5 1b2e1d63ff
```

Shundan keyin `1b2e1d63ff` ga `v2.5` nomi bilan murojaat qilinadi. Bu nomni boshqalar bilan ulashmoqchi bo‘lsangiz (masalan, reliz versiyasini belgilash uchun), **tag** obyekti yaratish va ehtimol uni imzolash kerak; `git-tag(1)` ga qarang.

Commit talab qiladigan har qanday Git buyrug‘i ushbu nomlardan istalganini qabul qiladi:

```console
$ git diff v2.5 HEAD     # joriy HEAD ni v2.5 bilan solishtirish
$ git branch stable v2.5 # v2.5 asosida "stable" tarmog‘ini yaratish
$ git reset --hard HEAD^ # tarmoq va ishchi katalogni HEAD^ holatiga qaytarish
```

> **Ogohlantirish:** Oxirgi buyruq ishchi katalogdagi o‘zgarishlarni yo‘qotishdan tashqari, shu tarmoqdagi barcha keyingi commitlarni ham olib tashlaydi. U commitlarni saqlovchi boshqa tarmoq bo‘lmasa, ular yo‘qoladi. Boshqa dasturchilar tortib oladigan ochiq tarmoqda `git reset` ishlatmang — bu tarixni tuzatish uchun ularga keraksiz merge’larni majbur qiladi. Push qilingan o‘zgarishni bekor qilish uchun `git revert` ishlating.

`git grep` loyihangizning istalgan versiyasida matn qidiradi:

```console
$ git grep "hello" v2.5
```

Bu `v2.5` ichidagi barcha `hello` uchrashuvlarini topadi. Commit nomi berilmasa, joriy katalogdagi Git kuzatayotgan fayllardan qidiradi:

```console
$ git grep "hello"
```

Ko‘p Git buyruqlari turli usulda ko‘rsatiladigan commitlar to‘plamini ham qabul qiladi:

```console
$ git log v2.5..v2.6            # v2.5 va v2.6 orasidagi commitlar
$ git log v2.5..                # v2.5 dan keyingi commitlar
$ git log --since="2 weeks ago" # so‘nggi ikki haftadagi commitlar
$ git log v2.5.. Makefile       # v2.5 dan keyin Makefile’ni o‘zgartirgan commitlar
```

Birinchi commit ikkinchisining ajdodi bo‘lishi shart bo‘lmagan oraliq ham berish mumkin. Masalan, `stable` va `master` umumiy commitdan ajralgan bo‘lsa:

```console
$ git log stable..master
```

`master` da bor, ammo `stable` da yo‘q commitlarni ko‘rsatadi. Aksincha:

```console
$ git log master..stable
```

`stable` da bor, ammo `master` da yo‘q commitlarni ko‘rsatadi.

`git log` ning zaif tomoni — commitlarni ro‘yxat ko‘rinishida berishi. Tarixdagi rivojlanish yo‘llari ajralib, keyin yana birlashganda, ularning ro‘yxatdagi tartibi mazmunli bo‘lmasligi mumkin.

Ko‘p ishtirokchili loyihalarda (Linux yadrosi yoki Git’ning o‘zi kabi) merge ko‘p bo‘ladi; `gitk` tarixni yaxshiroq tasvirlaydi:

```console
$ gitk --since="2 weeks ago" drivers/
```

Bu oxirgi ikki hafta ichida `drivers` katalogidagi fayllarni o‘zgartirgan commitlarni ko‘rish imkonini beradi. `gitk` shriftini Control tugmasini bosib turib `-` yoki `+` ni bosish orqali sozlash mumkin.

Fayl nomini qabul qiladigan buyruqlarning aksariyatida muayyan fayl versiyasini ko‘rsatish uchun uning oldidan commit yozish mumkin:

```console
$ git diff v2.5:Makefile HEAD:Makefile.in
$ git show v2.5:Makefile
```

## NEXT STEPS — KEYINGI QADAMLAR

Ushbu qo‘llanma loyihalaringizda asosiy taqsimlangan versiya nazoratini bajarish uchun yetarli bo‘lishi kerak. Git’ning imkoniyatlarini to‘liq tushunish uchun u asoslangan ikki oddiy g‘oyani bilish zarur:

- **Obyektlar ma’lumotlar bazasi** — loyiha tarixi, fayllar, kataloglar va commitlarni saqlovchi puxta tizim.

- **Indeks fayli** — katalog daraxti holatining keshi; commit yaratish, ishchi kataloglarni chiqarib olish va merge jarayonidagi turli daraxtlarni saqlash uchun ishlatiladi.

Qo‘llanmaning ikkinchi qismi obyektlar bazasi, indeks fayli va Git’dan samarali foydalanish uchun zarur boshqa mavzularni tushuntiradi: `gittutorial-2(7)`.

Hozircha davom ettirmasangiz, quyidagilar ham qiziqarli bo‘lishi mumkin:

- `git-format-patch(1)`, `git-am(1)` — commitlar ketma-ketligini email patchlariga va aksincha aylantiradi; email patchlariga ko‘p tayanadigan Linux yadrosi kabi loyihalarda foydali.

- `git-bisect(1)` — regressiya paydo bo‘lsa, aybdor aniq commitni tarixdan ikkilik qidiruv orqali topishga yordam beradi. Ko‘p tarmoqli va merge’li murakkab tarixda ham deyarli optimal qidiradi.

- `gitworkflows(7)` — tavsiya etilgan ish jarayonlari sharhi.

- `giteveryday(7)` — taxminan 20 ta buyruq bilan kundalik Git ishlari.

- `gitcvs-migration(7)` — CVS foydalanuvchilari uchun Git.

## SEE ALSO — SHUNINGDEK QARANG

`gittutorial-2(7)`, `gitcvs-migration(7)`, `gitcore-tutorial(7)`, `gitglossary(7)`, `git-help(1)`, `gitworkflows(7)`, `giteveryday(7)`, *The Git User’s Manual*.

## GIT

`git(1)` buyruqlar to‘plamining bir qismi.

## NOTES — IZOH

1. *The Git User’s Manual*: `git-htmldocs/user-manual.html`
