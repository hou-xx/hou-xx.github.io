# AOP (面向切面编程) 系列之二
上接 **java 注解（annotation）**、**AOP (面向切面编程) 系列之一**  。     
使用 Aspactj 注解的方式实现 AOP ，实现和 **AOP (面向切面编程) 系列之一** spring xml 配置方式相同的功能。
## show code
`Talk is cheap, just show you the code.`     
本文使用 AspectJ 注解完成 AOP 切入点、切面方法的定义。
### 用作切入点的注解
这次新建一个用于方法的注解，不再沿用前文的了，以免混淆。     

```
package com.hxx.annotation;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface AnnotationTwo {
    String value();
}
```

为了简单一点只定义一个 value 属性吧，用起来方便。（仅仅只是为了偷懒）

### 切入点和切面方法定义
一个类搞定切入点和切面方法的定义和配置。
为了有（zhi）始(xiang)有(tou)终(lan)，功能和前文的 SpringAspect 完全相同。

     
```
package com.hxx.aop;

import com.hxx.annotation.AnnotationTwo;

import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.After;
import org.aspectj.lang.annotation.AfterReturning;
import org.aspectj.lang.annotation.AfterThrowing;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.aspectj.lang.annotation.Pointcut;
import org.aspectj.lang.reflect.MethodSignature;
import org.springframework.stereotype.Component;

/**
 * <ul>
 * <li>功能说明：使用 aspactj 注解方式实现 AOP </li>
 * <li>作者：tal on 2018/10/17 0017 17:20 </li>
 * <li>邮箱：houxiangxiang@cibfintech.com</li>
 * </ul>
 */
@Aspect
@Component
public class AnnotationAspect {

    /**
     * 定义切入点
     */
    @Pointcut("@annotation(com.hxx.annotation.AnnotationTwo)")
    public void doPoint() {
    }

    /**
     * 围绕 切入点 doPoint() 执行的方法
     *
     * @param point
     * @return
     * @throws Throwable
     */
    @Around("doPoint()")
    public Object doAround(ProceedingJoinPoint point) throws Throwable {
        System.out.println("---------------> around start");
        MethodSignature methodSignature = (MethodSignature) point.getSignature();
        AnnotationTwo annotation = methodSignature.getMethod().getAnnotation(AnnotationTwo.class);
        System.out.println("annotation ---------------> " + annotation);
        Object proceed = 0;
        try {
            proceed = point.proceed();
        } catch (Throwable throwable) {
            throw throwable;
        }
        System.out.println("---------------> around end");
        return proceed;
    }

    /**
     * 被注解方法执行前执行的方法
     *
     * @param point
     */
    @Before("doPoint()")
    public void doBefore(JoinPoint point) {
        System.out.println("---------------> before ");
    }

    /**
     * 被注解方法执行前执行的方法
     *
     * @param point
     */
    @After("doPoint()")
    public void doAfter(JoinPoint point) {
        System.out.println("---------------> after");
    }

    /**
     * 后置异常通知：在方法抛出异常之后执行,可以访问到异常信息,且可以指定出现特定异常信息时执行代码
     *
     * @param point
     */
    @AfterThrowing(pointcut = "doPoint()", throwing = "throwable")
    public void afterThrowing(JoinPoint point, Throwable throwable) {
        System.out.println("---------------> afterThrowing");
        System.out.println(throwable.getMessage());
    }

    /**
     * @param point
     * @param returnValue
     */
    @AfterReturning(pointcut = "doPoint()", returning = "returnValue")
    public void afterReturning(JoinPoint point, int returnValue) {
        System.out.println("---------------> afterReturning +++ " + returnValue);
    }

}

```

都用了 AspectJ 注解，肯定是要引入 aspectj 依赖的。按照惯例，本文只举例 maven 的 pom 文件依赖方式，jar 包版本号一般选择最新版即可。
```
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjrt</artifactId>
    <version>${aspectj.version}</version>
</dependency>
```

细心的你一定能发现在类上我们用了一个 spring 的注解 `@Component` ，这里只是为了声明由 spring 自动实例化并管理我们的 AnnotationAspect 。
用到了 spring 注解，就要引入 spring 支持嘛：

```
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context-support</artifactId>
    <version>${spring.version}</version>
</dependency>
```

如果不交给 spring 管理或者自己手动实例化的话，那就不需要使用  `@Component` 注解，也不需要引入 spring 支持。

### 使用到的 aspactj 注解
再次重申：注解是 **元数据** ，**本身不具备任何业务处理能力**。        
 aspactj 注解的背后有一套逻辑处理，我们只需要使用这些注解即可。        

- **@Aspect**     
声明这是个切面，需要进行 AOP 的处理，类似 xml 配置方式中的 `aop:aspect` 标签。     
支持一个参数，指明切面的构造方式，默认单例（singleton），一般默认即可。    

- **@Pointcut**     
定义切入点，类似 xml 配置方式中的 `aop:pointcut` 标签。     
参数是切入点的表达式（expression），上例中 `"@annotation(com.hxx.annotation.AnnotationTwo)"` 是指代所有被 AnnotationTwo 注解标注的地方。 expression 的写法有很多种，此处继续挖坑，以后填。       
`@Pointcut` 标注的方法名会作为切入点在下面的注解中使用到，但本身这个注解非必需，且方法体并不会被执行。

- **@Around**       
围绕切入点执行的逻辑方法，类似 xml 配置方式中的 `aop:around` 标签。      
被标注的方法同样可以根据条件拒绝执行切点方法，也可以处理过程中产生的异常（吞掉或者包装）。   
方法名不重要，不会出现在任何显式被调用或者配置的地方（与 xml 配置方式不同）。   
注解参数是切入点表达式或者或者 `@Pointcut` 标注的方法名（二者等效，都指代一条切入点规则），本例中是后者。

- **@Before**              
在切入点执行前执行的内容，类似 xml 配置方式中的 `aop:before` 标签。     
被标注的方法名同样不重要，注解参数同 `@Around` 。  

- **@After**                
在切入点执行后执行的内容，类似 xml 配置方式中的 `aop:fter` 标签。     
其余同 `@Before`。

- **@AfterThrowing**        
切入点发生异常（且没有被 @Around 吞掉）时执行的方法，类似 xml 配置方式中的 `aop:after-throwing` 标签。       
如果不需要拿到异常（居然大部分场景都关心异常的吧），则和 `@Before` 配置没有差别。     
需要拿到异常时，注解参数 `pointcut` 表示切入点，`throwing` 表示接收异常的参数名。

- **@AfterReturning**       
切入点返回之后执行的逻辑，类似 xml 配置方式中的 `aop:after-returning` 标签。    
注解参数与 `@AfterThrowing` 类似，`returning` 表示接收返回值的参数名。

### 模拟使用场景
为了方便对比（懒癌上身），在 **AOP (面向切面编程) 系列之一** 文章的 ApiDemo 中添加一个方法，模拟模拟新的业务场景。其他配置不变，依旧交给 spring 管理 bean ，支持 spring 注解、支持 aop 。       

```
package com.hxx.api;

/**
 * <ul>
 * <li>功能说明：模拟业务接口</li>
 * <li>作者：tal on 2018/10/18 0018 14:34 </li>
 * <li>邮箱：houxiangxiang@cibfintech.com</li>
 * </ul>
 */

public interface ApiDemo {
    /**
     * 模拟业务方法定义1
     *
     * @param input 输入
     * @return 输出
     */
    int work(int input);

    /**
     * 模拟业务方法定义2
     *
     * @param input 输入
     * @return 输出
     */
    int workTwo(int input);
}
```

ApiDemo 的实现中添加 workTwo ，并使用我们刚定义好的 `AnnotationTwo`

```
package com.hxx.api.impl;

import com.hxx.annotation.AnnotationDemo;
import com.hxx.annotation.AnnotationTwo;
import com.hxx.api.ApiDemo;
import com.hxx.enums.UserType;

import org.springframework.stereotype.Service;

@Service("apiDemo")
public class ApiDemoImpl implements ApiDemo {

    @AnnotationDemo(time = 1, count = 2, name = "admin", userType = UserType.SYSTEM_ADMIN)
    public int work(int input) {
        System.out.println("------->> ApiDemo work");
        return 3 / input;
    }

    @AnnotationTwo("houxx")
    public int workTwo(int input) {
        System.out.println("------->> ApiDemo workTwo");
        return 5 / input;
    }

}
```

### 结果测试
在前文中的 junit 测试用例中添加两个测试方法：
```
package com.hxx.api;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

import javax.annotation.Resource;

@RunWith(SpringJUnit4ClassRunner.class) //使用junit4进行测试
@ContextConfiguration(locations = {"classpath*:applicationContext.xml"}) //加载配置文件
public class ApiDemoTest {

    @Resource(name = "apiDemo")
    private ApiDemo apiDemo;

    @Test
    public void testWork() throws Exception {
        apiDemo.work(1);
    }

    @Test
    public void testWorkException() throws Exception {
        apiDemo.work(0);
    }

    @Test
    public void testWorkTwo() throws Exception {
        apiDemo.workTwo(4);
    }

    @Test
    public void testWorkTwoException() throws Exception {
        apiDemo.workTwo(0);
    }

} 
```

运行 testWorkTwo() 可以得到结果：

```
---------------> around start
annotation ---------------> @com.hxx.annotation.AnnotationTwo(value=houxx)
---------------> before 
------->> ApiDemo workTwo
---------------> around end
---------------> after
---------------> afterReturning +++ 1
```

执行结果完全符合预期，且和 `testWork()` 的表现完全一致。 
我们再运行一下 testWorkTwoException() ，测试一下发生异常的情况：

```
---------------> around start
annotation ---------------> @com.hxx.annotation.AnnotationTwo(value=houxx)
---------------> before 
------->> ApiDemo workTwo
---------------> after
---------------> afterThrowing
/ by zero

java.lang.ArithmeticException: / by zero
    at com.hxx.api.impl.ApiDemoImpl.workTwo(ApiDemoImpl.java:22)
    ……
```
当出现异常且在 around 中没有吞掉时，afterThrowing 执行了，after 也执行了，但 afterReturning 无法再执行，around 中抛出异常后的语句也无法再执行。这和 testWorkException 的表现也完全一致。 
在来看一下 around 中吞掉异常的情况(仅仅注释 doAround 的 throw 语句)：
```
---------------> around start
annotation ---------------> @com.hxx.annotation.AnnotationTwo(value=houxx)
---------------> before 
------->> ApiDemo workTwo
---------------> around end
---------------> after
---------------> afterReturning +++ 0
```
由此看到可以在 around 中吞掉异常，给回默认返回值，表现也和 xml 配置方式完全一样。

## 对比 spring xml 配置方式和 AspctJ 注解方式
首先，二者能达到完全一样的效果。    
从使用上来看，spring 重度依赖患者可能更喜欢前者。    
但 **java 注解（annotation）** 一文中曾经说过：
> 很多情况下，XML 配置其实就是为了分离代码和配置而使用的。但有些场景，我们更希望与代码紧密结合。

我认为，这就是符合这个场景的实例。从代码习惯上来说，更喜欢切点、切面的定义和配置出现在一起，而不是拆分为两处。     
如果没有强制规范的话，喜欢那种方式就可以用那种方式，写得开心就好。不过，非常不建议混搭~