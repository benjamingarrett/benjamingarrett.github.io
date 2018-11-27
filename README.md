TopicTrender100
===============

## Introduction

This project centers around the development of a scalable, high performance, concurrent module capable of processing a stream of topics arising from their use as hashtag themes embedded in messages composed and distributed on some social media application, eg. Twitter, Facebook, etc. A priority queue of topics is maintained and ranked by how many times a given topic has been mentioned.

-----------------

## Scope and Objectives

Most social media platforms now offer the ability to annotate messages that users publish to their profiles. These annotations are prefixed by a symbol, such as "#". For example, the famous squash player and former world champion, Ramy Ashour commonly uses the annotations #tamethegame #spoton #nyc and others in his Facebook and Instagram posts.

For the purposes of this module, the task of parsing out these annotations within messages and stripping off the annotation prefix is assumed to have been accomplished by another, separate module. Furthermore, any constraints on the length of these annotations, the usage of prohibited characters, disallowed words, and other criteria that would render a given annotation invalid are assumed to have been taken care of by a separate module.

Another design decision that is important in this context is how to handle annotations that obviously refer to the same theme but have minor differences in their spelling and case. For example users might wish to adhere to a given theme by annotating their messages in a similar manner to other messages they've already seen. To do so they might see the annotation #tamethegame and write annotations like #TameTheGame, #TAMETHEGAME, #tame_the_game, and similar. Strictly speaking these annotations would not be considered as exact matches and would somehow "pollute" the list of trending topics and actually cause the topic #tamethegame to have a lower popularity rating than it would otherwise have if we intelligently considered the above variants to be semantically equivalent. For the purposes of this module such considerations are assumed to have already been dealt with in a module upstream from this one.

Thus the input stream of topics is assumed to conform to all relevant application constraints and will be processed as a valid, unique topic. Topics are assumed to conform to the length constraints defined in this module. Topics having length greater than `MAXTOPICLENGTH` are truncated before further processing.

------------------

## Policies and Terminology

This module offers the following interface:

  * **processTopic**: receives as input one topic string and optionally a number indicating how many times it has recently been mentioned, ie. its `usageFrequency`, assumed to be one if omitted.
  * **trendingTopics**: returns a list of the topics sorted in descending order of popularity. By default the top ten topics are returned.

The following terminology is defined:

  * **topic**: a unique, case-insensitive, string of ASCII characters having length less than or equal to `MAXTOPICLENGTH`.
  * **usageFrequency**: an unsigned 64-bit, positive integer indicating the aggregate number of times a given topic has been mentioned at the time a call to the service `trendingTopics` is made.
  * **MAXTOPICLENGTH**: an unsigned 64-bit, positive integer indicating the maximum length of any valid `topic`.
  * **NUMTOTALTOPICS**: an unsigned 64-bit integer indicating the maximum number of topics that is to be kept track of in the `topicPriorityQueue`.
  * **NUMTRENDINGTOPICS**: an unsigned 64-bit integer indicating the number of top trending topics to report when the service `trendingTopics` is called.
  * **cacheReplacementStrategy**: the name of the policy used to determine which topics are removed from the `topicPriorityQueue` when new topics arrive and the `topicPriorityQueue` already has `NUMTOTALTOPICS` items being tracked. In case of situations in which there are more than one `topic` having the least `usageFrequency` a decision must be made about which `topic` must be removed from the `topicPriorityQueue`.
  * **topicPriorityQueue**: a data structure containing key/value pairs, where the key is a `topic` and the value is its `usageFrequency`.

------------------

