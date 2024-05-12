---
title: "Deep Dive on Lisp Compilation"
date: 2024-04-22T17:25:46-05:00
draft: true
---

#### Phases of a Common Lisp Program's Execution

- *CHATGPT-GENERATED!!!*
```example
1. Read-time: This stage is known as "read-time" because it involves the process of reading textual representations of Lisp code and converting them into Lisp data structures. The reader is responsible for this stage.

2. Compile-time: This stage is called "compile-time" because it involves the compilation of Lisp code into low-level machine code or intermediate representations suitable for execution by the Lisp runtime system. The Lisp compiler handles this stage.

3. Load-time: This stage is known as "load-time" because it refers to the process of loading compiled code or Lisp source code into the Lisp environment. This is typically done using functions like LOAD.

4. Run-time: This stage is called "run-time" because it refers to the time during which the program is executed. The Lisp runtime system manages this stage, controlling the execution flow and providing access to built-in functions and data structures.

5. Debug-time: This stage is known as "debug-time" because it refers to the time during which programmers debug their code to identify and fix errors. Common Lisp provides various debugging tools and techniques to assist programmers during this stage.
```

#### FASL vs Images

- Common Lisp compiles down to FASL (which stands for "fast loading").
- `save-lisp-and-die` with or w/o `:executable t` for either images or executables

#### Different ways of compiling: https://lispcookbook.github.io/cl-cookbook/scripting.html

- SBCL:
```lisp
(sb-ext:save-lisp-and-die #P"path/name-of-executable"
	:toplevel #'my-app:main-function
	:executable t)
``` 

- ASDF:
```lisp
;;;; *.asd
:build-operation "program-op" ;; leave as is
:build-pathname "<here your final binary name>"
:entry-point "<my-package:main-function>"

;;;; Makefile
$(LISP) --load my-app.asd \
	--eval '(ql:quickload :my-app)' \
	--eval '(asdf:make :my-app)' \
	--eval '(quit)'
```
#### Enhancements

- Deploy (ship foreign library deps):
  - Occasionally, your project or its dependencies may rely on dynamic C/C++ shared libraries that are specific to your machine, but which may not be present on a different filesystem. This is where Deploy comes in. Deploy auto-discovers foreign library dependencies and places them in your project's `bin/` directory.
```lisp
:defsystem-depends-on (:deploy)  ;; (ql:quickload "deploy") before
:build-operation "deploy-op"     ;; instead of "program-op"
:build-pathname "my-application-name"  ;; doesn't change
:entry-point "my-package:my-start-function"  ;; doesn't change
```

- Core compression to reduce binary size (SBCL only)
  - CL binaries are quite large. To greatly reduce the size at virtually no cost to the startup, add the following definition to your asd.

```lisp
;;;; Check if this is available in a SBCL repl.
(find :sb-core-compression *features*)
:SB-CORE-COMPRESSION

;;;; *.asdf
;;; Add this anywhere outside of the defsystem.
#+sb-core-compression
(defmethod asdf:perform ((o asdf:image-op) (c asdf:system))
  (uiop:dump-image (asdf:output-file o c)
                   :executable t
                   :compression t))
```

- Deploy and the core compression technique can be combined to create small, portable binaries.

## NOTES

- Cannot save an image with multiple threads running:
  - http://www.sbcl.org/manual/#Saving-a-Core-Image
  - https://stackoverflow.com/questions/47888877/sbcl-building-a-standalone-executable
- FASL files:
  - https://www.common-lisp.com/support/documentation/current/compiling.html#22-fasl-files
- CL cookbook on scripting and binaries:
  - https://lispcookbook.github.io/cl-cookbook/scripting.html
