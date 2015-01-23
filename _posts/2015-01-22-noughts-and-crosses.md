---
layout: post
title: Noughts and Crosses in clojurescript
category: Software
tags: [Clojure, Clojurescript, Game]
date: 2015-01-22
---

<p>Noughts and crosses in clojure and clojurescript.</p>

I wanted to write a simple project that took advantage of the ability of clojure and clojurescript to have the same code that can run in the terminal and in the browser.

I used [cljx](https://github.com/lynaghk/cljx) to separate out the main game logic and then referred to that module in both a .clj file for a server impl and .cljs for the client side.

The project can be found [here](https://github.com/plasma147/clj-noughts-and-crosses) and the clojurescript version of the game can be played below. The terminal version can be run directly from the generated uberjar. 

<link href="/clj-noughts-and-crosses/style/main.css" rel="stylesheet" type="text/css">

<div class="game-container">
  <div class="grid-container">
    <div class="grid-row">
      <div class="grid-cell"></div>
      <div class="grid-cell"></div>
      <div class="grid-cell"></div>
    </div>
    <div class="grid-row">
      <div class="grid-cell"></div>
      <div class="grid-cell"></div>
      <div class="grid-cell"></div>
    </div>
    <div class="grid-row">
      <div class="grid-cell"></div>
      <div class="grid-cell"></div>
      <div class="grid-cell"></div>
    </div>
    <div class="grid-row">
      <strong id="message"></strong>
      <a id="reset" class="restart">RESTART</a>
    </div>
  </div>
</div>

<script type="text/javascript" src="/clj-noughts-and-crosses/game.js"></script>
<br style="clear:both;">

