---
title: Delegates
---

Delegates are blocking, happen on that frame.


```c++
// MyHeaderFile.h

// Declare a delegate
DECLARE_DYNAMIC_MULTICAST_DELEGATE(FSomeNameDelegate);

class MyClass
{
public:
	// Create it
	FSomeNameDelegate SomeNameDelegate;
}

```
```c++
// MyHeaderFile.cpp
void MyClass::DoSomething()
{
	// Broadcast to listeners
	FSomeNameDelegate.Broadcast();
}
```
```c++
// SomeOtherClass.cpp
void SomeOtherClass::SomeSetup()
{
	// Subscribe to delegate
}
```