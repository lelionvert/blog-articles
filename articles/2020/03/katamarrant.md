---
title: "Les Katas, c'est marrant."
author: Maxime Reynier 
published_date: 2020-03-02
description: Les Kata c'est marrant, Les katamarants... Oui, bon, je vais vous expliquer.
tags: kata, practice, software craftsmanship, training
---

Dans le monde du développement, il est important de s'entraîner régulièrement pour apprendre ou confirmer des compétences, que ce soit sur un aspect du métier de développeur ou sur un langage.
La plupart du temps, il n’est pas facile de s'entraîner sur les projets de développement sur lesquels on travaille.
C'est pourquoi il est intéressant de s'entrainer sur des exercices de plus petites tailles sur des domaines plus précis, que l'on appelle des Kata.


Dans cet article, je vais vous présenter ce que sont les `Kata`; en quoi peuvent-ils vous aider, et comment bien les utiliser.


## Kata Kezako ?

Le nom `Kata` est tiré des arts martiaux. Il s’agit d’une chorégraphie de mouvements, inspirée de combattants expérimentés. Ces mouvements sont utilisés pour la transmission de connaissances, de techniques ou de principe de combat.
Pour le Kata, l’idée est de répéter des mouvements basiques ou complexes au point que l’on puisse les reproduire sans avoir à y réfléchir consciemment, aka **Mémoire Musculaire**.

![Morpheus](/images/2020/03/katamarrant/morpheus.gif)

Un `Code Kata` reprend ce concept et l’applique au monde du développement. L'entrainement se fait selon le même principe ; par des exercices spécialisés pour travailler des aspects spécifiques du code. 

## Katas spécialisés  

La plupart du temps, un kata va avoir des consignes et des règles simples à respecter (en fonction de la difficulté du kata ) permettant de s'entraîner plus facilement que sur des projets réels.
En fonction de l’aspect du code que l’on souhaite améliorer, l'exercice du kata va changer de forme.

### Refactorisation

Le kata va plutôt être un projet avec un métier limité : quelques fichiers et du code comprenant des soucis de design plus ou moins évidents.
Le but est de s'entrainer à devoir améliorer le code de façon progressive et sans causer de régressions.

![Refactoring Hell](/images/2020/03/katamarrant/refactoring_hell.gif)

L'un des katas les plus connus de ce type est celui ci : [Gilded Rose Kata](https://github.com/emilybache/GildedRose-Refactoring-Kata)

Vous arrivez sur le projet en tant que nouveau developpeur et vous allez devoir ajouter une feature à celui-ci.
Seul problème, voici le code :

```java
package com.gildedrose;
  
  class GildedRose {
      Item[] items;
  
      public GildedRose(Item[] items) {
          this.items = items;
      }
  
      public void updateQuality() {
          for (int i = 0; i < items.length; i++) {
              if (!items[i].name.equals("Aged Brie")
                      && !items[i].name.equals("Backstage passes to a TAFKAL80ETC concert")) {
                  if (items[i].quality > 0) {
                      if (!items[i].name.equals("Sulfuras, Hand of Ragnaros")) {
                          items[i].quality = items[i].quality - 1;
                      }
                  }
              } else {
                  if (items[i].quality < 50) {
                      items[i].quality = items[i].quality + 1;
  
                      if (items[i].name.equals("Backstage passes to a TAFKAL80ETC concert")) {
                          if (items[i].sellIn < 11) {
                              if (items[i].quality < 50) {
                                  items[i].quality = items[i].quality + 1;
                              }
                          }
  
                          if (items[i].sellIn < 6) {
                              if (items[i].quality < 50) {
                                  items[i].quality = items[i].quality + 1;
                              }
                          }
                      }
                  }
              }
  
              if (!items[i].name.equals("Sulfuras, Hand of Ragnaros")) {
                  items[i].sellIn = items[i].sellIn - 1;
              }
  
              if (items[i].sellIn < 0) {
                  if (!items[i].name.equals("Aged Brie")) {
                      if (!items[i].name.equals("Backstage passes to a TAFKAL80ETC concert")) {
                          if (items[i].quality > 0) {
                              if (!items[i].name.equals("Sulfuras, Hand of Ragnaros")) {
                                  items[i].quality = items[i].quality - 1;
                              }
                          }
                      } else {
                          items[i].quality = items[i].quality - items[i].quality;
                      }
                  } else {
                      if (items[i].quality < 50) {
                          items[i].quality = items[i].quality + 1;
                      }
                  }
              }
          }
      }
  }
```
Une forêt de `if`, imbrication plusieurs niveaux, `magic number`, etc... 

Evidemment pas de tests unitaires disponibles pour pouvoir refactorer sereinement.

### Test Driven Development

Si le but du kata est d'améliorer sa pratique du [TDD](https://fr.wikipedia.org/wiki/Test_driven_development), le sujet du Kata va être un métier très simple auquel on va pouvoir appliquer le TDD sans perturbations que l’on pourrait avoir dans des projets réels.

![TDD_HELL](/images/2020/03/katamarrant/tdd_hell.png)

Exemple de Kata pour le TDD : [FizzBuzz](http://codingdojo.org/kata/FizzBuzz/)

Vous allez devoir, de manière itérative en écrivant les tests avant le code, developper un programme ayant comme sortie : 

```
  1
  2
  Fizz
  4
  Buzz
  Fizz
  7
  8
  Fizz
  Buzz
  11
  Fizz
  13
  14
  FizzBuzz
  16
  17
  Fizz
  19
  Buzz
  ... etc up to 100
  ```
  
Les multiples de 3 doivent être remplacés par `Fizz`, les multiples de 5 par `Buzz`, les multiples de 3 et de 5 par `FizzBuzz`

Il existe encore plein de sujets sur lesquels les katas peuvent nous aider à nous améliorer.

* BDD
* Mock
* SOLID
* etc...

En fonction de la difficulté et des contraintes (de temps par exemple) que vous vous imposez les Kata peuvent vous permettre d’apprendre les bases, de maîtriser les fondements d’un principe, ou alors de vous challenger en appliquant ces principes sur plusieurs katas.
En finalité, cela vous permet de confirmer vos acquis.
 
Quelques exemples de contraintes que vous pouvez vous imposer : 
 
 * Primitive Obsession :
    * Le principe est de ne pas utilisé directement des primitives (int, long, ...), mais plutôt des _Classes_ ayant un sens métier.
    ```java
     int money = 10; // Invalide
    ``` 
    
    ```java
     Money money = new Money(10); // Valide
    ```
 * Immutabilité
    * Aucun effets de bord
    * Chaque modification d'un objet ne le change pas mais renvoie une nouvelle instance de cet objet.
    
## Comment faire un Kata ? 

La première chose qu’il me vient en tête quand je veux faire un `Kata`, c’est de le faire à **plusieurs**.
En effet, le but d’un `Kata` est d’apprendre et de s'entraîner. Toujours dans cette idée, quand vous allez travailler à plusieurs, que ce soit en `mob` (_plusieurs developpeurs 1 pc_) ou en `pair` (_2 developpeurs 1 pc_), vous allez partager des connaissances et apprendre non seulement du kata mais aussi des autres.

![pair_prog](/images/2020/03/katamarrant/pair_prog.gif)

Ce qui peut être très enrichissant aussi, c’est de faire plusieurs groupes. Chacun d'eux travaille sur le kata pendant une durée définie.
A la fin de celle-ci, vous allez faire une **revue de code** par groupe et encore une fois partager et apprendre. Répétez cette étape sur plusieurs itérations.

Il est toutefois possible de faire un kata tout seul. En fonction de votre niveau, il est intéressant de se mettre une contrainte de temps pour vous challenger, mais cela ne sert à rien de se corser la tâche si vous n’avez pas encore **maîtriser les bases**.

Les Katas ne sont pas des exercices one shot. En effet, il faut refaire plusieurs fois un kata si on veut vraiment en exploiter le plein potentiel. Le but étant de pouvoir appliquer des principes de **manière systématique**, par **réflexe**. 

## Conclusion   

Les katas sont de bons outils d’apprentissage et de partage. Il est primordial de retenir que c’est la répétion qui importe et surtout que l’important n’est pas la destination mais le voyage !

> And yet the performance is not the goal. No martial artist practices his art so that they can perform on stage. A martial artist practices to achieve personal perfection in the art of self defense.
 ~ by [Uncle Bob Martin](https://sites.google.com/site/unclebobconsultingllc/home/articles/what-s-all-this-nonsense-about-katas) in _What's all this Nonsense about Katas?_


## Sources et Liens utiles

- [Uncle Bob Martin](https://blog.cleancoder.com/)
- [Kata Log](https://kata-log.rocks/)

Auteur: [Maxime Reynier](https://maximernr.github.io)



