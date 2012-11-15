AS3-Worker-Compat
=================

ActionScript Worker wrapper for compatibility with all AS3 versions of the
Flash Player (9 and later)

About
=====

What it does
------------

The WorkerCompat wrapper simply uses dynamic-lookup to determine if the Worker
API is available and supported.  This allows SWFs compiled with this code to be
playable on all version of the Flash Player, not just those with Worker support
(11.4 and later).

What it doesn't do
------------------

There's no magic - if your SWF is running in an environment without threading
support, there will still be UI blocking and pseudo-threading.  You'll need
to write your application with this in mind.  However, using this library
just gives you the peace of mind that your code will run on all Flash Players
(and even compile to AIR for mobile) without crashing, and it'll simply take
advantage of Worker threads when they're available.

Also, be aware when using dynamic/runtime class lookup, you don't get
compile-time type checking on those classes (Worker, WorkerDomain, etc).
However, when building the demos, I found that I didn't use those classes
at all but to instantiate my worker.  After that, it's all application logic.

Features
========

What's New in v0.2
------------------

The major new feature in v0.2 is XTSharedObject.  If your first concern is a
SWF that plays in all Flash Players, your second concern is how to easily get
your threads communicating together.

XTSharedObject is a dead-simple Object that's shared between all threads,
again, coded to work the same whether the Flash Player supports Workers or
not.  There are usage requirements / gotchas at the top of the comments in
XTSharedObject.as, and the WorkerCompatTest demo now uses it to pass a count
value from the background worker to the foreground.

While being incredibly easy to use, XTSharedObject is not the most performant
way to share data between threads - if you're passing around large chunks of
data you should use shared ByteArrays (available in FP 11.5+) - maybe I'll
add such support later.

What's New in v0.1
------------------

The initial release, v0.1 introduced the WorkerCompat wrapper.  It contains
the dynamic class lookup calls which are the basis of detecting the Worker
API regardless of Flash Player version.

This wrapper is an alternative to using static imports, which would cause a
runtime-error in Flash Players earlier than 11.4, something like:

<pre>
 An ActionScript error has occurred:
  ReferenceError: Error #1065: Variable flash.system::Worker is not defined.
</pre>

Demo
====

See the WorkerCompatTest.as example, and the compiled <a href="http://lilcodemonkey.com/github/AS3-Worker-Compat/WorkerCompatTest.swf">WorkerCompatTest.swf</a> for
the demo.  The demo shows a red "radar-like" graphic that is generated
on-the-fly, while also running a CPU-burning while loop.

If AS3 Workers are supported, the two tasks are run on separate threads
and the graphic is a smooth fading radar (subject of course to processor
performance and other system processes.)

If AS3 Workers are not supported, both tasks are run on the same thread
(AS3 is inherently single-threaded) and the radar graphic is choppy.

<a href="http://github.com/jcward/AS3-Worker-Compat/blob/master/demo_output.png">demo_output.png</a> shows examples of both:

<img src="http://lilcodemonkey.com/github/AS3-Worker-Compat/demo_output.png"/>

Most importantly, the above demo SWF can be run on any Flash Player
version 9 or above.

Usage / Conversion
==================

If you're familiar with AS3 Workers, then using WorkerCompat is as simple as changing
this code:

<pre>
  import flash.system.Worker;
  import flash.system.WorkerDomain;

  ...

  if (Worker.isSupported && Worker.current.isPrimordial) {
    var myWorker:Worker = WorkerDomain.current.createWorker(swfbytes);
  }
</pre>

To this code:

<pre>
  import com.lilcodemonkey.WorkerCompat;

  ...

  if (WorkerCompat.workersSupported && WorkerCompat.Worker.current.isPrimordial) {
    var myWorker:* = WorkerCompat.WorkerDomain.current.createWorker(swfbytes);
  }
</pre>

License (FreeBSD)
=================

Copyright (c) 2012, Jeff Ward
All rights reserved.

Redistribution and use in source and binary forms, with or without
modification, are permitted provided that the following conditions are met: 

1. Redistributions of source code must retain the above copyright notice, this
   list of conditions and the following disclaimer. 
2. Redistributions in binary form must reproduce the above copyright notice,
   this list of conditions and the following disclaimer in the documentation
   and/or other materials provided with the distribution. 

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND
ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED
WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE
DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT OWNER OR CONTRIBUTORS BE LIABLE FOR
ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES
(INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES;
LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND
ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT
(INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS
SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.

The views and conclusions contained in the software and documentation are those
of the authors and should not be interpreted as representing official policies, 
either expressed or implied, of the FreeBSD Project.
