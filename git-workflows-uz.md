# Git workflows — o‘zbekcha tarjima

> Manba: `git help workflows` (Git 2.50.1).

## NAME — NOMI

`gitworkflows` — Git bilan tavsiya etilgan ish jarayonlari sharhi.

## SYNOPSIS

```console
git *
```

## DESCRIPTION — TAVSIF

Bu hujjat Git loyihasining o‘zida qo‘llanadigan ish jarayonlari asosida umumiy tavsiyalar beradi. Har bir loyiha o‘z ehtiyojiga moslashtirishi mumkin.

## SEPARATE CHANGES — O‘ZGARISHLARNI AJRATISH

Har bir mantiqiy o‘zgarishni alohida commit qiling. Commit:

- bitta tushunarli maqsadga ega bo‘lsin;
- kompilyatsiya va testdan o‘tsin;
- bog‘liq bo‘lmagan formatlash yoki refaktorlarni aralashtirmasin;
- mazmunli sarlavha va kerak bo‘lsa batafsil sababni tushuntirsin.

Ishchi daraxtdagi ayrim bo‘laklarni tanlab staging qilish:

```console
$ git add -p
$ git diff --cached
$ git commit
```

So‘nggi commitni public bo‘lmasidan oldin tuzatish:

```console
$ git commit --amend
```

Bir nechta mahalliy commitni tartiblash, birlashtirish yoki tahrirlash:

```console
$ git rebase -i <upstream>
```

> Boshqalar foydalanayotgan public tarixni rebase qilib qayta yozmang. Public xatoni odatda `git revert` bilan yangi commit orqali bekor qiling.

Patch yuborishdan oldin:

```console
$ git diff --check
$ git log --check
$ git format-patch --cover-letter <upstream>
```

## MANAGING BRANCHES — TARMOQLARNI BOSHQARISH

Uzoq yashovchi integratsiya tarmoqlari va qisqa yashovchi topic branchlar ajratiladi.

Odatdagi rollar:

- `maint` — chiqarilgan versiyalarga muhim tuzatishlar;
- `master` yoki `main` — keyingi reliz uchun barqaror asos;
- `next` — keyingi siklga nomzod topic branchlar integratsiyasi;
- `seen` — tajribaviy, hali tayyor bo‘lmagan mavzular sinovi;
- `topic/*` — bitta xususiyat yoki tuzatish uchun alohida tarmoqlar.

Topic branch yaratish:

```console
$ git switch -c topic/my-change master
```

Upstream yangilanganda, topic hali e’lon qilinmagan bo‘lsa:

```console
$ git fetch origin
$ git rebase origin/master
```

Tayyor topicni merge qilish:

```console
$ git switch master
$ git merge --no-ff topic/my-change
```

`--no-ff` alohida topic tarixini ko‘rsatadigan merge commit yaratadi; loyiha siyosatiga qarab fast-forward yoki squash merge ham tanlanishi mumkin.

Tarmoqdagi ishlarni taqqoslash:

```console
$ git log master..topic/my-change
$ git diff master...topic/my-change
$ git range-diff old-base..old-tip new-base..new-tip
```

`seen` kabi tashlab qayta yaratiladigan integratsiya tarmoqlari public barqaror API hisoblanmaydi. Barqaror tarmoqlar esa odatda orqaga qaytarilmasligi kerak.

## DISTRIBUTED WORKFLOWS — TAQSIMLANGAN ISH JARAYONLARI

Git’da har bir dasturchi to‘liq repozitoriyga ega. O‘zgarishlar ikki asosiy yo‘l bilan uzatiladi:

### Patch orqali

```console
$ git format-patch origin/master
$ git send-email 000*.patch
```

Integrator:

```console
$ git am -3 000*.patch
```

Bu model email review, commit muallifligini saqlash va patchlarni arxivlash uchun qulay.

### Pull orqali

Contributor tarmog‘ini public joyga push qiladi:

```console
$ git push public topic/my-change
$ git request-pull origin/master public-url topic/my-change
```

Integrator o‘zgarishni oladi va tekshiradi:

```console
$ git fetch contributor topic/my-change
$ git log --show-signature HEAD..FETCH_HEAD
$ git diff HEAD...FETCH_HEAD
$ git merge FETCH_HEAD
```

Integratsiya pog‘onali bo‘lishi mumkin: contributor → subsystem maintainer → project maintainer. Har bir bosqich o‘zgarishni review va test qiladi.

Yaxshi taqsimlangan jarayon quyidagilarni aniq belgilaydi:

- qaysi tarmoq nima uchun ishlatilishi;
- patch yoki pull request formati;
- test va review talablari;
- kim qaysi tarmoqqa push/merge qila olishi;
- imzo, sign-off va release teg siyosati;
- public tarixni qayta yozish qoidalari.

## SEE ALSO

`giteveryday(7)`, `gitglossary(7)`, `gitworktree(1)`, `git-rebase(1)`, `git-merge(1)`, `gitworkflows(7)`.

## GIT

`git(1)` buyruqlar to‘plamining bir qismi.
