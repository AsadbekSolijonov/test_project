# Git FAQ — o‘zbekcha tarjima

> Manba: `git help faq` (Git 2.50.1).

## NAME — NOMI

`gitfaq` — Git’dan foydalanish bo‘yicha tez-tez so‘raladigan savollar.

## CONFIGURATION — SOZLASH

### Ism va email qanday sozlanadi?

```console
$ git config --global user.name "Your Name"
$ git config --global user.email "you@example.com"
```

Bitta repozitoriy uchun `--global` ni olib tashlang. Qiymat qayerdan kelganini ko‘rish:

```console
$ git config --show-origin --get-regexp '^user\.'
```

### Tahrirlagichni qanday tanlayman?

```console
$ git config --global core.editor "vim"
$ git config --global sequence.editor "vim"
```

`GIT_EDITOR`, `VISUAL` va `EDITOR` muhit o‘zgaruvchilari ham ta’sir qiladi.

### Standart branch nomini qanday tanlayman?

```console
$ git config --global init.defaultBranch main
```

### Buyruq uchun alias qanday yaratiladi?

```console
$ git config --global alias.lg 'log --graph --oneline --decorate --all'
```

Keyin `git lg` ishlaydi. `!` bilan boshlangan alias shell buyrug‘ini bajaradi; ishonchsiz konfiguratsiyada bunday alias xavfli bo‘lishi mumkin.

## CREDENTIALS — KIRISH MA’LUMOTLARI

### Git nega har safar parol so‘raydi?

Mos credential helper sozlang:

```console
$ git config --global credential.helper cache
```

Operatsion tizim keychain’i yoki Git Credential Manager kabi xavfsiz helper afzal. `credential.helper store` parol/tokenni ochiq matnda saqlaydi.

### HTTPS paroli ishlamayapti

Ko‘p hosting xizmatlari akkaunt paroli o‘rniga personal access token talab qiladi. Token huquqlarini minimal qiling va uni commit, URL yoki logga yozmang.

### SSH va HTTPS credentiallari bir xilmi?

Yo‘q. HTTPS Git credential helper’lardan, SSH esa SSH agent, kalit fayllari va `~/.ssh/config` dan foydalanadi.

## TRANSFERS — MA’LUMOT UZATISH

### `fetch` va `pull` farqi nima?

`git fetch` faqat masofaviy ma’lumot va remote-tracking ref’larni yangilaydi. `git pull` avval fetch, keyin sozlamaga qarab merge yoki rebase qiladi.

```console
$ git fetch origin
$ git log HEAD..origin/main
$ git merge origin/main
```

### Bitta branchni qanday klonlayman?

```console
$ git clone --single-branch --branch main <url>
```

Tarix hajmini kamaytirish uchun shallow yoki partial clone:

```console
$ git clone --depth=1 <url>
$ git clone --filter=blob:none <url>
```

Shallow clone ayrim tarix amallarini cheklaydi.

### Push non-fast-forward sababli rad etildi

Remote’dagi yangi commitlarni oling va o‘z ishingizni birlashtiring:

```console
$ git fetch origin
$ git rebase origin/main
$ git push origin main
```

Tarixni ataylab almashtirish zarur bo‘lsa, oddiy `--force` o‘rniga xavfsizroq:

```console
$ git push --force-with-lease origin main
```

Public branchda buni faqat jamoa siyosati ruxsat etsa bajaring.

## COMMON ISSUES — KENG TARQALGAN MUAMMOLAR

### Oxirgi commitni qanday tuzataman?

```console
$ git add <files>
$ git commit --amend
```

Bu commit ID’sini o‘zgartiradi; push qilingan commitni amend qilish tarixni qayta yozadi.

### Commit qilinmagan o‘zgarishni qanday qaytaraman?

```console
$ git restore <file>
```

Staging’dan chiqarish:

```console
$ git restore --staged <file>
```

Ikkalasi turli ish qiladi. Qaytarilgan commit qilinmagan tarkib odatda Git orqali tiklanmaydi.

### O‘chirilgan commitni qanday topaman?

```console
$ git reflog
$ git show <old-id>
$ git branch recovery <old-id>
```

Reflog faqat mahalliy va vaqtinchalik saqlanadi; garbage collection’dan oldin tiklang.

### Fayl nomining registri nega o‘zgarmadi?

Registrga befarq filesystem’da oraliq nom ishlating:

```console
$ git mv name.tmp intermediate.tmp
$ git mv intermediate.tmp Name.tmp
```

### Bo‘sh katalogni qanday kuzataman?

Git kataloglarni emas, fayl tarkibini kuzatadi. Katalogda `.gitkeep` kabi placeholder fayl yarating (nom Git uchun maxsus emas).

### Katta binary fayllar uchun nima qilaman?

Git LFS yoki artifact storage’dan foydalanishni ko‘rib chiqing. Oddiy Git tarixiga kiritilgan katta obyekt commit o‘chirilganda ham tarixda qolishi mumkin.

## MERGING AND REBASING — MERGE VA REBASE

### Merge va rebase farqi nima?

Merge ikki tarixni yangi merge commit bilan bog‘laydi va asl commitlarni saqlaydi. Rebase commitlarni yangi asos ustida qayta yaratib, chiziqli tarix beradi, ammo commit ID’larini o‘zgartiradi.

```console
$ git merge topic
$ git rebase main topic
```

Public tarixni rebase qilmaslik umumiy qoidadir.

### Konfliktni qanday hal qilaman?

```console
$ git status
# fayllarni tahrirlang
$ git add <resolved-files>
$ git merge --continue
# yoki rebase vaqtida:
$ git rebase --continue
```

Bekor qilish:

```console
$ git merge --abort
$ git rebase --abort
```

## HOOKS — HOOKLAR

Hooklar `.git/hooks/` ichidagi executable dasturlardir. Client-side hooklar klon bilan avtomatik ko‘chmaydi va xavfsizlik chegarasi emas. Majburiy siyosatni server-side `pre-receive` yoki `update` hooklarda tekshiring.

Hook ishlamasa:

- fayl executable ekanini;
- shebang va interpreter yo‘li to‘g‘riligini;
- hook nomida `.sample` qo‘shimchasi yo‘qligini;
- `core.hooksPath` boshqa katalogni ko‘rsatmayotganini tekshiring.

## CROSS-PLATFORM ISSUES — PLATFORMALARARO MUAMMOLAR

### Satr oxirlari

Git LF va CRLF o‘rtasidagi aylantirishni `core.autocrlf` va `.gitattributes` orqali boshqaradi. Loyiha siyosatini repozitoriydagi `.gitattributes` da aniq belgilang:

```gitattributes
* text=auto
*.sh text eol=lf
*.bat text eol=crlf
*.png binary
```

### Fayl rejimi

Ba’zi filesystem’lar executable bitni ishonchli saqlamaydi. `core.fileMode` tekshiruvni boshqaradi. Repozitoriydagi bitni aniq o‘zgartirish:

```console
$ git update-index --chmod=+x script.sh
```

### Fayl nomlari

Windows, macOS va Linux registr sezgirligi, Unicode normalizatsiyasi va taqiqlangan nomlar bo‘yicha farq qiladi. Platformalararo loyiha nomlarni faqat registr bilan farqlamasligi va portable belgilar ishlatishi kerak.

## GIT

`git(1)` buyruqlar to‘plamining bir qismi.
