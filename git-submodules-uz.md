# Git submodules — o‘zbekcha tarjima

> Manba: `git help submodules` (Git 2.50.1).

## NAME — NOMI

`gitsubmodules` — bir repozitoriyni boshqasining ichiga ulash.

## DESCRIPTION — TAVSIF

Submodule bir Git repozitoriysiga boshqa Git repozitoriysining muayyan commitini joylashtirish imkonini beradi. Tashqi (**superproject**) repozitoriy submodule fayllarini bevosita saqlamaydi; uning tree’sida `160000` rejimli **gitlink** yozuvi orqali submodule commit ID’sini saqlaydi.

Odatda yana `.gitmodules` fayli saqlanadi. U submodule nomi, yo‘li va tavsiya etilgan URL’ni beradi:

```ini
[submodule "libfoo"]
    path = deps/libfoo
    url = https://example.com/libfoo.git
```

Submodule qo‘shish:

```console
$ git submodule add https://example.com/libfoo.git deps/libfoo
$ git commit -m "Add libfoo submodule"
```

Superproject aynan submodule’dagi joriy commitni qayd etadi. Submodule branchining nomi yoki “eng yangi versiya” avtomatik saqlanmaydi.

## THE CONFIGURATION OF SUBMODULES — SUBMODULE SOZLAMALARI

Submodule xatti-harakatini bir nechta joy boshqaradi. Ularning ustuvorligi vaziyatga qarab farq qiladi.

### `.gitmodules`

Repozitoriy bilan birga commit qilinadigan tavsiya etilgan sozlama:

```ini
[submodule "libfoo"]
    path = deps/libfoo
    url = ../libfoo.git
    branch = main
    update = checkout
```

Muhim kalitlar:

- `submodule.<name>.path` — superproject ichidagi yo‘l;
- `submodule.<name>.url` — klonlash manzili;
- `submodule.<name>.branch` — remote’dan yangilashda kuzatiladigan branch;
- `submodule.<name>.update` — `checkout`, `merge`, `rebase`, `none` yoki maxsus buyruq;
- `submodule.<name>.ignore` — status/diff qaysi o‘zgarishlarni e’tiborsiz qoldirishi;
- `submodule.<name>.fetchRecurseSubmodules` — fetch ichkariga rekursiya qilishi;
- `submodule.<name>.shallow` — shallow clone tavsiyasi.

`.gitmodules` ishonchsiz repozitoriydan kelishi mumkin. Xavfsizlik sababli ayrim qiymatlar, masalan custom `update = !command`, undan bevosita bajarilmaydi.

### `.git/config`

Mahalliy, foydalanuvchiga xos sozlama. `git submodule init` `.gitmodules` dagi mos qiymatlarni shu yerga ko‘chiradi:

```console
$ git submodule init
$ git config submodule.libfoo.url ssh://git@example.com/libfoo.git
```

Mahalliy URL `.gitmodules` ni o‘zgartirmasdan override qilinadi.

### Buyruq satri

Ko‘p buyruqlarda `--recurse-submodules`, `--remote`, `--checkout`, `--merge`, `--rebase`, `--depth` kabi parametrlar bir martalik xatti-harakatni tanlaydi.

### Tree’dagi gitlink

Superproject commitidagi asosiy manba — qaysi submodule commit checkout qilinishi kerakligini bildiruvchi gitlink:

```console
$ git ls-tree HEAD deps/libfoo
160000 commit <object-id>    deps/libfoo
```

## FORMS — SUBMODULE HOLATLARI

Submodule quyidagi shakllarda bo‘lishi mumkin:

### Deinitialized

Gitlink va `.gitmodules` yozuvi mavjud, ammo ishchi katalog checkout qilinmagan va mahalliy `submodule.<name>.url` yo‘q.

```console
$ git submodule deinit deps/libfoo
```

### Initialized, ammo checkout qilinmagan

Mahalliy konfiguratsiya bor, lekin ishchi daraxt hali yaratilmagan.

```console
$ git submodule init deps/libfoo
```

### Populated

Submodule repozitoriysi va ishchi daraxti mavjud, gitlink ko‘rsatgan commit checkout qilingan:

```console
$ git submodule update --init deps/libfoo
```

### Modified

Submodule ichida commit qilinmagan o‘zgarish bor yoki uning `HEAD` commit’i superproject qayd etgan gitlink’dan farq qiladi:

```console
$ git status
$ git diff --submodule
$ git submodule status
```

`git submodule status` prefikslari:

- bo‘sh joy — kutilgan commit checkout qilingan;
- `-` — initialize qilinmagan;
- `+` — boshqa commit checkout qilingan;
- `U` — merge konflikti bor.

### Old-form submodule

Eski Git versiyalarida submodule `.git` katalogi bevosita submodule ishchi katalogi ichida bo‘lgan. Zamonaviy Git odatda Git katalogini superproject `.git/modules/<name>/` ostida saqlab, submodule ichida gitfile qoldiradi.

```console
$ git submodule absorbgitdirs
```

Bu eski ichki `.git` kataloglarini zamonaviy joylashuvga ko‘chiradi.

### Deleted submodule

Gitlink va `.gitmodules` yozuvi tarixdan o‘chirilgan bo‘lsa ham, lokal Git katalogi xavfsizlik uchun `.git/modules/` da qolishi mumkin. Uni qo‘lda o‘chirish qayta tiklab bo‘lmaydigan local commitlarni yo‘qotishi mumkin; avval ref va obyektlarni tekshiring.

## ACTIVE SUBMODULES — FAOL SUBMODULELAR

Git ayrim rekursiv amallarni faqat **active** submodulelarda bajaradi. Faollik quyidagi qoidalardan aniqlanadi:

1. `submodule.active` pathspec’iga mos kelsa — faol.
2. `submodule.<name>.active` `true` bo‘lsa — faol, `false` bo‘lsa — faol emas.
3. Yuqoridagilar belgilanmagan va `submodule.<name>.url` mavjud bo‘lsa — tarixiy moslik uchun faol.

Misol:

```ini
[submodule]
    active = deps/*
[submodule "large-data"]
    active = false
```

Faqat kerakli submodulelarni initialize qilish katta loyihalarda klonlash va fetch vaqtini kamaytiradi.

```console
$ git submodule update --init deps/libfoo
$ git submodule update --init --recursive
```

## WORKFLOW FOR A THIRD-PARTY LIBRARY — TASHQI KUTUBXONA BILAN ISHLASH

Submodule tashqi kutubxonaning aniq, tekshirilgan versiyasini loyiha bilan bog‘lash uchun ishlatilishi mumkin.

### Loyiha foydalanuvchisi

Submodulelar bilan klonlash:

```console
$ git clone --recurse-submodules <superproject-url>
```

Oddiy klon allaqachon qilingan bo‘lsa:

```console
$ git submodule update --init --recursive
```

Superproject yangilangach gitlinkdagi versiyalarni chiqarish:

```console
$ git pull
$ git submodule update --init --recursive
```

Rekursiv pull:

```console
$ git pull --recurse-submodules
```

Bu yangi commitlarni fetch qiladi, ammo checkout strategiyasi va mahalliy o‘zgarishlarga qarab alohida `submodule update` talab qilinishi mumkin.

### Kutubxona versiyasini yangilovchi dasturchi

Submodule ichida kerakli commitga o‘ting:

```console
$ cd deps/libfoo
$ git fetch origin
$ git switch --detach <tested-commit>
$ cd ../..
$ git add deps/libfoo
$ git commit -m "Update libfoo to <version>"
```

Superproject commit’i gitlink yangilanishini saqlaydi. Hamkasblar aynan shu commitni checkout qiladi.

Remote branchning so‘nggi holatiga yangilash:

```console
$ git submodule update --remote deps/libfoo
$ git diff --submodule
$ git add deps/libfoo
$ git commit
```

`--remote` avtomatik olingan natijani sinovsiz commit qilish degani emas; dependency API, lockfile, litsenziya va testlarni tekshiring.

### Submodule ichida o‘zgarish qilish

Submodule odatda detached HEAD’da bo‘ladi. Ishni yo‘qotmaslik uchun avval branch yarating:

```console
$ git -C deps/libfoo switch -c my-fix
# tahrir, test
$ git -C deps/libfoo add .
$ git -C deps/libfoo commit
$ git -C deps/libfoo push origin my-fix
$ git add deps/libfoo
$ git commit -m "Use libfoo fix"
```

Avval submodule commit’ini unga tegishli remote’ga push qiling, keyin superproject gitlink’ini push qiling. Aks holda hamkasblar superproject ko‘rsatgan obyektni ola olmaydi.

Push vaqtida tekshirish:

```console
$ git push --recurse-submodules=check
# yoki submodule commitlarini ham yuborish:
$ git push --recurse-submodules=on-demand
```

## WORKFLOW FOR AN ARTIFICIALLY SPLIT REPO — SUN’IY AJRATILGAN REPOZITORIY

Katta loyiha tashkiliy yoki access-control sabablari bilan bir necha repozitoriyga bo‘linib, superproject orqali birlashtirilishi mumkin. Bunda submodulelar bir-biriga mustaqil bo‘lmasligi ehtimoli bor.

Tavsiya etiladigan sozlamalar:

```console
$ git config status.submoduleSummary true
$ git config diff.submodule log
$ git config fetch.recurseSubmodules on-demand
$ git config push.recurseSubmodules check
```

Branchlarni birga yuritish kerak bo‘lsa:

```console
$ git submodule foreach --recursive 'git status --short'
$ git submodule foreach --recursive 'git fetch origin'
```

`foreach` ichidagi shell kodiga ehtiyot bo‘ling; submodule nomi va yo‘li ishonchsiz bo‘lishi mumkin.

Superproject branchini almashtirishda:

```console
$ git switch feature
$ git submodule update --init --recursive
```

Submoduledagi mahalliy o‘zgarish checkout’ga xalaqit bersa, Git ularni jim o‘chirib yubormasligi kerak. O‘zgarishlarni commit yoki stash qiling, keyin qayta urinib ko‘ring.

Parallel yangilash:

```console
$ git submodule update --init --recursive --jobs 8
```

## IMPLEMENTATION DETAILS — ICHKI TUZILISH

### Git katalogi

Zamonaviy joylashuv:

```text
superproject/.git/modules/<name>/
superproject/<path>/.git   # gitdir: ../../.git/modules/<name> ko‘rsatkichli fayl
```

Bu submodule ishchi katalogi o‘chirilganda ham uning local obyektlari va branchlarini saqlab qolishga yordam beradi.

### Gitlink

Tree yozuvi:

```text
160000 commit <submodule-commit-id> <path>
```

Superproject obyektlar bazasi submodule commit obyektini majburan saqlamaydi. Uni submodule remote’dan olish kerak.

### `.gitmodules` xavfsizligi

`.gitmodules` versiya nazoratida va remote’dan keladi. Git protokolga mos kelmaydigan yoki xavfli URL/command’larni cheklaydi. Klonlangan loyiha kodini, hooklarini yoki submodule ichidagi build skriptlarini ishonch hosil qilmasdan ishga tushirmang.

URL o‘zgarganda mahalliy konfiguratsiyani sinxronlash:

```console
$ git submodule sync --recursive
$ git submodule update --init --recursive
```

### Submodule merge konflikti

Ikki branch gitlinkni turli commitga yangilasa, Git faqat bir tomon ikkinchisining ajdodi bo‘lganda avtomatik tanlay oladi. Aks holda submodulega kirib, mos commitni checkout yoki merge qiling:

```console
$ git -C deps/libfoo log --graph --all --oneline
$ git -C deps/libfoo switch -c resolve <one-side>
$ git -C deps/libfoo merge <other-side>
$ git add deps/libfoo
$ git commit
```

Hosil qilingan submodule merge commit’ini remote’ga push qilishni unutmang.

### Submodule olib tashlash

Gitlink va `.gitmodules` yozuvini versiya nazoratidan olib tashlash:

```console
$ git rm deps/libfoo
$ git commit -m "Remove libfoo submodule"
```

`.git/modules/libfoo` dagi lokal repozitoriyni darhol o‘chirish majburiy emas. Unda push qilinmagan commit bo‘lishi mumkin; butunlay tozalashdan oldin tekshiring.

## SEE ALSO

`git-submodule(1)`, `gitmodules(5)`, `git-config(1)`, `git-diff(1)`.

## GIT

`git(1)` buyruqlar to‘plamining bir qismi.
