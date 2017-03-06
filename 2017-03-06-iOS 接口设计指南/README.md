## 通知定义

```objective-c
// .h
FOUNDATION_EXPORT NSNotificationName const NSSystemClockDidChangeNotification;

// .m
NSNotificationName const NSSystemClockDidChangeNotification = @"NSSystemClockDidChangeNotification";
```

优势：

-   更规范，一看就知道是 Notification；
-   系统直接地址匹配，不用 `isEqualToString:`；

## 是否可空

```objective-c
nullable
nonnull
```

### 整个头文件直接使用宏标记

```objective-c
NS_ASSUME_NONNULL_BEGIN

@interface NSDate : NSObject <NSCopying, NSSecureCoding>

@end

NS_ASSUME_NONNULL_END
```

### 属性

```objective-c
@property (nullable) NSNumber *status;
```

### 参数

```objective-c
- (void)doSomethingWithString:(nullable NSString *)str;
```

## NS_DESIGNATED_INITIALIZER 与 NS_UNAVAILABLE

如果类有唯一的初始化方法，便将其他方法标记为 `NS_UNAVAILABLE`。

如果类有多个初始化方法，而其中其他初始化方法都会调用同一个初始化方法（如 `init`），则将 `init` 标为 `NS_DESIGNATED_INITIALIZER`。

```objective-c
- (instancetype)initWithTitle:(NSString *)title date:(NSDate *)date NS_DESIGNATED_INITIALIZER;

+ (instancetype)new NS_UNAVAILABLE;
- (instancetype)init NS_UNAVAILABLE;
```

因为系统的 `NS_UNAVAILABLE` 不能带 message，所以可以加一个带 message 的宏：

```objective-c
#define NS_FUNC_UNAVAILABLE(_msg) __attribute__((unavailable(_msg)));

+ (instancetype)new NS_FUNC_UNAVAILABLE("Use initWithTitle: instead");
```

## 优先验证参数合法性

```objective-c
[NSException raise:@"error parameter" format:@"user id can not = %@", userID];
```

对必要的参数进行合法性验证，并直接 crash，方便错误跟踪。为确保线上没问题，可仅在 debug 环境下操作。

并且避免使用 `NSAssert`，防止出现 block 中循环引用问题。

```objective-c
#if DEBUG
#define YY_PARAMETER_EXCEPTION(condition, des) \
if (!(condition)) { \
[NSException raise:@"com.mocha.error" format:des]; \
}
#else
#define YY_PARAMETER_EXCEPTION(condition, des)
#endif
```

## 泛型

```objective-c
NSDictionary<NSMutableString *, NSMutableString *> *dic1;
– (NSEnumerator<ObjectType> *)objectEnumerator;
@property (nonatomic,readonly,copy) NSArray<__kindof UIView *> *subviews;
```

## 方法参数属性标识

```objective-c
// 不在方法内部修改参数
- (void)constTest:(const int)var;

// 在不修改接口的情况下，标明某个参数未用到
- (void)initWithAvg1:(unused int)avg1 avg2:(int)avg2;
```

## 强制调用 Super

```objective-c
- (void)viewWillAppear:(BOOL)animated NS_REQUIRES_SUPER;
```

## 标记弃用

```objective-c
// 方法弃用
- (void)testDeprecated OBJC_DEPRECATED("v5.0");

// 枚举弃用
typedef NS_ENUM(NSInteger, TestEnum) {
    TestEnum1,
    TestEnum2 __deprecated_enum_msg("v5.0"),
    TestEnum3
};
```

## 标记无状态

用于函数的返回值仅与入参有关，并且函数状态单一。`pure` 除了与入参有关外，还与全局变量有关。如果大量用到，最好写为 C 函数，优化调用。

```objective-c
- (void)func OS_PURE;
- (void)func OS_CONST;
```

