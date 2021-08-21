## C++ code style for CO [(中文)](readme_cn.md)

This document is a brief introduction of C++ code style for [CO](https://github.com/idealvin/co).



### Basic

- Basic indent: **4 spaces**, do not use `Tab`.
- Keep `{` on the same line, do not start a new line.



### namespace

- Use lowercase names.
- Do not indent code in the namespace.
- Add a comment `// namespace` at the end of the namespace.

```cpp
namespace xx {

int kk = 0;
void f() {
    int v = 0;
}

} // xx
```



### class

- Follow the [Pascal case](https://en.wiktionary.org/wiki/Pascal_case) naming style, with the first letter capitalized, e.g. `WaitGroup`. In some cases, lowercase can be used, e.g. `fs::file`, `fs::stream`, `xx::log_time_t`.
- Indent `public`, `protected` and `private` by 2 spaces relative to the keyword `class`.
- Names of private members start with `_`, such as `int _n;`.
- Methods in the class follow the [snake case](https://en.wiktionary.org/wiki/snake_case) naming style, e.g. `recv`, `try_lock`.

```cpp
class Mutex {
  public:
    Mutex();
    ~Mutex();

    void lock();
    void unlock();
    bool try_lock();

  private:
    co::xx::mutex_t _mutex;
    DISALLOW_COPY_AND_ASSIGN(Mutex);
};
```

- Initializer Lists

```cpp
WaitGroup(WaitGroup&& wg): _p(wg._p) {
    wg._p = 0;
}

SchedulerImpl::SchedulerImpl(uint32 id, uint32 sched_num, uint32 stack_size)
    : _wait_ms((uint32)-1), _id(id), _sched_num(sched_num),
      _stack_size(stack_size), _running(0), _co_pool(),
      _stop(false), _timeout(false) {
    _main_co = _co_pool.pop();
}
```



### function

- Follow the [snake case](https://en.wiktionary.org/wiki/snake_case) naming style, e.g. `recv`, `try_lock`.

```cpp
namespace co {

int bind(sock_t fd, const void* addr, int addrlen) {
    return ::bind(fd, (const struct sockaddr*)addr, (socklen_t)addrlen);
}

inline void set_tcp_nodelay(sock_t fd) {
    int v = 1;
    co::setsockopt(fd, IPPROTO_TCP, TCP_NODELAY, &v, sizeof(v));
}

} // co
```



### Variables

- Follow the [snake case](https://en.wiktionary.org/wiki/snake_case) naming style.
- Names of static variables may start with the letter 'k', e.g. `kCpuNum`.
- Names of global variables may start with the letter 'g', e.g. `gCpuNum`.

```cpp
static int kNum = 0;
int gThreadNum = 8;

void f() {
    int v = 0;
    int cpu_num = 8;
}
```



### Macro

- Use uppercase for names of macros, e.g. `LOG`, `DLOG`, `CHECK_EQ`.



### enum

- Follow the naming style of class.
- Use lowercase for enum members.

```cpp
enum LogLevel {
    debug = 0,
    info = 1,
    warning = 2,
    error = 3,
    fatal = 4
};

enum co_state_t: uint8 {
    st_init = 0, // initial state
    st_wait = 1, // wait for an event
    st_ready = 2, // ready to resume
    st_timeout = 4, // timeout
};
```


### Statement

- if statement

```cpp
if (!flag) goto no_value;

if (v> 0) {
    LOG << "v> 0";
} else {
    LOG << "v <= 0";
}
```

- Loop statement

```cpp
for (int i = 0; i <108; ++i) {
    // do something here
}

while (true) {
    // do something here
}

do {
    // something
} while (0);
```

- switch statement
  - Indent `case`, `default` by 2 spaces relative to `switch`.

```cpp
fastring flag_get_value(const Flag* flag) {
    switch (flag->type) {
      case TYPE_string:
        return *static_cast<fastring*>(flag->addr);
      case TYPE_int32:
        return int_to_string(*static_cast<int32*>(flag->addr));
      default:
        return "unknown flag type";
    }
}
```
