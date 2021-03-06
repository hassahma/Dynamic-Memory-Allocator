NAME
----
A dynamic memory allocator using the worst fit algorithm

SYNOPSIS
--------
void *calloc(size_t nmemb, size_t size);
void *malloc(size_t size);
void free(void *ptr);
void *realloc(void *ptr, size_t size);

DESCRIPTION
-----------
malloc() allocates size bytes and returns a pointer to the
allocated memory. The pointer returned can later be passed to
free() which de-allocates the memory. malloc() returns
NULL if the memory allocation failed. This may occur if the
system is out of memory or the process has exceeded it memory
limit(see getrlimit(2)).

free() frees the memory segment pointed to by pointer which
must point to an address previously returned by malloc().
If another pointer is passed undefined behavior occurs. If a
pointer to a memory segment that has already been freed is
passed to free() undefined behavior occurs. If a NULL
pointer is passed, free() returns immediately with no side
effects.

calloc() allocates enough space for nmemb items of size bytes
each and the initializes the memmory with zeros.

realloc() acts just like malloc() if ptr is NULL and just like
free() if size is 0. Otherwise, realloc() resizes the memory
block pointed to by ptr to match size. This may result in a
new block being allocated and the contents of ptr copied to
the new location. realloc() returns a ptr to the resized
memory block. 

FEATURES
--------
* All memory segments returned by malloc() are 8-byte
  aligned.
* Double-frees are caught and handled with an error message
  and immediate program exit.  
* As blocks of memory are freed, the heap size
* shrinks to minimum size.

USAGE
-----
This implementation of malloc() and free() is packaged as a
shared library. Linking to this library overrides the malloc()
and free() implementation in glibc.

TODO
----
* Optimize malloc_chunk_t struct for size by incorporating the
  'used' flag inside 'size' as a bit-field.  
* Include other optional memory chunk re-use algorithms
  besides 'worst fit'. 
* Make properties such as byte alignment, minimum allocation
  size tunable.
* Write comprehensive test suite.
* Set errno on allocation error to match the glibc API

TESTING
-------
The provided driver program will eventually be a comprehensive test
suite but currently the best way to test this library is by forcing a
known working binary to use it. This can be done by setting the
LD_PRELOAD environment variable.  This library is newly thread safe so
preloading should work with any binary. You can check what libraries a
program is linked against with the ldd command.

Example:`LD_PRELOAD=./libmymalloc.so /bin/vi`

The above method has proved to be an effective form of
testing.
