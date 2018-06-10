![](/assets/天津一汽tbox与ivi交互流程图.png)


## libdbus-c++ 安装使用
```
0.9.0
DBus-C++ Library Public API Calls

Introduction
DBus-c++ attempts to provide a C++ API for D-BUS. The library has a glib and an Ecore mainloop integration. It also offers an optional own main loop.

How is it installed for Linux?
   ./configure \
   make
   sudo make install
   
How to compile using DBus-C++?
pkgconfig (.pc) files are installed for every DBus-C++ mainloop integration. Thus, to compile using any of them, you can use something like the following:

   g++ *.cpp $(pkg-config dbus-c++-1 --cflags --libs)
```