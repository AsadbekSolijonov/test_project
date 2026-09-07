# Git tutorial, 2-qism — o‘zbekcha tarjima

> Manba: `git help tutorial-2` (Git 2.50.1). Buyruqlar va ularning namunaviy natijalari asl holida saqlangan.

## NAME — NOMI

`gittutorial-2` — Git bilan tanishtiruvchi qo‘llanmaning ikkinchi qismi.

## SYNOPSIS — QISQACHA ISHLATILISHI

```console
git *
```

## DESCRIPTION — TAVSIF

Ushbu qo‘llanmani o‘qishdan oldin `gittutorial(7)` bilan ishlab chiqishingiz kerak.

Bu qo‘llanmaning maqsadi Git arxitekturasining ikkita asosiy qismini — **obyektlar ma’lumotlar bazasi** va **indeks fayli**ni — tanishtirish hamda Git hujjatlarining qolgan qismini tushunish uchun zarur bilimlarni berishdir.

## THE GIT OBJECT DATABASE — GIT OBYEKTLAR MA’LUMOTLAR BAZASI

Yangi loyiha boshlaymiz va kichik tarix yaratamiz:

```console
$ mkdir test-project
$ cd test-project
$ git init
Initialized empty Git repository in .git/
$ echo 'hello world' > file.txt
$ git add .
$ git commit -a -m "initial commit"
[master (root-commit) 54196cc] initial commit
 1 file changed, 1 insertion(+)
 create mode 100644 file.txt
$ echo 'hello world!' >file.txt
$ git commit -a -m "add emphasis"
[master c4d59f3] add emphasis
 1 file changed, 1 insertion(+), 1 deletion(-)
```

Git commit javobida chiqargan 7 ta o‘n oltilik raqam nimani anglatadi?

Birinchi qismda commitlarning shunday nomlari borligini ko‘rdik. Git tarixidagi har bir obyekt 40 belgili o‘n oltilik nom ostida saqlanadi. Bu nom obyekt tarkibining SHA-1 xeshidir. Bu, boshqa narsalar qatori, Git bir xil ma’lumotni ikki marta saqlamasligini (bir xil ma’lumot bir xil SHA-1 nomini oladi) va Git obyekti tarkibi hech qachon o‘zgarmasligini (aks holda nomi ham o‘zgaradi) ta’minlaydi. Bu yerdagi 7 belgili satrlar 40 belgili nomlarning qisqartmasidir. Qisqartma yagona ma’noli bo‘lsa, to‘liq nom ishlatiladigan barcha joyda undan foydalanish mumkin.

Siz yaratgan commit xeshi namunadagidan farq qilishi tabiiy: commit obyekti yaratilgan vaqtni va commit qilgan shaxs nomini ham saqlaydi.

Muayyan obyekt haqida Git’dan `cat-file` orqali so‘rash mumkin. Namunadagi 40 belgini ko‘chirmang — o‘zingiz hosil qilgan qiymatdan foydalaning. Barcha 40 belgini yozmaslik uchun uni yetarlicha qisqartirish mumkin:

```console
$ git cat-file -t 54196cc2
commit
$ git cat-file commit 54196cc2
tree 92b8b694ffb1675e5975148e1121810081dbdffe
author J. Bruce Fields <bfields@puzzle.fieldses.org> 1143414668 -0500
committer J. Bruce Fields <bfields@puzzle.fieldses.org> 1143414668 -0500

initial commit
```

**Tree** bir yoki bir nechta **blob** obyektiga murojaat qilishi mumkin; har bir blob bitta faylga mos keladi. Tree boshqa tree obyektlariga ham murojaat qilib, kataloglar iyerarxiyasini yaratadi. Istalgan tree tarkibini `ls-tree` bilan ko‘rish mumkin:

```console
$ git ls-tree 92b8b694
100644 blob 3b18e512dba79e4c8300dd08aeb37f8e728b8dad    file.txt
```

Bu tree ichida bitta fayl bor. SHA-1 xeshi shu fayl ma’lumotiga ishora qiladi:

```console
$ git cat-file -t 3b18e512
blob
$ git cat-file blob 3b18e512
hello world
```

Blob — shunchaki fayl ma’lumoti. Bu eski tarkibdir: dastlabki commit murojaat qilgan tree o‘sha vaqtdagi katalog holatining suratini saqlaydi.

Barcha obyektlar Git katalogida SHA-1 nomlari ostida saqlanadi:

```console
$ find .git/objects/
.git/objects/
.git/objects/pack
.git/objects/info
.git/objects/3b
.git/objects/3b/18e512dba79e4c8300dd08aeb37f8e728b8dad
.git/objects/92
.git/objects/92/b8b694ffb1675e5975148e1121810081dbdffe
.git/objects/54
.git/objects/54/196cc2703dc165cbd373a65a4dcf22d50ae7f7
.git/objects/a0
.git/objects/a0/423896973644771497bdc03eb99d5281615b51
.git/objects/d0
.git/objects/d0/492b368b66bdabf2ac1fd8c92b39d3db916e59
.git/objects/c4
.git/objects/c4/d59f390b9cfd4318117afde11d601c1085f241
```

Bu fayllar siqilgan ma’lumotdan hamda uning uzunligi va turini bildiruvchi sarlavhadan iborat. Tur `blob`, `tree`, `commit` yoki `tag` bo‘ladi.

Eng oson topiladigan commit — `HEAD`. Uni `.git/HEAD` orqali aniqlaymiz:

```console
$ cat .git/HEAD
ref: refs/heads/master
```

Bu qaysi tarmoqda ekanimizni ko‘rsatadi va `.git` ichidagi fayl nomini beradi. O‘sha fayl commit obyektining SHA-1 nomini saqlaydi:

```console
$ cat .git/refs/heads/master
c4d59f390b9cfd4318117afde11d601c1085f241
$ git cat-file -t c4d59f39
commit
$ git cat-file commit c4d59f39
tree d0492b368b66bdabf2ac1fd8c92b39d3db916e59
parent 54196cc2703dc165cbd373a65a4dcf22d50ae7f7
author J. Bruce Fields <bfields@puzzle.fieldses.org> 1143418702 -0500
committer J. Bruce Fields <bfields@puzzle.fieldses.org> 1143418702 -0500

add emphasis
```

Bu yerdagi `tree` yangi katalog holatiga murojaat qiladi:

```console
$ git ls-tree d0492b36
100644 blob a0423896973644771497bdc03eb99d5281615b51    file.txt
$ git cat-file blob a0423896
hello world!
```

`parent` esa oldingi commitga murojaat qiladi:

```console
$ git cat-file commit 54196cc2
tree 92b8b694ffb1675e5975148e1121810081dbdffe
author J. Bruce Fields <bfields@puzzle.fieldses.org> 1143414668 -0500
committer J. Bruce Fields <bfields@puzzle.fieldses.org> 1143414668 -0500

initial commit
```

Tree — avval ko‘rganimiz. Bu commit noodatiy, chunki uning parent’i yo‘q.

Ko‘p commitlarda bitta parent bo‘ladi, lekin bir nechta parent ham keng tarqalgan. Bunday commit merge’ni ifodalaydi; parent murojaatlari birlashtirilgan tarmoqlarning uchlariga ishora qiladi.

Blob, tree va commitdan tashqari qolgan yagona obyekt turi — `tag`. U bu yerda ko‘rib chiqilmaydi; `git-tag(1)` ga qarang.

Git loyiha tarixini obyektlar bazasida quyidagicha ifodalaydi:

- **Commit obyektlari** tarixning muayyan nuqtasidagi katalog daraxti suratini ifodalovchi tree obyektlariga va commitlar loyiha tarixida qanday bog‘langanini ko‘rsatuvchi parent commitlarga murojaat qiladi.

- **Tree obyektlari** bitta katalog holatini ifodalaydi: fayl ma’lumotini saqlovchi bloblarni va quyi katalog ma’lumotini saqlovchi tree obyektlarini nomlar bilan bog‘laydi.

- **Blob obyektlari** hech qanday qo‘shimcha tuzilmasiz fayl ma’lumotini saqlaydi.

- Har bir tarmoq uchidagi commitga murojaatlar `.git/refs/heads/` ostidagi fayllarda saqlanadi.

- Joriy tarmoq nomi `.git/HEAD` da saqlanadi.

Ko‘p buyruqlar argument sifatida tree qabul qiladi. Tree’ga uning SHA-1 nomi, shu tree’ga murojaat qiluvchi commit nomi yoki uchi shu tree’ga murojaat qiluvchi tarmoq nomi orqali murojaat qilish mumkin. Bunday buyruqlarning aksariyati shu nomlarning istalganini qabul qiladi.

Buyruq sintaksislarida bunday argument ba’zan **tree-ish** deb ataladi.

## THE INDEX FILE — INDEKS FAYLI

Commit yaratishda ishlatgan asosiy vositamiz `git commit -a` bo‘lib, u ishchi daraxtdagi barcha o‘zgarishlarni commitga kiritadi. Faqat ayrim fayllar yoki fayllardagi ayrim o‘zgarishlarni commit qilmoqchi bo‘lsak-chi?

Commit ichki tomondan qanday yaratilishini ko‘rsak, moslashuvchanroq usullar borligini bilamiz.

Test loyihamizdagi `file.txt` ni yana o‘zgartiramiz:

```console
$ echo "hello world, again" >>file.txt
```

Bu safar darhol commit qilish o‘rniga oraliq qadam bajaramiz va jarayonni kuzatish uchun farqlarni so‘raymiz:

```console
$ git diff
--- a/file.txt
+++ b/file.txt
@@ -1 +1,2 @@
 hello world!
+hello world, again
$ git add file.txt
$ git diff
```

Oxirgi diff bo‘sh, ammo yangi commit yaratilmagan va `HEAD` hali yangi satrni saqlamaydi:

```console
$ git diff HEAD
diff --git a/file.txt b/file.txt
index a042389..513feba 100644
--- a/file.txt
+++ b/file.txt
@@ -1 +1,2 @@
 hello world!
+hello world, again
```

Demak, oddiy `git diff` `HEAD` bilan emas, indeks fayli bilan solishtiradi. Indeks `.git/index` da ikkilik formatda saqlanadi; tarkibini `ls-files` bilan ko‘ramiz:

```console
$ git ls-files --stage
100644 513feba2e53ebbd2532419ded848ba19de88ba00 0       file.txt
$ git cat-file -t 513feba2
blob
$ git cat-file blob 513feba2
hello world!
hello world, again
```

`git add` yangi blobni saqladi va unga murojaatni indeksga qo‘ydi. Faylni yana o‘zgartirsak, yangi o‘zgarish `git diff` da ko‘rinadi:

```console
$ echo 'again?' >>file.txt
$ git diff
index 513feba..ba3da7b 100644
--- a/file.txt
+++ b/file.txt
@@ -1,2 +1,3 @@
 hello world!
 hello world, again
+again?
```

Kerakli argumentlar bilan `git diff` ishchi katalog va oxirgi commit orasidagi yoki indeks va oxirgi commit orasidagi farqni ko‘rsatadi:

```console
$ git diff HEAD
diff --git a/file.txt b/file.txt
index a042389..ba3da7b 100644
--- a/file.txt
+++ b/file.txt
@@ -1 +1,3 @@
 hello world!
+hello world, again
+again?
$ git diff --cached
diff --git a/file.txt b/file.txt
index a042389..513feba 100644
--- a/file.txt
+++ b/file.txt
@@ -1 +1,2 @@
 hello world!
+hello world, again
```

Istalgan payt `git commit` ni `-a` siz bajarish mumkin. Yaratilgan commit ishchi daraxtdagi qo‘shimcha o‘zgarishni emas, faqat indeksdagi o‘zgarishlarni saqlaganini tekshiramiz:

```console
$ git commit -m "repeat"
$ git diff HEAD
diff --git a/file.txt b/file.txt
index 513feba..ba3da7b 100644
--- a/file.txt
+++ b/file.txt
@@ -1,2 +1,3 @@
 hello world!
 hello world, again
+again?
```

Standart holatda `git commit` commitni ishchi daraxtdan emas, indeksdan yaratadi. `-a` parametri avval indeksni ishchi daraxtdagi barcha kuzatilayotgan o‘zgarishlar bilan yangilashni aytadi.

`git add` indeksga qanday ta’sir qilishini yana ko‘ramiz:

```console
$ echo "goodbye, world" >closing.txt
$ git add closing.txt
$ git ls-files --stage
100644 8b9743b20d4b15be3955fc8d5cd2b09cd2336138 0       closing.txt
100644 513feba2e53ebbd2532419ded848ba19de88ba00 0       file.txt
```

`git add` indeksga bitta yozuv qo‘shdi. Yangi yozuv faylning joriy tarkibiga murojaat qiladi:

```console
$ git cat-file blob 8b9743b2
goodbye, world
```

`status` vaziyatning tezkor xulosasini beradi:

```console
$ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)

        new file:   closing.txt

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)

        modified:   file.txt
```

`closing.txt` ning joriy holati indeksda keshlanganligi uchun u **Changes to be committed** ostida turadi. `file.txt` ning ishchi katalogdagi o‘zgarishlari indeksda aks etmagani uchun u commitga tayyorlanmagan o‘zgarish sifatida belgilanadi. Hozir `git commit` bajarilsa, `closing.txt` yangi tarkibi bilan qo‘shiladi, ammo `file.txt` o‘zgarmaydi.

Oddiy `git diff` `file.txt` o‘zgarishlarini ko‘rsatadi, ammo `closing.txt` qo‘shilganini ko‘rsatmaydi, chunki `closing.txt` ning indeksdagi versiyasi ishchi katalogdagi versiyasi bilan bir xil.

Indeks yangi commitlar uchun staging maydoni bo‘lishdan tashqari, tarmoq checkout qilinganda obyektlar bazasidan to‘ldiriladi va merge jarayonidagi daraxtlarni saqlashda ishlatiladi. Batafsil ma’lumot uchun `gitcore-tutorial(7)` va tegishli man sahifalariga qarang.

## WHAT NEXT? — KEYIN NIMA?

Endi istalgan Git buyrug‘ining man sahifasini o‘qish uchun zarur asosiy bilimlarga egasiz. Boshlash uchun `giteveryday(7)` da tilga olingan buyruqlar yaxshi tanlov. Notanish atamalarni `gitglossary(7)` dan topishingiz mumkin.

*The Git User’s Manual* Git haqida yanada kengroq kirish beradi.

`gitcvs-migration(7)` CVS repozitoriysini Git’ga import qilishni va Git’dan CVS’ga o‘xshash usulda foydalanishni tushuntiradi.

Git’dan foydalanishning qiziqarli misollari uchun `howtos` ga qarang.

Git dasturchilari uchun `gitcore-tutorial(7)` yangi commit yaratish kabi jarayonlarda qatnashuvchi quyi darajadagi Git mexanizmlarini batafsil tushuntiradi.

## SEE ALSO — SHUNINGDEK QARANG

`gittutorial(7)`, `gitcvs-migration(7)`, `gitcore-tutorial(7)`, `gitglossary(7)`, `git-help(1)`, `giteveryday(7)`, *The Git User’s Manual*.

## GIT

`git(1)` buyruqlar to‘plamining bir qismi.

## NOTES — IZOH

1. *Git User’s Manual*: `git-htmldocs/user-manual.html`
2. *howtos*: `git-htmldocs/howto-index.html`
