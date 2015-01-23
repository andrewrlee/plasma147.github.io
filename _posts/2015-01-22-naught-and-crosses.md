---
layout: post
title: Naughts and Crosses in clojurescript
category: Software
tags: [Clojure, Clojurescript, Game]
date: 2015-01-22
---

<p>Naughts and crosses in clojure and clojurescript.</p>

I wanted to write a simple project that took advantage of the ability of clojure and clojurescript to have the same code that can run in the terminal and in the browser.

I used [cljx](https://github.com/lynaghk/cljx) to separate out the main game logic and then referred to that module in both a .clj file for a server impl and .cljs for the client side.

The project can be found [here](https://github.com/plasma147/clj-noughts-and-crosses) and the clojurescript version of the game can be played below. The terminal version can be run directly from the generated uberjar. 

<script type="text/javascript" src="/clj-noughts-and-crosses/game.js"></script>


