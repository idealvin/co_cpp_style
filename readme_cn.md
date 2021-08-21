## CO 中的 C++ 代码风格 [(English)](readme.md)

本文档简要介绍 [CO](https://github.com/idealvin/co) 的 C++ 代码风格，仅供参考。



### Basic

- 基本缩进：**4 个空格**，不使用 `Tab`。
- `{` 保持在同一行，不另起一行。



### namespace

- namespace 名字用小写。
- namespace 中的代码，相对于 namespace 无缩进。
- namespace 结束时，加上注释 `// namespace`。

```cpp
namespace xx {

int kk = 0;
void f();

} // xx
```



### class

- 一般使用 [Pascal case](https://en.wiktionary.org/wiki/Pascal_case) 命名方式，单词首字母大写，如 `WaitGroup`。少数情况可以使用小写形式，如 `fs::file`, `fs::stream`, `xx::log_time_t`。
- `public`, `protected`, `private` 相对关键字 `class` 缩进 2 个空格。
- 私有成员名以 `_` 开头，如 `int _n;`，利于编辑器自动补全。
- 类中的方法采用 [snake case](https://en.wiktionary.org/wiki/snake_case) 命名方式，与 C++ 标准库、unix 风格保持一致，如 `recv`, `try_lock`。

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

- 构造函数中的初值列表

```cpp
WaitGroup(WaitGroup&& wg) : _p(wg._p) {
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

- 函数名采用 [snake case](https://en.wiktionary.org/wiki/snake_case) 命名方式，与 C++ 标准库、unix 风格保持一致。

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



### 变量

- 变量名一般采用 [snake case](https://en.wiktionary.org/wiki/snake_case) 命名方式，如 `day`, `data_size`。
- static 变量名可以 'k' 开头，如 `kCpuNum`。
- 全部变量名可以 'g' 开头，如 `gCpuNum`。

```cpp
static int kNum = 0;
int gThreadNum = 8;

void f() {
    int v = 0;
    int cpu_num = 8;
}
```



### 宏

- 宏名一般用大写，如 `LOG`, `DLOG`, `CHECK_EQ`。



### enum

- enum 使用与 class 相同的命名规范。
- enum 成员一般用小写，以免与宏冲突。

```cpp
enum LogLevel {
    debug = 0,
    info = 1,
    warning = 2,
    error = 3,
    fatal = 4
};

enum co_state_t : uint8 {
    st_init = 0,     // initial state
    st_wait = 1,     // wait for an event
    st_ready = 2,    // ready to resume
    st_timeout = 4,  // timeout
};
```


### 语句

- if 语句

```cpp
if (!flag) goto no_value;

if (v > 0) {
    LOG << "v > 0";
} else {
    LOG << "v <= 0";
}
```

- 循环语句

```cpp
for (int i = 0; i < 108; ++i) {
    // do something here
}

while (true) {
    // do something here
}

do {
    // something
} while (0);
```

- switch 语句
  - `case`, `default` 相对于 `switch` 缩进 2 个空格。

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
