# Git namespaces — o‘zbekcha tarjima

> Manba: `git help namespaces` (Git 2.50.1).

## NAME — NOMI

`gitnamespaces` — Git nomlar fazolari.

## DESCRIPTION — TAVSIF

Git namespace bitta repozitoriydagi ref’larni mantiqan ajratib, tashqi tomondan bir nechta mustaqil repozitoriyga o‘xshatadi. Har bir namespace o‘z tarmoqlari, teglari va `HEAD` kabi ref’larini ko‘radi, ammo obyektlar bazasi va konfiguratsiyaning ayrim qismlarini umumiy ishlatadi.

Namespace tanlash:

```console
$ GIT_NAMESPACE=alice git show-ref
```

Yoki:

```console
$ git --namespace=alice show-ref
```

Namespace’dagi ref’lar ichkarida `refs/namespaces/<namespace>/refs/...` ostida saqlanadi. Masalan, `alice` namespace’dagi `refs/heads/main`:

```text
refs/namespaces/alice/refs/heads/main
```

Namespace nomi `/` saqlashi mumkin; har bir qism ichma-ich `refs/namespaces/` orqali kengaytiriladi.

Asosiy qo‘llanish holati — `git daemon`, SSH yoki HTTP orqali bitta obyektlar bazasidan bir necha mantiqiy repozitoriy xizmatini ko‘rsatish. Server `GIT_NAMESPACE` ni autentifikatsiya qilingan foydalanuvchi yoki virtual loyiha nomidan kelib chiqib belgilashi mumkin.

## SECURITY — XAVFSIZLIK

Namespace’lar ref’larni ajratadi, lekin obyektlarni to‘liq xavfsizlik chegarasi sifatida ajratmaydi. Bir namespace foydalanuvchisi boshqa namespace’dagi ref nomini ko‘rmasligi mumkin, ammo obyekt ID ma’lum bo‘lsa yoki obyekt boshqa yo‘l bilan erishiladigan bo‘lsa, umumiy obyektlar bazasidagi ma’lumot sizib chiqishi ehtimoli bor.

Shuning uchun:

- o‘zaro ishonmaydigan tenantlar uchun namespace’ni yagona maxfiylik himoyasi deb hisoblamang;
- qat’iy ajratish zarur bo‘lsa, alohida repozitoriy va filesystem ruxsatlaridan foydalaning;
- serverdagi upload/receive hooklari va yashirin ref sozlamalarini tekshiring;
- foydalanuvchidan kelgan namespace qiymatini tekshirmasdan muhitga qo‘ymang.

Namespace’lar administrativ va saqlash optimallashtirish vositasi; sandbox yoki access-control tizimi emas.

## GIT

`git(1)` buyruqlar to‘plamining bir qismi.

