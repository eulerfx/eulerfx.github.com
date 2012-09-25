---
layout: post
title: "Retry with the .NET Task Parallel Library (TPL)"
date: 2012-03-22 23:06
comments: true
categories: [c#, tpl]
---

The [.NET Task Parallel Library](http://msdn.microsoft.com/en-us/library/dd460717.aspx) simplifies development of and reasoning about asynchronous code. 
However, working with [Task](http://msdn.microsoft.com/en-us/library/system.threading.tasks.task.aspx) instances instead of blocking method results can still be difficult and un-intuitive. 
One example where this is true is in implementing retry logic for functions that return Task instances. 
Consider this helper function which retries a provided function a specified number of times:

{% gist 2161734 %}

This was a response to [this Stackoverflow.com question](http://stackoverflow.com/questions/6090026/how-to-implement-retry-logic-with-task-parallel-librarytpl) and works as expected for blocking methods. 
However, suppose you have a function which returns a Task<TResult> and you would like to decorate it with retry logic as well. 
The above implementation won't work because it will only handle exceptions that are thrown by calling the task returning function, 
not exceptions thrown inside the task. What is needed is something that will retry based on task faults. 
The basic idea is to add a continuation to the returned task that will determine whether a task is faulted, 
examine the exception and retry or fail accordingly. The following is a sample implementation:

{% gist 2161819 %}

The core functionality is in the RetryContinuation method. It takes a task instance, the original task returning function, 
a number of remaining attempts and a predicate function which determines whether a given exception should result in a retry. 
This function calls itself recursively until the task is either successful or the maximum number of attempts have been reached.