这是 The Cherno《How to Write a C++ Class》的笔记整理。这一集不再讲类的语法，而是用一个具体的 **Log（日志）类** 作为例子，演示如何从需求出发，一步步设计出一个有实际用途的类。核心思想是：**先想清楚类要解决什么问题，再决定它需要哪些数据和方法**。

### 需求驱动：先写用法，再写类

Cherno 的写法是先不急着定义类，而是在 `main` 里写出你希望这个类怎么用。对于 Log 类，目标用法是：
```cpp
Log logger;
logger.SetLevel(Log::LevelWarning);
logger.Info("App started");   // 不输出
logger.Warn("Low memory!");   // 输出
logger.Error("Critical!");    // 输出
```
先明确“外部希望怎么调用”，再回头设计类内部的结构，这样能避免写出一堆用不上的东西。

### 类的设计：数据私有，接口公开

根据上面的用法，Log 类需要：

**私有的数据**：当前日志级别，控制哪些消息会被打印。命名上使用 `m_` 前缀，这是 C++ 中常见的约定，一眼就能看出是成员变量。
```cpp
private:
    int m_LogLevel;
```

**公开的接口**：一个设置级别的方法，以及三个打印不同级别消息的方法。
```cpp
public:
    void SetLevel(int level) { m_LogLevel = level; }
    void Error(const char* message);
    void Warn(const char* message);
    void Info(const char* message);
```

每个打印方法内部都做同一件事：**先检查当前级别是否允许输出这条消息**，只有满足条件才打印对应的前缀。例如 `Warn` 方法：
```cpp
void Warn(const char* message) {
    if (m_LogLevel >= Warning)
        std::cout << "[WARNING]: " << message << std::endl;
}
```

### 用枚举代替“魔法数字”

上面的级别如果用 `0`、`1`、`2` 来硬编码，代码会很难读。更清晰的做法是在类内定义一个枚举：
```cpp
enum Level {
    Error = 0, Warn, Info
};
```
这样写 `SetLevel(Log::Warn)` 或 `if (m_LogLevel >= Warn)`，意图一目了然，不需要去查“1 到底代表什么级别”。

### 关键要点总结

- **从用法反推设计**：先在 `main` 里写出期望的调用方式，再决定类里放什么，这是写出“能用”的类的有效方法。
- **数据私有，接口公开**：`m_LogLevel` 放在 `private`，外部只能通过 `SetLevel` 修改，这是封装的基本形态。
- **枚举提升可读性**：不要用魔法数字表示状态或级别，用 `enum` 给它们起名字。
- **每个方法职责单一**：`Error`/`Warn`/`Info` 各自只做“判断级别 + 打印对应前缀”，逻辑清晰，互不干扰。