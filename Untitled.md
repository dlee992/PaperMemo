# Bad example

```C++
int main() {
    MyClass myClass = foo();
    myClass.field = 5; // Unexpected things happen, fooClass.field is still 0.
}

MyClass fooClass;
MyClass & foo() {
    fooClass.field = 0;
    return fooClass;
}
```

