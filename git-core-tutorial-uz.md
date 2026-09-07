# Git core tutorial — o‘zbekcha tarjima

> Manba: `git help core-tutorial` (Git 2.50.1). Bu qo‘llanma Git’ning quyi darajadagi (**plumbing**) mexanizmlarini tushuntiradi. Buyruqlar asl yozilishida saqlangan.

## NAME — NOMI

`gitcore-tutorial` — dasturchilar uchun Git yadrosi bo‘yicha qo‘llanma.

## SYNOPSIS — QISQACHA ISHLATILISHI

```console
git *
```

## DESCRIPTION — TAVSIF

Bu qo‘llanma Git repozitoriysini yaratish va u bilan ishlashda Git’ning **core** buyruqlaridan foydalanishni tushuntiradi.

Agar Git’dan faqat versiya nazorati tizimi sifatida foydalanmoqchi bo‘lsangiz, avval `gittutorial(7)` yoki *Git User Manual* dan boshlashingiz ma’qul. Ammo Git ichki tuzilishini tushunishda quyi darajadagi vositalarni bilish foydalidir.

Git yadrosi ko‘pincha **plumbing** (“quvurlar”), uning ustidagi qulay foydalanuvchi interfeyslari esa **porcelain** (“santexnika jihozlari”) deb ataladi. Plumbing buyruqlarini tez-tez bevosita ishlatmasangiz ham, yuqori darajadagi buyruq kutilganidek ishlamasa, ular nima qilishini bilish foydali.

Hujjat dastlab yozilganda ko‘p porcelain buyruqlari shell skriptlari edi. Soddalik uchun ular plumbing buyruqlari qanday birlashtirilishini ko‘rsatishda hanuz misol sifatida ishlatiladi. Ayrim eski skriptlar manba daraxtidagi `contrib/examples/` da mavjud. Hozir ular shell skripti bo‘lmasa ham, plumbing qatlamining tavsifi to‘g‘ri.

> **Izoh:** Chuqur texnik tafsilotlar “Izoh” sifatida ajratiladi; birinchi o‘qishda ularni tashlab ketishingiz mumkin.

## CREATING A GIT REPOSITORY — GIT REPOZITORIYSINI YARATISH

Har bir Git repozitoriysi bo‘sh holatdan boshlanadi. Yangi loyiha uchun bo‘sh yoki Git’ga import qilinadigan mavjud ishchi katalogni tanlang:

```console
$ mkdir git-tutorial
$ cd git-tutorial
$ git init
Initialized empty Git repository in .git/
```

Git mahalliy `.git` katalogini yaratadi. Undagi muhim qismlar:

- `HEAD` — odatda `ref: refs/heads/master` ni saqlaydi va joriy tarmoqqa ramziy murojaat qiladi. Birinchi commitgacha murojaat qilinayotgan fayl mavjud bo‘lmasligi tabiiy.

- `objects/` — loyihaning haqiqiy ma’lumotlarini saqlovchi barcha obyektlar katalogi.

- `refs/` — obyektlarga murojaatlar katalogi. Uning `heads/` qismi tarmoqlarga, `tags/` qismi teglar orqali nomlangan versiyalarga murojaatlarni saqlaydi.

`master` tarixiy standart tarmoq nomidir, ammo bu faqat kelishuv: tarmoqqa istalgan nom berish mumkin. Muhimi, `.git/HEAD` haqiqiy joriy tarmoqqa ishora qilsin.

> Obyekt 160 bitli SHA-1 xeshi bilan aniqlanadi. Obyektga murojaat uning 40 belgili o‘n oltilik ko‘rinishidir. `refs` ostidagi fayllar odatda shu qiymat va yakuniy yangi satrdan iborat bo‘ladi.

Repozitoriy tuzilishining chuqur tavsifi uchun `gitrepository-layout(5)` ga qarang.

## POPULATING A GIT REPOSITORY — REPOZITORIYNI MA’LUMOT BILAN TO‘LDIRISH

Ikki oddiy fayl yarating:

```console
$ echo "Hello World" >hello
$ echo "Silly example" >example
```

Ularni Git’ga yozish ikki bosqichdan iborat:

1. Ishchi daraxt holati bilan **index** (yoki cache) ni to‘ldirish.
2. Indeksni obyekt sifatida commit qilish.

Plumbing darajasida indeksni yangilash:

```console
$ git update-index --add hello example
```

`update-index` tasodifiy xatolardan himoyalanish uchun yangi yozuvda `--add`, o‘chirishda `--remove` ni ochiq ko‘rsatishni talab qiladi. Buyruq fayl tarkibini obyektlar bazasiga yozib, indeksga murojaat qo‘shadi.

```console
$ ls .git/objects/??/*
.git/objects/55/7db03de997c86a4a028e1ebd3a1ceb225be238
.git/objects/f2/4c74a2e500f5ee1332c86b94199f52b1d1d962
```

Obyekt turini va tarkibini ko‘rish:

```console
$ git cat-file -t 557db03de997c86a4a028e1ebd3a1ceb225be238
blob
$ git cat-file blob 557db03
Hello World
```

`557db03...` obyekti `hello` faylining ayni paytdagi tarkibidir.

> Obyektni faylning o‘zi bilan adashtirmang. Obyekt faqat muayyan tarkibni saqlaydi. Fayl keyin o‘zgarsa ham eski obyekt o‘zgarmaydi — Git obyektlari immutable, ya’ni o‘zgarmasdir.

`update-index` `.git/index` faylini ham yaratadi. Bu joriy ishchi daraxtni tavsiflaydi. Hali fayllarni commit qilmadik; faqat Git’ga ular haqida xabar berdik.

`hello` ni o‘zgartirib, indeksdagi holat bilan farqini ko‘ramiz:

```console
$ echo "It's a new day for git" >>hello
$ git diff-files -p
diff --git a/hello b/hello
index 557db03..263414f 100644
--- a/hello
+++ b/hello
@@ -1 +1,2 @@
 Hello World
+It's a new day for git
```

`git diff-files` indeksdagi holat bilan ishchi daraxtni solishtiradi. Uning keng ishlatiladigan qisqartmasi:

```console
$ git diff
```

## COMMITTING GIT STATE — GIT HOLATINI COMMIT QILISH

Indeksdagi fayllarni haqiqiy tree sifatida commit qilish ikki bosqichda bajariladi:

1. `tree` obyekti yaratish.
2. Tree va uning izohidan `commit` obyekti yaratish.

```console
$ git write-tree
8988da15d077d4829fc51d8544c097def6644dbb
```

`git write-tree` joriy indeksning barchasini tasvirlovchi tree obyektini yozadi; fayl nomlari, tarkibi va ruxsatlarini bir-biriga bog‘laydi.

Birinchi commitning parent’i yo‘q. `commit-tree` commit xabarini standart kirishdan oladi va commit obyektining nomini standart chiqishga yozadi:

```console
$ tree=$(git write-tree)
$ commit=$(echo 'Initial commit' | git commit-tree $tree)
$ git update-ref HEAD $commit
```

Bu buyruqlar indeksdan tree, undan commit yaratadi va `HEAD` murojaatini yangi commitga yangilaydi. Buni loyihada qo‘lda faqat birinchi commit uchun bajarish mumkin; keyingi commitlar oldingi commitning farzandi bo‘ladi.

Amalda bu ishlarning barchasini porcelain buyrug‘i bajaradi:

```console
$ git commit
```

## MAKING A CHANGE — O‘ZGARTIRISH KIRITISH

`hello` indeksga kiritilgandan keyin o‘zgartirilgan edi. `write-tree` ishchi katalogni emas, indeksni yozgani sababli commitda eski tarkib saqlandi. Bu indeks holati, ishchi daraxt va commit qilingan tree bir xil bo‘lishi shart emasligini ko‘rsatadi.

```console
$ git diff-index -p HEAD
# qisqa porcelain ko‘rinishi:
$ git diff HEAD
```

`diff-files` indeks bilan ishchi daraxtni, `diff-index` esa commit qilingan tree bilan indeks yoki ishchi daraxtni solishtiradi.

```console
$ git diff-index --cached -p HEAD
```

`--cached` tree’ni faqat indeks bilan solishtirib, ishchi daraxt tarkibini e’tiborsiz qoldiradi. Hozir indeks aynan `HEAD` ga yozilgani uchun natija bo‘sh.

> Texnik jihatdan `diff-index` doim fayllar ro‘yxati va metama’lumotni indeksdan oladi. `--cached` faqat solishtiriladigan fayl tarkibi ishchi daraxtdan olinishi yoki olinmasligini belgilaydi. Git unga ochiq aytilmagan fayllarni qidirmaydi; kuzatiladigan fayllar ro‘yxati indeksda turadi.

Ishchi daraxtdagi o‘zgarishni commit qilish uchun avval indeksni yangilang:

```console
$ git update-index hello
$ git diff-files -p
$ git diff-index -p HEAD
$ git diff-index --cached -p HEAD
```

Endi `diff-files` bo‘sh, chunki indeks va ishchi daraxt mos. `diff-index` hamda uning `--cached` ko‘rinishi esa `HEAD` dan farqni ko‘rsatadi.

Yangi commitni plumbing buyruqlari bilan yaratish:

```console
$ tree=$(git write-tree)
$ new_commit=$(echo 'Update hello' | git commit-tree $tree -p HEAD)
$ git update-ref HEAD $new_commit
```

`-p HEAD` oldingi commitni yangi commitning parent’i qiladi. Oddiy ishda buning o‘rniga `git commit` ishlatiladi.

## INSPECTING CHANGES — O‘ZGARISHLARNI TEKSHIRISH

`git diff-tree` ikkita tree yoki commit orasidagi farqni ko‘rsatadi:

```console
$ git diff-tree -p HEAD^
$ git diff-tree -p HEAD^ HEAD
```

Bitta commit berilganda uning parent’i bilan solishtiriladi. `-p` farqni patch shaklida ko‘rsatadi.

Commit ma’lumotini tekshirish:

```console
$ git cat-file commit HEAD
$ git show HEAD
```

`git show` commit metama’lumoti va patchni birga chiqaruvchi qulay porcelain buyrug‘idir. Tarixni ko‘rish:

```console
$ git log
$ git log --stat
$ git log -p
```

Plumbing nuqtayi nazaridan Git tarix bo‘ylab har bir commitning `parent` murojaatini kuzatib yuradi.

## TAGGING A VERSION — VERSIYAGA TEG QO‘YISH

SHA-1 nomlarini eslab qolish noqulay. Muayyan commitga tushunarli nom — teg — berish mumkin:

```console
$ git tag my-first-tag HEAD
```

Bu `.git/refs/tags/my-first-tag` murojaatini yaratadi. Endi commitga teg orqali murojaat qilinadi:

```console
$ git diff my-first-tag HEAD
$ git show my-first-tag
```

Oddiy (**lightweight**) teg bevosita obyektga murojaatdir. Izohli teg alohida tag obyekti bo‘lib, muallif, sana, xabar va ixtiyoriy kriptografik imzoni saqlaydi:

```console
$ git tag -a v1.0 -m "Version 1.0"
$ git tag -s v1.0-signed
```

Batafsil ma’lumot: `git-tag(1)`.

## COPYING REPOSITORIES — REPOZITORIYLARNI NUSXALASH

Git repozitoriysi barcha tarix va obyektlarni o‘z ichiga olgani sababli uni klonlash mumkin:

```console
$ cd ..
$ git clone git-tutorial git-tutorial-copy
```

Klon alohida, to‘liq repozitoriy bo‘lib, asl tarixning o‘z nusxasiga ega. Git odatda asl manzilni `origin` nomli remote sifatida saqlaydi:

```console
$ git config --get remote.origin.url
$ git branch -r
```

Mahalliy yo‘l, SSH yoki HTTP kabi qo‘llab-quvvatlanadigan transportlar orqali klonlash mumkin.

## CREATING A NEW BRANCH — YANGI TARMOQ YARATISH

Tarmoq shunchaki commitga siljiydigan murojaatdir:

```console
$ git branch mybranch
$ git switch mybranch
```

Plumbing darajasida yangi ref yaratib, `HEAD` ni unga ramziy bog‘lash mumkin, ammo amalda `git branch` va `git switch` xavfsizroq hamda qulayroq.

Tarmoqda o‘zgarish qiling:

```console
$ echo "Work on my branch" >>hello
$ git commit -a -m "Work in mybranch"
```

`mybranch` yangi commitga siljiydi, boshqa tarmoq esa avvalgi commitda qoladi. Tarmoqlar o‘rtasida o‘tishda Git tree’ni indeks va ishchi katalogga chiqaradi.

```console
$ git switch master
$ git switch mybranch
```

Tarmoqlar va umumiy tarixni ko‘rish:

```console
$ git show-branch
$ git log --all --graph --oneline
```

## MERGING TWO BRANCHES — IKKI TARMOQNI BIRLASHTIRISH

Ikki tarmoq mustaqil rivojlangach, biridagi ishni boshqasiga merge qilish mumkin:

```console
$ git switch master
$ git merge mybranch
```

Agar joriy tarmoq merge qilinayotgan tarmoqning ajdodi bo‘lsa, **fast-forward** yuz beradi: yangi commit yaratilmaydi, joriy ref oldinga siljiydi.

Ikkala tomon ham o‘zgargan bo‘lsa, Git uch tomonlama merge bajaradi:

- umumiy ajdod — merge base;
- joriy tarmoq uchi — “ours”;
- qo‘shilayotgan tarmoq uchi — “theirs”.

Natija odatda ikki parentli merge commit bo‘ladi:

```console
$ git merge -m "Merge mybranch" mybranch
```

## MERGING EXTERNAL WORK — TASHQI ISHNI BIRLASHTIRISH

Boshqa repozitoriydagi ishni olish ikki bosqichdan iborat:

```console
$ git fetch <repository> <branch>
$ git merge FETCH_HEAD
```

`fetch` kerakli obyektlarni yuklaydi va olingan tarmoq uchini `FETCH_HEAD` da qayd etadi. `merge` uni joriy tarmoq bilan birlashtiradi. Ikkalasining qulay birikmasi:

```console
$ git pull <repository> <branch>
```

Takror ishlatiladigan manzilga remote nomi beriladi:

```console
$ git remote add origin <repository>
$ git fetch origin
$ git merge origin/master
# yoki
$ git pull origin master
```

`fetch` xavfsiz: u joriy ishchi daraxtni merge qilmaydi. Olingan o‘zgarishlarni avval tekshirish mumkin:

```console
$ git log HEAD..FETCH_HEAD
$ git diff HEAD...FETCH_HEAD
```

## HOW DOES THE MERGE WORK? — MERGE QANDAY ISHLAYDI?

Git avval merge base’ni topadi, keyin uchta tree’ni o‘qiydi:

```text
stage 1 — umumiy ajdod (base)
stage 2 — joriy tarmoq (ours)
stage 3 — qo‘shilayotgan tarmoq (theirs)
```

Toza birlashadigan yo‘llar indeksning stage 0 holatiga yoziladi. Konfliktli yo‘llarda indeks 1, 2 va 3-bosqich obyektlarini saqlaydi:

```console
$ git ls-files --unmerged
$ git ls-files --stage
100644 <base-object>   1 hello
100644 <our-object>    2 hello
100644 <their-object>  3 hello
```

Ishchi faylda konflikt belgilari paydo bo‘ladi:

```text
<<<<<<< HEAD
joriy tarmoqdagi matn
=======
boshqa tarmoqdagi matn
>>>>>>> other-branch
```

Faylni tahrirlab to‘g‘ri natijani qoldiring, konflikt belgilarini olib tashlang va hal qilingan faylni indeksga qo‘shing:

```console
$ git add hello
$ git commit
```

`git add` stage 1–3 yozuvlarini bitta stage 0 yozuvi bilan almashtiradi. Merge’dan voz kechish:

```console
$ git merge --abort
```

Konflikt vaqtida farqlarni ko‘rish uchun `git diff`, `git diff --ours`, `git diff --theirs` va `git diff --base` kabi ko‘rinishlar foydali.

## PUBLISHING YOUR WORK — ISHINGIZNI E’LON QILISH

Boshqalar sizdan o‘zgarishlarni olishi uchun odatda alohida ochiq repozitoriy tayyorlanadi. Asosiy ish `.git` ostidagi shaxsiy repozitoriyda bajariladi; sifatli holatga kelgan o‘zgarishlar ochiq repozitoriyga **push** qilinadi.

Masofaviy kompyuterda bo‘sh repozitoriy yarating:

```console
$ mkdir my-git.git
$ GIT_DIR=my-git.git git init
```

Hozirgi tavsiya etiladigan ekvivalent:

```console
$ git init --bare my-git.git
```

Masofaviy repozitoriy tanlangan transport orqali boshqalarga ochiq bo‘lishi, yozish uchun esa sizda ruxsat bo‘lishi kerak. SSH orqali push ichkarida mahalliy `git send-pack` va masofaviy `git-receive-pack` ni bog‘laydi.

Mahalliy mashinadan yuborish:

```console
$ git push <public-host>:/path/to/my-git.git master
```

Bu ochiq repozitoriyning `master` uchini va undan erishiladigan obyektlarni mahalliy repozitoriy bilan sinxronlaydi.

> Eski “dumb HTTP” orqali tarqatishda `post-update` hook va `git update-server-info` talab qilinishi mumkin. Zamonaviy smart HTTP serverlarida bu odatda kerak emas.

## PACKING YOUR REPOSITORY — REPOZITORIYNI PACK QILISH

Har bir yangi Git obyekti dastlab `.git/objects/??/` ostida alohida **loose object** sifatida saqlanadi. Bu xavfsiz va atomar yaratish uchun qulay, ammo tarmoq orqali tashish uchun samarasiz. O‘zgarmas obyektlarni bitta pack ichiga zichlash mumkin:

```console
$ git repack
```

`.git/objects/pack/` ichida o‘zaro bog‘liq ikkita fayl paydo bo‘ladi:

- `pack-*.pack` — obyektlarning siqilgan ma’lumoti;
- `pack-*.idx` — obyektlarga tezkor murojaat qilish indeksi.

Ularni qo‘lda nusxalash zarur bo‘lsa, har doim birga nusxalang. Pack butunligini tekshirish:

```console
$ git verify-pack .git/objects/pack/pack-*.idx
```

Pack ichida mavjud loose obyektlarni olib tashlash:

```console
$ git prune-packed
```

Holatni tekshirish:

```console
$ find .git/objects -type f
$ git count-objects -v
```

Faol loyihani vaqti-vaqti bilan repack qilish saqlash joyi va uzatishni optimallashtiradi. Amalda kundalik texnik xizmatni ko‘pincha quyidagi porcelain buyruq boshqaradi:

```console
$ git gc
```

## WORKING WITH OTHERS — BOSHQALAR BILAN ISHLASH

Git to‘liq taqsimlangan tizim bo‘lsa-da, loyihani norasmiy dasturchilar iyerarxiyasi bilan tashkil qilish qulay. Git bu iyerarxiyani majburlamaydi; faqat bitta repozitoriydan olish shart emas.

### Loyiha rahbari uchun tavsiya etilgan jarayon

1. Mahalliy asosiy ish repozitoriysini tayyorlang.
2. Boshqalarga ochiq public repozitoriy yarating.
3. Asosiy repozitoriydan public repozitoriyga push qiling.
4. Public repozitoriyni repack qiling; transport mos bo‘lsa, keraksiz obyektlarni tozalang.
5. O‘z o‘zgarishlaringiz, email patchlari va subsystem maintainer repozitoriylaridan merge’lar bilan ishlashda davom eting.
6. Tayyor o‘zgarishlarni public repozitoriyga push qilib, e’lon qiling.
7. Vaqti-vaqti bilan public repozitoriyni repack qiling va ishni davom ettiring.

### Subsystem maintainer uchun jarayon

1. Loyiha rahbarining public repozitoriysini klonlang.
2. O‘zingizning public repozitoriyingizni tayyorlang.
3. Zarur bo‘lsa pack fayllar yoki `objects/info/alternates` orqali obyektlarni ulashing.
4. Asosiy ish repozitoriysidan public repozitoriyga push va repack qiling.
5. O‘z ishlaringiz, email patchlari, rahbar va quyi maintainer repozitoriylaridan merge’lar bilan davom eting.
6. O‘zgarishlarni public repozitoriyga push qilib, rahbaringizdan ularni pull qilishni so‘rang.
7. Vaqti-vaqti bilan repack qiling.

### Public repozitoriysiz individual dasturchi uchun jarayon

1. Rahbar yoki subsystem maintainer public repozitoriysini klonlang.
2. O‘z tarmog‘ingizda ishlang.
3. Vaqti-vaqti bilan upstream’dan faqat o‘zgarishlarni oling:

   ```console
   $ git fetch origin
   ```

4. Patchlardan qaysilari qabul qilinganini ko‘ring yoki qolganlarini yangi upstream ustiga ko‘chiring:

   ```console
   $ git cherry origin/master
   $ git rebase origin/master
   ```

5. Email orqali yuborish uchun patchlar tayyorlang:

   ```console
   $ git format-patch origin/master
   ```

## WORKING WITH OTHERS, SHARED REPOSITORY STYLE — UMUMIY REPOZITORIY USLUBI

CVS’dan kelgan foydalanuvchilarga yuqoridagi hamkorlik usuli yangi tuyulishi mumkin. Git bir nechta foydalanuvchi bevosita bitta umumiy public repozitoriyga yozadigan uslubni ham qo‘llaydi. Batafsil ma’lumot uchun `gitcvs-migration(7)` ga qarang.

Umumiy repozitoriy odatda guruh ruxsatlari bilan yaratiladi:

```console
$ git init --bare --shared project.git
```

Serverdagi ruxsatlar va branch himoyasi kim push qila olishini boshqarishi kerak.

## BUNDLING YOUR WORK TOGETHER — ISHLARNI BIRLASHTIRISH

Bir vaqtning o‘zida bir nechta mustaqil vazifa ustida ishlashda tarmoqlardan foydalanish qulay. Holat:

```console
$ git show-branch
! [commit-fix] Fix commit message normalization.
 ! [diff-fix] Fix rename detection.
  * [master] Release candidate #1
---
 +  [diff-fix] Fix rename detection.
 +  [diff-fix~1] Better common substring algorithm.
+   [commit-fix] Fix commit message normalization.
  * [master] Release candidate #1
++* [diff-fix~2] Pretty-print messages.
```

Ikki tuzatishni ketma-ket merge qilish mumkin:

```console
$ git merge -m "Merge fix in diff-fix" diff-fix
$ git merge -m "Merge fix in commit-fix" commit-fix
```

Agar o‘zgarishlar haqiqatan mustaqil bo‘lsa, bir nechta tarmoqni birdan merge qilish — **octopus merge** — mumkin. Avval misoldagi ikki merge’dan oldingi holatga qaytish:

```console
$ git reset --hard master~2
$ git merge commit-fix diff-fix
```

Natijada uch parentli merge commit yaratiladi:

```console
$ git show-branch
! [commit-fix] Fix commit message normalization.
 ! [diff-fix] Fix rename detection.
  * [master] Octopus merge of branches 'diff-fix' and 'commit-fix'
```

> Octopus merge’ni shunchaki mumkin bo‘lgani uchun ishlatmang. U bir vaqtning o‘zida ikkitadan ortiq **mustaqil va konfliktsiz** o‘zgarishni birlashtirganda tarixni soddalashtiradi. Konflikt chiqsa, tarmoqlar aslida mustaqil emas. Ularni ikkitadan merge qilib, konflikt qanday va nima sababli hal qilinganini commit tarixida hujjatlashtiring.

## SEE ALSO — SHUNINGDEK QARANG

`gittutorial(7)`, `gittutorial-2(7)`, `gitcvs-migration(7)`, `git-help(1)`, `giteveryday(7)`, *The Git User’s Manual*.

## GIT

`git(1)` buyruqlar to‘plamining bir qismi.

## NOTES — IZOH

1. *The Git User Manual*: `git-htmldocs/user-manual.html`
2. Randy Dunlap taqdimoti: `https://web.archive.org/web/20120915203609/http://www.xenotime.net/linux/mentor/linux-mentoring-2006.pdf`
