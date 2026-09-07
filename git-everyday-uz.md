# Everyday Git — o‘zbekcha tarjima

> Manba: `git help everyday` (Git 2.50.1). Kundalik Git uchun taxminan 20 ta foydali buyruq. Buyruq misollari asl holida saqlangan.

## NAME — NOMI

`giteveryday` — Git’dan kundalik foydalanish uchun foydali minimal buyruqlar to‘plami.

## SYNOPSIS — QISQACHA MAZMUNI

**Everyday Git With 20 Commands Or So** — taxminan 20 ta buyruq bilan kundalik Git.

## DESCRIPTION — TAVSIF

Kundalik foydali Git buyruqlarini tushuntirish uchun foydalanuvchilar to‘rt guruhga ajratiladi:

- **Individual Developer (Standalone)** — yolg‘iz ishlasa ham commit yaratuvchi har bir dasturchiga zarur buyruqlar.

- **Individual Developer (Participant)** — boshqa odamlar bilan birga ishlaydigan dasturchiga qo‘shimcha zarur buyruqlar.

- **Integrator** — boshqalarning ishini ko‘rib chiqib, birlashtiradigan odam uchun qo‘shimcha buyruqlar.

- **Repository Administration** — Git repozitoriylarini yaratib, xizmat ko‘rsatuvchi tizim administratorlari uchun buyruqlar.

## INDIVIDUAL DEVELOPER (STANDALONE) — MUSTAQIL DASTURCHI

Mustaqil dasturchi boshqalar bilan patch almashmaydi va bitta repozitoriyda yolg‘iz ishlaydi.

### Asosiy buyruqlar

- `git init` — yangi repozitoriy yaratish.
- `git log` — tarixda nima bo‘lganini ko‘rish.
- `git switch`, `git branch` — tarmoqlar yaratish va almashtirish.
- `git add` — indeks (staging area) ni boshqarish.
- `git diff`, `git status` — joriy ish holatini ko‘rish.
- `git commit` — joriy tarmoq tarixini oldinga siljitish.
- `git restore` — o‘zgarishlarni bekor qilish.
- `git merge` — mahalliy tarmoqlarni birlashtirish.
- `git rebase` — mavzuli tarmoqlarni yuritish.
- `git tag` — tarixdagi ma’lum nuqtani belgilash.

### Misol: tar arxividan yangi repozitoriy yaratish

```console
$ tar zxf frotz.tar.gz
$ cd frotz
$ git init
$ git add .
$ git commit -m "import of frotz source tree."
$ git tag v2.43
```

`git add .` joriy katalog ostidagi hamma narsani indeksga qo‘shadi. `git tag v2.43` yengil, izohsiz teg yaratadi.

### Misol: topic branch yaratib, unda ishlash

```console
$ git switch -c alsa-audio
$ edit/compile/test
$ git restore curses/ux_audio_oss.c
$ git add curses/ux_audio_alsa.c
$ edit/compile/test
$ git diff HEAD
$ git commit -a -s
$ edit/compile/test
$ git diff HEAD^
$ git commit -a --amend
$ git switch master
$ git merge alsa-audio
$ git log --since='3 days ago'
$ git log v2.43.. curses/
```

Buyruqlar izohi:

1. `switch -c` yangi `alsa-audio` mavzuli tarmog‘ini yaratib, unga o‘tadi.
2. `restore` muvaffaqiyatsiz o‘zgarishlarni `curses/ux_audio_oss.c` dan qaytaradi.
3. Yangi faylni Git’ga `add` bilan ochiq bildirish kerak. Keyingi `commit -a` kuzatilayotgan fayllarning o‘zgarishi va o‘chirilishini aniqlaydi, ammo yangi faylni aniqlamaydi.
4. `diff HEAD` commit qilinadigan o‘zgarishlarni ko‘rsatadi.
5. `commit -a -s` sinovdan o‘tgan barcha kuzatilayotgan o‘zgarishlarni sign-off bilan commit qiladi.
6. `diff HEAD^` oldingi commitni ham qo‘shgan holda barcha o‘zgarishlarni ko‘rsatadi.
7. `commit --amend` yangi o‘zgarishlarni avvalgi commitga, uning asl xabarini saqlagan holda qo‘shadi.
8. `master` tarmog‘iga qaytiladi.
9. Topic branch `master` ga birlashtiriladi.
10. Oxirgi uch kundagi commitlar ko‘riladi. `-10`, `--until=2005-12-10` kabi cheklovlarni birga ishlatish mumkin.
11. `v2.43` dan beri faqat `curses/` ichiga ta’sir qilgan o‘zgarishlar ko‘riladi.

## INDIVIDUAL DEVELOPER (PARTICIPANT) — JAMOA ISHTIROKCHISI

Jamoaviy loyihada qatnashuvchi dasturchi mustaqil dasturchi buyruqlariga qo‘shimcha ravishda boshqalar bilan aloqa qilishni bilishi kerak.

### Qo‘shimcha buyruqlar

- `git clone` — upstream repozitoriysidan mahalliy repozitoriy yaratish.
- `git pull`, `git fetch` — `origin` dan yangiliklarni olish.
- `git push` — umumiy repozitoriyga o‘zgarish yuborish.
- `git format-patch` — email orqali yuboriladigan patchlarni tayyorlash.
- `git send-email` — email dasturi patchni buzmasdan uni jo‘natish.
- `git request-pull` — upstream tortib olishi uchun o‘zgarishlar xulosasini yaratish.

### Misol: upstream’ni klonlash va unga o‘zgarish yuborish

```console
$ git clone git://git.kernel.org/pub/scm/.../torvalds/linux-2.6 my2.6
$ cd my2.6
$ git switch -c mine master
$ edit/compile/test; git commit -a -s
$ git format-patch master
$ git send-email --to="person <email@example.com>" 00*.patch
$ git switch master
$ git pull
$ git log -p ORIG_HEAD.. arch/i386 include/asm-i386
$ git ls-remote --heads http://git.kernel.org/.../jgarzik/libata-dev.git
$ git pull git://git.kernel.org/pub/.../jgarzik/libata-dev.git ALL
$ git reset --hard ORIG_HEAD
$ git gc
```

Buyruqlar izohi:

1. `master` asosida `mine` tarmog‘i yaratiladi.
2. Tahrirlash, kompilyatsiya, test va sign-off’li commit zaruricha takrorlanadi.
3. `master` ga nisbatan topic branch patchlari chiqariladi.
4. Patchlar email orqali yuboriladi.
5. Yangiliklarni ko‘rish uchun `master` ga qaytiladi.
6. Oddiy `pull` standart holatda `origin` dan oladi va joriy tarmoqqa merge qiladi.
7. Pull’dan so‘ng faqat qiziqtirgan kataloglardagi upstream o‘zgarishlari tekshiriladi.
8. Tashqi repozitoriydagi tarmoq nomlari ko‘riladi.
9. Muayyan repozitoriyning `ALL` tarmog‘i olinib, merge qilinadi.
10. `reset --hard ORIG_HEAD` oxirgi pull’ni bekor qiladi. **Bu commit qilinmagan o‘zgarishlarni ham yo‘qotishi mumkin.**
11. `gc` bekor qilingan pull’dan qolgan erishib bo‘lmaydigan obyektlarni keyinchalik tozalash va repozitoriyni optimallashtirishga yordam beradi.

### Misol: boshqa repozitoriyga push qilish

```console
satellite$ git clone mothership:frotz frotz
satellite$ cd frotz
satellite$ git config --get-regexp '^(remote|branch)\.'
remote.origin.url mothership:frotz
remote.origin.fetch refs/heads/*:refs/remotes/origin/*
branch.master.remote origin
branch.master.merge refs/heads/master

satellite$ git config remote.origin.push \
           +refs/heads/*:refs/remotes/satellite/*
satellite$ edit/compile/test/commit
satellite$ git push origin

mothership$ cd frotz
mothership$ git switch master
mothership$ git merge satellite/master
```

Bu misolda:

1. `mothership` kompyuteridagi `frotz` repozitoriysi `satellite` ga klonlanadi.
2. `clone` `remote.origin.*` va `branch.master.*` konfiguratsiyasini avtomatik yaratadi.
3. Push barcha mahalliy tarmoqlarni mothership’dagi `refs/remotes/satellite/*` kuzatuv tarmoqlariga joylash uchun sozlanadi.
4. `satellite` dagi ish push qilinadi. Bu bir tomonlama ulanishda zaxira nusxa usuli sifatida ham ishlashi mumkin.
5. `mothership` da `satellite/master` joriy `master` ga merge qilinadi.

> Ref spetsifikatsiyasidagi bosh `+` non-fast-forward yangilanishiga ruxsat beradi. Uni faqat tarixni majburan almashtirish oqibatini tushunsangiz ishlating.

### Misol: ma’lum tegdan tarmoq ajratish

```console
$ git switch -c private2.6.14 v2.6.14
$ edit/compile/test; git commit -a
$ git switch master
$ git cherry-pick v2.6.14..private2.6.14
```

`private2.6.14` taniqli, ammo biroz eski `v2.6.14` tegi asosida yaratiladi. `cherry-pick` uning barcha o‘zgarishlarini formal merge commitsiz `master` ga ko‘chiradi.

Uzunroq muqobil usul:

```console
$ git format-patch -k -m --stdout v2.6.14..private2.6.14 | git am -3 -k
```

Ishtirokchi `git request-pull` yoki GitHub’dagi pull request kabi mexanizmlar orqali upstream’ga hissasi haqida xabar berishi ham mumkin.

## INTEGRATOR — INTEGRATOR

Integrator jamoada boshqalar qilgan o‘zgarishlarni qabul qiladi, tekshiradi, birlashtiradi va natijani e’lon qiladi. Subsystem rahbari ko‘pincha ham ishtirokchi, ham integrator rolini bajaradi.

### Qo‘shimcha buyruqlar

- `git am` — contributorlar email orqali yuborgan patchlarni qo‘llash.
- `git pull` — ishonchli maintainer tarmoqlarini birlashtirish.
- `git format-patch` — contributorga muqobil yechim taklifini tayyorlash.
- `git revert` — noto‘g‘ri commitni yangi commit bilan xavfsiz bekor qilish.
- `git push` — eng yangi natijani e’lon qilish.

### Integratorning odatiy Git kuni

```console
$ git status
$ git branch --no-merged master
$ mailx
& s 2 3 4 5 ./+to-apply
& s 7 8 ./+hold-linus
& q
$ git switch -c topic/one master
$ git am -3 -i -s ./+to-apply
$ compile/test
$ git switch -c hold/linus && git am -3 -i -s ./+hold-linus
$ git switch topic/one && git rebase master
$ git switch -C seen next
$ git merge topic/one topic/two && git merge hold/linus
$ git switch maint
$ git cherry-pick master~4
$ compile/test
$ git tag -s -m "GIT 0.99.9x" v0.99.9x
$ git fetch ko && for branch in master maint next seen
  do
      git show-branch ko/$branch $branch
  done
$ git push --follow-tags ko
```

Jarayon izohi:

1. `status` tugallanmagan ish bor-yo‘qligini ko‘rsatadi.
2. `branch --no-merged master` hali `master` ga qo‘shilmagan tarmoqlarni ko‘rsatadi. Xuddi shu usul `maint`, `next`, `seen` uchun ishlaydi.
3. Email patchlari qo‘llanadigan va hali tayyor bo‘lmagan guruhlarga ajratiladi.
4. `am -3 -i -s` patchlarni uch tomonlama fallback, interaktiv tasdiq va sign-off bilan qo‘llaydi.
5. Zarur topic branch yaratilib, boshqa patchlar ham qo‘llanadi.
6. Hali umumiy tarixga chiqarilmagan ichki topic branch `master` ustiga rebase qilinadi.
7. `seen` tarmog‘i har safar `next` dan qayta boshlanadi.
8. Hali tayyorlanayotgan topic branchlar sinov uchun birlashtiriladi.
9. Muhim tuzatish eski qo‘llab-quvvatlash tarmog‘iga backport qilinadi.
10. Imzolangan reliz tegi yaratiladi.
11. `ko` dagi public tarmoqlar olinadi.
12. Mahalliy public tarmoqlar tasodifan ilgari e’lon qilingan tarixdan orqaga qaytmagani tekshiriladi.
13. Yangi tarix va unga ishora qiluvchi teglar push qilinadi.

Misoldagi `ko` Git maintainer repozitoriysiga qisqa nomdir:

```ini
[remote "ko"]
        url = kernel.org:/pub/scm/git/git.git
        fetch = refs/heads/*:refs/remotes/ko/*
        push = refs/heads/master
        push = refs/heads/next
        push = +refs/heads/seen
        push = refs/heads/maint
```

`seen` tajribaviy tarmoq bo‘lgani uchun uning push refspec’i `+` bilan non-fast-forward yangilanishiga ruxsat beradi.

## REPOSITORY ADMINISTRATION — REPOZITORIY MA’MURIYATI

Repozitoriy administratori dasturchilarning kirishini yaratadi va xizmatlarni yuritadi.

### Server vositalari

- `git daemon` — repozitoriydan anonim yuklab olishga ruxsat berish.
- `git shell` — umumiy markaziy repozitoriy foydalanuvchilari uchun cheklangan login shell.
- `git http-backend` — fetch va push’ni qo‘llovchi server tomonidagi Smart HTTP.
- `gitweb` — Git repozitoriylari uchun veb-interfeys; `git instaweb` bilan tez ishga tushirish mumkin.

Bundan tashqari Gitolite, Gerrit Code Review va cgit kabi keng tarqalgan hosting, ko‘rish va review yechimlari mavjud.

### Misol: `inetd` orqali `git daemon`

`/etc/services` da:

```text
git             9418/tcp                # Git Version Control System
```

`/etc/inetd.conf` da (amalda bitta satrda yoziladi):

```text
git stream tcp nowait nobody \
  /usr/bin/git-daemon git-daemon --inetd --export-all /pub/scm
```

### Misol: `xinetd` orqali `git daemon`

```text
service git
{
        disable = no
        type            = UNLISTED
        port            = 9418
        socket_type     = stream
        wait            = no
        user            = nobody
        server          = /usr/bin/git-daemon
        server_args     = --inetd --export-all --base-path=/pub/scm
        log_on_failure  += USERID
}
```

Bu Fedora misoli; boshqa tizimlarda `xinetd(8)` sozlamasi farq qilishi mumkin.

### Misol: `git-shell` orqali faqat push/pull ruxsati

```text
alice:x:1000:1000::/home/alice:/usr/bin/git-shell
bob:x:1001:1001::/home/bob:/usr/bin/git-shell
cindy:x:1002:1002::/home/cindy:/usr/bin/git-shell
david:x:1003:1003::/home/david:/usr/bin/git-shell
```

`/etc/shells` da:

```text
/usr/bin/git-shell
```

`git-shell` odatiy shell buyruqlarini cheklab, Git push/pull xizmatlariga ruxsat beradi. Foydalanuvchilar mashinaga SSH orqali kira olishi kerak. Ko‘p distributivlarda login shell `/etc/shells` ichida ro‘yxatlangan bo‘lishi shart.

### Misol: CVS uslubidagi umumiy repozitoriy

```text
git:x:9418:alice,bob,cindy,david
```

Dasturchilar bir xil `git` guruhiga qo‘shiladi va shared repozitoriy guruh uchun yoziladigan qilinadi. `update` hook orqali tarmoq siyosati belgilanadi. Masalan:

```text
refs/heads/master       alice\|cindy
refs/heads/doc-update   bob
refs/tags/v[0-9]*       david
```

Bu siyosatda:

- `master` ga Alice va Cindy push qila oladi;
- `doc-update` ga faqat Bob push qila oladi;
- versiya teglarini faqat reliz menejeri David yarata oladi.

Zamonaviy boshlang‘ich shared repozitoriy yaratish buyrug‘i:

```console
$ git init --bare --shared /home/devo.git
```

## GIT

`git(1)` buyruqlar to‘plamining bir qismi.

## NOTES — IZOH

1. Update hook namunasi: `git-htmldocs/howto/update-hook-example.html`
