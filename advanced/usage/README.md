---
description: Learn how to make NgRX Auto-Entity for you
---

# Advanced Usage

## What is it?

NgRX Auto-Entity aims to provide a seamless means of utilizing a standard set entity actions and effects with minimal repetitive code requirements, while preserving the fundamental nature of NgRX itself. This library is _**not**_ a replacement for or alternative to NgRX. It works within the standard paradigm that NgRX has set forth, making use of actions, reducers & effects like any other NgRX application.

#### Generic, reusable, flexible

What Auto-Entity does do is provide a set of ready-made, **generic** actions for handling all of the standard _CRUD operations_ for _**entities**_, so you neither have to write nor generate any of that code yourself. Auto-Entity presents a flexible framework that you may use in its entirety for all of your entity needs, or use piecemeal as necessary in order to achieve your specific goals.

**High Performance and Efficiency**

Auto-Entity has been implemented with high performance in mind, and is capable of reducing very large data sets. We have tested NgRx Auto-Entity with up to millions of records in state, while adding millions more, with reduction times in the fractions of a second. We do not generally recommend loading such significant volumes of data into your Angular applications, and for more common usage at much lower volumes, you should encounter no performance limitations with Auto-Entity. 

Should your application need to work with millions of entities in state in the browser, NgRx Auto-Entity has been tested under high entity volumes. With tens to hundreds of thousands of entities, reduction times should be sub-second, although subject to memory limitations. For millions of entities, performance may vary with memory, and under limited memory situations reductions of very large volumes of entities may require more time. Tests with MacBook Pros with 16 gigabytes of ram, reduction of 2 million entities takes 0.5 to 2 seconds under normal usage.

#### Compatibility with @ngrx/entity

 While it is not required and Auto-Entity is an entirely independent library that solely depends on Angular 8/9/10 and NgRX 8/9/10, Auto-Entity manages state in a manner that is compatible with @ngrx/entity as well, in the event you wish to utilize some of the utilities from that library in your own custom reducers.







