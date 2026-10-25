这是 The Cherno《CLASSES in C++》的笔记整理。这一集的核心观点是：**类本质上只是一种代码组织方式（语法糖），它把数据和操作数据的函数打包在一起，本身并不增加任何新的底层功能**。

### 类是什么：从散乱变量到打包

没有类的时候，描述一个游戏玩家需要散落的变量和独立的函数：
```cpp
int playerX, playerY;
int playerSpeed = 2;
void Move(int& x, int& y, int speed) { /* ... */ }
```

有了类之后，数据和操作被“打包”成一个整体：
```cpp
class Player {
    int x, y;
    int speed;
    void Move(int xa, int ya) {
        x += xa * speed;
        y += ya * speed;
    }
};
```

`Player` 是**蓝图**，`Player player1;` 创建出来的 `player1` 是**对象（实例）**。每个对象都有自己的一份 `x`、`y`、`speed`。

### 默认访问权限：私有

`class` 中的一个关键细节：**不写访问修饰符时，成员默认是 `private` 的**。

```cpp
class Player {
    int x, y;        // 默认 private，外部无法访问
public:
    int health;      // 显式声明 public，外部可以访问
private:
    int secret;      // 私有，仅类内部可用
};
```

如果你在类外写 `player1.x = 5;`，编译器会报错，因为 `x` 是私有的。`public` 和 `private` 的作用是**控制谁能看到和修改这些数据**，这就是封装的基本形式。

### 方法：属于类的函数

类内部的函数叫**方法**。它和普通函数最大的区别是：方法**自动知道自己属于哪个对象**，可以直接访问该对象的成员变量，不需要像普通函数那样把对象作为参数传进来。

对比一下：
```cpp
// 独立函数：必须显式传入对象
void Move(Player& player, int xa, int ya) {
    player.x += xa * player.speed;
}

// 类方法：直接访问自己的成员
void Move(int xa, int ya) {
    x += xa * speed;   // x 和 speed 就是当前对象的
}
```

调用时也更自然：`player1.Move(1, -1);`。

### 关键要点总结

- **类是蓝图，对象是实例**。类本身不占运行时内存，创建对象时才分配。
- **`class` 默认 `private`**，需要对外暴露的成员必须显式写 `public`。
- **方法是属于类的函数**，自动绑定到调用它的对象，可直接访问该对象的私有数据。
- **类只是组织手段**：它不创造新能力，只是把原本散落的变量和函数收拾到一个结构里，让代码更清晰、更易维护。