## Explicitní implementace interface metody

```c#
interface I1 {
    public void m1();
    public void m2();
}
class A : I1 {
    public void m1(){...}
    void I1.m2(){...}   // zadna viditelnost !!!
}

// nelze volat primo na A
A a = new A();
a.m1();  // ok
a.m2();  // error
(a as I1).m2()  // ok
```

Příklad:

```c#
interface IReader {
    public void Close();
}
interface IWriter {
    public void Close();
}
class ConsoleReaderWriter : IReader, IWriter {
    void IReader.Close() {...}
    void IWriter.Close() {...}
    public void Close() {
        ((IReader) this).Close();
        ((IWriter) this).Close();
    }
}
```

## Abstract static interface methods

Brand new feature -- C# 10/11. Umí to i static abstract properties.

```c#
interface I1 {
    public void m1(I1 this, int x);
    public static abstract void m2(int y);  // no implicit I1 this !!!
}

class A : I1 {
    public void m1(A this, int x) {...}
    public static void m2(int y) {...}  // no implicit A this !!!
}

A a = new A();
a.m2();  // error
A.m2();  // ok
```

## Virtual static interface methods

```c#
interface I1<TSelf> where TSelf : I1<TSelf> {
    public static abstract int Value { get; }
    public static virtual int Times(int n) {
        return TSelf.Value * n;
    }
}

class A : I1<A> {
    public static int Value { get; } = 5;
    // Times pouziva defaultni implementaci toho interfacu
}

class B : I1<B> {
    public static int Value { get; } = 5;
    public static int Times(int n) {   // nemusim override
        return B.Value + n;   // jina implementace
    }
}
```
