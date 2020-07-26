---
title: Publikacja stron wygenerowanych w 11ty na github pages
description: Opis jak zdeployować stronę na github pages
date: 2020-07-19
tags:
    - 11ty
    - gh-pages
    - notes
layout: layouts/post.njk
---

Github pages są super, niestety w dokumentacji i w poradnikach związanych z 11ty mówi się głównie o publikacji na netlify, ew. s3 albo innym podobnym rozwiązaniu. W przypadku gh-pages nie natrafiłem na rozwiązania inne niż opierające się o githubowe _actions_.

A przecież jest jeszcze inne, w moim przekonaniu prostsze rozwiązanie - **git push** bezpośrednio do repozytorium służącego do publikacji naszych stron. Pomysł jak to zrobić znalazłem w dokumentacji **Jigsaw** - generatora stron statycznych opartego o elementy ze środowiska PHP / Laravel.

Po paru próbach i przy założeniu, że chcę mieć na gałęzi **master** zmiany w kodzie samego bloga, a w osobnym repozytorium w sam blog, podział repozytoriów wygląda następująco:
* repozytorium z kodem, gałąź **master** - zmiany w kodzie bloga,
* repozytorium z kodem, gałąź **publishing** - gałąź na której będę buduję statyczną zawartość strony i skąd jest robiony za pomocą **git subtree split** zrzut do gałęzi, z której kod jest wysyłany do osobnego repozytorium, w którym jest zaktywowane github pages
* repozytorium z blogiem, gałąź gh-pages - tu jest publikowany blog

Poniżej opis krok po kroku jak to zrobić.

1. Budujemy strony statyczne na gałęzi **publishing**, commit zmian

2. Wydzielamy zbudowane strony (domyślnie w katalogu **_site**) do osobnej gałęzi - zawierającej tylko te zbudowane strony

```
git subtree split --prefix _site -b <nazwa_brancha_gh_pages>
```

3. Robimy checkout do nowej gałęzi, push do gałęzi repozytorium, wskazując którą lokalną gałąź do której zdalnej wysyłamy - gdzie publikujemy nasze strony

```
git push -f <nazwa_zdalnego_repo_gh_pages> <nazwa_lokalnego_brancha_gh_pages>:<nazwa_zdalnego_brancha_gh_pages>
```
4. Usuwamy lokalnie gałąź gh-pages
```
git branch -D <nazwa_brancha_gh_pages>
```

Flaga **-f** powyżej raczej będzie konieczna - w publikacji mniej istotna jest (dla mnie osobiście) historia, w swoim modelu publikacji w jednym repo trzymam kod źródłowy stron, w innym kod html/css/js stron do publikacji, każde jest podłączone do repo o innej nazwie (jedno origin, drugie blog).

