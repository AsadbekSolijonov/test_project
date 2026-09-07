# Git for CVS users — o‘zbekcha tarjima

> Manba: `gitcvs-migration(7)`. Mahalliy Git 2.50.1 paketida man sahifa o‘rnatilmaganligi sababli rasmiy Git hujjatidagi qo‘llanma asosida tarjima qilindi.

## NAME — NOMI

`gitcvs-migration` — CVS foydalanuvchilari uchun Git.

## SYNOPSIS

```console
git cvsimport *
```

## DESCRIPTION — TAVSIF

Git CVS’dan farq qiladi: har bir ishchi daraxt loyiha tarixining to‘liq nusxasiga ega repozitoriyni saqlaydi va hech bir repozitoriy tabiatan boshqasidan muhimroq emas. Shunga qaramay, hamma sinxronlanadigan yagona shared repozitoriy belgilab, CVS modeliga o‘xshash ishlash mumkin.

Avval `gittutorial(7)` va `gitglossary(7)` bilan tanishish yetarli boshlang‘ich bilim beradi.

## DEVELOPING AGAINST A SHARED REPOSITORY — UMUMIY REPOZITORIY BILAN ISHLASH

`foo.com` dagi `/pub/repo.git` ni klonlash:

```console
$ git clone foo.com:/pub/repo.git/ my-project
$ cd my-project
```

CVS’dagi `cvs update` ga yaqin amal:

```console
$ git pull origin
```

O‘zgarishlarni yuborish:

```console
$ git push origin master
```

Git’da ish avval mahalliy commit qilinadi, keyin umumiy repozitoriyga push qilinadi. Shu sababli tarmoq uzilgan paytda ham commit, log va diff ishlaydi.

## SETTING UP A SHARED REPOSITORY — UMUMIY REPOZITORIY YARATISH

Bare shared repozitoriy yaratish:

```console
$ git init --bare --shared /pub/my-repo.git
```

Barcha committerlarni bitta tizim guruhiga kiriting va repozitoriyni shu guruhga bering:

```console
$ chgrp -R <group> /pub/my-repo.git
```

Kataloglar boshqa guruh a’zolari tomonidan yozilishi va ko‘rilishi uchun mos `umask` hamda setgid ruxsatlari zarur. `--shared` kerakli Git konfiguratsiyasini va boshlang‘ich ruxsatlarni sozlashga yordam beradi.

Har bir dasturchi repozitoriyni klonlab, mahalliy commitlar yaratadi va tayyor ishni push qiladi. Push rad etilsa, avval yangiliklarni pull/rebase qilib, konfliktlarni hal qilish kerak.

## IMPORTING A CVS ARCHIVE — CVS ARXIVINI IMPORT QILISH

Tarixiy qo‘llanmadagi usul:

```console
$ git cvsimport -C <destination> <module>
```

Bu CVS modulini `<destination>` ichidagi Git repozitoriysiga import qiladi. `git cvsimport` `cvsps` ga tayanadi.

> Zamonaviy bir martalik migratsiyada `cvs2git` yoki `cvs-fast-export` yaxshiroq natija berishi mumkin. Import qilingan branchlarda bevosita ishlashdan oldin mualliflar, teglar, tarmoqlar, fayl tarkibi va commit vaqtlarini tekshiring.

Masofaviy nom bilan incoming tarmoqlarni ajratish tavsiya qilinadi:

```console
$ git cvsimport -C project-git -r cvs <module>
```

Import tekshiruvi:

```console
$ git fsck --full
$ git log --all --graph --decorate --oneline
$ git tag --list
```

Shared public repozitoriy kerak bo‘lsa, import natijasidan bare klon yarating:

```console
$ git clone --bare project-git project.git
```

## ADVANCED SHARED REPOSITORY MANAGEMENT — KENGAYTIRILGAN BOSHQARUV

Branch bo‘yicha ruxsat va siyosatni server hooklari, ayniqsa `pre-receive` va `update`, orqali tekshirish mumkin. Masalan:

- faqat ayrim foydalanuvchilarga muayyan branchga push ruxsati;
- non-fast-forward push’ni taqiqlash;
- teglarni o‘chirish yoki almashtirishni cheklash;
- commit xabari yoki imzosini tekshirish.

Hook rad etsa, push qabul qilinmaydi. Hooklar executable bo‘lishi va ishonchli server tomonida saqlanishi kerak.

## PROVIDING CVS ACCESS TO A GIT REPOSITORY — GIT’GA CVS KIRISHINI BERISH

Eski CVS mijozlariga Git repozitoriysini CVS serveri sifatida ko‘rsatish uchun `git cvsserver` dan foydalanish mumkin. Tafsilotlar `git-cvsserver(1)` da.

Bu moslik qatlami Git’ning barcha imkoniyatlarini bermaydi; imkon bo‘lsa foydalanuvchilarni to‘g‘ridan-to‘g‘ri Git ish jarayoniga ko‘chirish ma’qul.

## ALTERNATIVE DEVELOPMENT MODELS — MUQOBIL ISH MODELLARI

CVS foydalanuvchilari umumiy repozitoriyga bir guruh dasturchi commit qilishiga o‘rgangan. Git buni qo‘llaydi, ammo taqsimlangan tabiati boshqa modellarni ham beradi:

- bitta maintainer asosiy public repozitoriyni yuritadi;
- dasturchilar uni klonlab, mustaqil tarmoqlarda ishlaydi;
- tayyor o‘zgarishlar uchun pull request yoki pull so‘rovi yuboradi;
- maintainer o‘zgarishlarni tekshiradi va asosiy tarixga qo‘shadi;
- kichik jamoalar markaziy maintainersiz bir-birining repozitoriylaridan pull qilishi mumkin.

## SEE ALSO

`gittutorial(7)`, `gittutorial-2(7)`, `gitcore-tutorial(7)`, `gitglossary(7)`, `giteveryday(7)`.

