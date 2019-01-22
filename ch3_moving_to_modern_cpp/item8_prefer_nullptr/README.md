# Item 8: Prefer nullptr to 0 and NULL

In C++, 0 is int, and NULL is the same.

## avoid misuse function

They don't have pointer type.

    void f(int);
    void f(bool);
    void f(void*);

    f(0);  // calls f(int), not f(void*)
    f(NULL);  // Might not compile, but typically calls f(int), not f(void*)

C++98: Avoid overloading on pointer and integral types.

nullptr has type std::nullptr_t. They type std::nullptr_t implicitly converts to all raw pointer types.
    
    f(nullptr);  // calls f(void*) overload

## nullptr is more clear
``` c++
    // not clear whether result is pointer
    auto result = findRecord(...);
    if (result == 0) {
        ...
    }

    // result is a pointer
    if (result == nullptr) {
    }
```
## Pass 0 and NULL to a template function may not work

0 and NULL in template function may be deduced as type int, so may not be used further.
```c++
    template<typename FuncType,
             typename MuxType,
             typename PtrType>
    decltype(auto) lockAndCall(FuncType func,     // c++14
                              MuxType& mutex,
                              PtrType ptr) {
      MuxGuard g(mutex);
      return func(ptr);
    }

    int f1(std::shared_ptr<Widget> spw);
    int f2(std::unique_ptr<Widget> upw);
    bool f3(Widget* pw);

    std::mutex f1m, f2m, f3m;
    
    auto result1 = lockAndCall(f1, f1m, 0);       // error! The problem is that when 0 is passed ot **lockAndCall**,the type of 0 is always **int**,that's also 
    ...                                           // the type of the ptr,so that's not compatible with the **std::shared_ptr<Widget>** parameter that f1 expects.
    auto result2 = lockAndCall(f2, f2m, NULL);    // error! The problem is essentially the same.
    ...
    auto result3 = lockAndCall(f3, f3m, nullptr); // okay!
    ...
```

## Things to Remember

* Prefer nulltpr to 0 and NULL.

* Avoid overloading integral and pointer types.
