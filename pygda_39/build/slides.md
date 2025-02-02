title: Mój wonsz wygląda jak Twój wonsz
class: animation-fade
layout: true

---

class: impact

# {{title}}

---

class: impact

## Czyli rzecz o statycznej analizie i narzędziach do formatowania kodu

### Albo: Jak wykorzystać narzędzia by pisać lepszy kod?

---

class: impact

## Albo: Jak wykorzystać narzędzia by pisać lepszy kod?

---

# Piotr Gaczkowski

![DoomHammer](doomhammer.jpg)

[https://github.com/DoomHammer](https://github.com/DoomHammer)

[https://doomhammer.info](https://doomhammer.info)

---

class: impact

# Jak wygląda Twój proces wydawniczy?

---

# Jak wygląda Twój proces wydawniczy?

--

* Rsync / Ftp / Scp

--

* Git

--

* `setup.py` / `make` / ...

--

* Ansible

--

* Docker

--

* Jenkins / Travis / poważne CI/CD

---

class: impact

# Pośmiejemy się?

---

background-image: url(kompilator.jpg)
background-size: 100% 90%

???

![W C++ o błędach mówi nam kompilator, w PHP -- klient](kompilator.jpg)

---

class: impact

# Skąd wiesz że Twój kod będzie działał na produkcji?

---

# Skąd wiesz że Twój kod będzie działał na produkcji?

* Dlaczego miałby nie działać?

--

* U mnie działa

--

* Piszemy tylko dobry kod

--

* Mam przecież testy

---

class: impact

# Statyczna analiza kodu

---

# Czym jest statyczna analiza kodu?

--

- szukanie błędów w kodzie źródłowym bez uruchamiania go

--

- "debuggowanie na sucho"

--

```python
print("Hello, world')
```

---

# Narzędzia do statycznej analizy

--

## [pylint](https://www.pylint.org/)
### https://www.pylint.org/

--

* wyszukiwanie błędów 

--

* sprawdzanie złożoności

--

* gratis: diagramy UML

--

* integruje się z edytorem (lub IDE)

---

# Narzędzia do statycznej analizy

## [bandit](https://github.com/PyCQA/bandit)
### https://github.com/PyCQA/bandit

wyszukuje problemy związane z bezpieczeństwem w kodzie Python

--

## [PyT](https://github.com/python-security/pyt) (PyTaint)
### https://github.com/python-security/pyt

j.w.

---

# Narzędzia do statycznej analizy

## [vulture](https://github.com/jendrikseipp/vulture)
### https://github.com/jendrikseipp/vulture

żywi się padliną (martwe klasy, funkcje, nieużywane zmienne, ...)

---

class: impact

# Jak nie pisać kodu?

---

```python
#!/usr/bin/env python
# -*- coding: UTF-8 -*-

import re
import os
import xlwt

dirs = ['0', '1', '2', '3', '4', '5', '6', '7', '8', '9']
for diro in dirs:
```

--

```python
  if not os.path.exists(diro):
    continue
  files = os.listdir(diro)
  filety = map(lambda x: diro + '/' + x, \
    filter(lambda x: re.match('^.*\.csv$', x), files))
  filety.sort()

  bunsen = dict()
  oswald = dict()
  ref = dict()
```

---

```python
  for filet in filety:
    print filet
    with open(filet) as f:
      i_hash = f.readline().split(':')[0]
      if not i_hash in bunsen:
        bunsen[i_hash] = dict()
      line = f.readline().strip()
      i_stage = line.split(':')[0]
      if i_stage == 'Stage1':
        inputs = dict()
        i = 0
        state = 0
```

---

background-image: url(snare_drum.gif)
background-size: 100% 90%

---

```python
        while line:
          if state==1:
            i_first = line.split(':')[0]
            state = 2
          elif state==2:
            i_left = line.split(':')[0]
            state = 3
          elif state==3:
            i_right = line.split(':')[0]
            state = 4
          elif state==4:
            i_value = line.split(':')[1]
            state = 1
            if not i_stage in bunsen[i_hash]:
              bunsen[i_hash][i_stage] = list()
            if i_first in inputs:
              i_first = "Input " + str(inputs[i_first])
            bunsen[i_hash][i_stage].append([i_first, i_left, i_right, i_value])
```

---

class: impact

# PyLint nie był zachwycony...

---

class: middle, center

<video width="560" height="420" autoplay>
    <source src="202698.cast.mp4" type="video/mp4">
</video>

---

class: impact

# Statyczne typowanie w Pythonie?

---

# Po co nam statyczne typowanie w dynamicznym języku?

--

- prototyp to nie produkt

--

- dokumentacja -- statyczne typy dokumentują interfejs

--

- test na inteligencję

---

# Narzędzia do statycznego typowania

--

## `typing` od Python 3.5

- opcjonalne adnotacje funkcji

--

```python
def fib(n: int) -> Iterator[int]:
    a, b = 0, 1
    while a < n:
        yield a
        a, b = b, a+b
```

--

## [mypy](http://www.mypy-lang.org/)
### http://www.mypy-lang.org/

- sprawdzanie poprawności typów przy wołaniach funkcji

---

# Narzędzia do statycznego typowania

## [PyAnnotate](https://github.com/dropbox/pyannotate)

### https://github.com/dropbox/pyannotate

- automatycznie domniemuje typy na podstawie przebiegu programu

--

(np. wykonania testów)

---

# Narzędzia do statycznego typowania

```
[
    {
        "path": "gcd.py",
        "line": 1,
        "func_name": "main",
        "type_comments": [
            "() -> None"
        ],
        "samples": 1
    },
    {
        "path": "gcd.py",
        "line": 5,
        "func_name": "gcd",
        "type_comments": [
            "(int, int) -> int"
        ],
        "samples": 2
    }
]
```

---

# Narzędzia do statycznego typowania

## [PyAnnotate](https://github.com/dropbox/pyannotate)

```
Refactored gcd.py
--- gcd.py        (original)
+++ gcd.py        (refactored)
@@ -1,8 +1,10 @@
 def main():
+    # type: () -> None
     print(gcd(15, 10))
     print(gcd(45, 12))
 
 def gcd(a, b):
+    # type: (int, int) -> int
     while b:
         a, b = b, a%b
     return a
Files that were modified:
gcd.py
```

---

class: impact

# Automatyczne formatowanie kodu

---

background-image: url(bruces.jpg)

???
- Skecz o Bruce'ach

---

# Automatyczne formatowanie kodu

## [yapf](https://github.com/google/yapf)

### https://github.com/google/yapf

--

* zrobiony przez inżynierów Google

--

* wymusza wspólny styl (czy głupi styl jest lepszy niż żaden?)

--

```

    # yapf: disable
    naszBrzydkiKod   =wywolaj_mnie(   'A', 'B',   'C'  )
    # yapf: enable
```

--

* a co z długością wiersza?

---

# Automatyczne formatowanie kodu

## [yapf](https://github.com/google/yapf)

* ładna stylówka:

```ini
[style]
based_on_style = pep8
column_limit = 120
```

---

# Dlaczego warto stosować jednolite formatowanie?

--

* mniej dyskusji

--

* ładniejsze diffy

--

* Zen Pythona a Zen (Ad maiorem Dei gloriam)

---

# Dlaczego warto stosować jednolite formatowanie?

<img src="bus.jpg" width="600">

---

class: impact

# Automatyzacja formatowania i analizy

---

# Automatyzacja formatowania i analizy

## Dla siebie

### Półautomat

```sh
find . -maxdepth 1 -name '*.py' |\
  xargs yapf -i --style='{based_on_style: pep8, column_limit: 120}' \
&& find . -maxdepth 1 -name '*.py' |\
  xargs pylint --max-line-length=120
```

---

# Automatyzacja formatowania i analizy

## Dla siebie

### Githooks

[https://github.com/icefox/git-hooks](https://github.com/icefox/git-hooks)

--

* napisany w czystym `bash`

--

* można zainstalować przy pomocy `brew`

--

* pozwala na elegancką integrację z `make`

---

# Automatyzacja formatowania i analizy

## Dla innych

--

- `Makefile`

--

- recenzja kodu

--

- CI/CD

---

class: impact

## Pylint, MyPy i yapf sprawiają że mój wonsz wygląda jak Twój wonsz

![](wonsz.jpg)

---

class: impact

# Czego automat za nas nie zrobi?

---

# Czego automat za nas nie zrobi?

* Semantyczne nazwy zmiennych

--

* Funkcje o pojedynczym przeznaczeniu

--

* Rozsądne interfejsy

--

* Testy, atrapy, zaślepki

---

class: impact

## Dlaczego warto stosować analizę statyczną i automatyczne formatowanie kodu?
---

background-image: url(ball.gif)
background-size: 80% 70%

---

class: impact

# Dziękuję!

![DoomHammer](doomhammer.jpg)

### [https://github.com/DoomHammer](https://github.com/DoomHammer)

### [https://doomhammer.info](https://doomhammer.info)
