# annntation-logger
make logger easier for using.

## Problem #1
在实际开发中，我们经常要在方法上打印入参和方法签名,为了方便跟踪问题解决bug。
## Sloution
为什么这个繁琐的打印入参的工作交给AOP?
```java
import com.google.common.base.Joiner;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Pointcut;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Component;

/**
 * @author <a href="i@qiancheng.me">千橙</a>
 */
@Aspect
@Component
public class LogAdvice {

    private static final String COMMA = ", ";

    @Pointcut("execution(* me.qiancheng.qianworks.logger.controller.*.*(..))")
    public void pointcut(){ }

    @Around(value="pointcut()")
    public void log(ProceedingJoinPoint point) throws Throwable {
        long time=System.currentTimeMillis();
        point.proceed();
        Class clazz = point.getTarget().getClass();
        String className = clazz.getName();//获取目标类名
        String methodName = point.getSignature().getName();//获取目标方法签名
        Object[] paramArray = point.getArgs();//获取目标方法体参数
        String params = argsToString(paramArray);
        final Logger LOG = getLogger(clazz);
        LOG.info(className+":"+ methodName+" :: "+(System.currentTimeMillis()-time)+"ms.");
        LOG.info("==>params={}",params);
    }

    private String argsToString(Object[] paramArray) {
        return Joiner.on(COMMA).
                appendTo(new StringBuilder(),paramArray).
                toString();
    }

    private final Logger getLogger(Class<?> clazz) {
        return LoggerFactory.getLogger(clazz);
    }

}
```

## Usage

```java
    @Pointcut("execution(* me.qiancheng.qianworks.logger.controller.*.*(..))")
    public void pointcut(){ }
```    

## Source Code
- <https://github.com/iqiancheng/annotation-logger>
- [简化java开发中的日志引入［注解方式］](http://qiancheng.me/coding/logger-injection-with-annotation.md)


