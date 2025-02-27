# Introduction

## Introduction

Dryly speaking, the "goal" of this part of the book is to establish a theoretical understanding, terminology, and vocabulary that ill serve us well in the following parts of the book. That is, I want to have a unified source that could be used to initiate anyone into discussing any of the advanced disparate topics that I plan to cover in the following parts.

However, my _true_ goal is to get the reader excited about the theory of decentralized consensus. Why? Because it is _beautiful_.&#x20;

Consensus theory is elementary enough to make approachable to a relatively broad audience, while deep enough to provide (with _some_ effort) rewarding a-ha! moments that will hopefully make the reader finish this book (or whatever parts of it) feeling smarter than they were before reading. While not requiring high proficiency in any particular branch of math, consensus theory provides a nice sight-seeing tour through realms of probability, combinatorics, graph theory, game theory, cryptography, algorithmics, and so on. And if all of that isn't enough, the story of this scientific paradigm shift is wrapped in mystery, espionage, and aspirations for a better world.

That being said, this is _not_ a historical review. While I do point out major milestones, I present the theory in a uniform, modern approach, one that naturally risen to consolidate the many different works and ideas that diverged from Satoshi et al.'s original work.

I am hopeful that _any_ reader could better appreciate cryptocurrencies and the struggles they are facing with the tools and thought frameworks in this volume, even if they do not decide to apply this insight to study GHOSTDAG.

## Overview

TODO: rewrite to accommodate new structure

This part introduces a will be divided into six chapters. In the first, we visit the place where it all began: the Byzantine generals problem, and consider Byzantine fault tolerance and where it stands with regards to proof-of-work.

The second chapter will be all about block _chain_ protocols, starting from a systemic framework for describing them as _chain selection rules_ and culminating with the two most ubiquitous examples: the Heaviest Chain Rule and GHOST.

In the third chapter, we dive deep into security, of blockchains and in general. We discuss what a security property even _is_ before isolating the common security properties used for block chains. We will describe safety, liveness, and confirmation time and, for the mathier audience, we will also sketch the proof of Bitcoin's security.

In the fourth chapter we will expend the ideas of the second chapter to define the blockDAG paradigm by replacing _selection_ rules with _ordering_ rules, and generalize some of the security discussion of the third chapter to this extended context.
