# RF24 custom fork for RPi Utils

Forked from https://github.com/nRF24/RF24. This fork was created to allow passing custom user data to the interruption handler, in form of a pointer, in order to execute complex behavior when receiving a message. This is only valid for the **RPi utility set**.

**See http://tmrh20.github.io/RF24 for all documentation**

## Main differences

Normally, you will attach the interruption handler this way:
```cpp
attachInterrupt(std::stoi(config.at("int_pin")), INT_EDGE_FALLING, fnHandler);
```

With this fork you can also pass a pointer to an object:
```cpp
attachInterrupt(std::stoi(config.at("int_pin")), INT_EDGE_FALLING, fnHandler, *ptrObject);
```

For example:
```cpp
// In your main.cpp or wherever you want:
DomoApp domo(mgr, radio);
attachInterrupt(std::stoi(config.at("int_pin")), INT_EDGE_FALLING, DomoApp::answerMessage, &domo);

// In the DomoApp class:
static void answerMessage(void* self);
void DomoApp::answerMessage(void* domo)
{
    DomoApp* self = reinterpret_cast<DomoApp*>(domo);
    LOG_DEBUG << "Interruption triggered";
    // Whatever...
}
```

Changes are at RF24/utility/RPi. The attach signature method has changed...

from
```cpp
int attachInterrupt(int pin, int mode, void (* function)(void))
```
to
```cpp
int attachInterrupt(int pin, int mode, void(*function)(void*), void* userData)
```
