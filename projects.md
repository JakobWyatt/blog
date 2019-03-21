---
layout: page
title: Projects
permalink: /projects/
---

## [Maple Interpreter](https://github.com/JakobWyatt/open_maple)
open_maple is an open source interpreter for the proprietary mathematics programming language, maple.  
It was written in go, and includes tools to build a maple AST and traverse a maple AST.

## [libDNN](https://github.com/JakobWyatt/libdnn)
libDNN was my first major project, written during my last year of high school.  
It includes a linear algebra library and an inheritance-based model for building and executing neural networks.
This allows different network layers to be used together, and custom layers to be implemented very easily.  
I'm planning to refactor a lot of the code once C++ 20 comes out, and use a concepts/constraints model rather than inheritance. This will allow better memory locality and less runtime indirection, allowing even faster speeds due to caching.  
I'll also parallelise the linear algebra using std::execution_policy.

## [In Progress: Vegan Shop](https://github.com/JakobWyatt/Vegan_Shop)
An app that finds foods for you in stores, based on your dietary requirements. Written using Xamarin and C#/.NET, with a backend in ASP.NET and SQL. Still a work in progress.

## [Blackjack](https://github.com/JakobWyatt/R_Blackjack)
This is a simple, ~150 line R program that allows you to play against the dealer in a game of blackjack.  
Great for practicing your strategies before you go to the casino, as the dealer follows the same rules they do in real life.

## [BMP Writer](https://github.com/JakobWyatt/bmp_writer)
A C++ library for creating bitmap images, using a modern C++ iterator interface. Optimized for extremely fast read/write speeds.

## [The Collection](https://github.com/JakobWyatt/The-Collection)
A collection of useful C++ features, all put into one library. Includes parallelized linear algebra operations, generic container views (before the GSL made them cool), and some nice type manipulation functions.  
This was a collaborative project between a friend and I.

## [Music Library](https://github.com/JakobWyatt/Music_Library)
A command line tool to help manage song metadata.
Allows the user to search for music, filter songs, and save music libraries to disk.  
This was written in Java, as part of a Curtin University assignment submission.
