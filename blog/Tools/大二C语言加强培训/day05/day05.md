# day05

## 结构体,共用体和枚举类型

>   结构体是程序员自定义数据类型，把不同类型的数据组合成一个整体来自定义类型

### 结构体定义

>   占用内存空间大小为成员变量的加和(如果不考虑字节对齐)

*   定义形式

```
struct 结构体名
{
    类型名 成员名;
    类型名 成员名;
    ... ...
    类型名 成员名;
}变量名表;
```

*   其他的定义形式

    *   方式一

        ```
        struct Studeng
        {
        	int age;
        	char name[32]; 
        }s1;
        ```

    *   方式二

        ```
        struct
        {
           int age;
           char name[30]
        }s1;
        ```

    *   方式三

        ```
        typedef struct student
        {
            int age;
            char name[30];
        }Student;
        Student s1, s2;
        ```

### 结构体初始化

*   结构体变量的i初始化

    ```
    结构体类型名 结构体n变量=[={初始值表};]
    ```

*   结构体数组的初始化

    ```
    结构体类型名 结构体数组名[数组长度] [={初始值表}];
    ```



### 共用体定义

>   与结构体一样,内存分配为几个变量公用一块内存空间,占内存空间最大的变量,即为整个共用体的内存大小

```
union 类型标识符
{
  共用体成员表
};
```

### 共用体变量引用

>   `共用体变量名.成员名`

### 枚举类型

>   enum 类型标识符 { 枚举值名表 };
>
>   *   不写默认从0开始
>   *   当前值的数值是它前面的值+1
>
>   例:
>
>   `emum weekday {sun, mon, tue, wed, thu, fri, sat};`

## typedef

### 用法

>   `typedef 类型名 新类型名`

## 指针

### 指针变量的定义

>   `基类型名 * 指针变量名[=&变量名]`

### 指针相关的运算符

>*   `*`指向运算符
>*   `&`取地址运算符,取出某变量的内存地址

### 指针数组和数组指针

*   指针数组是一个数组,每块内存都是指针类型的

    >   `int * arr[12];`,可以不写,但是通过初始值列表来判断大小.

*   数组指针是一个指针,指向一个数组

    >   `int (* arr)[12];`,必须要写大小,指向一个多大的数组