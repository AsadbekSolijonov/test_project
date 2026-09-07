# Git glossary — o‘zbekcha lug‘at

> Manba: `git help glossary` (Git 2.50.1). Atamalar qidirishni osonlashtirish uchun inglizcha nomlari saqlangan.

## Asosiy atamalar

### alternate object database

Boshqa repozitoriyning obyektlar bazasini ulash mexanizmi. `.git/objects/info/alternates` yoki `GIT_ALTERNATE_OBJECT_DIRECTORIES` orqali belgilanadi.

### bare repository

Ishchi daraxti bo‘lmagan repozitoriy; odatda server va almashish uchun ishlatiladi. Ko‘pincha `.git` qo‘shimchali katalog bo‘ladi.

### blob object

Bitta fayl tarkibini saqlovchi, ammo fayl nomini saqlamaydigan obyekt. Nom va rejim tree obyektida turadi.

### branch

Rivojlanish chizig‘i. Texnik jihatdan branch — commitga ishora qiluvchi va yangi commit bilan oldinga siljiydigan ref.

### cache / index

Ishchi daraxt bilan commit orasidagi staging maydoni. Keyingi commit uchun tayyorlangan tree holatini saqlaydi.

### chain

Obyektlar ro‘yxati bo‘lib, har bir obyekt o‘zidan oldingisiga yoki keyingisiga murojaat qiladi; masalan, commitlarning parent zanjiri.

### changeset

Boshqa SCM tizimlarida yagona o‘zgarishlar to‘plami. Git bu ma’noni commit orqali ifodalaydi.

### checkout

Tree yoki branch holatini ishchi daraxtga chiqarish. Zamonaviy Git’da branch almashtirish uchun `git switch`, fayl tiklash uchun `git restore` aniqroq.

### cherry-pick

Bitta mavjud commit kiritgan o‘zgarishni joriy branch uchida yangi commit sifatida qayta qo‘llash.

### clean

Ishchi daraxt kuzatiladigan fayllarda commit qilinmagan o‘zgarish yo‘q holat. Untracked fayllar alohida hisoblanishi mumkin.

### commit

Loyiha tarixidagi nuqta. Commit tree, parent commit(lar), muallif, committer, vaqt va xabarni saqlaydi.

### commit object

Muayyan tree suratini tarixga bog‘laydigan Git obyekti. Odatda bitta parent, merge’da bir nechta parent, root commitda parent yo‘q.

### commit-ish

Oxir-oqibat commit obyektiga yechiladigan nom: commit ID, branch, tag, `HEAD~2` va hokazo.

### core Git

Git’ning asosiy ma’lumot modeli va quyi darajadagi vositalari; ko‘pincha plumbing deb ataladi.

### DAG

Directed Acyclic Graph — yo‘naltirilgan siklsiz graf. Git commit tarixi parent yo‘nalishlari orqali DAG hosil qiladi.

### dangling object

Boshqa obyektlardan erishib bo‘lmaydigan, lekin repozitoriyda mavjud obyekt. `git fsck` uni ko‘rsatishi mumkin.

### detached HEAD

`HEAD` branchga emas, bevosita commitga ishora qiladigan holat. Bu holatda commit yaratish mumkin, ammo uni saqlash uchun branch yoki teg yaratish kerak.

### directory

Git tree obyektlari ifodalovchi katalog. Git bo‘sh katalogni alohida kuzatmaydi.

### dirty

Ishchi daraxtda indeks yoki `HEAD` ga nisbatan o‘zgarish bor holat.

### evil merge

Parentlardagi o‘zgarishlarni shunchaki birlashtirishdan tashqari, kutilmagan yangi o‘zgarish ham kiritgan merge commit.

### fast-forward

Joriy branch qo‘shilayotgan commitning ajdodi bo‘lganda, merge commitsiz branch ref’ini oldinga siljitish.

### fetch

Boshqa repozitoriydan obyekt va ref ma’lumotlarini olish, ammo ularni joriy branchga avtomatik merge qilmaslik.

### file system

Git ishchi fayllarni saqlaydigan tizim. Registr, Unicode va fayl rejimi xatti-harakati platformaga bog‘liq.

### git archive

Muayyan tree tarkibini `.tar` yoki `.zip` kabi arxivga chiqarish; `.git` tarixi kiritilmaydi.

### gitfile

Oddiy `.git` katalogi o‘rnidagi `gitdir: <path>` satrli fayl. Worktree va submodulelarda haqiqiy Git katalogiga ishora qiladi.

### grafts

Commit parentlarini mahalliy almashtirishning tarixiy mexanizmi. Hozir `git replace` afzal.

### hash / object ID (OID)

Obyekt tarkibidan hisoblangan identifikator. Ko‘p mavjud repozitoriylarda SHA-1, yangi formatlarda SHA-256 bo‘lishi mumkin.

### head

Branch uchidagi eng so‘nggi commitga murojaat. `refs/heads/*` mahalliy branchlarni saqlaydi.

### HEAD

Joriy checkout holatini bildiruvchi maxsus ref. Odatda joriy branchga ramziy ref, detached holatda bevosita commit ID.

### head ref

`refs/heads/` ostidagi branch ref’i.

### hook

Git hodisasi oldidan yoki keyin chaqiriladigan maxsus dastur. `.git/hooks` yoki `core.hooksPath` da joylashadi.

### index entry

Indeksdagi yo‘l, obyekt ID, fayl rejimi va stage raqamidan iborat yozuv.

### master / main

Standart branch uchun keng tarqalgan nomlar. Git ma’lumot modelida ular maxsus emas; `init.defaultBranch` bilan tanlanadi.

### merge

Bir nechta rivojlanish chizig‘ini birlashtirish. Natija fast-forward yoki bir nechta parentli merge commit bo‘lishi mumkin.

### merge base

Merge qilinayotgan commitlarning eng yaxshi umumiy ajdodi; uch tomonlama merge uchun asos.

### object

Git obyektlar bazasidagi blob, tree, commit yoki tag. Obyekt ID orqali nomlanadi va yaratilgach o‘zgarmaydi.

### object database

Git obyektlari saqlanadigan tizim; odatda `.git/objects`, pack fayllari va ixtiyoriy alternates’dan iborat.

### object type

To‘rt asosiy tur: blob, tree, commit va tag.

### octopus merge

Ikkitadan ortiq branchni bitta merge commitda birlashtirish. Mustaqil, konfliktsiz mavzular uchun mos.

### orphan branch

Boshlang‘ich commitining parent’i bo‘lmagan, mavjud tarixdan ajratilgan yangi branch.

### origin

Klon qilingan boshlang‘ich remote uchun standart nom. Git uchun majburiy yoki maxsus nom emas.

### overlay / no-overlay checkout

Overlay rejimida maqsad tree’da yo‘q fayllar ishchi daraxtdan olib tashlanmaydi; no-overlay maqsadga aniq moslashtiradi.

### pack / packfile

Ko‘p obyektni delta siqish bilan bitta `.pack` faylda saqlash formati; `.idx` indeksi bilan birga ishlaydi.

### parent

Commitdan oldingi commitga murojaat. Birinchi parent ko‘pincha asosiy rivojlanish chizig‘ini bildiradi.

### pathspec

Git buyrug‘i ishlaydigan yo‘llar to‘plamini belgilovchi pattern. Literal nom, glob va `:(attr:...)` kabi magic ishlatishi mumkin.

### pickaxe

Diff ichida muayyan matn yoki regex o‘zgarishini `-S` yoki `-G` orqali qidirish mexanizmi.

### plumbing

Past darajadagi barqaror ma’lumot modelini boshqaradigan Git buyruqlari; porcelain ularni birlashtirib qulay interfeys beradi.

### porcelain

Foydalanuvchiga mo‘ljallangan yuqori darajadagi buyruqlar, masalan `git add`, `git commit`, `git switch`.

### pull

Odatda `fetch` dan keyin `merge` yoki `rebase` bajaradigan birikma amal.

### push

Mahalliy obyektlarni masofaviy repozitoriyga uzatib, uning ref’larini yangilash so‘rovi.

### reachable

Ref yoki boshqa boshlang‘ich obyektdan obyekt murojaatlari bo‘ylab topish mumkin bo‘lgan obyekt.

### rebase

Commitlar ketma-ketligini boshqa asos ustida qayta yaratish. Commit ID’lari o‘zgaradi.

### ref

Obyekt ID’ga ishora qiluvchi nom, masalan `refs/heads/main` yoki `refs/tags/v1.0`.

### reflog

Ref’ning mahalliy yangilanishlar jurnali. `main@{yesterday}` yoki `HEAD@{2}` kabi eski holatlarga murojaat qilish imkonini beradi.

### refspec

Fetch/push vaqtida manba ref’ni maqsad ref’ga moslash qoidasi: `[+]<src>:<dst>`.

### remote

Boshqa repozitoriy uchun nomlangan konfiguratsiya: URL va fetch/push refspec’lari.

### remote-tracking branch

Remote branchning oxirgi olingan holatini saqlovchi mahalliy ref, masalan `origin/main`. Unda bevosita commit qilinmaydi.

### repository

Ref’lar, obyektlar bazasi va konfiguratsiyani saqlovchi Git ma’lumotlar to‘plami; ishchi daraxtli yoki bare bo‘lishi mumkin.

### resolve

Nomni obyekt ID’ga aylantirish yoki merge konfliktini hal qilish ma’nolarida ishlatiladi.

### revision

Odatda commit yoki commitlar to‘plamini ifodalovchi nom. `gitrevisions(7)` sintaksisiga qarang.

### rewind

Branch ref’ini uning eski ajdodiga qaytarish; public branchda non-fast-forward tarix almashtirilishiga olib keladi.

### SCM

Source Code Management — manba kodini boshqarish tizimi; Git shunday tizimlardan biri.

### SHA-1

Git’ning tarixiy obyekt xesh algoritmi. Obyekt nomlari odatda 40 hex belgili; qisqa, yagona prefiks ishlatilishi mumkin.

### shallow clone

Tarixning faqat cheklangan chuqurligiga ega klon. Ayrim tarix va merge-base amallari to‘liq bo‘lmasligi mumkin.

### stash

Ishchi daraxt va indeksdagi tugallanmagan o‘zgarishlarni vaqtincha commitga o‘xshash obyektlarda saqlash mexanizmi.

### submodule

Bitta repozitoriy tree’sida boshqa repozitoriyning muayyan commitiga ishora qiluvchi gitlink yozuvi.

### symref

Obyekt ID o‘rniga boshqa ref nomiga ishora qiluvchi ramziy ref. `HEAD` odatda symref.

### tag

Tarixdagi obyektga doimiy nom. Lightweight teg ref, annotated teg esa qo‘shimcha tag obyektidir.

### tag object

Obyekt, teg nomi, tagger, sana, xabar va ixtiyoriy imzoni saqlovchi annotated tag obyekti.

### topic branch

Bitta xususiyat yoki tuzatishga bag‘ishlangan, odatda qisqa yashovchi branch.

### tree

Katalog suratini ifodalovchi obyekt: fayl nomi/rejimini blob bilan, katalog nomini quyi tree bilan bog‘laydi.

### tree-ish

Oxir-oqibat tree obyektiga yechiladigan nom: tree ID, commit, branch yoki teg.

### unmerged index

Konflikt sababli ayrim yo‘llari stage 1, 2 va 3 yozuvlarga ega indeks.

### unreachable object

Ref, indeks yoki boshqa himoyalangan ildizdan topib bo‘lmaydigan obyekt. Vaqt o‘tgach garbage collection uni o‘chirishi mumkin.

### upstream branch

Mahalliy branch fetch/pull va status taqqoslashida kuzatadigan branch, masalan `main` uchun `origin/main`.

### working tree

Checkout qilingan fayllar ko‘rinadigan katalog. Bitta repozitoriy `git worktree` orqali bir nechta ishchi daraxtga ega bo‘lishi mumkin.

## SEE ALSO

`git(1)`, `gitrevisions(7)`, `gittutorial(7)`, `gitcore-tutorial(7)`.

## GIT

`git(1)` buyruqlar to‘plamining bir qismi.

