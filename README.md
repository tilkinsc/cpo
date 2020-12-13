![CPO](https://user-images.githubusercontent.com/7494772/102008644-22519b80-3d00-11eb-9110-d0b88f46e9d1.png)

# CPO
Collect Process Output - A welcomed cross-platform command line application programming paradigm

# Building
CPO is only a header and source file. You can directly compile and link against cpo.c, create a dll and import library, or create a static library. Each has it's own uses.

Using the source directly:
```batch
gcc -Wall -g0 -O2 -s -o example.exe main.c cpo.c
```

Creating and linking against a static library:
```batch
gcc -Wall -g0 -O2 -o cpo.o cpo.c
ar rcs -o libcpo.a cpo.o

gcc -Wall -g0 -O2 -s -L. -o example.exe main.c -lcpo
```

Creating and linking against a DLL:
```batch
gcc -Wall -g0 -O2 -DCPO_DLL_EXPORT -o cpo.o cpo.c
gcc -Wall -g0 -O2 -shared -Wl,--out-implib=libcpo.dll.a -o cpo.dll cpo.o

gcc -Wall -g0 -O2 -s -L. -o example.exe main.c -lcpo.dll
```

## Why use the paradigm?
It is simple, efficient, easy on the brain, and the majority of the programming is deciding what you want your switches to be while actually implementing your features. Also known as it's possible to make skeleton code for jumping straight into a project. (In fact, said skeleton code is included)

## Core description
This library's mission statement is to simplify the flow of a command line application by introducing 3 steps in a cross platform manner: Collect, Process, and Output. These steps are defined as follows. Collect is the collection process, in which command line arguments are gathered, evaluated based on the programmers requirements (create rules like no -E with -W), and to collect the file and stdin streams. Process is the meat of an application, in which, based on command line arguments, data is processed and commited to output-ready buffers when finished. Output is the last step of an application, in which, based on command line arguments, data is sent from output-ready streams or to stdout.

## Why the paradigm?
Throughout my programming career, I have seen many programs in which the programmer(s) chose to handle command line arguments themselves and attempt to either construct bitflag booleans out of an unsigned int, or create a list of booleans in .data, juggling all those variables around. The latter is followed by actually implementing logic based on them while the former is a lot of preprocessor work and manual bittwiddling. This involves a lot of manual string matching, operating system dependent programming, bugs, and several oddities such as the lack of switch compatibility, one true or OS dependent switch prefix, and an aggregate reuse of code between seperate programs by separate entities (why reinvent the wheel?). This also includes the oddity of bigger programs take 3rd party projects and create dll's to use very simple programs functionality such as common linux commands on windows.

## Features and detailed description
CPO comes with many features. Let's abuse them.

### Errors
Errors happen. User input is required after all. Who handles this? Both CPO and the user application.  
CPO grants you the ability to collect errors and handle them through a callback system, much like OpenGL does.  
Sometimes you even get some data in the form of a string indicating the errornous string.  

```C
// .text
void CPOErrorCallback(Return error) {
	fprintf(stderr, "An error has occured.\n > code: `%Iu`\n > string: `%s`\n > data: `%p`\n", error.code, error.string, error.data);
}

// main
CPOSetErrorCallback(CPOErrorCallback);
```

In order to correctly handle the error, you have to identify what it is.  
The error code is found through `error.code`.  
The error message is found through `error.string`.  
The error faulting data is found through `error.data`, however you can't rely on it having data in all circumstances.  

Utilize the defines found in the header to decide what errors you want to handle.  
You have the power to crash hard at any error or crash under certain ones.  

```C
// .text
void CPOErrorCallback(Return error) {
	fprintf(stderr, "An error has occured.\n > code: `%Iu`\n > string: `%s`\n > data: `%p`\n", error.code, error.string, error.data);
  switch(error.code & CPO_ERROR_TYPE_MASK) {
  case CPO_ERROR_API:
      fputs("This was a API error, not your fault.\n", stderr);
      break;
  case CPO_ERROR_USER:
      fprintf("This was a user error, your fault. %s\n",
  }
}

// main
CPOSetErrorCallback(CPOErrorCallback);
```
