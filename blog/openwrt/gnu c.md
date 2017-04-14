# GNU gcc/g++


## g++

## thread

* include thread head. *
```
#inlcude <pthread.h>
```
* compile add -lpthread *

```
$ gcc -o test test.c -lpthread
$ g++ -o test test.cpp -lpthread
```

## thread function

#### thread_create()

`int pthread_create(pthread_t *thread, pthread_attr_t *attr, void *(*start_routine)(void *),void *arg);`

`void *(*start_routine)(void *)`

#### thread_exit()

`void pthread_exit(void *retval);`

#### thread_join()

`int pthread_join(pthread_t th, void **thread_return);`


