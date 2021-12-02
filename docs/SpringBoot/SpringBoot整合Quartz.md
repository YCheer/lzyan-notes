## Quartz简介

Quartz是OpenSymphony开源组织在Job scheduling领域又一个开源项目。

是完全由java开发的一个开源的任务日程管理系统，“任务进度管理器”就是一个在预先确定（被纳入日程）的时间到达时，负责执行（或者通知）其他软件组件的系统。 

## Quartz的核心概念

1. Scheduler - 与调度程序交互的主要API，代表一个调度容器，一个调度容器中可以注册多个JobDetail和Tigger。当Tigger与JobDetail组合，就可以被Scheduler容器调度。
2. Job - 你想要调度器执行的任务组件需要实现的接口，表示一个作业要执行的具体内容。
3. JobDetail - 用于定义作业的实例，Job是这个可执行调度程序所要执行的内容，另外JobDetail还包含了这个任务调度的方案和策略。
4. Trigger（即触发器） - 定义执行给定工作的计划的组件，代表一个调度参数的配置，什么时候去调度。
5. JobBuilder - 用于定义/构建 JobDetail 实例，用于定义作业的实例。
6. TriggerBuilder - 用于定义/构建触发器实例。

Scheduler 的生命期，从 SchedulerFactory 创建它时开始，到 Scheduler 调用shutdown() 方法时结束；Scheduler 被创建后，可以增加、删除和列举 Job 和 Trigger，以及执行其它与调度相关的操作（如暂停 Trigger）。但是，Scheduler 只有在调用 start() 方法后，才会真正地触发 trigger（即执行 job）

## Quartz的定时器种类

Quartz 中五种类型的 Trigger：SimpleTrigger，CronTirgger，DateIntervalTrigger，NthIncludedDayTrigger和Calendar 类（ org.quartz.Calendar）。

最常用的：

SimpleTrigger：用来触发只需执行一次或者在给定时间触发并且重复N次且每次执行延迟一定时间的任务。 
CronTrigger：按照日历触发，例如“每个周五”，每个月10日中午或者10：15分。 

## 存储的方式

`RAMJobStore` 和 `JDBCJobStore`

`RAMJobStore`：不要外部数据库，配置容易，运行速度快。因为调度程序信息是存储在被分配给JVM的内存里面，所以，当应用程序停止运行时，所有调度信息将被丢失。另外因为存储到JVM内存里面，所以可以存储多少个Job和Trigger将会受到限制

`JDBCJobStore`：支持集群，因为所有的任务信息都会保存到数据库中，可以控制事物，还有就是如果应用服务器关闭或者重启，任务信息都不会丢失，并且可以恢复因服务器关闭或者重启而导致执行失败的任务，运行速度的快慢取决与连接数据库的快慢

## 基于RAMJobStore存储方式的简单实现

此实现方式不接入外部数据库，实现对定时任务的开启、暂停、删除、修改等相关操作

### 引入依赖

创建一个springboot项目（略），在pom.xml下引入quartz坐标

```xml
    <!-- quartz 发音：快儿特-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-quartz</artifactId>
    </dependency>
```

### 初始化项目

创建相关的包、类

![](https://resource.lzyan.fun/PigGo/20211127211928.png)

### 编码

entity包下创建`Task.class`，这个作为requestBody，用户传的json数据
```java
public class Task {
    private int id;
    private String jobName;
    private String jobGroup;
    private String todo;
    private String time;

    //getter setter...
}
```
scheduler包下创建`SchedulerJobManager.class`，用来管理调度的任务

```java
@Configuration
public class SchedulerJobManager {

    @Autowired
    private Scheduler scheduler;

    /**
     * 开启定时任务
     *
     * @param task
     */
    public void startJob(Task task) throws SchedulerException {
        // 通过JobBuilder构建JobDetail实例，JobDetail规定只能是实现Job接口的实例
        // JobDetail 是具体Job实例
        JobDetail jobDetail = JobBuilder.newJob(QuartzJob.class).withIdentity(task.getJobName(), task.getJobGroup())
                .usingJobData("jobName", task.getJobName()) //传值，在实现定时任务逻辑的时候取
                .usingJobData("jobGroup", task.getJobGroup())
                .usingJobData("todo", task.getTodo())
                .build();
        // 根据corn表达式创建触发器
        CronScheduleBuilder cronScheduleBuilder = CronScheduleBuilder.cronSchedule("0/5 * * * * ? *");//每5秒执行一次
        // CronTrigger表达式触发器继承于Trigger,常用的还有SimpleTrigger
        // TriggerBuilder 用于构建触发器实例
        CronTrigger cronTrigger = TriggerBuilder.newTrigger().withIdentity(task.getJobName(), task.getJobGroup()).withSchedule(cronScheduleBuilder).build();
        scheduler.scheduleJob(jobDetail, cronTrigger);
    }

    /**
     * 获取job的信息
     */
    public String getJobInfo(String name, String group) throws SchedulerException {
        TriggerKey triggerKey = new TriggerKey(name, group);
        CronTrigger cronTrigger = (CronTrigger) scheduler.getTrigger(triggerKey);
        if (cronTrigger == null) {
            return "cronTrigger is null";
        }
        return String.format("time:%s,state:%s", cronTrigger.getCronExpression(), scheduler.getTriggerState(triggerKey).name());
    }

    /**
     * 暂停定时任务
     */
    public void pauseJob(String name, String group) throws SchedulerException {
        JobKey jobKey = new JobKey(name, group);
        JobDetail jobDetail = scheduler.getJobDetail(jobKey);
        if (jobDetail != null) {
            scheduler.pauseJob(jobKey);
        }
    }

    /**
     * 暂停所有任务
     */
    public void pauseAllJob() throws SchedulerException {
        scheduler.pauseAll();
    }

    /**
     * 恢复所有任务
     */
    public void resumeAllJob() throws SchedulerException {
        scheduler.resumeAll();
    }

    /**
     * 恢复某个任务
     */
    public void resumeJob(String name, String group) throws SchedulerException {
        JobKey jobKey = new JobKey(name, group);
        JobDetail jobDetail = scheduler.getJobDetail(jobKey);
        if (jobDetail != null) {
            scheduler.resumeJob(jobKey);
        }
    }

    /**
     * 修改某个任务的执行时间
     */
    public boolean modifyJob(String name, String group, String time) throws SchedulerException {
        Date date = null;
        TriggerKey triggerKey = new TriggerKey(name, group);
        CronTrigger cronTrigger = (CronTrigger) scheduler.getTrigger(triggerKey);
        String oldCron = cronTrigger.getCronExpression();
        if (!oldCron.equalsIgnoreCase(time)) {
            CronScheduleBuilder cronScheduleBuilder = CronScheduleBuilder.cronSchedule(time);
            CronTrigger trigger = TriggerBuilder.newTrigger().withIdentity(name, group).withSchedule(cronScheduleBuilder).build();
            date = scheduler.rescheduleJob(triggerKey, trigger);
        }
        return date != null;
    }

    /**
     * 删除某个任务
     *
     * @throws SchedulerException
     */
    public void deleteJob(String name, String group) throws SchedulerException {
        JobKey jobKey = new JobKey(name, group);
        JobDetail jobDetail = scheduler.getJobDetail(jobKey);
        if (jobDetail != null) {
            scheduler.deleteJob(jobKey);
        }
    }
}
```

job包下创建`QuartzJob.class`，这个类表示一个作业要执行的具体内容,需要时间Job接口，实现execute方法
```java
public class QuartzJob implements Job {

    private String jobName;
    private String jobGroup;
    private String todo;

    public String getJobName() {
        return jobName;
    }

    public void setJobName(String jobName) {
        this.jobName = jobName;
    }

    public String getJobGroup() {
        return jobGroup;
    }

    public void setJobGroup(String jobGroup) {
        this.jobGroup = jobGroup;
    }

    public String getTodo() {
        return todo;
    }

    public void setTodo(String todo) {
        this.todo = todo;
    }

    @Override
    public void execute(JobExecutionContext jobExecutionContext) {
        System.out.println("执行定时任务的实现逻辑");
        JobKey jobKey = jobExecutionContext.getJobDetail().getKey();
        String todo = getTodo();
        System.out.println("接收自定义参数,定时任务:{name:" + jobKey.getName() + ",group:" + jobKey.getGroup() + "}" + ",todo:" + todo + LocalDateTime.now());
    }
}
```

controller包下创建`QuartzJobController.class`，编写调度定时任务的执行的相关接口
```java
@RestController
@RequestMapping("/quartz")
public class QuartzJobController {

    @Autowired
    private SchedulerJobManager schedulerJobManager;

    /**
     * 开启定时任务
     * 通过传入的数据，实现自定义定时任务的内容或者定时的时间等
     *
     * @param task
     */
    @PostMapping("/start")
    public boolean startQuartzJob(@RequestBody Task task) {
        try {
            schedulerJobManager.startJob(task);
            return true;
        } catch (SchedulerException e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 获取定时任务的信息
     *
     * @param task
     * @return
     */
    @PostMapping("/info")
    public String getQuartzJob(@RequestBody Task task) {
        String info = null;
        try {
            info = schedulerJobManager.getJobInfo(task.getJobName(), task.getJobGroup());
        } catch (SchedulerException e) {
            e.printStackTrace();
        }
        return info;
    }

    /**
     * 修改某个任务信息
     *
     * @param task
     * @return
     */
    @PostMapping("/modify")
    public boolean modifyQuartzJob(@RequestBody Task task) {
        boolean flag = true;
        try {
            flag = schedulerJobManager.modifyJob(task.getJobName(), task.getJobGroup(), task.getTime());
        } catch (SchedulerException e) {
            e.printStackTrace();
        }
        return flag;
    }

    /**
     * 暂停某个任务
     *
     * @param task
     * @return
     */
    @PostMapping("/pause")
    public boolean pauseQuartzJob(@RequestBody Task task) {
        try {
            schedulerJobManager.pauseJob(task.getJobName(), task.getJobGroup());
            return true;
        } catch (SchedulerException e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 暂停所有任务
     *
     * @return
     */
    @PostMapping("/pauseAllJob")
    public boolean pauseAllQuartzJob() {
        try {
            schedulerJobManager.pauseAllJob();
            return true;
        } catch (SchedulerException e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 恢复某个任务
     *
     * @param task
     * @return
     */
    @PostMapping("/resumeJob")
    public boolean resumeJob(@RequestBody Task task) {
        try {
            schedulerJobManager.resumeJob(task.getJobName(), task.getJobGroup());
            return true;
        } catch (SchedulerException e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 恢复所有任务
     *
     * @return
     */
    @PostMapping("/resumeAllJob")
    public boolean resumeAllJob() {
        try {
            schedulerJobManager.resumeAllJob();
            return true;
        } catch (SchedulerException e) {
            e.printStackTrace();
            return false;
        }
    }


    @PostMapping("/deleteJob")
    public boolean deleteJob(@RequestBody Task task) {
        try {
            schedulerJobManager.deleteJob(task.getJobName(), task.getJobGroup());
            return true;
        } catch (SchedulerException e) {
            e.printStackTrace();
            return false;
        }
    }

}
```
创建`SpringbootQuartzListener.class`，这是用来在项目开启，spring容器加载完毕后，启动定时任务，不用可以注释掉
```java
@Configuration
public class SpringbootQuartzListener implements ApplicationListener<ContextRefreshedEvent> {

    @Autowired
    private SchedulerJobManager schedulerJobManager;

    @Override
    public void onApplicationEvent(ContextRefreshedEvent event) {
        Task task = new Task();
        task.setId(1);
        task.setJobName("name1");
        task.setJobGroup("group1");
        task.setTodo("everything");
        task.setTime("ddd");
        try {
         //  schedulerJobManager.startJob(task);
        } catch (SchedulerException e) {
            e.printStackTrace();
        }
    }
}
```

### 测试

这里直接使用IDEA(2021版本)的HTTP Client来进行测试，点击这个小地球图标选择Generate request in HTTP Client

![](https://resource.lzyan.fun/PigGo/20211127232300.png)

在创建的`.http`文件中编写测试接口

```http
###
POST http://localhost:8080/quartz/start
Content-Type: application/json

{
  "id": 1,
  "jobName": "job1",
  "jobGroup": "group1",
  "todo": "do everything1",
  "time": "da"
}

###
POST http://localhost:8080/quartz/info
Content-Type: application/json

{
  "id": 1,
  "jobName": "job2",
  "jobGroup": "group2",
  "todo": "do everything",
  "time": "da"
}


###
POST http://localhost:8080/quartz/pause
Content-Type: application/json

{
  "id": 1,
  "jobName": "job2",
  "jobGroup": "group2",
  "todo": "do everything",
  "time": "da"
}

###
POST http://localhost:8080/quartz/resumeJob
Content-Type: application/json

{
  "id": 1,
  "jobName": "job1",
  "jobGroup": "group1",
  "todo": "do everything",
  "time": "da"
}


###
POST http://localhost:8080/quartz/resumeAllJob
Content-Type: application/json

{
  "id": 1,
  "jobName": "job1",
  "jobGroup": "group1",
  "todo": "do everything",
  "time": "da"
}

###
POST http://localhost:8080/quartz/deleteJob
Content-Type: application/json

{
  "id": 1,
  "jobName": "job1",
  "jobGroup": "group1",
  "todo": "do everything",
  "time": "da"
}

```

启动springboot项目，然后在.http文件中点击启动接口按钮进行测试

![](https://resource.lzyan.fun/PigGo/20211127232740.png)

## 参考

[W3Cschool Quartz官方文档](https://www.w3cschool.cn/quartz_doc/)