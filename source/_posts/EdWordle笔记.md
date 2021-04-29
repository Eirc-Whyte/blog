---
title: EdWordle笔记
date: 2021-03-31 10:27:23
tags:
- 可视化
- 项目开发
- 词云
categories:
- 可视化
---

Edwordle项目主要算法学习笔记

<!--more-->

## 摘要

Edwordle允许用户在保持与其他单词的邻居关系时移动编辑单词，提出了可保持邻居关系的Wordle算法，并将其与带约束的刚体力学系统结合，来构造更加紧密的词云布局。

## EdWordle

> After the wordle is loaded, we first apply a customized rigid body dynamics approach, which helps us to make the layout more compact while preserving the neighborhood relationships

首先使用`customized rigid body dynamics`方法将零散的单词进行紧密化。

> To do so, after each step the rigid body dynamics step is automatically invoked again

在用户移动完某个单词之后，重新使用该方法保持单词的紧密性。

> At any time, the result can be further improved by performing a Re-Wordle

使用`local-wordle-layout`方法进行优化。

> All steps are based on our two-level box representation for the words, which allows us to create compact representations without words squeezing in between characters of other larger words.

所有的操作都基于`two-level box representation`两级矩形表示法来构建单词刚体，使得编辑过程中可以保持单词位置的连贯性以及无重叠。

## Rigid Body Dynamic Based Layout

todos

## Two-level box representation&customized external forces approach

todos

## local Wordle layout

todos