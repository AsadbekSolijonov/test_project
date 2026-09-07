# Git diffcore — o‘zbekcha tarjima

> Manba: `git help diffcore` (Git 2.50.1).

## NAME — NOMI

`gitdiffcore` — diff natijasini o‘zgartiruvchi ichki mexanizmlar.

## DESCRIPTION — TAVSIF

`git diff-*`, `git log -p`, `git format-patch` kabi buyruqlar avval fayl juftliklari to‘plamini hosil qiladi, so‘ng uni **diffcore** transformatsiyalar zanjiridan o‘tkazadi. Bu qatlam qayta yozilgan fayllarni ajratish, rename/copy topish, natijani tartiblash va boshlanish joyini aylantirish kabi ishlarni bajaradi.

## THE CHAIN OF OPERATION — AMALLAR ZANJIRI

Ichki kirish har bir yo‘l uchun taxminan quyidagi ma’lumotlardan iborat:

```text
old-path old-mode old-object-id
new-path new-mode new-object-id
```

Yaratilgan faylda eski tomon, o‘chirilgan faylda yangi tomon yo‘q. Oddiy o‘zgarishda eski va yangi yo‘l bir xil.

Transformatsiyalar odatda quyidagi tartibda ishlaydi:

1. `diffcore-break` — to‘liq qayta yozishni delete + create juftiga ajratadi.
2. `diffcore-rename` — delete/create juftlaridan rename yoki copy topadi.
3. `diffcore-merge-broken` — rename topilmagan singan juftlarni qayta birlashtiradi.
4. `diffcore-pickaxe` — muayyan matn yoki regex o‘zgarishiga aloqador fayllarni tanlaydi.
5. `diffcore-order` — yo‘llarni maxsus tartibga soladi.
6. `diffcore-rotate` — chiqish boshlanadigan yo‘lni o‘zgartiradi.

## DIFFCORE-BREAK — TO‘LIQ QAYTA YOZILISHNI AJRATISH

`-B[<break>[/<merge>]]` bir fayldagi juda katta o‘zgarishni eski fayl o‘chirilgani va yangi fayl yaratilganidek ko‘rsatadi:

```console
$ git diff -B
$ git diff -B60/70
```

`break` chegarasi faylning qanchasi saqlanmaganda uni to‘liq rewrite deb hisoblashni boshqaradi. Standart qiymat 50%. Kichikroq son rewrite’ni osonroq tan oladi.

`merge` chegarasi keyingi `merge-broken` bosqichida delete/create qayta bitta modification’ga birlashtirilishi shartini boshqaradi. Standart qiymat 80% atrofidagi “asl tarkibdan qolgan qism” mezoniga mos keladi.

`-B` `-M` bilan ishlatilganda to‘liq rewrite qilingan fayl rename uchun manba sifatida ham qatnashishi mumkin.

## DIFFCORE-RENAME — RENAME VA COPY ANIQLASH

Git rename’ni alohida metadata sifatida saqlamaydi. Diffcore o‘chirilgan va yaratilgan fayllar tarkibining o‘xshashligini hisoblab rename’ni taxmin qiladi:

```console
$ git diff -M
$ git diff --find-renames=90%
```

`-M<n>` o‘xshashlik chegarasini belgilaydi. `-M90%` kamida 90% o‘xshashlikni talab qiladi. Foizsiz `-M5` `-M50%` ma’nosida talqin qilinishi mumkin.

Copy aniqlash:

```console
$ git diff -C
$ git diff --find-copies-harder
```

Oddiy `-C` o‘zgartirilgan manbalarni copy nomzodi sifatida ko‘radi. `--find-copies-harder` o‘zgarmagan fayllarni ham tekshiradi va qimmatroq hisoblash talab qiladi.

Rename/copy qidiruvini cheklash:

```console
$ git diff -l1000 -M -C
```

Nomzodlar juda ko‘payganda aniq (`O(N²)`) bosqich qimmatga tushadi. `-l<num>` yoki `diff.renameLimit` uning limitini boshqaradi.

## DIFFCORE-MERGE-BROKEN — AJRATILGAN REWRITE’NI QAYTA BIRLASHTIRISH

`diffcore-break` hosil qilgan delete/create jufti rename/copy sifatida ishlatilmasa, `diffcore-merge-broken` ularni bitta modification yozuviga qaytarishi mumkin. Shunda patch eski faylning hammasini olib tashlab, yangisini qo‘shgandek ko‘rinsa-da, statistikada modification sifatida turadi.

`-B` dagi ikkinchi chegara bu qayta birlashtirishni boshqaradi.

## DIFFCORE-PICKAXE — MATN BO‘YICHA O‘ZGARISH TANLASH

Hujjatning sarlavhalar ro‘yxatida alohida ko‘rinmasa ham, zanjirning muhim bosqichi pickaxe’dır.

Muayyan satr uchrashuvlari sonini o‘zgartirgan commitlarni topish:

```console
$ git log -S'function_name' -p
```

Regex’ga mos satr qo‘shilgan yoki o‘chirilgan patchlarni topish:

```console
$ git log -G'regular.*expression' -p
```

`--pickaxe-all` mos kelgan commitdagi barcha fayl o‘zgarishlarini ko‘rsatadi; `--pickaxe-regex` `-S` argumentini kengaytirilgan regex sifatida talqin qiladi.

## DIFFCORE-ORDER — NATIJANI YO‘L BO‘YICHA TARTIBLASH

`-O<orderfile>` diff fayllarini maxsus tartibda chiqaradi:

```console
$ git diff -Oorder.txt
```

Orderfile har satrda glob pattern saqlaydi. Birinchi mos pattern oldin keladi; hech biriga mos bo‘lmagan yo‘llar oxirida odatiy tartibda chiqadi. Bo‘sh satrlar va `#` bilan boshlangan izohlar e’tiborsiz qoldiriladi.

Misol:

```text
README*
Documentation/**
include/**
src/**
```

## DIFFCORE-ROTATE — CHIQISH BOSHLANISHINI O‘ZGARTIRISH

`--rotate-to=<path>` natijani berilgan yo‘ldan boshlab, oldingi yo‘llarni oxirga aylantiradi:

```console
$ git diff --rotate-to=src/main.c
```

`--skip-to=<path>` esa undan oldingi natijalarni umuman tashlab ketadi:

```console
$ git diff --skip-to=src/main.c
```

Bu parametrlar asosan `git difftool` va tashqi diff iste’molchilariga qulay.

## SEE ALSO

`git-diff(1)`, `git-log(1)`, `git-format-patch(1)`.

## GIT

`git(1)` buyruqlar to‘plamining bir qismi.

