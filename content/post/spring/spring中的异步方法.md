---
title: spring中的异步方法
description: spring中的异步方法
slug: spring中的异步方法
date: 2022-08-25 14:41:00+0000
categories:
    - Java
tags:
    - Java
---

# spring中的异步方法

@Async注解可以使被修饰的方法成为异步方法，曾经看到一个文章说，异步方法让编程提升了一个台阶，确实在实际项目中，异步方法可以很大的提高用户体验，使用效率，不再是那个一个依赖另一个串行任务，在前一个任务不知道什么时候完成时在那傻等，然而彼此间没有什么强依赖关系。

## 1 配置异步编程

```java
// 基于Java配置的启用方式：
@Configuration
@EnableAsync
public class SpringAsyncConfig { ... }

// Spring boot启用：
@EnableAsync
@EnableTransactionManagement
public class SettlementApplication {
    public static void main(String[] args) {
        SpringApplication.run(SettlementApplication.class, args);
    }
}
```

## 2 使用@Async方法来处理问题

```java
@Service
public class DeviceProcessServiceImpl impleme
nts DeviceProcessService {

    @Autowired
    private DeviceRpcService deviceRpcService;

    @Async("taskExecutor")
    @Override
    public Future<Map<Long, List<ProcessDTO>>> queryDeviceProcessAbilities(List<BindDeviceDO> bindDevices) {
        if (CollectionUtils.isEmpty(bindDevices)) {
            return new AsyncResult<>(Maps.newHashMap());
        }
        List<Long> deviceIds = bindDevices.stream().map(BindDeviceDO::getDeviceId).collect(Collectors.toList());

        List<DeviceInstanceWithProcessResp> devices = deviceRpcService.getDeviceProcessAbility(deviceIds);
        Map<Long, List<ProcessDTO>> deviceAbilityMap = Maps.newHashMap();
        ...
        return new AsyncResult<>(deviceAbilityMap);
    }
}
```

注意返回值为Future

原文章中，提到加了注解但是不生效的问题，但实际上@Async注解和事务注解一样，Spring在生成动态代理类时，当入口方法没有异步注解，通过一个同步方法去调用一个同类的异步方法，是无法使这个方法异步执行的。
